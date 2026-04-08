# Panduan Deployment

## Ikhtisar

Panduan ini mencakup berbagai opsi deployment untuk Ministry Mapper, mulai dari menggunakan layanan hosted hingga konfigurasi self-hosting yang lebih canggih.

## Pohon Keputusan Deployment Cepat

```
┌─────────────────────────────────────┐
│ Apakah Anda memiliki keahlian       │
│ teknis dan kebutuhan self-hosting   │
│ yang spesifik?                      │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      YA             TIDAK
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ Self-Host    │              │ Gunakan Layanan  │
│              │              │ Hosted           │
│ Lihat di     │              │                  │
│ Bawah        │              │ ministry-mapper  │
└──────────────┘              │ .com             │
                              └──────────────────┘
```

## Opsi 1: Layanan Hosted (Disarankan)

**Terbaik untuk:** Sebagian besar jemaat, organisasi kecil hingga menengah

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### Keuntungan

✅ **Tanpa Pengaturan** - Mulai digunakan segera
✅ **Dukungan Profesional** - Bantuan saat Anda membutuhkannya
✅ **Pembaruan Otomatis** - Selalu menggunakan versi terbaru
✅ **Infrastruktur yang Dikelola** - Tanpa manajemen server
✅ **Keamanan yang Lebih Baik** - Praktik keamanan profesional
✅ **Bantuan Kepatuhan** - Bantuan dengan GDPR, CCPA, dll.
✅ **Pencadangan Termasuk** - Backup data otomatis
✅ **Skalabilitas** - Menangani pertumbuhan secara otomatis
✅ **Hemat Biaya** - Sering lebih murah daripada self-hosting

### Memulai

