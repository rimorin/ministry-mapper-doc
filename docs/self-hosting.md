# Self-Hosting Ministry Mapper

!!! warning "Self-Hosting Not Recommended"
    Self-hosting Ministry Mapper requires significant technical expertise and ongoing maintenance. **We recommend using our hosted service at [ministry-mapper.com](https://ministry-mapper.com) instead.** Only proceed with self-hosting if you have:
    
    - Experienced system administrators or developers
    - Resources for ongoing maintenance and updates
    - Understanding of security best practices
    - Ability to comply with privacy regulations
    - Specific requirements that cannot be met by the hosted service

## Why We Don't Encourage Self-Hosting

While Ministry Mapper is open source, self-hosting comes with challenges:

- **Technical Complexity**: Requires knowledge of Docker, databases, web servers, and cloud infrastructure
- **Security Responsibility**: You're responsible for keeping everything secure and updated
- **Maintenance Burden**: Regular updates, backups, and monitoring are essential
- **Privacy Compliance**: You must ensure GDPR, CCPA, and other privacy law compliance
- **Cost**: Server costs, API costs, and time investment often exceed hosted service fees
- **Support**: Limited community support for self-hosting issues

## Prerequisites for Self-Hosting

If you've decided to self-host despite our recommendations, ensure you have:

### Technical Requirements
- Experience with Linux server administration
- Understanding of Docker and containerization
- Familiarity with environment variables and configuration
- Knowledge of web server configuration (Nginx/Apache)
- Experience with SSL/TLS certificates
- Understanding of DNS and domain configuration

### Infrastructure Requirements
- Cloud hosting service (Railway, Render, DigitalOcean, AWS, etc.)
- Domain name for your instance
- Email service for notifications (optional)
- Budget for optional API costs (Sentry, etc.)

### Time Commitment
- Initial setup: 6-10 hours (see [Time Investment](#time-investment) for the breakdown)
- Ongoing maintenance: 3-7 hours per month
- Emergency support when issues arise

## Architecture Overview

Ministry Mapper consists of two separate components:

1. **Backend (ministry-mapper-be)**: PocketBase server with Go extensions
   - Repository: [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - Technology: Go, PocketBase, SQLite
   - Runs on port 8080 (Docker) or 8090 (local)

2. **Frontend (ministry-mapper-v2)**: React web application
   - Repository: [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - Technology: React, TypeScript, Vite
   - Static files deployed to hosting service

Both must be deployed and configured to work together.

The backend keeps **all** of its state in a single directory, `pb_data/`: the SQLite databases (`data.db` and `auxiliary.db`), their write-ahead logs, uploaded files, `logs.db`, and PocketBase's own configuration. There is no separate database server to run, and no part of the state lives anywhere else. A persistent volume for that directory is not optional.

**How the official instance is deployed**, if you would like to copy it: both components run on [Coolify](https://coolify.io), a self-hostable platform-as-a-service, with [Cloudflare](https://www.cloudflare.com) in front for DNS, TLS and CDN. Coolify builds the backend image straight from the repository, probes it with `GET /api/db-health`, and mounts one volume at `/app/pb_data`; the frontend's release workflow finishes by POSTing a Coolify deploy webhook. That path is documented in more detail in the [Deployment Guide](deployment.md#reference-deployment-coolify-cloudflare). Everything in this guide works the same way whether you use Coolify, another platform, or a bare server.

## Access Model

Worth understanding before you configure anything, because it decides who can do what on your instance.

There are **three congregation roles**, stored one row per person per congregation:

- **`read_only`** - can view territories, maps and the address grid, sort and switch views, get directions, and read messages
- **`conductor`** - everything above, plus assignment links (quick links, the assignments list, assigning a link), creating and updating addresses, and resetting or deleting territories
- **`administrator`** - everything above, plus creating territories and maps, the whole Manage and Congregation menus, editing map codes and floors, deleting units, and generating reports

Then there is a fourth way in that is **not** a role: a **publisher holding an assignment link**. That link is itself the credential - no account, no login, no role row. It is scoped to exactly one map and expires. Anyone holding the link has that access, which is why links should never be posted publicly and why administrators can shorten link expiry to narrow the window.

"Publisher" is therefore an access path, not a role. If you see documentation describing four roles with Publisher as one of them, it is out of date.

The first superuser (the PocketBase admin account at `/_/`) is separate again: it is not a congregation role, it has no `users` record, and it can see and change everything. Treat it as the equivalent of root.

## Backend Self-Hosting Guide

### Backend Environment Configuration

Create a `.env` file with these settings:

```bash
# Application Settings
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# Initial Admin Account (change password after first login!)
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# Email Configuration (SMTP)
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# Security Settings
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# Authentication (optional)
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# Google Sign-In (optional; both required, and absent from .env.sample)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# Error Tracking (optional but recommended)
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# Service Integration (optional)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# AI Report Summaries (optional; also needs a LaunchDarkly flag - see below)
OPENAI_API_KEY=
```

Notes on the ones that catch people out:

- **Nothing here is strictly required.** Every variable has a fallback or a disabled-by-default path. What changes is what stops working - with no MailerSend credentials the digest and report emails do nothing, with no Sentry DSN error reporting is off.
- **Some variables only apply on the very first startup.** `PB_APP_NAME`, `PB_APP_URL`, `PB_ADMIN_EMAIL`, `PB_ADMIN_PASSWORD`, all six `PB_SMTP_*`, `PB_HIDE_CONTROLS`, `PB_ENABLE_RATE_LIMITING`, `PB_OTP_ENABLED`, `PB_MFA_ENABLED`, `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` are read by database migrations rather than by the running server. Once migrations have run against a `pb_data`, changing them and restarting does nothing - change the corresponding setting in the admin UI at `/_/` instead. This is the most common "my configuration change did nothing" complaint.
- **The admin account is created for you.** The superuser is bootstrapped from `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD` on the first migration run - there is no manual account-creation command. Set both: the built-in fallbacks are `testing_account@ministry-mapper.com` and `pb123456789`, which are publicly documented credentials.
- **`PB_OTP_ENABLED` and `PB_MFA_ENABLED` are compared against the literal string `true`.** `TRUE` and `1` are treated as false.
- **`PB_ALLOW_ORIGINS` defaults to `*`** when unset - set it to your frontend origin.
- **Both MailerSend variables are needed together.** The API key alone will not send anything; the digests and monthly reports need `MAILERSEND_FROM_EMAIL` too.
- **`SOURCE_COMMIT`** is meant to be injected by your build platform. It is logged at boot and used as the Sentry release. It is not in `.env.sample`.
- **Google sign-in needs both `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET`**, and neither appears in `.env.sample`, so nothing hints that they exist. Set both or the provider is not configured at all.

### AI Report Summaries

The monthly territory report and the message, note and instruction digests can carry an AI-written summary. It takes **two** things, not one:

1. `OPENAI_API_KEY` set on the backend.
2. The LaunchDarkly flag `enable-report-ai-summary` turned on, which means `LAUNCHDARKLY_SDK_KEY` must also be configured.

Unlike the eight background-job flags - which default to **enabled** when LaunchDarkly is absent - this flag defaults to **disabled** without a LaunchDarkly client. So AI summaries stay off if you self-host without LaunchDarkly, even with a valid OpenAI key. The feature also fails silently: a missing key, an API error or an unparseable response simply omits the summary from the email.

If you do enable it, note what leaves your server: the text of publisher messages and property notes is sent to OpenAI. As the data controller you must disclose OpenAI as a sub-processor in your own privacy policy. See [Privacy and Legal Compliance](#privacy-and-legal-compliance).

### Docker Deployment

1. **Clone the Repository**

    ```bash
    git clone https://github.com/rimorin/ministry-mapper-be.git
    cd ministry-mapper-be
    ```

2. **Build Docker Image**

    ```bash
    docker build -t ministry-mapper-backend .
    ```

    The repository has no `docker-compose.yml`, and there is no published image to pull - you build from source. A cold build's peak memory is the Go linker at roughly 1.76 GB, single-threaded, so a 1 GB or 2 GB host will thrash or be killed. Build on a larger machine and push the image, or build in CI.

3. **Run Container**

    ```bash
    docker run -d \
      --name ministry-mapper \
      --restart unless-stopped \
      -p 8080:8080 \
      -v /srv/ministry-mapper/pb_data:/app/pb_data \
      --env-file .env \
      ministry-mapper-backend
    ```

4. **Access Admin Panel**

    Navigate to `https://your-backend-domain.com/_/` and log in with your admin credentials. Database migrations, including the superuser bootstrap, run automatically on startup - there is no separate migration step.

**Important Notes:**
- Container runs on port 8080 internally
- Mount a persistent volume to `/app/pb_data` to preserve data. Without it, `docker rm` destroys the database
- Your database lives in the `pb_data` folder, along with everything else the app stores
- The image is built for **amd64 only** - there is no arm64 build, so it will not run natively on Graviton, Ampere or Apple-silicon hosts
- The container runs as **root** and declares no `HEALTHCHECK`. Configure the probe on your platform against `GET /api/db-health`, which is unauthenticated and runs a `PRAGMA quick_check`. `curl` is present in the image for exactly this purpose
- Run **one container at a time** against a given `pb_data`. SQLite has a single writer, and two containers on one volume corrupts data

### Local Development

For testing on your local machine:

```bash
# Clone repository
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# Install dependencies
./scripts/install.sh

# Configure environment
cp .env.sample .env
# Edit .env with your settings

# Start application
./scripts/start.sh
```

Backend runs at `http://localhost:8090` by default.

### Backup and Restore

Your data is critical, and all of it is in `pb_data/`. **Lose that directory and you lose everything** - the congregation, its territories and addresses, every audit log, every account, every uploaded file. Nothing else needs backing up, and nothing in the repository backs it up for you. Setting this up is entirely on you.

**Option 1: PocketBase's built-in backups (recommended).** PocketBase can snapshot the whole `pb_data` directory into a single zip archive while the server is running.

- In the admin UI, go to **Settings → Backups**. Use *Initiate new backup* for one now, or enable the automatic schedule and set how many archives to keep. The same screen restores an archive (the server restarts itself afterwards) and can push archives to S3-compatible storage.
- Or drive it over the API with a superuser token:

```bash
# List
curl -H "Authorization: $SUPERUSER_TOKEN" \
  https://your-backend-domain.com/api/backups

# Create
curl -X POST -H "Authorization: $SUPERUSER_TOKEN" \
  https://your-backend-domain.com/api/backups

# Download
curl -H "Authorization: $SUPERUSER_TOKEN" -O \
  https://your-backend-domain.com/api/backups/<name>
```

Archives land **inside** `pb_data/backups/` - on the same volume as the data they protect. Copy them off the host, or configure S3 storage, or the backup will not survive the failure you are backing up against.

**Option 2: archive the directory yourself.** If you would rather copy files, checkpoint the write-ahead log first. SQLite holds recent writes in `data.db-wal`, so a copy of `data.db` alone can be missing the newest changes or be internally inconsistent:

```bash
# Stop writes (ideally stop the container), then:
sqlite3 /srv/ministry-mapper/pb_data/data.db "PRAGMA wal_checkpoint(TRUNCATE);"
sqlite3 /srv/ministry-mapper/pb_data/auxiliary.db "PRAGMA wal_checkpoint(TRUNCATE);"

# Manual backup
tar -czf backup-$(date +%Y%m%d).tar.gz -C /srv/ministry-mapper pb_data

# Automated daily backup (add to crontab)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz -C /srv/ministry-mapper pb_data
```

The `sqlite3` CLI is not installed in the backend image - run it on the host against the mounted volume.

**Restoring:**

- Stop the backend so nothing is writing
- Replace `pb_data` with the archive's contents, or use the built-in restore
- Start the backend; migrations run automatically and are idempotent
- Check `GET /api/db-health` - a `200` means `PRAGMA quick_check` passed against the restored database

**Rehearse it.** An untested backup is a guess. At least quarterly, restore an archive onto a scratch instance, sign in, and open a territory to confirm the data is really there.

### Security Checklist

- [ ] Change default admin password immediately
- [ ] Use HTTPS only (never HTTP)
- [ ] Set `PB_ALLOW_ORIGINS` to specific domain (not `*`)
- [ ] Enable `PB_HIDE_CONTROLS=true`
- [ ] Enable `PB_ENABLE_RATE_LIMITING=true`
- [ ] Configure firewall to restrict access
- [ ] Set up automated backups
- [ ] Configure Sentry for error monitoring
- [ ] Use strong, unique passwords
- [ ] Keep dependencies updated
- [ ] Review PocketBase security best practices
- [ ] Change `PB_ADMIN_EMAIL` and `PB_ADMIN_PASSWORD` away from the shipped defaults before first startup
- [ ] Run exactly one backend container against a given `pb_data`
- [ ] Verify a restore, not just that backups exist
- [ ] Keep backup archives off the host that holds the data

## Frontend Self-Hosting Guide

### Frontend Environment Configuration

Create a `.env` file:

```bash
# System Environment (local | development | staging | production)
VITE_SYSTEM_ENVIRONMENT=production

# PocketBase Backend URL (no trailing slash)
VITE_POCKETBASE_URL=https://your-backend-domain.com

# Maps: tiles, address search and directions (required)
VITE_GEOAPIFY_API_KEY=your_geoapify_key

# Legal Pages (required for sign-up)
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms

# Optional
VITE_ABOUT_URL=https://your-domain.com/about
VITE_MAINTENANCE_MODE=false
VITE_LAUNCHDARKLY_CLIENT_ID=

# Error Tracking (optional but recommended)
VITE_SENTRY_DSN=your_sentry_dsn

# Analytics (optional; both of the first two are needed or nothing is tracked)
VITE_UMAMI_SRC_URL=
VITE_UMAMI_WEBSITE_ID=
VITE_UMAMI_DOMAINS=
```

Notes:

- **These are baked in at build time.** Changing one means rebuilding and redeploying; restarting a web server does nothing. Everything prefixed `VITE_` is shipped to the browser, so none of it is secret.
- **`VITE_GEOAPIFY_API_KEY` is required for maps to work at all.** One [Geoapify](https://www.geoapify.com) key covers all three geo features: map tiles, address autocomplete, and routing for Directions. Without it, tiles do not render, address search returns nothing, and Directions fails. Geoapify is the only geo provider - if you find older instructions mentioning two separate map or routing keys, they are out of date.
- **`VITE_POCKETBASE_URL`** must have no trailing slash. If it is missing, the app renders a "Missing PocketBase URL" setup page instead of loading.
- **Do not set a version variable.** `VITE_APP_VERSION` is injected at build time from `package.json`, and it drives the Sentry release, the version footer and the in-app update check. There is no `VITE_VERSION`.
- **`VITE_MAINTENANCE_MODE=true`** is a build-time kill switch that serves a maintenance page instead of the app. It is compared against the literal string `true`.

Three more are **build-time only** - not `VITE_`-prefixed, never shipped to the browser - and upload source maps so Sentry stack traces are readable:

```bash
SENTRY_AUTH_TOKEN=your_token
SENTRY_ORG=your_org
SENTRY_PROJECT=your_project
```

Keep those as build secrets in your CI or hosting platform. Without them the build still succeeds; you just get minified stack traces.

### Building for Production

```bash
# Clone repository
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# Install dependencies
npm install

# Build
npm run build
```

Output will be in the `build/` directory.

### Deployment Options

#### Static Hosting Services (Easiest)

**Vercel:**
1. Import GitHub repository
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Add environment variables

**Netlify:**
1. New site from Git
2. Build command: `npm run build`
3. Publish directory: `build`
4. Add environment variables

**Cloudflare Pages:**
1. Create Pages project
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Add environment variables

**Coolify** (what the official instance uses):
1. Create a Static Site application pointing at the repository
2. Build command: `npm run build`
3. Output directory: `build`
4. Add environment variables
5. Copy the deploy webhook URL and API token, and store them as CI secrets so your release workflow can trigger a deploy with a **POST** request. The webhook rejects `GET` with a `405`, so use `curl --request POST --fail-with-body` - without `--fail-with-body`, curl exits `0` on that `405` and the deploy silently never happens

#### Self-Hosted Web Server

**Nginx Configuration:**

```nginx
server {
    listen 443 ssl;
    http2 on;
    server_name your-domain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
}
```

Two details in that block are deliberate:

- `listen 443 ssl;` with a separate `http2 on;`. Passing `http2` as a `listen` parameter has been deprecated since nginx 1.25 and logs a warning.
- No `X-XSS-Protection`. That header's filter has been removed from every current browser; it does nothing, and in older browsers it could introduce vulnerabilities of its own. `Referrer-Policy` is a header worth having in its place.

The `try_files ... /index.html` line is the SPA history-mode fallback: any unknown path must be served `index.html` so the client-side router can handle it.

If you also reverse-proxy the **backend** through this nginx, that server block needs one extra setting or live updates will not work - see [Realtime Updates (SSE)](#realtime-updates-sse).

**Apache Configuration:**

```apache
<VirtualHost *:443>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build
    
    SSLEngine on
    SSLCertificateFile /path/to/cert.pem
    SSLCertificateKeyFile /path/to/key.pem

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

### Sentry Setup (Optional)

1. Create account at [sentry.io](https://sentry.io)
2. Create React project
3. Get DSN
4. Add to `.env` as `VITE_SENTRY_DSN`

## Realtime Updates (SSE)

Live updates - a publisher marking a house done, an administrator resetting a map - reach every open browser over **Server-Sent Events**. There are no WebSockets anywhere in Ministry Mapper. In practice SSE is one long-lived HTTP response that trickles events out over minutes or hours, which is exactly the kind of response an ordinary reverse proxy is built to buffer and compress.

When something in front of the backend buffers it, updates arrive in a clump much later, or never - and there is no error on either side to tell you. The app looks fine until two people are working the same map. This is the most common self-hosting problem that is not an outright crash.

**Nginx** - the setting that matters is `proxy_buffering off;`:

```nginx
server {
    listen 443 ssl;
    http2 on;
    server_name api.your-domain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_buffering off;      # required for Server-Sent Events
        proxy_cache off;          # never cache an event stream
        proxy_read_timeout 1h;    # the connection is meant to stay open
    }
}
```

Elsewhere:

- **Apache**: disable output buffering for the proxied vhost (`SetEnv proxy-sendchunked`), and make sure `mod_deflate` does not compress the stream
- **Caddy, Traefik and Coolify's built-in proxy**: these stream by default, nothing to configure
- **Cloudflare or any CDN**: leave `/api/realtime` uncached, and turn off anything that rewrites or aggregates responses
- **Gzip**: compress static assets, never the event stream
- **Header stripping**: publishers reach the app through an assignment link rather than a login, and their subscriptions carry that link in the subscription's own headers. Do not strip unknown request headers at the proxy

One quirk worth knowing before you go hunting: a few operations deliberately do **not** broadcast. Territory cascade deletes and the sequence-repair command write with raw SQL specifically so publishers working a map are not flooded with events. That silence is intentional, not a broken stream.

**How to check it works:** open the app in two browsers on the same map and change a status in one. The other should update within a second or two. If it only updates on reload, something between the browser and the backend is buffering.

## Maintenance and Updates

### Regular Maintenance Tasks

**Weekly:**
- Check Sentry for errors
- Review application logs
- Verify backups are working

**Monthly:**
- Update dependencies: `npm install` and `go get -u`
- Review security advisories
- Test disaster recovery procedures
- Check disk space usage
- Review user feedback

**Quarterly:**
- Security audit
- Performance review
- Update documentation
- Review and update privacy policy
- Test all features thoroughly

### Updating to New Versions

**Backend Update:**
```bash
cd ministry-mapper-be
git pull
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  --name ministry-mapper \
  --restart unless-stopped \
  -p 8080:8080 \
  -v /srv/ministry-mapper/pb_data:/app/pb_data \
  --env-file .env \
  ministry-mapper-backend
```

`docker rm` only removes the container - the data survives because it lives in the mounted volume, not in the container. Any new migrations run automatically when the new container starts. Take a backup first anyway.

**Frontend Update:**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# Deploy build/ folder to your hosting service
```

**Always backup before updating!**

### Monitoring

**What to Monitor:**
- Server uptime and health
- Application error rates (Sentry)
- Database size growth and free disk space
- Backup success/failure, and that archives are leaving the host
- SSL certificate expiration
- User authentication issues
- Performance metrics

**Health endpoints you already have:**
- `GET /api/health` - PocketBase's built-in liveness check. Answers as long as the process is up
- `GET /api/db-health` - the database probe: `PRAGMA quick_check` with a 10-second cap, `200` when the database is structurally sound, `503` plus a Sentry event when it is not. This is the one to point an uptime monitor and a platform healthcheck at. A `503` here means real structural damage, so restore from a backup rather than restarting repeatedly

**Tools:**
- Sentry for error tracking - the only monitoring integration built into either codebase
- Your Geoapify dashboard for map, geocoding and routing usage against your key
- Uptime monitors hitting `/api/db-health` (UptimeRobot, Pingdom, Healthchecks.io)
- Host-level monitoring of your own choosing (Prometheus, Grafana, your provider's metrics). Neither codebase exposes a metrics endpoint or exporter, so these watch the host, not the app
- Log aggregation from container logs (Loki, ELK stack, CloudWatch)

## Troubleshooting Self-Hosted Instances

### Backend Issues

**Port Already in Use:**
```bash
lsof -i :8080
kill -9 <process_id>
```

**Database Locked:**
- Stop all instances accessing the database
- Ensure only one backend process is running
- Check for hung processes

**Email Not Sending:**
- Verify SMTP credentials
- Check firewall isn't blocking SMTP ports
- For Gmail, use App Passwords
- Review the delivery logs in the admin UI under **Logs** (PocketBase stores them in `pb_data/logs.db` - a database file, not a folder of text logs)

**Cannot Access Admin Panel:**
- URL must be `https://your-domain.com/_/` (note underscore)
- Check `PB_HIDE_CONTROLS` setting
- Verify backend is running
- Clear browser cache

**Configuration Change Had No Effect:**
- Many `PB_*` variables are read by migrations, not by the running server, so they only apply on the first run against a `pb_data`
- Change the corresponding setting in the admin UI at `/_/` instead
- `PB_OTP_ENABLED` and `PB_MFA_ENABLED` are compared against the literal string `true` - `TRUE` and `1` count as false

**AI Summaries Never Appear:**
- The feature needs `OPENAI_API_KEY` **and** the LaunchDarkly flag `enable-report-ai-summary`
- Without a LaunchDarkly client that flag defaults to off, unlike the background-job flags which default to on
- It degrades silently by design, so there will be no error - the summary section is simply absent from the email

**Docker Build Killed or Stalls at Linking:**
- The Go linker peaks at roughly 1.76 GB and is single-threaded
- Build on a host with more than 2 GB of RAM, or build elsewhere and pull the image

**Container Will Not Start on an ARM Host:**
- The image is built for amd64 only; there is no arm64 build
- Use an amd64 host

### Frontend Issues

**Blank Map, No Address Search, or Directions Failing:**
- Set `VITE_GEOAPIFY_API_KEY` and rebuild - one key covers tiles, geocoding and routing
- Check the browser console for 401 responses from `geoapify.com`, which mean the key is wrong or out of quota

**Live Updates Never Arrive:**
- The app only refreshing on reload means response buffering is on somewhere in front of the backend
- Set `proxy_buffering off;` in the backend's nginx block and leave `/api/realtime` uncached - see [Realtime Updates (SSE)](#realtime-updates-sse)

**Cannot Connect to Backend:**
- Verify `VITE_POCKETBASE_URL` is correct
- Check CORS settings in backend
- Ensure backend is accessible
- Check browser console for errors

**Build Failures:**
- Ensure Node.js version 24 or newer (the frontend requires `>=24.0.0`)
- Delete `node_modules` and reinstall
- Check all environment variables are set
- Review build error messages

### Performance Issues

**Slow Response Times:**
- Check server resources (CPU, RAM, disk)
- Review database size and queries
- Enable database optimizations
- Check network latency
- Review PocketBase logs for slow queries

## Privacy and Legal Compliance

### Your Responsibilities as Self-Hoster

When self-hosting, you are the data controller and must:

- **Comply with Privacy Laws**: GDPR (EU), CCPA (California), etc.
- **Create Privacy Policy**: Explain data collection and usage
- **Create Terms of Service**: Define user responsibilities
- **Implement Data Security**: Encryption, access controls, backups
- **Handle Data Requests**: User access, deletion, portability rights
- **Report Data Breaches**: Within legal timeframes
- **Maintain Records**: Data processing activities
- **Appoint DPO**: If required by regulations

**Disclose your sub-processors.** Whichever of these you enable, they process your users' data on your behalf and belong in your privacy policy by name and purpose:

- **Geoapify** - map tiles, address geocoding, routing
- **MailerSend** - digest and report email
- **Sentry** - error tracking
- **LaunchDarkly** - feature flags
- **Umami** - analytics, if enabled
- **Google** - OAuth2 sign-in, if enabled
- **OpenAI** - AI summaries, if enabled. Note specifically that the text of publisher messages and property notes is sent to OpenAI when this feature is on
- **Your hosting and CDN providers**

Also be accurate about what you store. Beyond names and email addresses, the app records address coordinates, last-login timestamps, and three audit-log collections covering address status changes, assignment grants and revocations, and role changes. Accounts have their own lifecycle: an account with no role assigned is disabled after 7 days and permanently deleted at day 37, while an inactive account is disabled at 183 days and never auto-deleted. State those retention periods rather than leaving them implicit.

### Required Legal Documents

You must provide:

1. **Privacy Policy** - Required by law, must explain:
   - What data you collect
   - How you use it
   - How long you keep it
   - User rights
   - How to contact you

2. **Terms of Service** - Defines:
   - Acceptable use
   - User responsibilities
   - Liability limitations
   - Termination policies

3. **Cookie Policy** - If you use cookies/tracking

### Data Protection Measures

- Use HTTPS everywhere
- Encrypt data at rest
- Implement access controls
- Regular security audits
- Backup encryption
- Secure password policies
- Multi-factor authentication
- Activity logging
- Regular security updates

## Cost Considerations

### Infrastructure Costs

These figures are reconciled with the [Deployment Guide](deployment.md#cost-estimates) - the two pages use one set of numbers. Treat them as of mid-2026 and as rough; provider pricing moves.

**Backend Hosting:**
- VPS: $12-48/month (DigitalOcean, Hetzner, Linode) - $12-24 for 2 GB, $24-48 for 4 GB
- PaaS: $5-25/month (Railway, Render). Railway has no free tier, and a Render free instance can neither mount a disk nor stay awake, so neither works for this backend on a free plan
- Cloud: Variable (AWS, GCP)

**Frontend Hosting:**
- Static hosting: $0-20/month (Cloudflare Pages, Vercel, Netlify) - free tiers are genuinely sufficient here
- CDN costs: Usually included

**Domain:**
- $10-20/year

**SSL Certificate:**
- Free (Let's Encrypt, or Cloudflare Universal SSL)

### Service Costs

**Email Service:**
- Gmail SMTP: Free (limited)
- MailerSend: Free tier, then roughly $25/month
- SendGrid: Free tier available

**Error Tracking:**
- Sentry: Free tier available
- Paid: $26/month and up

**Backup Storage:**
- $1-5/month for object storage at congregation scale

**AI Summaries (optional):**
- OpenAI usage is charged per token. At a few summaries per congregation per month this is cents, not dollars - but it is metered, and the LaunchDarkly flag it depends on has its own free tier

### Time Investment

**Initial Setup:**
- Backend: 2-3 hours
- Frontend: 1-2 hours
- Configuration: 1-2 hours
- Testing: 2-3 hours
- **Total: 6-10 hours**

**Monthly Maintenance:**
- Monitoring: 1-2 hours
- Updates: 1-2 hours
- Support: 1-3 hours
- **Total: 3-7 hours**

### Total Cost of Ownership (Annual)

**Small congregation (< 100 users):**
- Infrastructure: $144-576
- APIs: $0-100 (usually free tier)
- Services: $0-380
- Domain/SSL: $10-20
- **Total: $150-1,000 + 36-84 hours/year**

Plus 6-10 hours of initial setup in the first year.

Compare this to a hosted service subscription before committing.

## Getting Help

### Community Support

- GitHub Issues: [ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) and [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- Read existing issues before creating new ones
- Provide detailed information when reporting problems

### What to Include in Support Requests

1. Version numbers (backend and frontend)
2. Deployment environment (Docker, local, hosting service)
3. Error messages (full text)
4. Steps to reproduce
5. Expected vs actual behavior
6. Relevant logs
7. Configuration (sanitized, no secrets)

### Limited Support

!!! info "Support Limitations"
    Self-hosting support is community-based and limited. The Ministry Mapper team prioritizes the hosted service. For professional support, consider using the hosted service instead.

## Alternatives to Self-Hosting

Before self-hosting, consider:

1. **Use the Official Hosted Service at [ministry-mapper.com](https://ministry-mapper.com)**
   - No maintenance burden
   - Professional support
   - Automatic updates
   - Better reliability
   - Compliance handled for you
   - Available now!

2. **Request Features**
   - If you need customization, request features in the main project
   - Benefits everyone
   - Maintained by the team
   - May be added to hosted service

3. **Contribute to the Project**
   - Improve the main codebase
   - Help others
   - Get community support

## Conclusion

Self-hosting Ministry Mapper is possible but requires significant expertise, time, and resources. **We strongly recommend using the hosted service at [ministry-mapper.com](https://ministry-mapper.com)** unless you have specific requirements that justify the additional complexity.

### Use the Hosted Service Instead

Visit **[ministry-mapper.com](https://ministry-mapper.com)** for:
- Instant setup - no technical knowledge required
- Professional support when you need it
- Automatic backups and security updates
- Legal compliance assistance
- Better reliability and uptime
- Lower total cost of ownership

### Still Want to Self-Host?

If you proceed with self-hosting:
- Follow all security best practices
- Keep systems updated
- Maintain regular backups
- Ensure legal compliance
- Monitor system health
- Budget for ongoing costs
- Allocate time for maintenance

Make sure you've read this entire guide and understand the implications. Good luck!
