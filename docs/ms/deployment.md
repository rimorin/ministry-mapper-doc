# Panduan Pelaksanaan

## Gambaran Keseluruhan

Panduan ini merangkumi pelbagai pilihan pelaksanaan untuk Ministry Mapper, daripada menggunakan perkhidmatan yang dihoskan hingga konfigurasi pengehosan sendiri yang lanjutan.

## Pokok Keputusan Pelaksanaan Pantas

```
┌─────────────────────────────────────┐
│ Adakah anda mempunyai kepakaran     │
│ teknikal dan keperluan pengehosan   │
│ sendiri yang khusus?                │
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
│ Hosti Sendiri│              │ Gunakan          │
│              │              │ Perkhidmatan     │
│ Lihat Di     │              │ yang Dihoskan    │
│ Bawah        │              │                  │
└──────────────┘              │ ministry-mapper  │
                              │ .com             │
                              └──────────────────┘
```

## Pilihan 1: Perkhidmatan yang Dihoskan (Disyorkan)

**Terbaik untuk:** Kebanyakan jemaah, organisasi kecil hingga sederhana

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### Kelebihan

✅ **Tiada Persediaan** - Mula menggunakan segera
✅ **Sokongan Profesional** - Bantuan apabila anda memerlukannya
✅ **Kemas Kini Automatik** - Sentiasa pada versi terkini
✅ **Infrastruktur Diselenggarakan** - Tiada pengurusan pelayan
✅ **Keselamatan Lebih Baik** - Amalan keselamatan profesional
✅ **Bantuan Pematuhan** - Bantuan dengan GDPR, CCPA, dll.
✅ **Sandaran Termasuk** - Sandaran data automatik
✅ **Skalabiliti** - Mengendalikan pertumbuhan secara automatik
✅ **Kos Efektif** - Selalunya lebih murah daripada pengehosan sendiri

### Memulakan