1. Kunjungi [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akun
3. Verifikasi email Anda
4. Hubungi admin untuk akses jemaat
5. Mulai gunakan segera

**Tidak diperlukan pengetahuan teknis!**

---

## Opsi 2: Self-Hosting

**Terbaik untuk:** Organisasi dengan keahlian teknis dan persyaratan spesifik

**Persyaratan:**
- Administrator sistem atau pengembang yang berpengalaman
- Pemahaman tentang Docker, database, dan web server
- Kemampuan untuk memelihara dan memperbarui sistem
- Pengetahuan tentang praktik keamanan terbaik
- Keahlian kepatuhan untuk undang-undang privasi

### Ikhtisar Self-Hosting

Ministry Mapper terdiri dari dua komponen:

1. **Backend** - Aplikasi PocketBase Go dengan SQLite
2. **Frontend** - Aplikasi web statis React

Keduanya harus di-deploy dan dihubungkan.

---

## Opsi Deployment Backend

### Opsi 1: Railway (Disarankan untuk Self-Hosting)

**Terbaik untuk:** Self-hosting mudah dengan konfigurasi minimal

**Langkah Pengaturan:**

1. **Buat Akun Railway**
   ```
   Kunjungi: https://railway.app
   Daftar dengan GitHub
   ```

2. **Deploy Backend**
   ```bash
   # Fork repository
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # Hubungkan ke Railway
   railway init
   railway link
   ```

3. **Konfigurasi Environment**
   ```bash
   # Tambahkan variabel lingkungan di dashboard Railway
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **Deploy**
   ```bash
   railway up
   ```

**Fitur:**
- Tier gratis tersedia
- HTTPS otomatis
- Penskalaan mudah
- Pemantauan bawaan
- Deployment satu klik

---

### Opsi 2: Render

**Langkah Pengaturan:**

1. **Buat Akun Render**
   ```
   Kunjungi: https://render.com
   Daftar
   ```

2. **Buat Web Service Baru**
   - Hubungkan repository GitHub
   - Pilih `ministry-mapper-be`
   - Pilih deployment Docker

3. **Konfigurasi Environment**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **Deploy**
   - Render secara otomatis membangun dan men-deploy
   - Membutuhkan 5-10 menit untuk deployment pertama

**Fitur:**
- Tier gratis dengan keterbatasan
- HTTPS otomatis
- Penskalaan mudah
- Integrasi GitHub

---

### Opsi 3: DigitalOcean Droplet

**Terbaik untuk:** Kontrol penuh, deployment produksi

**Langkah Pengaturan:**

1. **Buat Droplet**
   ```bash
   # Ubuntu 22.04 LTS disarankan
   # Minimum: 1GB RAM, 1 CPU, 25GB SSD
   # Disarankan: 2GB RAM, 2 CPU, 50GB SSD
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

4. **Konfigurasi Environment**
   ```bash
   cp .env.sample .env
   nano .env  # Edit dengan pengaturan Anda
   ```

5. **Build dan Jalankan**
   ```bash
   docker-compose up -d
   ```

6. **Siapkan Nginx Reverse Proxy**
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

7. **Siapkan SSL dengan Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**Fitur:**
- Kontrol penuh
- Harga yang dapat diprediksi ($6-12/bulan)
- Kinerja tinggi
- Konfigurasi kustom

---

### Opsi 4: AWS (Canggih)

**Terbaik untuk:** Deployment enterprise, kebutuhan skalabilitas tinggi

**Arsitektur:**
```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
Application Load Balancer
    ↓
ECS Fargate (Container)
    ↓
RDS PostgreSQL (jika bermigrasi dari SQLite)
    ↓
S3 (Penyimpanan File)
```

**Ikhtisar Pengaturan:**

1. **Buat ECS Cluster**
2. **Build dan Push Docker Image ke ECR**
3. **Buat Task Definition**
4. **Konfigurasi Load Balancer**
5. **Siapkan CloudFront CDN**
6. **Konfigurasi Route 53 DNS**

**Biaya:** ~$50-200/bulan tergantung penggunaan

**Kompleksitas:** Tinggi - memerlukan keahlian AWS

---

## Opsi Deployment Frontend

### Opsi 1: Vercel (Disarankan)

**Terbaik untuk:** Deployment cepat dan mudah dengan DX yang sangat baik

**Langkah Pengaturan:**

1. **Buat Akun Vercel**
   ```
   Kunjungi: https://vercel.com
   Daftar dengan GitHub
   ```

2. **Impor Repository**
   ```
   - Klik "New Project"
   - Impor ministry-mapper-v2
   - Framework: Vite
   - Build Command: npm run build
   - Output Directory: build
   ```

3. **Konfigurasi Variabel Lingkungan**
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

4. **Deploy**
   - Vercel secara otomatis men-deploy saat push
   - Membutuhkan 2-3 menit

**Fitur:**
- Gratis untuk proyek pribadi
- HTTPS otomatis
- Edge network (cepat secara global)
- Preview deployment untuk PR
- Domain kustom

---

### Opsi 2: Netlify

**Langkah Pengaturan:**

1. **Buat Akun Netlify**
2. **Impor Repository**
   - Hubungkan GitHub
   - Pilih `ministry-mapper-v2`
3. **Pengaturan Build**
   ```
   Build Command: npm run build
   Publish Directory: build
   ```
4. **Tambahkan Variabel Lingkungan**
5. **Deploy**

**Fitur:**
- Tier gratis
- HTTPS otomatis
- Penanganan form
- Fungsi serverless

---

### Opsi 3: Cloudflare Pages

**Langkah Pengaturan:**

1. **Buat Akun Cloudflare**
2. **Buat Proyek Pages**
   - Hubungkan GitHub
   - Pilih repository
3. **Konfigurasi Build**
   ```
   Framework: Vite
   Build Command: npm run build
   Output Directory: build
   ```
4. **Variabel Lingkungan**
5. **Deploy**

**Fitur:**
- Bandwidth tak terbatas
- CDN global
- Perlindungan DDoS
- Web Analytics

---

### Opsi 4: AWS S3 + CloudFront

**Terbaik untuk:** Deployment yang berpusat di AWS

**Langkah Pengaturan:**

1. **Buat S3 Bucket**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **Konfigurasi Hosting Statis**
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

4. **Buat Distribusi CloudFront**
   - Origin: S3 bucket
   - Viewer Protocol: Redirect HTTP ke HTTPS
   - Price Class: Gunakan semua edge locations

5. **Konfigurasi Domain Kustom**

**Biaya:** ~$1-5/bulan untuk situs kecil

---

## Konfigurasi Lingkungan

### Variabel Lingkungan Backend

**Wajib:**
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

**Keamanan:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**Pemantauan:**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**Layanan Opsional:**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### Variabel Lingkungan Frontend

**Wajib:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**Halaman Legal:**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**API Opsional:**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**Pemantauan:**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## Pengaturan Sertifikat SSL/TLS

### Opsi 1: Let's Encrypt (Gratis)

**Menggunakan Certbot:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**Pembaruan otomatis:**
```bash
sudo certbot renew --dry-run
```

### Opsi 2: Cloudflare SSL

- SSL gratis termasuk dengan Cloudflare
- Pembaruan otomatis
- Universal SSL

### Opsi 3: SSL Penyedia Cloud

Sebagian besar penyedia cloud (Vercel, Netlify, Railway) menyertakan SSL otomatis

---

## Strategi Backup Database

### Backup Otomatis

**Script Backup Harian:**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# Buat backup
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# Kompres
gzip $BACKUP_DIR/backup_$DATE.db

# Simpan 30 hari terakhir
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Jadwal Cron:**
```bash
0 2 * * * /path/to/backup-script.sh
```

### Backup Cloud

**AWS S3:**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

**Render:**
- Backup otomatis termasuk
- Snapshot harian
- Retensi 7 hari

---

## Pemantauan & Logging

### Pengaturan Sentry

1. Buat akun di [sentry.io](https://sentry.io)
2. Buat proyek baru (React + Go)
3. Salin DSN
4. Tambahkan ke variabel lingkungan
5. Konfigurasi alert

**Manfaat:**
- Pelacakan error real-time
- Pemantauan kinerja
- Release tracking
- Umpan balik pengguna

### Pemantauan Aplikasi

**Metrik yang Perlu Dilacak:**
- Tingkat error
- Waktu respons
- Ukuran database
- Pengguna aktif
- Panggilan API
- Penggunaan memori
- Ruang disk

**Alat:**
- Grafana + Prometheus
- Datadog
- New Relic
- Pemantauan penyedia cloud

---

## Praktik Terbaik Keamanan

### Keamanan Backend

1. **Gunakan Kata Sandi yang Kuat**
   - Min 12 karakter
   - Campuran huruf, angka, simbol

2. **Aktifkan Pembatasan Rate**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **Konfigurasi CORS**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **Pembaruan Rutin**
   ```bash
   # Periksa pembaruan setiap bulan
   docker pull your-image:latest
   ```

5. **Aturan Firewall**
   ```bash
   # Hanya izinkan port 80, 443, 22 (SSH)
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### Keamanan Frontend

1. **Variabel Lingkungan**
   - Jangan pernah commit file `.env`
   - Gunakan UI variabel lingkungan platform

2. **Pembatasan Kunci API**
   - Batasi kunci API ke API tertentu

3. **Content Security Policy**
   ```html
   <meta http-equiv="Content-Security-Policy" 
         content="default-src 'self'; 
                  script-src 'self' 'unsafe-inline'; 
                  style-src 'self' 'unsafe-inline';">
   ```

4. **Hanya HTTPS**
   - Terapkan HTTPS di produksi
   - Atur header HSTS

---

## Optimasi Kinerja

### Optimasi Backend

1. **Aktifkan Kompresi Gzip**
2. **Pengindeksan Database** (sudah dikonfigurasi)
3. **Connection Pooling**
4. **Header Caching**

### Optimasi Frontend

1. **Code Splitting** (otomatis dengan Vite)
2. **Optimasi Gambar**
3. **CDN untuk Aset Statis**
4. **Service Worker Caching**

**Target Skor Lighthouse:**
- Kinerja: >90
- Aksesibilitas: >95
- Praktik Terbaik: >95
- SEO: >90

---

## Pertimbangan Penskalaan

### Keterbatasan Saat Ini

- SQLite single-writer
- Baik untuk <1000 pengguna bersamaan
- Cocok untuk jemaat kecil-menengah

### Opsi Penskalaan

**Vertical Scaling:**
- Tingkatkan sumber daya server
- Lebih banyak RAM, CPU, penyimpanan

**Horizontal Scaling:**
- PocketBase read replicas
- CDN untuk frontend
- Load balancer

**Migrasi Database:**
- Migrasi ke PostgreSQL untuk skala yang lebih besar
- PocketBase mendukung PostgreSQL

---

## Pemecahan Masalah Deployment

### Masalah Backend

**Masalah:** Database terkunci
**Solusi:** Pastikan hanya satu instance yang berjalan

**Masalah:** Email tidak terkirim
**Solusi:** Periksa kredensial SMTP dan port

**Masalah:** Penggunaan memori tinggi
**Solusi:** Tingkatkan RAM server atau optimalkan kueri

### Masalah Frontend

**Masalah:** Build gagal
**Solusi:** Periksa versi Node.js (memerlukan 22+)

**Masalah:** Error CORS
**Solusi:** Konfigurasi `PB_ALLOW_ORIGINS` di backend

---

## Estimasi Biaya

### Layanan Hosted
- Hubungi ministry-mapper.com untuk harga
- Termasuk dukungan, pembaruan, backup

### Biaya Self-Hosting

**Pengaturan Minimal:**
- Railway/Render: $0-5/bulan (tier gratis)
- Vercel/Netlify: $0/bulan (tier gratis)
- **Total: $0-5/bulan**

**Pengaturan yang Disarankan:**
- Backend (Railway): $5/bulan
- Frontend (Vercel): $0/bulan
- Sentry: $0/bulan (tier gratis)
- **Total: $5/bulan**

**Pengaturan Produksi:**
- DigitalOcean Droplet: $12/bulan
- CloudFront CDN: $2/bulan
- Sentry Pro: $26/bulan
- **Total: $40/bulan**

**Pengaturan Enterprise:**
- Infrastruktur AWS: $100-500/bulan
- Kontrak dukungan: Bervariasi
- **Total: $100-500+/bulan**

**Investasi Waktu:**
- Pengaturan awal: 4-8 jam
- Pemeliharaan bulanan: 2-4 jam
- Dukungan darurat: Sesuai kebutuhan

---

## Langkah Selanjutnya

### Setelah Deployment

1. **Uji Secara Menyeluruh**
   - Buat akun tes
   - Buat wilayah contoh
   - Uji semua fitur
   - Verifikasi email berfungsi

2. **Pelatihan Pengguna**
   - Buat dokumentasi
   - Latih administrator
   - Adakan sesi pengguna

3. **Monitor Sistem**
   - Periksa Sentry setiap hari
   - Tinjau log setiap minggu
   - Perbarui setiap bulan

4. **Verifikasi Backup**
   - Uji proses restore
   - Verifikasi integritas backup
   - Dokumentasikan prosedur

---

## Sumber Daya Dukungan

### Dokumentasi
- [Ikhtisar Arsitektur](architecture.md)
- [Pengaturan Backend](backend-setup.md)
- [Pengaturan Frontend](frontend-setup.md)

### Komunitas
- GitHub Issues
- Situs dokumentasi
- Forum komunitas

### Dukungan Profesional
- Dukungan layanan hosted
- Konsultasi tersedia
- Pengembangan kustom
