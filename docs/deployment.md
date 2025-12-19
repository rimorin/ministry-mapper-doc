# Deployment Guide

## Overview

This guide covers various deployment options for Ministry Mapper, from using the hosted service to advanced self-hosting configurations.

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
✅ **Backups Included** - Automatic data backups
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

1. **Backend** - PocketBase Go application with SQLite
2. **Frontend** - React static web application

Both must be deployed and connected.

---

## Backend Deployment Options

### Option 1: Railway (Recommended for Self-Hosting)

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

4. **Deploy**
   ```bash
   railway up
   ```

**Features:**
- Free tier available
- Automatic HTTPS
- Easy scaling
- Built-in monitoring
- One-click deployments

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

4. **Deploy**
   - Render automatically builds and deploys
   - Takes 5-10 minutes for first deployment

**Features:**
- Free tier with limitations
- Automatic HTTPS
- Easy scaling
- GitHub integration

---

### Option 3: DigitalOcean Droplet

**Best for:** Full control, production deployments

**Setup Steps:**

1. **Create Droplet**
   ```bash
   # Ubuntu 22.04 LTS recommended
   # Minimum: 1GB RAM, 1 CPU, 25GB SSD
   # Recommended: 2GB RAM, 2 CPU, 50GB SSD
   ```

2. **Install Docker**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

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
   docker-compose up -d
   ```

6. **Setup Nginx Reverse Proxy**
   ```nginx
   server {
       listen 80;
       server_name api.your-domain.com;
   
       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

7. **Setup SSL with Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**Features:**
- Full control
- Predictable pricing ($6-12/month)
- High performance
- Custom configurations

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
ECS Fargate (Container)
    ↓
RDS PostgreSQL (if migrating from SQLite)
    ↓
S3 (File Storage)
```

**Setup Overview:**

1. **Create ECS Cluster**
2. **Build and Push Docker Image to ECR**
3. **Create Task Definition**
4. **Configure Load Balancer**
5. **Setup CloudFront CDN**
6. **Configure Route 53 DNS**

**Cost:** ~$50-200/month depending on usage

**Complexity:** High - requires AWS expertise

---

## Frontend Deployment Options

### Option 1: Vercel (Recommended)

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
   VITE_VERSION=$npm_package_version
   VITE_GOOGLE_MAPS_API_KEY=your_key
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```

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

**Required:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP (Email):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**Security:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**Monitoring:**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**Optional Services:**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### Frontend Environment Variables

**Required:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_GOOGLE_MAPS_API_KEY=your_key
VITE_POCKETBASE_URL=https://your-backend.com
```

**Legal Pages:**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**Monitoring:**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

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

## Database Backup Strategy

### Automated Backups

**Daily Backup Script:**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# Create backup
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# Compress
gzip $BACKUP_DIR/backup_$DATE.db

# Keep last 30 days
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Cron Schedule:**
```bash
0 2 * * * /path/to/backup-script.sh
```

### Cloud Backups

**AWS S3:**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

**Render:**
- Automatic backups included
- Daily snapshots
- 7-day retention

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

### Application Monitoring

**Metrics to Track:**
- Error rate
- Response time
- Database size
- Active users
- API calls
- Memory usage
- Disk space

**Tools:**
- Grafana + Prometheus
- Datadog
- New Relic
- Cloud provider monitoring

---

## Security Best Practices

### Backend Security

1. **Use Strong Passwords**
   - Min 12 characters
   - Mix of letters, numbers, symbols

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
   ```html
   <meta http-equiv="Content-Security-Policy" 
         content="default-src 'self'; 
                  script-src 'self' 'unsafe-inline'; 
                  style-src 'self' 'unsafe-inline';">
   ```

4. **HTTPS Only**
   - Enforce HTTPS in production
   - Set HSTS headers

---

## Performance Optimization

### Backend Optimization

1. **Enable Gzip Compression**
2. **Database Indexing** (already configured)
3. **Connection Pooling**
4. **Caching Headers**

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

### Current Limitations

- SQLite single-writer
- Good for <1000 concurrent users
- Suitable for small-medium congregations

### Scaling Options

**Vertical Scaling:**
- Increase server resources
- More RAM, CPU, storage

**Horizontal Scaling:**
- PocketBase read replicas
- CDN for frontend
- Load balancer

**Database Migration:**
- Migrate to PostgreSQL for larger scale
- PocketBase supports PostgreSQL

---

## Troubleshooting Deployments

### Backend Issues

**Problem:** Database locked
**Solution:** Ensure only one instance running

**Problem:** Email not sending
**Solution:** Check SMTP credentials and port

**Problem:** High memory usage
**Solution:** Increase server RAM or optimize queries

### Frontend Issues

**Problem:** Build fails
**Solution:** Check Node.js version (requires 22+)

**Problem:** CORS errors
**Solution:** Configure `PB_ALLOW_ORIGINS` in backend

---

## Cost Estimates

### Hosted Service
- Contact ministry-mapper.com for pricing
- Includes support, updates, backups

### Self-Hosting Costs

**Minimal Setup:**
- Railway/Render: $0-5/month (free tier)
- Vercel/Netlify: $0/month (free tier)
- **Total: $0-5/month**

**Recommended Setup:**
- Backend (Railway): $5/month
- Frontend (Vercel): $0/month
- Sentry: $0/month (free tier)
- **Total: $5/month**

**Production Setup:**
- DigitalOcean Droplet: $12/month
- CloudFront CDN: $2/month
- Sentry Pro: $26/month
- **Total: $40/month**

**Enterprise Setup:**
- AWS Infrastructure: $100-500/month
- Support contracts: Variable
- **Total: $100-500+/month**

**Time Investment:**
- Initial setup: 4-8 hours
- Monthly maintenance: 2-4 hours
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
   - Test restore process
   - Verify backup integrity
   - Document procedures

---

## Support Resources

### Documentation
- [Architecture Overview](architecture.md)
- [Backend Setup](backend-setup.md)
- [Frontend Setup](frontend-setup.md)

### Community
- GitHub Issues
- Documentation site
- Community forums

### Professional Support
- Hosted service support
- Consulting available
- Custom development
