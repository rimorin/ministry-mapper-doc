# Self-Hosting Ministry Mapper

!!! warning "Self-Hosting Tidak Disarankan"
    Self-hosting Ministry Mapper memerlukan keahlian teknis yang signifikan dan pemeliharaan berkelanjutan. **Kami menyarankan menggunakan layanan hosted kami di [ministry-mapper.com](https://ministry-mapper.com) sebagai gantinya.** Hanya lanjutkan dengan self-hosting jika Anda memiliki:
    
    - Administrator sistem atau pengembang yang berpengalaman
    - Sumber daya untuk pemeliharaan dan pembaruan berkelanjutan
    - Pemahaman tentang praktik keamanan terbaik
    - Kemampuan untuk mematuhi peraturan privasi
    - Persyaratan spesifik yang tidak dapat dipenuhi oleh layanan hosted

## Mengapa Kami Tidak Mendorong Self-Hosting

Meskipun Ministry Mapper adalah open source, self-hosting hadir dengan tantangan:

- **Kompleksitas Teknis**: Memerlukan pengetahuan tentang Docker, database, web server, dan infrastruktur cloud
- **Tanggung Jawab Keamanan**: Anda bertanggung jawab untuk menjaga semuanya tetap aman dan diperbarui
- **Beban Pemeliharaan**: Pembaruan rutin, backup, dan pemantauan sangat penting
- **Kepatuhan Privasi**: Anda harus memastikan kepatuhan GDPR, CCPA, dan undang-undang privasi lainnya
- **Biaya**: Biaya server, biaya API, dan investasi waktu sering kali melebihi biaya layanan hosted
- **Dukungan**: Dukungan komunitas yang terbatas untuk masalah self-hosting

## Prasyarat untuk Self-Hosting

Jika Anda memutuskan untuk melakukan self-hosting meskipun rekomendasi kami, pastikan Anda memiliki:

### Persyaratan Teknis
- Pengalaman dengan administrasi server Linux
- Pemahaman tentang Docker dan kontainerisasi
- Keakraban dengan variabel lingkungan dan konfigurasi
- Pengetahuan tentang konfigurasi web server (Nginx/Apache)
- Pengalaman dengan sertifikat SSL/TLS
- Pemahaman tentang konfigurasi DNS dan domain

### Persyaratan Infrastruktur
- Layanan hosting cloud (Railway, Render, DigitalOcean, AWS, dll.)
- Nama domain untuk instance Anda
- Layanan email untuk notifikasi (opsional)
- Anggaran untuk biaya API opsional (Sentry, dll.)

### Komitmen Waktu
- Pengaturan awal: 2-4 jam
- Pemeliharaan berkelanjutan: 2-4 jam per bulan
- Dukungan darurat saat masalah muncul

## Ikhtisar Arsitektur

Ministry Mapper terdiri dari dua komponen terpisah:

1. **Backend (ministry-mapper-be)**: Server PocketBase dengan ekstensi Go
   - Repository: [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - Teknologi: Go, PocketBase, SQLite
   - Berjalan di port 8080 (Docker) atau 8090 (lokal)

2. **Frontend (ministry-mapper-v2)**: Aplikasi web React
   - Repository: [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - Teknologi: React, TypeScript, Vite
   - File statis yang di-deploy ke layanan hosting

Keduanya harus di-deploy dan dikonfigurasi agar dapat bekerja bersama.

## Panduan Self-Hosting Backend

### Konfigurasi Environment

Buat file `.env` dengan pengaturan ini:

```bash
# Pengaturan Aplikasi
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# Akun Admin Awal (ganti kata sandi setelah login pertama!)
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# Konfigurasi Email (SMTP)
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# Pengaturan Keamanan
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# Autentikasi (opsional)
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# Pelacakan Error (opsional tapi disarankan)
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# Integrasi Layanan (opsional)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Deployment Docker

1. **Clone Repository**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **Build Docker Image**
```bash
docker build -t ministry-mapper-backend .
```

3. **Jalankan Container**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Catatan Penting:**
- Container berjalan di port 8080 secara internal
- Mount volume persisten ke `/app/pb_data` untuk menyimpan data
- Database Anda berada di folder `pb_data`

4. **Akses Panel Admin**
Navigasi ke `https://your-backend-domain.com/_/` dan login dengan kredensial admin Anda.

### Pengembangan Lokal

Untuk pengujian di mesin lokal Anda:

```bash
# Clone repository
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# Install dependensi
./scripts/install.sh

# Konfigurasi environment
cp .env.sample .env
# Edit .env dengan pengaturan Anda

# Mulai aplikasi
./scripts/start.sh
```

Backend berjalan di `http://localhost:8090` secara default.

### Backup Database

Data Anda sangat penting. Siapkan backup otomatis:

```bash
# Backup manual
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# Backup harian otomatis (tambahkan ke crontab)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### Daftar Periksa Keamanan

- [ ] Ganti kata sandi admin default segera
- [ ] Gunakan HTTPS saja (jangan pernah HTTP)
- [ ] Atur `PB_ALLOW_ORIGINS` ke domain tertentu (bukan `*`)
- [ ] Aktifkan `PB_HIDE_CONTROLS=true`
- [ ] Aktifkan `PB_ENABLE_RATE_LIMITING=true`
- [ ] Konfigurasi firewall untuk membatasi akses
- [ ] Siapkan backup otomatis
- [ ] Konfigurasi Sentry untuk pemantauan error
- [ ] Gunakan kata sandi yang kuat dan unik
- [ ] Perbarui dependensi secara rutin
- [ ] Tinjau praktik keamanan terbaik PocketBase

## Panduan Self-Hosting Frontend

### Konfigurasi Environment

Buat file `.env`:

```bash
# System Environment
VITE_SYSTEM_ENVIRONMENT=production

# Version
VITE_VERSION=$npm_package_version

# URL Backend PocketBase (tanpa trailing slash)
VITE_POCKETBASE_URL=https://your-backend-domain.com

# Halaman Legal (wajib)
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# Pelacakan Error (opsional tapi disarankan)
VITE_SENTRY_DSN=your_sentry_dsn
```

### Build untuk Produksi

```bash
# Clone repository
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# Install dependensi
npm install

# Build
npm run build
```

Output akan berada di direktori `build/`.

### Opsi Deployment

#### Layanan Hosting Statis (Termudah)

**Vercel:**
1. Impor repository GitHub
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Tambahkan variabel lingkungan

**Netlify:**
1. Situs baru dari Git
2. Build command: `npm run build`
3. Publish directory: `build`
4. Tambahkan variabel lingkungan

**Cloudflare Pages:**
1. Buat proyek Pages
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Tambahkan variabel lingkungan

#### Web Server Self-Hosted

**Konfigurasi Nginx:**

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
    
    # Header keamanan
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**Konfigurasi Apache:**

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

### Pengaturan Sentry (Opsional)

1. Buat akun di [sentry.io](https://sentry.io)
2. Buat proyek React
3. Dapatkan DSN
4. Tambahkan ke `.env` sebagai `VITE_SENTRY_DSN`

## Pemeliharaan dan Pembaruan

### Tugas Pemeliharaan Rutin

**Mingguan:**
- Periksa Sentry untuk error
- Tinjau log aplikasi
- Verifikasi backup berjalan

**Bulanan:**
- Perbarui dependensi: `npm install` dan `go get -u`
- Tinjau advisory keamanan
- Uji prosedur pemulihan bencana
- Periksa penggunaan ruang disk
- Tinjau umpan balik pengguna

**Triwulanan:**
- Audit keamanan
- Tinjauan kinerja
- Perbarui dokumentasi
- Tinjau dan perbarui kebijakan privasi
- Uji semua fitur secara menyeluruh

### Pembaruan ke Versi Baru

**Pembaruan Backend:**
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

**Pembaruan Frontend:**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# Deploy folder build/ ke layanan hosting Anda
```

**Selalu backup sebelum memperbarui!**

### Pemantauan

**Yang Perlu Dipantau:**
- Uptime dan kesehatan server
- Tingkat error aplikasi (Sentry)
- Pertumbuhan ukuran database
- Keberhasilan/kegagalan backup
- Kedaluwarsa sertifikat SSL
- Masalah autentikasi pengguna
- Metrik kinerja

**Alat:**
- Sentry untuk pelacakan error
- Google Cloud Console untuk penggunaan API
- Pemantauan server (Prometheus, Grafana, dll.)
- Monitor uptime (UptimeRobot, Pingdom)
- Agregasi log (ELK stack, CloudWatch)

## Pemecahan Masalah Instance Self-Hosted

### Masalah Backend

**Port Sudah Digunakan:**
```bash
lsof -i :8080
kill -9 <process_id>
```

**Database Terkunci:**
- Hentikan semua instance yang mengakses database
- Pastikan hanya satu proses backend yang berjalan
- Periksa proses yang tergantung

**Email Tidak Terkirim:**
- Verifikasi kredensial SMTP
- Periksa firewall tidak memblokir port SMTP
- Untuk Gmail, gunakan App Passwords
- Tinjau log email di `pb_data/logs/`

**Tidak Dapat Mengakses Panel Admin:**
- URL harus `https://your-domain.com/_/` (perhatikan garis bawah)
- Periksa pengaturan `PB_HIDE_CONTROLS`
- Verifikasi backend berjalan
- Bersihkan cache browser

### Masalah Frontend

**Tidak Dapat Terhubung ke Backend:**
- Verifikasi `VITE_POCKETBASE_URL` sudah benar
- Periksa pengaturan CORS di backend
- Pastikan backend dapat diakses
- Periksa konsol browser untuk error

**Kegagalan Build:**
- Pastikan Node.js versi 22+
- Hapus `node_modules` dan instal ulang
- Periksa semua variabel lingkungan sudah diatur
- Tinjau pesan error build

### Masalah Kinerja

**Waktu Respons Lambat:**
- Periksa sumber daya server (CPU, RAM, disk)
- Tinjau ukuran database dan kueri
- Aktifkan optimasi database
- Periksa latensi jaringan
- Tinjau log PocketBase untuk kueri lambat

## Privasi dan Kepatuhan Hukum

### Tanggung Jawab Anda sebagai Self-Hoster

Saat melakukan self-hosting, Anda adalah pengontrol data dan harus:

- **Mematuhi Undang-Undang Privasi**: GDPR (UE), CCPA (California), dll.
- **Membuat Kebijakan Privasi**: Jelaskan pengumpulan dan penggunaan data
- **Membuat Ketentuan Layanan**: Tentukan tanggung jawab pengguna
- **Menerapkan Keamanan Data**: Enkripsi, kontrol akses, backup
- **Menangani Permintaan Data**: Hak akses, penghapusan, portabilitas pengguna
- **Melaporkan Pelanggaran Data**: Dalam kerangka waktu hukum
- **Menyimpan Catatan**: Aktivitas pemrosesan data
- **Menunjuk DPO**: Jika diperlukan oleh peraturan

### Dokumen Hukum yang Diperlukan

Anda harus menyediakan:

1. **Kebijakan Privasi** - Diwajibkan oleh hukum, harus menjelaskan:
   - Data apa yang Anda kumpulkan
   - Bagaimana Anda menggunakannya
   - Berapa lama Anda menyimpannya
   - Hak pengguna
   - Cara menghubungi Anda

2. **Ketentuan Layanan** - Mendefinisikan:
   - Penggunaan yang dapat diterima
   - Tanggung jawab pengguna
   - Pembatasan kewajiban
   - Kebijakan penghentian

3. **Kebijakan Cookie** - Jika Anda menggunakan cookie/pelacakan

### Tindakan Perlindungan Data

- Gunakan HTTPS di mana-mana
- Enkripsi data saat istirahat
- Terapkan kontrol akses
- Audit keamanan rutin
- Enkripsi backup
- Kebijakan kata sandi yang aman
- Autentikasi multi-faktor
- Log aktivitas
- Pembaruan keamanan rutin

## Pertimbangan Biaya

### Biaya Infrastruktur

**Hosting Backend:**
- VPS: $10-50/bulan (DigitalOcean, Linode)
- PaaS: $10-30/bulan (Railway, Render)
- Cloud: Bervariasi (AWS, GCP)

**Hosting Frontend:**
- Hosting statis: $0-10/bulan (Vercel, Netlify, Cloudflare)
- Biaya CDN: Biasanya termasuk

**Domain:**
- $10-20/tahun

**Sertifikat SSL:**
- Gratis (Let's Encrypt) atau $0-100/tahun

### Biaya Layanan

**Layanan Email:**
- Gmail SMTP: Gratis (terbatas)
- MailerSend: Tier gratis tersedia
- SendGrid: Tier gratis tersedia

**Pelacakan Error:**
- Sentry: Tier gratis tersedia
- Berbayar: $26-80/bulan

**Penyimpanan Backup:**
- $5-20/bulan tergantung ukuran

### Investasi Waktu

**Pengaturan Awal:**
- Backend: 2-3 jam
- Frontend: 1-2 jam
- Konfigurasi: 1-2 jam
- Pengujian: 2-3 jam
- **Total: 6-10 jam**

**Pemeliharaan Bulanan:**
- Pemantauan: 1-2 jam
- Pembaruan: 1-2 jam
- Dukungan: 1-3 jam
- **Total: 3-7 jam**

### Total Biaya Kepemilikan (Tahunan)

**Jemaat kecil (< 100 pengguna):**
- Infrastruktur: $240-720
- API: $0-100 (biasanya tier gratis)
- Layanan: $0-300
- Domain/SSL: $10-20
- **Total: $250-1.140 + 48-96 jam/tahun**

Bandingkan ini dengan langganan layanan hosted sebelum berkomitmen.

## Mendapatkan Bantuan

### Dukungan Komunitas

- GitHub Issues: [ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) dan [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- Baca issue yang ada sebelum membuat yang baru
- Berikan informasi yang detail saat melaporkan masalah

### Yang Perlu Disertakan dalam Permintaan Dukungan

1. Nomor versi (backend dan frontend)
2. Environment deployment (Docker, lokal, layanan hosting)
3. Pesan error (teks lengkap)
4. Langkah untuk mereproduksi
5. Perilaku yang diharapkan vs aktual
6. Log yang relevan
7. Konfigurasi (sudah disanitasi, tanpa rahasia)

### Dukungan yang Terbatas

!!! info "Keterbatasan Dukungan"
    Dukungan self-hosting berbasis komunitas dan terbatas. Tim Ministry Mapper memprioritaskan layanan hosted. Untuk dukungan profesional, pertimbangkan menggunakan layanan hosted sebagai gantinya.