1. Lawati [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akaun
3. Sahkan e-mel anda
4. Hubungi pentadbir untuk akses jemaah
5. Mula menggunakan segera

**Tiada pengetahuan teknikal diperlukan!**

---

## Pilihan 2: Pengehosan Sendiri

**Terbaik untuk:** Organisasi dengan kepakaran teknikal dan keperluan khusus

**Keperluan:**
- Pentadbir sistem atau pembangun yang berpengalaman
- Pemahaman tentang Docker, pangkalan data, dan pelayan web
- Keupayaan untuk menyelenggarakan dan mengemas kini sistem
- Pengetahuan tentang amalan terbaik keselamatan
- Kepakaran pematuhan untuk undang-undang privasi

### Gambaran Keseluruhan Pengehosan Sendiri

Ministry Mapper terdiri daripada dua komponen:

1. **Bahagian Belakang** - Aplikasi PocketBase Go dengan SQLite
2. **Bahagian Hadapan** - Aplikasi web statik React

Kedua-dua mesti dilaksanakan dan disambungkan.

---

## Pilihan Pelaksanaan Bahagian Belakang

### Pilihan 1: Railway (Disyorkan untuk Pengehosan Sendiri)

**Terbaik untuk:** Pengehosan sendiri mudah dengan konfigurasi minimum

**Langkah Persediaan:**

1. **Buat Akaun Railway**
   ```
   Lawati: https://railway.app
   Daftar dengan GitHub
   ```

2. **Laksanakan Bahagian Belakang**
   ```bash
   # Fork repositori
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # Sambung ke Railway
   railway init
   railway link
   ```

3. **Konfigurasikan Persekitaran**
   ```bash
   # Tambah pembolehubah persekitaran dalam papan pemuka Railway
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **Laksanakan**
   ```bash
   railway up
   ```

**Ciri-ciri:**
- Peringkat percuma tersedia
- HTTPS automatik
- Penskalaan mudah
- Pemantauan terbina dalam
- Pelaksanaan satu klik

---

### Pilihan 2: Render

**Langkah Persediaan:**

1. **Buat Akaun Render**
2. **Buat Perkhidmatan Web Baru**
   - Sambung repositori GitHub
   - Pilih `ministry-mapper-be`
   - Pilih pelaksanaan Docker
3. **Konfigurasikan Persekitaran**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```
4. **Laksanakan**
   - Render membina dan melaksanakan secara automatik
   - Mengambil masa 5-10 minit untuk pelaksanaan pertama

---

### Pilihan 3: DigitalOcean Droplet

**Terbaik untuk:** Kawalan penuh, pelaksanaan pengeluaran

**Langkah Persediaan:**

1. **Buat Droplet**
   ```bash
   # Ubuntu 22.04 LTS disyorkan
   # Minimum: 1GB RAM, 1 CPU, 25GB SSD
   # Disyorkan: 2GB RAM, 2 CPU, 50GB SSD
   ```

2. **Pasang Docker**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **Klon Repositori**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **Konfigurasikan Persekitaran**
   ```bash
   cp .env.sample .env
   nano .env  # Edit dengan tetapan anda
   ```

5. **Bina dan Jalankan**
   ```bash
   docker-compose up -d
   ```

6. **Sediakan Proksi Terbalik Nginx**
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

7. **Sediakan SSL dengan Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**Ciri-ciri:**
- Kawalan penuh
- Harga boleh diramal ($6-12/bulan)
- Prestasi tinggi
- Konfigurasi tersuai

---

### Pilihan 4: AWS (Lanjutan)

**Terbaik untuk:** Pelaksanaan perusahaan, keperluan skalabiliti tinggi

**Seni Bina:**
```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
Application Load Balancer
    ↓
ECS Fargate (Kontena)
    ↓
RDS PostgreSQL (jika berhijrah dari SQLite)
    ↓
S3 (Penyimpanan Fail)
```

**Kos:** ~$50-200/bulan bergantung pada penggunaan

**Kerumitan:** Tinggi - memerlukan kepakaran AWS

---

## Pilihan Pelaksanaan Bahagian Hadapan

### Pilihan 1: Vercel (Disyorkan)

**Terbaik untuk:** Pelaksanaan pantas dan mudah

**Langkah Persediaan:**

1. **Buat Akaun Vercel**
2. **Import Repositori**
   ```
   - Klik "New Project"
   - Import ministry-mapper-v2
   - Rangka kerja: Vite
   - Arahan Binaan: npm run build
   - Direktori Output: build
   ```
3. **Konfigurasikan Pembolehubah Persekitaran**
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
4. **Laksanakan**
   - Vercel melaksanakan secara automatik semasa push

**Ciri-ciri:**
- Percuma untuk projek peribadi
- HTTPS automatik
- Rangkaian tepi (pantas di seluruh dunia)
- Pratonton pelaksanaan untuk PR
- Domain tersuai

---

### Pilihan 2: Netlify

**Langkah Persediaan:**

1. **Buat Akaun Netlify**
2. **Import Repositori** - Sambung GitHub, pilih `ministry-mapper-v2`
3. **Tetapan Binaan**
   ```
   Arahan Binaan: npm run build
   Direktori Terbit: build
   ```
4. **Tambah Pembolehubah Persekitaran**
5. **Laksanakan**

---

### Pilihan 3: Cloudflare Pages

**Langkah Persediaan:**

1. **Buat Akaun Cloudflare**
2. **Buat Projek Pages** - Sambung GitHub, pilih repositori
3. **Konfigurasi Binaan**
   ```
   Rangka kerja: Vite
   Arahan Binaan: npm run build
   Direktori Output: build
   ```
4. **Pembolehubah Persekitaran**
5. **Laksanakan**

**Ciri-ciri:**
- Lebar jalur tanpa had
- CDN global
- Perlindungan DDoS
- Analitik Web

---

## Konfigurasi Persekitaran

### Pembolehubah Persekitaran Bahagian Belakang

**Diperlukan:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP (E-mel):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**Keselamatan:**
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

**Perkhidmatan Pilihan:**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### Pembolehubah Persekitaran Bahagian Hadapan

**Diperlukan:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**Halaman Undang-undang:**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**API Pilihan:**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**Pemantauan:**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## Persediaan Sijil SSL/TLS

### Pilihan 1: Let's Encrypt (Percuma)

**Menggunakan Certbot:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**Pembaharuan Auto:**
```bash
sudo certbot renew --dry-run
```

### Pilihan 2: Cloudflare SSL

- SSL percuma termasuk dengan Cloudflare
- Pembaharuan automatik
- SSL Universal

### Pilihan 3: SSL Pembekal Cloud

Kebanyakan pembekal cloud (Vercel, Netlify, Railway) termasuk SSL automatik

---

## Strategi Sandaran Pangkalan Data

### Sandaran Harian Automatik

```bash
# Tambah ke crontab
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### Sandaran ke S3

```bash
# Gunakan AWS CLI
aws s3 cp backup.tar.gz s3://your-bucket/backups/
```

### Dasar Pengekalan Sandaran

- Simpan 30 hari terakhir sandaran harian
- Simpan 12 bulan terakhir sandaran bulanan
- Simpan 5 tahun terakhir sandaran tahunan
- Uji prosedur pemulihan secara tetap

---

## Penyelesaian Masalah Pelaksanaan

### Bahagian Belakang Tidak Boleh Dihubungi

- Sahkan bahagian belakang berjalan: `docker ps`
- Semak log: `docker logs ministry-mapper`
- Uji sambungan: `curl http://localhost:8080/api/health`
- Semak tetapan CORS dan tembok api

### Ralat Binaan Bahagian Hadapan

- Sahkan semua pembolehubah persekitaran ditetapkan
- Semak versi Node.js: `node --version` (memerlukan 22+)
- Padam dan pasang semula dependensi: `rm -rf node_modules && npm install`
- Semak mesej ralat TypeScript

### Isu Penyambungan CORS

```bash
# Dalam konfigurasi bahagian belakang
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
```

### Peta Tidak Dipaparkan

- Sahkan sambungan internet
- Semak konsol pelayar untuk ralat
- Sahkan Leaflet/OpenStreetMap dapat diakses

---

## Senarai Semak Pelaksanaan

**Bahagian Belakang:**
- [ ] Pembolehubah persekitaran ditetapkan
- [ ] Pangkalan data dapat diakses
- [ ] SMTP dikonfigurasi (pilihan)
- [ ] Sentry dikonfigurasi (pilihan)
- [ ] HTTPS diaktifkan
- [ ] CORS dikonfigurasi dengan betul
- [ ] Panel pentadbir disembunyikan (PB_HIDE_CONTROLS=true)
- [ ] Sandaran automatik disediakan
- [ ] Kata laluan lalai ditukar

**Bahagian Hadapan:**
- [ ] Pembolehubah persekitaran ditetapkan
- [ ] Binaan berjaya
- [ ] URL bahagian belakang betul
- [ ] HTTPS diaktifkan
- [ ] Peta dipaparkan dengan betul
- [ ] Pengesahan pengguna berfungsi

---

## Langkah Seterusnya

- [Panduan Penghosing Sendiri](self-hosting.md) - Panduan pengehosan sendiri yang terperinci
- [Persediaan Bahagian Belakang](backend-setup.md) - Konfigurasi bahagian belakang
- [Persediaan Bahagian Hadapan](frontend-setup.md) - Konfigurasi bahagian hadapan
