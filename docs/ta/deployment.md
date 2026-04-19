# பயன்படுத்தல் வழிகாட்டி

## கண்ணோட்டம்

இந்த வழிகாட்டி Ministry Mapper-க்கான பல்வேறு பயன்படுத்தல் விருப்பங்களை உள்ளடக்கியது — ஹோஸ்ட் செய்யப்பட்ட சேவையை பயன்படுத்துவது முதல் மேம்பட்ட சொந்தமாக ஹோஸ்ட் செய்யும் கட்டமைப்புகள் வரை.

## விரைவான பயன்படுத்தல் முடிவு மரம்

```
┌─────────────────────────────────────┐
│ உங்களுக்கு தொழில்நுட்ப அறிவு      │
│ மற்றும் சொந்த ஹோஸ்டிங் தேவைகள்   │
│ உள்ளதா?                             │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      ஆம்             இல்லை
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ சொந்தமாக    │              │ ஹோஸ்ட்          │
│ ஹோஸ்ட்      │              │ சேவையை           │
│ செய்யவும்    │              │ பயன்படுத்தவும்   │
│ கீழே காணவும் │              │                  │
└──────────────┘              │ ministry-mapper  │
                              │ .com             │
                              └──────────────────┘
```

## விருப்பம் 1: ஹோஸ்ட் செய்யப்பட்ட சேவை (பரிந்துரைக்கப்படுகிறது)

**சிறந்தது:** பெரும்பாலான சபைகளுக்கு, சிறிய முதல் நடுத்தர அமைப்புகளுக்கு

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### நன்மைகள்

✅ **அமைவு இல்லை** - உடனடியாக பயன்படுத்தத் தொடங்கலாம்
✅ **தொழில்முறை ஆதரவு** - தேவைப்படும்போது உதவி
✅ **தானியங்கி புதுப்பிப்புகள்** - எப்போதும் சமீபத்திய பதிப்பில்
✅ **பராமரிக்கப்பட்ட உள்கட்டமைப்பு** - சேவையக மேலாண்மை இல்லை
✅ **சிறந்த பாதுகாப்பு** - தொழில்முறை பாதுகாப்பு நடைமுறைகள்
✅ **இணக்கத்தன்மை உதவி** - GDPR, CCPA போன்றவற்றுக்கு உதவி
✅ **காப்புப்பிரதிகள் உள்ளடங்கியது** - தானியங்கி தரவு காப்புப்பிரதிகள்
✅ **அளவிடுதிறன்** - வளர்ச்சியை தானாகவே கையாளுகிறது
✅ **செலவு-பயனுள்ளது** - பெரும்பாலும் சொந்தமாக ஹோஸ்ட் செய்வதை விட மலிவானது

### தொடங்குவது எப்படி

