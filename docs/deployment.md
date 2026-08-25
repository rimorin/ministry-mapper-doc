# Deployment Guide

## Overview

This guide covers various deployment options for Ministry Mapper, from using the hosted service to advanced self-hosting configurations.

Ministry Mapper itself is deployed on **[Coolify](https://coolify.io)** with **[Cloudflare](https://www.cloudflare.com)** in front: Coolify builds the backend straight from its repository, and the frontend release workflow finishes by POSTing a Coolify deploy webhook. That is the reference path, documented under [Reference Deployment: Coolify + Cloudflare](#reference-deployment-coolify-cloudflare). Every provider walkthrough further down this page is an **alternative for self-hosters** who would rather not run Coolify.

## Quick Deployment Decision Tree

```
┌─────────────────────────────────────┐
│ Do you have technical expertise     │
│ and specific self-hosting needs?    │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      YES              NO
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ Self-Host    │              │ Use Hosted       │
│              │              │ Service          │
│ See Below    │              │                  │
└──────────────┘              │ ministry-mapper  │
                              │ .com             │
                              └──────────────────┘
```

## Option 1: Hosted Service (Recommended)

**Best for:** Most congregations, small to medium organizations

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### Advantages

✅ **Zero Setup** - Start using immediately
✅ **Professional Support** - Help when you need it
✅ **Automatic Updates** - Always on latest version
✅ **Maintained Infrastructure** - No server management
✅ **Better Security** - Professional security practices
✅ **Compliance Assistance** - Help with GDPR, CCPA, etc.
✅ **Backups Managed** - Backups taken and restore-tested for you
✅ **Scalability** - Handles growth automatically
✅ **Cost-Effective** - Often cheaper than self-hosting

### Getting Started

1. Visit [ministry-mapper.com](https://ministry-mapper.com)
2. Create an account
3. Verify your email
4. Contact admin for congregation access
5. Start using immediately

**No technical knowledge required!**

---

## Option 2: Self-Hosting

**Best for:** Organizations with technical expertise and specific requirements

**Requirements:**
- Experienced system administrators or developers
- Understanding of Docker, databases, and web servers
- Ability to maintain and update systems
- Knowledge of security best practices
- Compliance expertise for privacy laws

### Self-Hosting Overview

Ministry Mapper consists of two components:

1. **Backend** - PocketBase Go application with SQLite, deployed as a container
2. **Frontend** - React static web application, deployed as a bundle of files

Both must be deployed and connected.

The backend keeps **all** of its state in one directory, `pb_data/` - the SQLite databases (`data.db`, `auxiliary.db`) and their WAL files, uploaded files, and PocketBase's own configuration. A persistent volume for it is therefore not optional, and there is no separate database server to provision.

---

## Reference Deployment: Coolify + Cloudflare

**Best for:** Anyone who wants the same setup Ministry Mapper itself runs on

[Coolify](https://coolify.io) is a self-hostable platform-as-a-service: you run it on your own VPS and it gives you push-to-deploy, healthchecks and volume management without a proprietary platform. Both Ministry Mapper components are deployed this way, with Cloudflare handling DNS, TLS and CDN in front.

### Backend on Coolify

**Best for:** The documented backend path - build from source, healthcheck-gated, one persistent volume

**Setup Steps:**

1. **Create the Application**
   ```
   Coolify → Project → New Resource → Application
   Source: the ministry-mapper-be Git repository
   Build Pack: Dockerfile
   ```
   Coolify builds the image from the repository's own multi-stage `Dockerfile`. There is no pre-built image to pull, and no `docker-compose.yml` in the repository.

2. **Expose the Port**
   ```
   Ports Exposed: 8080
   ```
   The image's command is `./main serve --http=0.0.0.0:8080`.

3. **Attach a Persistent Volume**
   ```
   Destination: /app/pb_data
   ```
   `serve` with no `--dir` writes to `./pb_data` relative to the binary, which is `/app/pb_data` in this image. That directory is the entire application state. Losing it loses everything.

4. **Configure the Healthcheck**
   ```
   Method: GET
   Path:   /api/db-health
   Port:   8080
   ```
   `GET /api/db-health` is the intended platform probe. It needs no authentication and runs a `PRAGMA quick_check` against the database with a 10-second cap, answering `200` when the database is structurally sound and `503` with the failure string when it is not. It also reports failures to Sentry. A plain `SELECT 1` was rejected as a probe because a trivial query still succeeds on a corrupted database - index corruption only shows up in a structural scan. **`curl` is installed in the runtime image specifically so this probe can run inside the container.**

5. **Add Environment Variables**
   Paste the backend variables from [Environment Configuration](#environment-configuration) into Coolify's environment editor. Let the platform inject `SOURCE_COMMIT` per deployment rather than hard-coding it - the backend logs it at boot and uses it as the Sentry release.

6. **Deploy**
   Coolify rebuilds on push to the tracked branch, or on demand from the dashboard. Database migrations run automatically at startup, so there is no separate migration step.

**Features:**
- Builds directly from the repository - no image registry required
- Healthcheck-gated rollouts using `/api/db-health`
- Per-deployment `SOURCE_COMMIT` injection, which becomes the Sentry release
- Runs on infrastructure you already pay for

**Cost:** the VPS only - $12-48/month depending on size (see [Cost Estimates](#cost-estimates))

**Complexity:** Medium - one VPS to run and patch, then push-to-deploy

### Frontend on Coolify

**Best for:** The documented frontend path - built and released by CI, deployed by webhook

The frontend is a static bundle. Its release workflow (`.github/workflows/release.yaml`, triggered on push to `master`) runs format → lint → test → build, publishes the release with `semantic-release`, and finishes by calling a Coolify deploy webhook:

```bash
curl --fail-with-body --silent --show-error \
  --request POST "$COOLIFY_WEBHOOK" \
  --header "Authorization: Bearer $COOLIFY_TOKEN"
```

Two details in that command are load-bearing, and both cost a release to learn:

- **`--request POST`.** The webhook answers `GET` with `405 This endpoint has changed to a POST request`, even though older Coolify documentation still shows `GET`.
- **`--fail-with-body`.** Without it, `curl` exits `0` on a 4xx or 5xx response - so that `405` was reported as a successful step and releases silently deployed nothing.

**Setup Steps:**

1. Create a **Static Site** application in Coolify pointing at the frontend repository (build command `npm run build`, output directory `build`).
2. Add the frontend environment variables from [Environment Configuration](#environment-configuration).
3. Copy the application's deploy webhook URL and API token.
4. Store them as repository secrets `COOLIFY_WEBHOOK` and `COOLIFY_TOKEN` so the release workflow can call them.

**Cost:** included in the same VPS

**Complexity:** Low once the webhook is wired up

### Cloudflare in Front

- DNS for both the frontend and the backend hostnames
- Universal SSL, so there is no certificate to renew on the origin
- CDN caching for the static frontend bundle
- Icons and logos are served from a separate host, `assets.ministry-mapper.com`, which any Content Security Policy must allow (see [Frontend Security](#frontend-security))
- Do **not** cache or buffer `/api/realtime` - see [Realtime (SSE) Through a Proxy or CDN](#realtime-sse-through-a-proxy-or-cdn)

---

## The Backend Container Image

Every container-based backend deployment - Coolify, Railway, Render, a plain Droplet, ECS - builds the same multi-stage image from the repository's `Dockerfile`. What it produces:

- **Builder stage:** `golang:1.27.0-alpine`, deliberately pinned to match the `go 1.27` directive in `go.mod`. An older toolchain would download 1.27 at build time instead of compiling with what is in the image.
- **Build command:** `CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s"`. `CGO_ENABLED=0` is possible only because the SQLite driver is the pure-Go `modernc.org/sqlite`.
- **Architecture:** `GOARCH=amd64` only. There is **no arm64 or multi-arch build**, so Graviton, Ampere and Apple-silicon hosts cannot run this image natively.
- **Runtime stage:** `alpine:3.24`, plus `tzdata` (congregation timezone handling) and `curl` (the platform healthcheck).
- **Contents:** the `main` binary and the `templates/` directory. Email templates are parsed by relative path at runtime, so the binary must always run from a directory that contains `templates/`.
- **Size:** roughly **73.6 MB**.
- **Port:** `EXPOSE 8080`; `CMD ["./main", "serve", "--http=0.0.0.0:8080"]`.
- **User:** root - there is no `USER` directive. Add a non-root user at the platform level if your policy requires one.
- **No `HEALTHCHECK` instruction.** The probe is configured platform-side, which is exactly why `curl` is in the image.
- **State:** mount a persistent volume at **`/app/pb_data`**.

**Build resources:** a cold build's high-water mark is the single-threaded Go linker at about **1.76 GB**. A 1 GB or 1.9 GB host will thrash or fail, and `-p=1` raises the peak rather than lowering it. Build on a bigger machine (or in CI) and pull the image instead of building in place. The BuildKit `--mount=type=cache,target=/root/.cache/go-build` GOCACHE mount in the `Dockerfile` cuts a one-line-change rebuild from 21.4 s to 3.7 s, but it does not reduce peak memory.

**Build context:** `.dockerignore` is an allowlist - it denies `*`, then re-admits only `go.mod`, `go.sum`, `main.go`, `internal/`, `migrations/` and `templates/`. That keeps `.env` and any local database dump out of the builder stage, and cut the build context from 911 MB to 815 kB.

**Migrations** run automatically when the server starts, so a normal deploy has no migration step. Note that variables read only by migrations take effect **only on the first run against a given database** - see [Environment Configuration](#environment-configuration).

---

## Backend Deployment Alternatives

These are alternatives for self-hosters who do not want to run Coolify. All of them deploy the same container image described above, and all of them need a persistent volume at `/app/pb_data` and a healthcheck on `/api/db-health`.

Because PocketBase uses SQLite, **exactly one backend instance may run against a given `pb_data` at a time.** Do not enable autoscaling, multiple replicas, or rolling deploys that briefly run two containers on the same volume.

### Option 1: Railway

**Best for:** Easy self-hosting with minimal configuration

**Setup Steps:**

1. **Create Railway Account**
   ```
   Visit: https://railway.app
   Sign up with GitHub
   ```

2. **Deploy Backend**
   ```bash
   # Fork the repository
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # Connect to Railway
   railway init
   railway link
   ```

3. **Configure Environment**
   ```bash
   # Add environment variables in Railway dashboard
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **Add a Volume and a Healthcheck**
   ```
   Volume mount path: /app/pb_data
   Healthcheck path:  /api/db-health
   ```

5. **Deploy**
   ```bash
   railway up
   ```

**Features:**
- Automatic HTTPS
- Built-in monitoring
- One-click deployments
- Persistent volumes for `pb_data`

**Cost:** Railway has **no free tier** - it was retired. Paid usage starts at a $5/month minimum.

**Complexity:** Low

---

### Option 2: Render

**Setup Steps:**

1. **Create Render Account**
   ```
   Visit: https://render.com
   Sign up
   ```

2. **Create New Web Service**
   - Connect GitHub repository
   - Select `ministry-mapper-be`
   - Choose Docker deployment

3. **Configure Environment**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **Add a Persistent Disk**
   ```
   Mount Path: /app/pb_data
   ```
   Persistent disks require a paid instance type. A free instance also spins down when idle, which stops the scheduled background jobs - so a free instance is not usable for this backend.

5. **Set the Healthcheck Path**
   ```
   /api/db-health
   ```

6. **Deploy**
   - Render automatically builds and deploys
   - Takes 5-10 minutes for first deployment

**Features:**
- Automatic HTTPS
- GitHub integration
- Managed disks and healthchecks

**Cost:** paid instance required (free instances cannot mount a disk and sleep when idle)

**Complexity:** Low

---

### Option 3: DigitalOcean Droplet

**Best for:** Full control, production deployments

**Setup Steps:**

1. **Create Droplet**
   ```bash
   # Ubuntu 24.04 LTS recommended
   # Minimum:     2GB RAM, 1 CPU, 25GB SSD
   # Recommended: 4GB RAM, 2 CPU, 50GB SSD
   # Architecture: amd64 (the backend image has no arm64 build)
   ```
   A 1 GB droplet cannot build the image: the Go linker peaks at around 1.76 GB. Either size up, or build elsewhere and pull.

2. **Install Docker**
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo systemctl enable --now docker
   ```
   The Compose plugin is not needed - the backend repository has no `docker-compose.yml`. (The old standalone `docker-compose` v1 command is deprecated in any case; modern Docker uses `docker compose`.)

3. **Clone Repository**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **Configure Environment**
   ```bash
   cp .env.sample .env
   nano .env  # Edit with your settings
   ```

5. **Build and Run**
   ```bash
   docker build -t ministry-mapper-backend .

   docker run -d \
     --name ministry-mapper \
     --restart unless-stopped \
     -p 8080:8080 \
     -v /srv/ministry-mapper/pb_data:/app/pb_data \
     --env-file .env \
     ministry-mapper-backend
   ```
   The `-v` mount is what keeps your data. Without it, every `docker rm` destroys the database.

6. **Setup Nginx Reverse Proxy**
   ```nginx
   server {
       listen 80;
       server_name api.your-domain.com;

       location / {
           proxy_pass http://localhost:8080;
           proxy_http_version 1.1;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;

           # Required for Server-Sent Events (PocketBase realtime).
           # With buffering on, nginx holds the event stream in its own
           # buffer and live updates arrive late or never.
           proxy_buffering off;
           proxy_cache off;
           proxy_read_timeout 1h;
       }
   }
   ```
   `proxy_buffering off;` is not optional - realtime updates are delivered over Server-Sent Events. See [Realtime (SSE) Through a Proxy or CDN](#realtime-sse-through-a-proxy-or-cdn).

7. **Setup SSL with Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**Features:**
- Full control
- Predictable pricing
- High performance
- Custom configurations

**Cost:** $12-48/month depending on droplet size

**Complexity:** Medium-High - you own the OS, the proxy, the certificates and the backups

---

### Option 4: AWS (Advanced)

**Best for:** Enterprise deployments, high scalability needs

**Architecture:**
```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
Application Load Balancer
    ↓
ECS Fargate  --  exactly ONE task, desired count 1
    ↓
EFS volume mounted at /app/pb_data
```

There is no managed-database tier in this picture. PocketBase stores everything in SQLite inside `pb_data`, so the state lives on a mounted volume and the service **cannot** be scaled to more than one task. Set the desired count to 1, disable autoscaling, and use a stop-then-start deployment rather than a rolling one so two tasks never hold the same volume.

**Setup Overview:**

1. **Create ECS Cluster**
2. **Build and Push Docker Image to ECR** (linux/amd64 - there is no arm64 build, so Graviton/Fargate ARM is not an option)
3. **Create Task Definition** with an EFS volume mounted at `/app/pb_data`
4. **Configure Load Balancer** with the target-group health check set to `/api/db-health`
5. **Setup CloudFront CDN** for the frontend bundle
6. **Configure Route 53 DNS**

**Cost:** ~$50-200/month depending on usage

**Complexity:** High - requires AWS expertise

---

## Frontend Deployment Alternatives

Alternatives to [Frontend on Coolify](#frontend-on-coolify). All of them build the same static bundle with `npm run build` and serve the `build/` directory, and all of them need SPA history-mode fallback (every unknown path served `index.html`).

### Option 1: Vercel

**Best for:** Fast, easy deployment with excellent DX

**Setup Steps:**

1. **Create Vercel Account**
   ```
   Visit: https://vercel.com
   Sign up with GitHub
   ```

2. **Import Repository**
   ```
   - Click "New Project"
   - Import ministry-mapper-v2
   - Framework: Vite
   - Build Command: npm run build
   - Output Directory: build
   ```

3. **Configure Environment Variables**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_GEOAPIFY_API_KEY=your_geoapify_key
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```
   Do not set a version variable - `VITE_APP_VERSION` is injected at build time from `package.json`. See [Frontend Environment Variables](#frontend-environment-variables) for the full list.

4. **Deploy**
   - Vercel automatically deploys on push
   - Takes 2-3 minutes

**Features:**
- Free for personal projects
- Automatic HTTPS
- Edge network (fast globally)
- Preview deployments for PRs
- Custom domains

---

### Option 2: Netlify

**Setup Steps:**

1. **Create Netlify Account**
2. **Import Repository**
   - Connect GitHub
   - Select `ministry-mapper-v2`
3. **Build Settings**
   ```
   Build Command: npm run build
   Publish Directory: build
   ```
4. **Add Environment Variables**
5. **Deploy**

**Features:**
- Free tier
- Automatic HTTPS
- Form handling
- Serverless functions

---

### Option 3: Cloudflare Pages

**Setup Steps:**

1. **Create Cloudflare Account**
2. **Create Pages Project**
   - Connect GitHub
   - Select repository
3. **Build Configuration**
   ```
   Framework: Vite
   Build Command: npm run build
   Output Directory: build
   ```
4. **Environment Variables**
5. **Deploy**

**Features:**
- Unlimited bandwidth
- Global CDN
- DDoS protection
- Web Analytics

---

### Option 4: AWS S3 + CloudFront

**Best for:** AWS-centric deployments

**Setup Steps:**

1. **Create S3 Bucket**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **Configure Static Hosting**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **Upload Build**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. **Create CloudFront Distribution**
   - Origin: S3 bucket
   - Viewer Protocol: Redirect HTTP to HTTPS
   - Price Class: Use all edge locations

5. **Configure Custom Domain**

**Cost:** ~$1-5/month for small sites

---

## Environment Configuration

### Backend Environment Variables

The backend reads 25 variables, and **none of them are strictly required** - every one has a fallback or a nil-client path. What varies is what stops working: with no MailerSend credentials the email jobs do nothing, with no LaunchDarkly key the feature flags fall back to their defaults, with no Sentry DSN error reporting is simply off.

Two things to know before you fill anything in:

- **Migration-read variables only take effect on the FIRST run against a database.** `PB_APP_NAME`, `PB_APP_URL`, `PB_ADMIN_EMAIL`, `PB_ADMIN_PASSWORD`, the six `PB_SMTP_*` variables, `PB_HIDE_CONTROLS`, `PB_ENABLE_RATE_LIMITING`, `PB_OTP_ENABLED`, `PB_MFA_ENABLED`, `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` are read by migrations, not by the running server. Once migrations have run against a `pb_data`, editing them in your platform's UI and redeploying changes nothing - change the corresponding setting in the PocketBase admin UI at `/_/` instead.
- **`PB_OTP_ENABLED` and `PB_MFA_ENABLED` are compared against the literal string `true`.** `TRUE` and `1` do not work.

**Core:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```
The superuser is bootstrapped automatically from `PB_ADMIN_EMAIL` / `PB_ADMIN_PASSWORD` on the first migration run - there is no manual account-creation step. **Set both.** The values that ship as fallbacks are `testing_account@ministry-mapper.com` and `pb123456789`; leaving them in place means publicly known credentials on a publicly reachable admin panel. `PB_ALLOW_ORIGINS` defaults to `*` when unset.

**SMTP (Email):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```
These cover PocketBase's own authentication mail - verification, password reset, email change, and the login OTP. The digests and monthly reports are sent through MailerSend instead.

**Security:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```
`PB_ENABLE_RATE_LIMITING=true` activates the rate-limit rules written by the settings migration (for example `*:auth` at 3 requests per 5 s and `/api/` at 100 per 5 s).

**Monitoring:**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=
```
`SENTRY_ENV` also picks the trace sample rate - `production` 0.2, `staging` 0.5, anything else turns on SDK debug mode. Leave `SOURCE_COMMIT` empty and let the build platform inject the commit SHA; it is logged at boot and used as the Sentry release. It is absent from `.env.sample`.

**Email Delivery (MailerSend):**
```bash
MAILERSEND_API_KEY=your_key
MAILERSEND_FROM_EMAIL=noreply@your-domain.com
```
Both are needed for any digest or report email to go out - the key alone is not enough. This is the one integration that is genuinely required if you want the email features to do anything.

**Feature Flags (LaunchDarkly):**
```bash
LAUNCHDARKLY_SDK_KEY=your_key
LAUNCHDARKLY_CONTEXT_KEY=production
```
Optional. With no SDK key there is no LaunchDarkly client, and the eight scheduled background jobs then default to **enabled**. The AI-summary flag behaves the opposite way - see below.

**AI Report Summaries (OpenAI):**
```bash
OPENAI_API_KEY=your_key
```
The key on its own does not switch the feature on. See [AI Report Summaries](#ai-report-summaries).

**Google Sign-In:**
```bash
GOOGLE_CLIENT_ID=your_client_id
GOOGLE_CLIENT_SECRET=your_client_secret
```
Both must be set or the provider is not configured at all. Both are **missing from `.env.sample`**, so nothing in the sample hints that Google sign-in needs them. They are read by a migration, so they apply only on the first run against a database - afterwards, add the provider in the admin UI.

### AI Report Summaries

The monthly territory report and the message, note and instruction digests can each carry an AI-written summary. Turning that on takes **two** things, and it is easy to do only one:

1. `OPENAI_API_KEY` set on the backend. Without it the client is nil and the feature is off.
2. The LaunchDarkly flag `enable-report-ai-summary` switched **on**.

Unlike the eight background-job flags, which default to enabled when LaunchDarkly is not configured, `enable-report-ai-summary` defaults to **disabled** with no LaunchDarkly client. **AI summaries therefore stay off without LaunchDarkly, even with a valid `OPENAI_API_KEY`.** That is the single most common reason the feature appears to do nothing.

Other operational facts:

- Provider is OpenAI, model `gpt-5.4-mini`, JSON mode, temperature 0.3, 90-second timeout.
- It degrades silently. A nil client, an API error or an unparseable response all simply omit the summary section from the email; nothing fails and nothing retries.
- **Privacy consequence:** with the flag on, the text of publisher messages and property notes is sent to OpenAI. If you self-host, OpenAI becomes a sub-processor you must disclose in your own privacy policy.

### Frontend Environment Variables

Frontend variables are baked in at **build time**. Changing one means rebuilding and redeploying - restarting a server does nothing. Everything prefixed `VITE_` is shipped to the browser, so none of those values are secrets.

**Required:**
```bash
VITE_POCKETBASE_URL=https://your-backend.com
VITE_SYSTEM_ENVIRONMENT=production
VITE_GEOAPIFY_API_KEY=your_geoapify_key
```
- `VITE_POCKETBASE_URL` - backend base URL, no trailing slash. If it is missing the app renders a "Missing PocketBase URL" setup page instead of loading.
- `VITE_SYSTEM_ENVIRONMENT` - one of `local`, `development`, `staging`, `production`. Gates Sentry capture, the STG/DEV corner badge and the version footer.
- `VITE_GEOAPIFY_API_KEY` - **required for maps to work.** A single Geoapify key covers all three geo features: map tiles, address autocomplete, and routing for Directions. Without it tiles do not render, address search returns nothing, and Directions fails. Geoapify is the only geo provider; older documentation listing two separate optional map keys is out of date.

**Legal Pages (required for sign-up):**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
```

**Optional:**
```bash
VITE_ABOUT_URL=https://your-site.com/about
VITE_MAINTENANCE_MODE=false
VITE_LAUNCHDARKLY_CLIENT_ID=your_client_side_id
VITE_SENTRY_DSN=your_sentry_dsn
VITE_UMAMI_SRC_URL=https://your-umami-host/script.js
VITE_UMAMI_WEBSITE_ID=your_website_id
VITE_UMAMI_DOMAINS=your-domain.com
```
- `VITE_MAINTENANCE_MODE=true` is a build-time kill switch that serves a maintenance page instead of the app. It is compared against the literal string `true`.
- `VITE_LAUNCHDARKLY_CLIENT_ID` is the client-side ID, not the server SDK key. Empty means client-side flags are disabled entirely.
- Umami analytics needs **both** `VITE_UMAMI_SRC_URL` and `VITE_UMAMI_WEBSITE_ID`. With only one of them set, analytics initialization returns early and nothing is tracked.

**Do not set:**
```bash
# VITE_APP_VERSION - injected at build time from package.json
```
`VITE_APP_VERSION` is defined by the Vite config from `package.json.version`, and it drives the Sentry release, the version footer and the in-app update check. Setting it by hand desynchronizes all three. There is no `VITE_VERSION` variable.

**Build-time only (never shipped to the browser):**
```bash
SENTRY_AUTH_TOKEN=your_token
SENTRY_ORG=your_org
SENTRY_PROJECT=your_project
```
These three upload source maps during a production build so Sentry stack traces are readable. They are not `VITE_`-prefixed and never reach the client - keep them as build secrets in CI or in your hosting platform. Without them the build still succeeds; you just get minified stack traces.

---

## SSL/TLS Certificate Setup

### Option 1: Let's Encrypt (Free)

**Using Certbot:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**Auto-renewal:**
```bash
sudo certbot renew --dry-run
```

### Option 2: Cloudflare SSL

- Free SSL included with Cloudflare
- Automatic renewal
- Universal SSL

### Option 3: Cloud Provider SSL

Most cloud providers (Vercel, Netlify, Railway) include automatic SSL

---

## Realtime (SSE) Through a Proxy or CDN

Live updates - a publisher marking a house, an administrator resetting a map - are delivered over **Server-Sent Events**. There are no WebSockets anywhere in the stack. SSE is one long-lived HTTP response that trickles out events, and that is exactly what an ordinary reverse proxy is built to buffer and compress. If it does, updates arrive in a clump minutes later, or never - with no error on either side. This is the single most common self-hosting failure that is not a crash.

**Nginx** - the setting that matters is `proxy_buffering off;`:

```nginx
location / {
    proxy_pass http://localhost:8080;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    proxy_buffering off;          # required for SSE
    proxy_cache off;              # never cache an event stream
    proxy_read_timeout 1h;        # the connection is meant to stay open
}
```

**Apache** - use `SetEnv proxy-sendchunked` and disable output buffering for the proxied vhost; `mod_deflate` must not compress the stream.

**Caddy, Traefik and Coolify's built-in proxy** stream by default and need no extra configuration.

**Cloudflare and other CDNs** - leave `/api/realtime` uncached and unbuffered. Any "optimization" that rewrites or aggregates responses will break the stream.

**How to tell it is working:** open the app in two browsers on the same map and change a status in one. If the other updates within a second or two, the stream is fine. If it only updates on refresh, buffering is on somewhere between the browser and the backend.

---

## Backup and Restore

**Everything lives in `pb_data/`.** The SQLite databases (`data.db` and `auxiliary.db`), their WAL files, uploaded files and PocketBase's own configuration are all in that one directory. Lose it and you lose the congregation, its territories and addresses, every audit log and every account. There is nothing else to back up - and **no backup tooling ships in either repository**, so everything below is something you have to set up yourself.

### Option 1: PocketBase's Built-in Backups (Recommended)

PocketBase can snapshot the whole `pb_data` directory into a single zip archive while the server is running. It is the simplest and safest mechanism available, and it is the one to reach for first.

**From the admin UI:**

1. Open `https://your-backend-domain.com/_/`
2. Go to **Settings → Backups**
3. Use **Initiate new backup** for one now, or enable the automatic schedule and set how many archives to retain
4. Download an archive with the download action, or restore one with the restore action - the server restarts itself afterwards

**From the API** (needs a superuser token):
```bash
# List backups
curl -H "Authorization: $SUPERUSER_TOKEN" \
  https://your-backend-domain.com/api/backups

# Create a backup
curl -X POST -H "Authorization: $SUPERUSER_TOKEN" \
  https://your-backend-domain.com/api/backups

# Download one
curl -H "Authorization: $SUPERUSER_TOKEN" -O \
  https://your-backend-domain.com/api/backups/<name>
```

Archives are written **inside** `pb_data/backups/` - on the same volume as the data they protect. **Copy them off the host**, or they will not survive the failure you are backing up against. PocketBase can push them to S3-compatible storage for you; configure that under the same Settings → Backups screen.

### Option 2: Copying the Files Yourself

If you would rather archive the directory from the filesystem, checkpoint the write-ahead log first. SQLite holds recent writes in `data.db-wal`, so a copy of `data.db` on its own can be missing the newest changes:

```bash
# Stop writes (ideally stop the container), then:
sqlite3 /srv/ministry-mapper/pb_data/data.db "PRAGMA wal_checkpoint(TRUNCATE);"
sqlite3 /srv/ministry-mapper/pb_data/auxiliary.db "PRAGMA wal_checkpoint(TRUNCATE);"

DATE=$(date +%Y%m%d_%H%M%S)
tar -czf /backups/ministry-mapper-$DATE.tar.gz -C /srv/ministry-mapper pb_data

# Keep the last 30 days
find /backups -name "ministry-mapper-*.tar.gz" -mtime +30 -delete
```

**Cron Schedule:**
```bash
0 2 * * * /path/to/backup-script.sh
```

The `sqlite3` CLI is not installed in the backend image, so run this on the host against the mounted volume.

### Getting Backups Off the Host

```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

Any equivalent works, and a scheduled provider snapshot of the volume holding `pb_data` is a perfectly good second layer. What matters is that at least one copy is somewhere the host cannot take with it.

### Restoring

1. Stop the backend so nothing is writing.
2. Replace `pb_data` with the archive's contents - or use the built-in restore, from the admin UI or the API, which does this for you.
3. Start the backend. Migrations run automatically at startup and are idempotent.
4. Check `GET /api/db-health`. A `200` means `PRAGMA quick_check` passed against the restored database.

### Verify Restores, Not Just Backups

An untested backup is a guess. At least quarterly, restore an archive onto a scratch instance, sign in, open a territory and confirm the data is really there. A restore procedure that has never been rehearsed is usually discovered to be broken at the worst possible moment.

---

## Monitoring & Logging

### Sentry Setup

1. Create account at [sentry.io](https://sentry.io)
2. Create new project (React + Go)
3. Copy DSN
4. Add to environment variables
5. Configure alerts

**Benefits:**
- Real-time error tracking
- Performance monitoring
- Release tracking
- User feedback

### What Is Actually Instrumented

Only four things are built in. Everything else is something you add.

- **Sentry** - error and log forwarding on both the backend and the frontend. `SENTRY_ENV` selects the trace sample rate; `SOURCE_COMMIT` (backend) and the injected `VITE_APP_VERSION` (frontend) become the release identifiers.
- **Umami** - optional, privacy-friendly frontend analytics, enabled by setting `VITE_UMAMI_SRC_URL` and `VITE_UMAMI_WEBSITE_ID` together.
- **`GET /api/health`** - PocketBase's built-in liveness endpoint. Answers as long as the process is up.
- **`GET /api/db-health`** - the custom database probe: `PRAGMA quick_check` with a 10-second cap, `200` when healthy, `503` plus a Sentry event when not. Use this one as your platform healthcheck.

One caveat worth knowing: the `/link/map` and `/map/addresses` endpoints return raw error bodies rather than PocketBase's standard envelope, and because of how they write the response those errors are invisible to the Sentry middleware. They will not appear in your error dashboard.

### Application Monitoring (Bring Your Own)

**Metrics worth watching:**
- Error rate (Sentry)
- Response time
- `pb_data` size growth and free disk space
- Memory usage - the backend is a single process holding a SQLite database
- Backup success or failure
- SSL certificate expiry

**Generic tools you can point at it.** None of these are integrated, and neither codebase exposes a metrics endpoint or an exporter, so treat them as options rather than features:
- Your platform's own monitoring (Coolify, Railway, Render, or your cloud provider)
- An uptime checker hitting `/api/db-health` (UptimeRobot, Pingdom, Healthchecks.io)
- A generic metrics/dashboard stack such as Prometheus + Grafana, Datadog or New Relic, scraping the host rather than the app
- Log aggregation from container logs (Loki, ELK, CloudWatch)

---

## Security Best Practices

### Backend Security

1. **Use Strong Passwords**
   - Min 12 characters
   - Mix of letters, numbers, symbols
   - This is policy, not enforcement: PocketBase itself only enforces a 6-character minimum, so the superuser password in particular is on you

2. **Enable Rate Limiting**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **Configure CORS**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **Regular Updates**
   ```bash
   # Check for updates monthly
   docker pull your-image:latest
   ```

5. **Firewall Rules**
   ```bash
   # Only allow ports 80, 443, 22 (SSH)
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### Frontend Security

1. **Environment Variables**
   - Never commit `.env` files
   - Use platform environment variable UI

2. **API Key Restrictions**
   - Restrict API keys to specific APIs

3. **Content Security Policy**

   A `default-src 'self'` policy will break the app. The frontend legitimately loads map tiles, geocoding and routing from Geoapify, icons from a separate asset host, and - when configured - Sentry, LaunchDarkly and Umami. A working starting point:

   ```html
   <meta http-equiv="Content-Security-Policy"
         content="default-src 'self';
                  script-src 'self' 'unsafe-inline';
                  style-src 'self' 'unsafe-inline';
                  img-src 'self' data: blob: https://*.geoapify.com https://*.openstreetmap.org https://assets.ministry-mapper.com;
                  font-src 'self' data: https://assets.ministry-mapper.com;
                  connect-src 'self' https://your-backend.example.com https://api.geoapify.com https://*.geoapify.com https://*.ingest.sentry.io https://*.launchdarkly.com;
                  worker-src 'self' blob:;
                  manifest-src 'self';
                  base-uri 'self';
                  frame-ancestors 'none';">
   ```

   What each host is for:
   - `*.geoapify.com` / `api.geoapify.com` - map tiles (`img-src`) plus geocode autocomplete and routing (`connect-src`)
   - `*.openstreetmap.org` - only needed if you point the tile layer at OSM directly instead of Geoapify
   - `assets.ministry-mapper.com` - the icons and logos the app loads from the asset host
   - `*.ingest.sentry.io` - error reporting, only if `VITE_SENTRY_DSN` is set
   - `*.launchdarkly.com` - feature flags, only if `VITE_LAUNCHDARKLY_CLIENT_ID` is set
   - `worker-src blob:` - the PWA service worker
   - Add your Umami host to `script-src` and `connect-src` if you enable analytics
   - Replace `your-backend.example.com` with your own backend origin, and drop any line you are not using

4. **HTTPS Only**
   - Enforce HTTPS in production
   - Set HSTS headers

---

## Performance Optimization

### Backend Optimization

1. **Enable Gzip Compression** at the proxy - but never on `/api/realtime`, which is an event stream
2. **Database Indexing** (already configured - the schema ships with the indexes it needs, and six redundant ones were deliberately removed)
3. **Keep `pb_data` on local SSD**, not network storage. SQLite over NFS or a slow network volume is the fastest way to make the whole app feel broken
4. **Caching Headers** for static assets
5. **Run `VACUUM` occasionally.** Deleted rows and dropped indexes leave pages on SQLite's freelist rather than returning them to the filesystem. It cannot be done from a migration (migrations run inside a transaction), so run it manually during a quiet window if the database file looks larger than the data warrants

### Frontend Optimization

1. **Code Splitting** (automatic with Vite)
2. **Image Optimization**
3. **CDN for Static Assets**
4. **Service Worker Caching**

**Lighthouse Score Target:**
- Performance: >90
- Accessibility: >95
- Best Practices: >95
- SEO: >90

---

## Scaling Considerations

### The Shape of the Constraint

PocketBase is **SQLite-only**. There is no PostgreSQL mode, no read-replica mode, and no supported path to a managed database service - the database is a file on the backend's own volume. That single fact defines how this application scales:

- **One writer.** SQLite serializes writes, and the whole application state is one database file. Exactly one backend instance may run against a given `pb_data` at a time.
- **No horizontal scaling of the backend.** Two containers on the same volume is data corruption, not capacity. Set replicas to 1, disable autoscaling, and prefer stop-then-start deploys over rolling ones.
- **The design target is fewer than 1000 concurrent users**, which comfortably covers a congregation - or a good number of them on one instance.

### Scaling Options

**Vertical scaling** is the backend's answer, and in practice it is enough:
- More RAM and CPU on the host
- Faster local SSD for `pb_data` (never network storage)
- The database has been exercised against a production copy of **1,160,985 addresses**, so the ceiling is much higher than a congregation-sized workload

**The frontend scales horizontally as much as you like** - it is a static bundle:
- Serve it from a CDN (Cloudflare, or your host's)
- Cache the hashed assets aggressively; leave `index.html` uncached

**If a single instance is genuinely not enough**, the answer is more instances with separate databases - a second deployment for a different set of congregations - not a cluster over shared state. There is no supported way to shard or replicate one `pb_data`.

---

## Troubleshooting Deployments

### Backend Issues

**Problem:** Database locked
**Solution:** Ensure only one instance is running against the volume - SQLite has a single writer

**Problem:** Email not sending
**Solution:** Check SMTP credentials and port for authentication mail; for digests and reports, check that **both** `MAILERSEND_API_KEY` and `MAILERSEND_FROM_EMAIL` are set

**Problem:** High memory usage
**Solution:** Increase server RAM or optimize queries

**Problem:** Docker build is killed, or hangs at the link step
**Solution:** The Go linker peaks at about 1.76 GB and is single-threaded. Build on a host with more than 2 GB of RAM, or build elsewhere and pull the image

**Problem:** Container will not start on an ARM host
**Solution:** The image is `GOARCH=amd64` only - there is no arm64 build. Use an amd64 host

**Problem:** Environment variable changes have no effect
**Solution:** If it is one of the migration-read variables, it only applied on the first run against that database. Change the setting in the admin UI at `/_/` instead

**Problem:** Healthcheck fails with 503
**Solution:** `/api/db-health` runs `PRAGMA quick_check`, so a 503 means real structural damage, not just load. Restore from a backup rather than restarting the container repeatedly

**Problem:** AI summaries never appear in reports or digests
**Solution:** The feature needs `OPENAI_API_KEY` **and** the LaunchDarkly flag `enable-report-ai-summary`. Without LaunchDarkly that flag defaults to off, and the feature degrades silently

### Frontend Issues

**Problem:** Build fails
**Solution:** Check Node.js version (requires 24+)

**Problem:** CORS errors
**Solution:** Configure `PB_ALLOW_ORIGINS` in backend

**Problem:** Map is blank, address search returns nothing, or Directions fails
**Solution:** Set `VITE_GEOAPIFY_API_KEY` and rebuild - one key covers tiles, geocoding and routing

**Problem:** Live updates never arrive; the app only refreshes on reload
**Solution:** Response buffering is on somewhere in front of the backend. Set `proxy_buffering off;` in nginx and leave `/api/realtime` uncached - see [Realtime (SSE) Through a Proxy or CDN](#realtime-sse-through-a-proxy-or-cdn)

**Problem:** Version footer or Sentry release is wrong
**Solution:** Do not set `VITE_APP_VERSION` yourself - it is injected from `package.json` at build time

**Problem:** Blank page with console errors about blocked resources
**Solution:** Your Content Security Policy is too strict; see [Frontend Security](#frontend-security)

---

## Cost Estimates

### Hosted Service
- Contact ministry-mapper.com for pricing
- Includes support, updates, backups

### Self-Hosting Costs

These figures are the same set used in [Self-Hosting](self-hosting.md) - the two pages are reconciled. Treat them as of mid-2026 and as rough: provider pricing moves.

**Minimal Setup (PaaS, smallest paid tiers):**
- Backend (Railway or Render, paid instance): $5-10/month
- Frontend (Cloudflare Pages, Vercel or Netlify free tier): $0/month
- **Total: $5-10/month**

**Recommended Setup (one VPS running Coolify, Cloudflare in front):**
- VPS, 2 GB RAM: $12-24/month
- Cloudflare DNS/CDN/SSL: $0/month (free plan)
- Sentry: $0/month (free tier)
- **Total: $12-25/month**

**Production Setup:**
- VPS, 4 GB RAM: $24-48/month
- Sentry, paid tier: $26/month
- Off-host backup storage: $1-5/month
- **Total: $50-80/month**

**Enterprise Setup:**
- AWS infrastructure: $100-500/month
- Support contracts: Variable
- **Total: $100-500+/month**

**Also, in every tier:**
- Domain: $10-20/year
- SSL: free (Let's Encrypt, or Cloudflare Universal SSL)
- MailerSend: free tier, then roughly $25/month once you outgrow it

**Annual total cost of ownership** for a small congregation, at the Recommended tier: roughly **$150-1,000/year** plus the time below.

**Time Investment:**
- Initial setup: 6-10 hours
- Ongoing maintenance: 3-7 hours per month (36-84 hours a year)
- Emergency support: As needed

---

## Next Steps

### After Deployment

1. **Test Thoroughly**
   - Create test account
   - Create sample territory
   - Test all features
   - Verify emails working

2. **User Training**
   - Create documentation
   - Train administrators
   - Hold user sessions

3. **Monitor System**
   - Check Sentry daily
   - Review logs weekly
   - Update monthly

4. **Backup Verification**
   - Turn on PocketBase's scheduled backups under Settings → Backups
   - Confirm archives are being copied **off** the host
   - Restore one to a scratch instance and sign in - a backup you have never restored is a guess
   - Document the procedure while you still remember it

---

## Support Resources

### Documentation
- [Architecture Overview](architecture.md)
- [Backend Setup](backend-setup.md)
- [Frontend Setup](frontend-setup.md)
- [Self-Hosting Guide](self-hosting.md)

### Community
- GitHub Issues
- Documentation site
- Community forums

### Professional Support
- Hosted service support
- Consulting available
- Custom development
