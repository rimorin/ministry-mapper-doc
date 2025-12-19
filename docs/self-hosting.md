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
- Initial setup: 2-4 hours
- Ongoing maintenance: 2-4 hours per month
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

## Backend Self-Hosting Guide

### Environment Configuration

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

# Error Tracking (optional but recommended)
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# Service Integration (optional)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

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

3. **Run Container**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Important Notes:**
- Container runs on port 8080 internally
- Mount a persistent volume to `/app/pb_data` to preserve data
- Your database lives in the `pb_data` folder

4. **Access Admin Panel**
Navigate to `https://your-backend-domain.com/_/` and log in with your admin credentials.

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

### Database Backups

Your data is critical. Set up automated backups:

```bash
# Manual backup
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# Automated daily backup (add to crontab)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

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

## Frontend Self-Hosting Guide

### Environment Configuration

Create a `.env` file:

```bash
# System Environment
VITE_SYSTEM_ENVIRONMENT=production

# Version
VITE_VERSION=$npm_package_version

# PocketBase Backend URL (no trailing slash)
VITE_POCKETBASE_URL=https://your-backend-domain.com

# Legal Pages (required)
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# Error Tracking (optional but recommended)
VITE_SENTRY_DSN=your_sentry_dsn
```

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

#### Self-Hosted Web Server

**Nginx Configuration:**

```nginx
server {
    listen 443 ssl http2;
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
    add_header X-XSS-Protection "1; mode=block" always;
}
```

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
git pull origin main
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

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
- Database size growth
- Backup success/failure
- SSL certificate expiration
- User authentication issues
- Performance metrics

**Tools:**
- Sentry for error tracking
- Google Cloud Console for API usage
- Server monitoring (Prometheus, Grafana, etc.)
- Uptime monitors (UptimeRobot, Pingdom)
- Log aggregation (ELK stack, CloudWatch)

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
- Review email logs in `pb_data/logs/`

**Cannot Access Admin Panel:**
- URL must be `https://your-domain.com/_/` (note underscore)
- Check `PB_HIDE_CONTROLS` setting
- Verify backend is running
- Clear browser cache

### Frontend Issues

**Cannot Connect to Backend:**
- Verify `VITE_POCKETBASE_URL` is correct
- Check CORS settings in backend
- Ensure backend is accessible
- Check browser console for errors

**Build Failures:**
- Ensure Node.js version 22+
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

**Backend Hosting:**
- VPS: $10-50/month (DigitalOcean, Linode)
- PaaS: $10-30/month (Railway, Render)
- Cloud: Variable (AWS, GCP)

**Frontend Hosting:**
- Static hosting: $0-10/month (Vercel, Netlify, Cloudflare)
- CDN costs: Usually included

**Domain:**
- $10-20/year

**SSL Certificate:**
- Free (Let's Encrypt) or $0-100/year

### Service Costs

**Email Service:**
- Gmail SMTP: Free (limited)
- MailerSend: Free tier available
- SendGrid: Free tier available

**Error Tracking:**
- Sentry: Free tier available
- Paid: $26-80/month

**Backup Storage:**
- $5-20/month depending on size

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
- Infrastructure: $240-720
- APIs: $0-100 (usually free tier)
- Services: $0-300
- Domain/SSL: $10-20
- **Total: $250-1,140 + 48-96 hours/year**

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