1. [ministry-mapper.com](https://ministry-mapper.com) -ஐ பார்வையிடவும்
2. கணக்கு உருவாக்கவும்
3. உங்கள் மின்னஞ்சலை சரிபார்க்கவும்
4. சபை அணுகலுக்கு நிர்வாகியை தொடர்பு கொள்ளவும்
5. உடனடியாக பயன்படுத்தத் தொடங்கவும்

**தொழில்நுட்ப அறிவு தேவையில்லை!**

---

## விருப்பம் 2: சொந்தமாக ஹோஸ்ட் செய்தல்

**சிறந்தது:** தொழில்நுட்ப அறிவும் குறிப்பிட்ட தேவைகளும் கொண்ட அமைப்புகளுக்கு

**தேவைகள்:**
- அனுபவமிக்க சிஸ்டம் நிர்வாகிகள் அல்லது டெவலப்பர்கள்
- Docker, தரவுத்தளங்கள் மற்றும் வலை சேவையகங்களின் புரிதல்
- அமைப்புகளை பராமரிக்கவும் புதுப்பிக்கவும் திறன்
- பாதுகாப்பு சிறந்த நடைமுறைகளின் அறிவு
- தனியுரிமை சட்டங்களுக்கான இணக்க நிபுணத்துவம்

### சொந்தமாக ஹோஸ்ட் செய்தல் கண்ணோட்டம்

Ministry Mapper இரண்டு கூறுகளை கொண்டுள்ளது:

1. **Backend** - SQLite உடன் PocketBase Go பயன்பாடு
2. **Frontend** - React நிலையான வலை பயன்பாடு

இரண்டும் பயன்படுத்தப்பட்டு இணைக்கப்பட வேண்டும்.

---

## Backend பயன்படுத்தல் விருப்பங்கள்

### விருப்பம் 1: Railway (சொந்தமாக ஹோஸ்ட் செய்வதற்கு பரிந்துரைக்கப்படுகிறது)

**சிறந்தது:** குறைந்தபட்ச கட்டமைப்புடன் எளிதான சொந்த ஹோஸ்டிங்

**அமைவு படிகள்:**

1. **Railway கணக்கு உருவாக்கவும்**
   ```
   Visit: https://railway.app
   Sign up with GitHub
   ```

2. **Backend பயன்படுத்தவும்**
   ```bash
   # Fork the repository
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # Connect to Railway
   railway init
   railway link
   ```

3. **சூழலை கட்டமைக்கவும்**
   ```bash
   # Add environment variables in Railway dashboard
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **பயன்படுத்தவும்**
   ```bash
   railway up
   ```

**அம்சங்கள்:**
- இலவச அடுக்கு கிடைக்கும்
- தானியங்கி HTTPS
- எளிதான அளவிடுதல்
- உள்ளமைக்கப்பட்ட கண்காணிப்பு
- ஒரே கிளிக் பயன்படுத்தல்கள்

---

### விருப்பம் 2: Render

**அமைவு படிகள்:**

1. **Render கணக்கு உருவாக்கவும்**
   ```
   Visit: https://render.com
   Sign up
   ```

2. **புதிய வலை சேவை உருவாக்கவும்**
   - GitHub இணைப்பியை இணைக்கவும்
   - `ministry-mapper-be` தேர்ந்தெடுக்கவும்
   - Docker பயன்படுத்தல் தேர்வு செய்யவும்

3. **சூழலை கட்டமைக்கவும்**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **பயன்படுத்தவும்**
   - Render தானாகவே உருவாக்கி பயன்படுத்துகிறது
   - முதல் பயன்படுத்தலுக்கு 5-10 நிமிடங்கள் ஆகும்

**அம்சங்கள்:**
- கட்டுப்பாடுகளுடன் இலவச அடுக்கு
- தானியங்கி HTTPS
- எளிதான அளவிடுதல்
- GitHub ஒருங்கிணைப்பு

---

### விருப்பம் 3: DigitalOcean Droplet

**சிறந்தது:** முழு கட்டுப்பாடு, உற்பத்தி பயன்படுத்தல்கள்

**அமைவு படிகள்:**

1. **Droplet உருவாக்கவும்**
   ```bash
   # Ubuntu 22.04 LTS recommended
   # Minimum: 1GB RAM, 1 CPU, 25GB SSD
   # Recommended: 2GB RAM, 2 CPU, 50GB SSD
   ```

2. **Docker நிறுவவும்**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **இணைப்பியை குளோன் செய்யவும்**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **சூழலை கட்டமைக்கவும்**
   ```bash
   cp .env.sample .env
   nano .env  # Edit with your settings
   ```

5. **உருவாக்கி இயக்கவும்**
   ```bash
   docker-compose up -d
   ```

6. **Nginx தலைகீழ் Proxy அமைக்கவும்**
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

7. **Certbot மூலம் SSL அமைக்கவும்**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**அம்சங்கள்:**
- முழு கட்டுப்பாடு
- கணிக்கக்கூடிய விலை ($6-12/மாதம்)
- அதிக செயல்திறன்
- தனிப்பயன் கட்டமைப்புகள்

---

### விருப்பம் 4: AWS (மேம்பட்டது)

**சிறந்தது:** நிறுவன பயன்படுத்தல்கள், அதிக அளவிடுதிறன் தேவைகள்

**கட்டமைப்பு:**
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

**அமைவு கண்ணோட்டம்:**

1. **ECS Cluster உருவாக்கவும்**
2. **Docker படத்தை உருவாக்கி ECR-க்கு தள்ளவும்**
3. **பணி வரையறை உருவாக்கவும்**
4. **Load Balancer கட்டமைக்கவும்**
5. **CloudFront CDN அமைக்கவும்**
6. **Route 53 DNS கட்டமைக்கவும்**

**செலவு:** பயன்பாட்டைப் பொறுத்து ~$50-200/மாதம்

**சிக்கலானது:** அதிகம் — AWS நிபுணத்துவம் தேவை

---

## Frontend பயன்படுத்தல் விருப்பங்கள்

### விருப்பம் 1: Vercel (பரிந்துரைக்கப்படுகிறது)

**சிறந்தது:** சிறந்த டெவலப்பர் அனுபவத்துடன் வேகமான, எளிதான பயன்படுத்தல்

**அமைவு படிகள்:**

1. **Vercel கணக்கு உருவாக்கவும்**
   ```
   Visit: https://vercel.com
   Sign up with GitHub
   ```

2. **இணைப்பியை இறக்குமதி செய்யவும்**
   ```
   - Click "New Project"
   - Import ministry-mapper-v2
   - Framework: Vite
   - Build Command: npm run build
   - Output Directory: build
   ```

3. **சூழல் மாறிகளை கட்டமைக்கவும்**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_VERSION=$npm_package_version
   VITE_OPENROUTE_API_KEY=your_key
   VITE_LOCATIONIQ_API_KEY=your_key
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```

4. **பயன்படுத்தவும்**
   - Vercel தள்ளுவதன் மூலம் தானாகவே பயன்படுத்துகிறது
   - 2-3 நிமிடங்கள் ஆகும்

**அம்சங்கள்:**
- தனிப்பட்ட திட்டங்களுக்கு இலவசம்
- தானியங்கி HTTPS
- Edge நெட்வொர்க் (உலகளவில் வேகமானது)
- PR-களுக்கு முன்னோட்ட பயன்படுத்தல்கள்
- தனிப்பயன் டொமைன்கள்

---

### விருப்பம் 2: Netlify

**அமைவு படிகள்:**

1. **Netlify கணக்கு உருவாக்கவும்**
2. **இணைப்பியை இறக்குமதி செய்யவும்**
   - GitHub இணைக்கவும்
   - `ministry-mapper-v2` தேர்ந்தெடுக்கவும்
3. **உருவாக்கல் அமைவுகள்**
   ```
   Build Command: npm run build
   Publish Directory: build
   ```
4. **சூழல் மாறிகளை சேர்க்கவும்**
5. **பயன்படுத்தவும்**

**அம்சங்கள்:**
- இலவச அடுக்கு
- தானியங்கி HTTPS
- படிவம் கையாளுதல்
- சேவையகமற்ற செயல்பாடுகள்

---

### விருப்பம் 3: Cloudflare Pages

**அமைவு படிகள்:**

1. **Cloudflare கணக்கு உருவாக்கவும்**
2. **Pages திட்டம் உருவாக்கவும்**
   - GitHub இணைக்கவும்
   - இணைப்பியை தேர்ந்தெடுக்கவும்
3. **உருவாக்கல் கட்டமைப்பு**
   ```
   Framework: Vite
   Build Command: npm run build
   Output Directory: build
   ```
4. **சூழல் மாறிகள்**
5. **பயன்படுத்தவும்**

**அம்சங்கள்:**
- வரம்பற்ற அலைவரிசை
- உலகளாவிய CDN
- DDoS பாதுகாப்பு
- வலை பகுப்பாய்வு

---

### விருப்பம் 4: AWS S3 + CloudFront

**சிறந்தது:** AWS-மைய பயன்படுத்தல்கள்

**அமைவு படிகள்:**

1. **S3 வாளி உருவாக்கவும்**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **நிலையான ஹோஸ்டிங் கட்டமைக்கவும்**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **உருவாக்கலை பதிவேற்றவும்**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. **CloudFront விநியோகம் உருவாக்கவும்**
   - மூலம்: S3 வாளி
   - பார்வையாளர் நெறிமுறை: HTTP-ஐ HTTPS-க்கு திருப்பிவிடவும்
   - விலை வகுப்பு: அனைத்து edge இடங்களையும் பயன்படுத்தவும்

5. **தனிப்பயன் டொமைன் கட்டமைக்கவும்**

**செலவு:** சிறிய தளங்களுக்கு ~$1-5/மாதம்

---

## சூழல் கட்டமைப்பு

### Backend சூழல் மாறிகள்

**தேவையானவை:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP (மின்னஞ்சல்):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**பாதுகாப்பு:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**கண்காணிப்பு:**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**விருப்பத்தேர்வு சேவைகள்:**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### Frontend சூழல் மாறிகள்

**தேவையானவை:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**சட்ட பக்கங்கள்:**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**விருப்பத்தேர்வு API-கள்:**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**கண்காணிப்பு:**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## SSL/TLS சான்றிதழ் அமைவு

### விருப்பம் 1: Let's Encrypt (இலவசம்)

**Certbot பயன்படுத்தி:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**தானியங்கி புதுப்பித்தல்:**
```bash
sudo certbot renew --dry-run
```

### விருப்பம் 2: Cloudflare SSL

- Cloudflare உடன் இலவச SSL உள்ளடங்கியது
- தானியங்கி புதுப்பித்தல்
- Universal SSL

### விருப்பம் 3: Cloud வழங்குநர் SSL

பெரும்பாலான cloud வழங்குநர்கள் (Vercel, Netlify, Railway) தானியங்கி SSL உள்ளடக்குகின்றன

---

## தரவுத்தள காப்புப்பிரதி மூலோபாயம்

### தானியங்கி காப்புப்பிரதிகள்

**தினசரி காப்புப்பிரதி ஸ்கிரிப்ட்:**
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

**Cron அட்டவணை:**
```bash
0 2 * * * /path/to/backup-script.sh
```

### Cloud காப்புப்பிரதிகள்

**AWS S3:**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

**Render:**
- தானியங்கி காப்புப்பிரதிகள் உள்ளடங்கியது
- தினசரி snapshots
- 7-நாள் தக்கவைப்பு

---

## கண்காணிப்பு & பதிவு செய்தல்

### Sentry அமைவு

1. [sentry.io](https://sentry.io) -இல் கணக்கு உருவாக்கவும்
2. புதிய திட்டம் உருவாக்கவும் (React + Go)
3. DSN நகலெடுக்கவும்
4. சூழல் மாறிகளில் சேர்க்கவும்
5. விழிப்பூட்டல்களை கட்டமைக்கவும்

**நன்மைகள்:**
- நிகழ்நேர பிழை கண்காணிப்பு
- செயல்திறன் கண்காணிப்பு
- வெளியீடு கண்காணிப்பு
- பயனர் கருத்து

### பயன்பாட்டு கண்காணிப்பு

**கண்காணிக்க வேண்டிய அளவீடுகள்:**
- பிழை விகிதம்
- பதில் நேரம்
- தரவுத்தள அளவு
- செயலில் உள்ள பயனர்கள்
- API அழைப்புகள்
- நினைவக பயன்பாடு
- வட்டு இடம்

**கருவிகள்:**
- Grafana + Prometheus
- Datadog
- New Relic
- Cloud வழங்குநர் கண்காணிப்பு

---

## பாதுகாப்பு சிறந்த நடைமுறைகள்

### Backend பாதுகாப்பு

1. **வலிமையான கடவுச்சொற்களை பயன்படுத்தவும்**
   - குறைந்தது 12 எழுத்துக்கள்
   - எழுத்துக்கள், எண்கள், சின்னங்களின் கலவை

2. **விகித கட்டுப்பாட்டை இயக்கவும்**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **CORS கட்டமைக்கவும்**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **வழக்கமான புதுப்பிப்புகள்**
   ```bash
   # Check for updates monthly
   docker pull your-image:latest
   ```

5. **Firewall விதிகள்**
   ```bash
   # Only allow ports 80, 443, 22 (SSH)
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### Frontend பாதுகாப்பு

1. **சூழல் மாறிகள்**
   - `.env` கோப்புகளை ஒருபோதும் commit செய்யாதீர்கள்
   - தளம் சூழல் மாறி UI பயன்படுத்தவும்

2. **API திறவுகோல் கட்டுப்பாடுகள்**
   - API திறவுகோல்களை குறிப்பிட்ட API-களுக்கு மட்டுப்படுத்தவும்

3. **உள்ளடக்க பாதுகாப்பு கொள்கை**
   ```html
   <meta http-equiv="Content-Security-Policy" 
         content="default-src 'self'; 
                  script-src 'self' 'unsafe-inline'; 
                  style-src 'self' 'unsafe-inline';">
   ```

4. **HTTPS மட்டும்**
   - உற்பத்தியில் HTTPS கட்டாயப்படுத்தவும்
   - HSTS தலைப்புகளை அமைக்கவும்

---

## செயல்திறன் மேம்படுத்தல்

### Backend மேம்படுத்தல்

1. **Gzip சுருக்கம் இயக்கவும்**
2. **தரவுத்தள குறியீட்டு** (ஏற்கனவே கட்டமைக்கப்பட்டது)
3. **இணைப்பு Pooling**
4. **Caching தலைப்புகள்**

### Frontend மேம்படுத்தல்

1. **குறியீடு பிரிப்பு** (Vite உடன் தானியங்கி)
2. **படம் மேம்படுத்தல்**
3. **நிலையான சொத்துகளுக்கு CDN**
4. **Service Worker Caching**

**Lighthouse மதிப்பெண் இலக்கு:**
- செயல்திறன்: >90
- அணுகல்தன்மை: >95
- சிறந்த நடைமுறைகள்: >95
- SEO: >90

---

## அளவிடுதல் பரிசீலனைகள்

### தற்போதைய வரம்புகள்

- SQLite ஒற்றை-எழுத்தர்
- <1000 ஒரே நேரத்தில் பயனர்களுக்கு நல்லது
- சிறிய-நடுத்தர சபைகளுக்கு ஏற்றது

### அளவிடுதல் விருப்பங்கள்

**செங்குத்து அளவிடுதல்:**
- சேவையக வளங்களை அதிகரிக்கவும்
- அதிக RAM, CPU, சேமிப்பு

**கிடைமட்ட அளவிடுதல்:**
- PocketBase வாசிப்பு படிகள்
- Frontend-க்கு CDN
- Load balancer

**தரவுத்தள இடம்பெயர்வு:**
- பெரிய அளவிற்கு PostgreSQL-க்கு இடம்பெயரவும்
- PocketBase PostgreSQL ஐ ஆதரிக்கிறது

---

## பயன்படுத்தல் சிக்கல்களை தீர்த்தல்

### Backend சிக்கல்கள்

**சிக்கல்:** தரவுத்தளம் பூட்டப்பட்டது
**தீர்வு:** ஒரே ஒரு நிகழ்வு மட்டுமே இயங்குவதை உறுதிப்படுத்தவும்

**சிக்கல்:** மின்னஞ்சல் அனுப்பப்படவில்லை
**தீர்வு:** SMTP சான்றுகள் மற்றும் போர்ட்டை சரிபார்க்கவும்

**சிக்கல்:** அதிக நினைவக பயன்பாடு
**தீர்வு:** சேவையக RAM அதிகரிக்கவும் அல்லது queries மேம்படுத்தவும்

### Frontend சிக்கல்கள்

**சிக்கல்:** உருவாக்கல் தோல்வியடைகிறது
**தீர்வு:** Node.js பதிப்பை சரிபார்க்கவும் (22+ தேவை)

**சிக்கல்:** CORS பிழைகள்
**தீர்வு:** Backend-இல் `PB_ALLOW_ORIGINS` கட்டமைக்கவும்

---

## செலவு மதிப்பீடுகள்

### ஹோஸ்ட் செய்யப்பட்ட சேவை
- விலை நிர்ணயத்திற்கு ministry-mapper.com ஐ தொடர்பு கொள்ளவும்
- ஆதரவு, புதுப்பிப்புகள், காப்புப்பிரதிகள் உள்ளடங்கியது

### சொந்தமாக ஹோஸ்ட் செய்யும் செலவுகள்

**குறைந்தபட்ச அமைவு:**
- Railway/Render: $0-5/மாதம் (இலவச அடுக்கு)
- Vercel/Netlify: $0/மாதம் (இலவச அடுக்கு)
- **மொத்தம்: $0-5/மாதம்**

**பரிந்துரைக்கப்பட்ட அமைவு:**
- Backend (Railway): $5/மாதம்
- Frontend (Vercel): $0/மாதம்
- Sentry: $0/மாதம் (இலவச அடுக்கு)
- **மொத்தம்: $5/மாதம்**

**உற்பத்தி அமைவு:**
- DigitalOcean Droplet: $12/மாதம்
- CloudFront CDN: $2/மாதம்
- Sentry Pro: $26/மாதம்
- **மொத்தம்: $40/மாதம்**

**நிறுவன அமைவு:**
- AWS உள்கட்டமைப்பு: $100-500/மாதம்
- ஆதரவு ஒப்பந்தங்கள்: மாறுபடும்
- **மொத்தம்: $100-500+/மாதம்**

**நேர முதலீடு:**
- ஆரம்ப அமைவு: 4-8 மணி நேரம்
- மாதாந்திர பராமரிப்பு: 2-4 மணி நேரம்
- அவசர ஆதரவு: தேவைக்கேற்ப

---

## அடுத்த படிகள்

### பயன்படுத்திய பிறகு

1. **முழுமையாக சோதிக்கவும்**
   - சோதனை கணக்கு உருவாக்கவும்
   - மாதிரி பகுதி உருவாக்கவும்
   - அனைத்து அம்சங்களையும் சோதிக்கவும்
   - மின்னஞ்சல்கள் வேலை செய்கின்றனவா என சரிபார்க்கவும்

2. **பயனர் பயிற்சி**
   - ஆவணங்கள் உருவாக்கவும்
   - நிர்வாகிகளுக்கு பயிற்சி அளிக்கவும்
   - பயனர் அமர்வுகளை நடத்தவும்

3. **அமைப்பை கண்காணிக்கவும்**
   - தினசரி Sentry சரிபார்க்கவும்
   - வாராந்திர பதிவுகளை மதிப்பாய்வு செய்யவும்
   - மாதாந்திர புதுப்பிக்கவும்

4. **காப்புப்பிரதி சரிபார்ப்பு**
   - மீட்டெடுப்பு செயல்முறையை சோதிக்கவும்
   - காப்புப்பிரதி ஒருமைப்பாட்டை சரிபார்க்கவும்
   - நடைமுறைகளை ஆவணப்படுத்தவும்

---

## ஆதரவு வளங்கள்

### ஆவணங்கள்
- [கட்டமைப்பு கண்ணோட்டம்](architecture.md)
- [Backend அமைவு](backend-setup.md)
- [Frontend அமைவு](frontend-setup.md)

### சமூகம்
- GitHub Issues
- ஆவண தளம்
- சமூக மன்றங்கள்

### தொழில்முறை ஆதரவு
- ஹோஸ்ட் செய்யப்பட்ட சேவை ஆதரவு
- ஆலோசனை கிடைக்கும்
- தனிப்பயன் மேம்பாடு
