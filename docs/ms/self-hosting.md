# Hos Sendiri Ministry Mapper

!!! warning "Hos Sendiri Tidak Disyorkan"
    Hos sendiri Ministry Mapper memerlukan kepakaran teknikal yang ketara dan penyelenggaraan berterusan. **Kami mengesyorkan menggunakan perkhidmatan dihoskan kami di [ministry-mapper.com](https://ministry-mapper.com) sebaliknya.** Teruskan dengan hos sendiri hanya jika anda mempunyai:

    - Pentadbir sistem atau pembangun berpengalaman
    - Sumber untuk penyelenggaraan dan kemas kini berterusan
    - Pemahaman tentang amalan terbaik keselamatan
    - Keupayaan untuk mematuhi peraturan privasi
    - Keperluan khusus yang tidak dapat dipenuhi oleh perkhidmatan dihoskan

## Mengapa Kami Tidak Menggalakkan Hos Sendiri

Walaupun Ministry Mapper adalah sumber terbuka, hos sendiri datang dengan cabaran:

- **Kerumitan Teknikal**: Memerlukan pengetahuan tentang Docker, pangkalan data, pelayan web, dan infrastruktur awan
- **Tanggungjawab Keselamatan**: Anda bertanggungjawab untuk memastikan segala-galanya selamat dan dikemas kini
- **Beban Penyelenggaraan**: Kemas kini, sandaran, dan pemantauan berkala adalah penting
- **Pematuhan Privasi**: Anda mesti memastikan pematuhan GDPR, CCPA, dan undang-undang privasi lain
- **Kos**: Kos pelayan, kos API, dan pelaburan masa sering melebihi yuran perkhidmatan dihoskan
- **Sokongan**: Sokongan komuniti terhad untuk masalah hos sendiri

## Prasyarat untuk Hos Sendiri

Jika anda telah memutuskan untuk hos sendiri walaupun dengan pengesyoran kami, pastikan anda mempunyai:

### Keperluan Teknikal
- Pengalaman dengan pentadbiran pelayan Linux
- Pemahaman tentang Docker dan kontainerisasi
- Kebiasaan dengan pemboleh ubah persekitaran dan konfigurasi
- Pengetahuan tentang konfigurasi pelayan web (Nginx/Apache)
- Pengalaman dengan sijil SSL/TLS
- Pemahaman tentang DNS dan konfigurasi domain

### Keperluan Infrastruktur
- Perkhidmatan pengehosan awan (Railway, Render, DigitalOcean, AWS, dll.)
- Nama domain untuk contoh anda
- Perkhidmatan e-mel untuk pemberitahuan (pilihan)
- Bajet untuk kos API pilihan (Sentry, dll.)

### Komitmen Masa
- Persediaan awal: 2-4 jam
- Penyelenggaraan berterusan: 2-4 jam sebulan
- Sokongan kecemasan apabila masalah timbul

## Gambaran Keseluruhan Seni Bina

Ministry Mapper terdiri daripada dua komponen berasingan:

1. **Backend (ministry-mapper-be)**: Pelayan PocketBase dengan sambungan Go
   - Repositori: [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - Teknologi: Go, PocketBase, SQLite
   - Berjalan pada port 8080 (Docker) atau 8090 (tempatan)

2. **Frontend (ministry-mapper-v2)**: Aplikasi web React
   - Repositori: [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - Teknologi: React, TypeScript, Vite
   - Fail statik ditempatkan ke perkhidmatan pengehosan

Kedua-duanya mesti ditempatkan dan dikonfigurasi untuk berfungsi bersama.

## Panduan Hos Sendiri Backend

### Konfigurasi Persekitaran

Cipta fail `.env` dengan tetapan ini:

```bash
# Tetapan Aplikasi
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# Akaun Pentadbir Awal (tukar kata laluan selepas log masuk pertama!)
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# Konfigurasi E-mel (SMTP)
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# Tetapan Keselamatan
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# Pengesahan (pilihan)
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# Penjejakan Ralat (pilihan tetapi disyorkan)
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# Integrasi Perkhidmatan (pilihan)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Penempatan Docker

1. **Klon Repositori**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **Bina Imej Docker**
```bash
docker build -t ministry-mapper-backend .
```

3. **Jalankan Kontena**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Nota Penting:**
- Kontena berjalan pada port 8080 secara dalaman
- Lekapkan volum berterusan ke `/app/pb_data` untuk mengekalkan data
- Pangkalan data anda berada dalam folder `pb_data`

4. **Akses Panel Pentadbir**
Navigasi ke `https://your-backend-domain.com/_/` dan log masuk dengan kelayakan pentadbir anda.

### Pembangunan Tempatan

Untuk ujian pada mesin tempatan anda:

```bash
# Klon repositori
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# Pasang kebergantungan
./scripts/install.sh

# Konfigurasi persekitaran
cp .env.sample .env
# Edit .env dengan tetapan anda

# Mulakan aplikasi
./scripts/start.sh
```

Backend berjalan di `http://localhost:8090` secara lalai.

### Sandaran Pangkalan Data

Data anda adalah kritikal. Sediakan sandaran automatik:

```bash
# Sandaran manual
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# Sandaran harian automatik (tambah ke crontab)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### Senarai Semak Keselamatan

- [ ] Tukar kata laluan pentadbir lalai serta-merta
- [ ] Gunakan HTTPS sahaja (jangan sekali-kali HTTP)
- [ ] Tetapkan `PB_ALLOW_ORIGINS` kepada domain khusus (bukan `*`)
- [ ] Aktifkan `PB_HIDE_CONTROLS=true`
- [ ] Aktifkan `PB_ENABLE_RATE_LIMITING=true`
- [ ] Konfigurasi tembok api untuk menyekat akses
- [ ] Sediakan sandaran automatik
- [ ] Konfigurasi Sentry untuk pemantauan ralat
- [ ] Gunakan kata laluan yang kuat dan unik
- [ ] Kekalkan kebergantungan dikemas kini
- [ ] Semak amalan terbaik keselamatan PocketBase

## Panduan Hos Sendiri Frontend

### Konfigurasi Persekitaran

Cipta fail `.env`:

```bash
# Persekitaran Sistem
VITE_SYSTEM_ENVIRONMENT=production

# Versi
VITE_VERSION=$npm_package_version

# URL Backend PocketBase (tiada garis miring di hujung)
VITE_POCKETBASE_URL=https://your-backend-domain.com

# Halaman Undang-undang (diperlukan)
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# Penjejakan Ralat (pilihan tetapi disyorkan)
VITE_SENTRY_DSN=your_sentry_dsn
```

### Membina untuk Pengeluaran

```bash
# Klon repositori
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# Pasang kebergantungan
npm install

# Bina
npm run build
```

Output akan berada dalam direktori `build/`.

### Pilihan Penempatan

#### Perkhidmatan Pengehosan Statik (Paling Mudah)

**Vercel:**
1. Import repositori GitHub
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Tambah pemboleh ubah persekitaran

**Netlify:**
1. New site from Git
2. Build command: `npm run build`
3. Publish directory: `build`
4. Tambah pemboleh ubah persekitaran

**Cloudflare Pages:**
1. Create Pages project
2. Framework: Vite
3. Build command: `npm run build`
4. Output directory: `build`
5. Tambah pemboleh ubah persekitaran

#### Pelayan Web Hos Sendiri

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

    # Pengepala keselamatan
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

### Persediaan Sentry (Pilihan)

1. Cipta akaun di [sentry.io](https://sentry.io)
2. Cipta projek React
3. Dapatkan DSN
4. Tambah ke `.env` sebagai `VITE_SENTRY_DSN`

## Penyelenggaraan dan Kemas Kini

### Tugas Penyelenggaraan Berkala

**Mingguan:**
- Semak Sentry untuk ralat
- Semak log aplikasi
- Sahkan sandaran berfungsi

**Bulanan:**
- Kemas kini kebergantungan: `npm install` dan `go get -u`
- Semak nasihat keselamatan
- Uji prosedur pemulihan bencana
- Semak penggunaan ruang cakera
- Semak maklum balas pengguna

**Suku Tahunan:**
- Audit keselamatan
- Semakan prestasi
- Kemas kini dokumentasi
- Semak dan kemas kini dasar privasi
- Uji semua ciri dengan teliti

### Mengemas Kini ke Versi Baharu

**Kemas Kini Backend:**
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

**Kemas Kini Frontend:**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# Tempatkan folder build/ ke perkhidmatan pengehosan anda
```

**Sentiasa sandarkan sebelum mengemas kini!**

### Pemantauan

**Apa yang Perlu Dipantau:**
- Masa aktif dan kesihatan pelayan
- Kadar ralat aplikasi (Sentry)
- Pertumbuhan saiz pangkalan data
- Kejayaan/kegagalan sandaran
- Tamat tempoh sijil SSL
- Masalah pengesahan pengguna
- Metrik prestasi

**Alat:**
- Sentry untuk penjejakan ralat
- Google Cloud Console untuk penggunaan API
- Pemantauan pelayan (Prometheus, Grafana, dll.)
- Monitor masa aktif (UptimeRobot, Pingdom)
- Pengagregatan log (tindanan ELK, CloudWatch)

## Penyelesaian Masalah Contoh Hos Sendiri

### Masalah Backend

**Port Sudah Digunakan:**
```bash
lsof -i :8080
kill -9 <process_id>
```

**Pangkalan Data Terkunci:**
- Hentikan semua contoh yang mengakses pangkalan data
- Pastikan hanya satu proses backend berjalan
- Semak proses yang tergantung

**E-mel Tidak Dihantar:**
- Sahkan kelayakan SMTP
- Semak tembok api tidak menyekat port SMTP
- Untuk Gmail, gunakan Kata Laluan Aplikasi
- Semak log e-mel dalam `pb_data/logs/`

**Tidak Boleh Akses Panel Pentadbir:**
- URL mesti `https://your-domain.com/_/` (perhatikan garis bawah)
- Semak tetapan `PB_HIDE_CONTROLS`
- Sahkan backend berjalan
- Kosongkan cache pelayar

### Masalah Frontend

**Tidak Boleh Sambung ke Backend:**
- Sahkan `VITE_POCKETBASE_URL` betul
- Semak tetapan CORS dalam backend
- Pastikan backend boleh diakses
- Semak konsol pelayar untuk ralat

**Kegagalan Binaan:**
- Pastikan versi Node.js 22+
- Padam `node_modules` dan pasang semula
- Semak semua pemboleh ubah persekitaran ditetapkan
- Semak mesej ralat binaan

### Masalah Prestasi

**Masa Tindak Balas Perlahan:**
- Semak sumber pelayan (CPU, RAM, cakera)
- Semak saiz dan pertanyaan pangkalan data
- Aktifkan pengoptimuman pangkalan data
- Semak kependaman rangkaian
- Semak log PocketBase untuk pertanyaan perlahan

## Privasi dan Pematuhan Undang-undang

### Tanggungjawab Anda sebagai Hos Sendiri

Apabila hos sendiri, anda adalah pengawal data dan mesti:

- **Patuhi Undang-undang Privasi**: GDPR (EU), CCPA (California), dll.
- **Cipta Dasar Privasi**: Terangkan pengumpulan dan penggunaan data
- **Cipta Terma Perkhidmatan**: Tentukan tanggungjawab pengguna
- **Laksanakan Keselamatan Data**: Penyulitan, kawalan akses, sandaran
- **Tangani Permintaan Data**: Hak akses, pemadaman, kebolehpindahan pengguna
- **Laporkan Pelanggaran Data**: Dalam tempoh masa undang-undang
- **Kekalkan Rekod**: Aktiviti pemprosesan data
- **Lantik DPO**: Jika dikehendaki oleh peraturan

### Dokumen Undang-undang Diperlukan

Anda mesti menyediakan:

1. **Dasar Privasi** - Dikehendaki oleh undang-undang, mesti menerangkan:
   - Data apa yang anda kumpulkan
   - Bagaimana anda menggunakannya
   - Berapa lama anda menyimpannya
   - Hak pengguna
   - Cara menghubungi anda

2. **Terma Perkhidmatan** - Mentakrifkan:
   - Penggunaan yang boleh diterima
   - Tanggungjawab pengguna
   - Had liabiliti
   - Dasar penamatan

3. **Dasar Kuki** - Jika anda menggunakan kuki/penjejakan

### Langkah Perlindungan Data

- Gunakan HTTPS di mana-mana
- Sulitkan data dalam keadaan rehat
- Laksanakan kawalan akses
- Audit keselamatan berkala
- Penyulitan sandaran
- Dasar kata laluan selamat
- Pengesahan berbilang faktor
- Pengelogan aktiviti
- Kemas kini keselamatan berkala

## Pertimbangan Kos

### Kos Infrastruktur

**Pengehosan Backend:**
- VPS: $10-50/bulan (DigitalOcean, Linode)
- PaaS: $10-30/bulan (Railway, Render)
- Awan: Berubah-ubah (AWS, GCP)

**Pengehosan Frontend:**
- Pengehosan statik: $0-10/bulan (Vercel, Netlify, Cloudflare)
- Kos CDN: Biasanya disertakan

**Domain:**
- $10-20/tahun

**Sijil SSL:**
- Percuma (Let's Encrypt) atau $0-100/tahun

### Kos Perkhidmatan

**Perkhidmatan E-mel:**
- Gmail SMTP: Percuma (terhad)
- MailerSend: Peringkat percuma tersedia
- SendGrid: Peringkat percuma tersedia

**Penjejakan Ralat:**
- Sentry: Peringkat percuma tersedia
- Berbayar: $26-80/bulan

**Penyimpanan Sandaran:**
- $5-20/bulan bergantung pada saiz

### Pelaburan Masa

**Persediaan Awal:**
- Backend: 2-3 jam
- Frontend: 1-2 jam
- Konfigurasi: 1-2 jam
- Ujian: 2-3 jam
- **Jumlah: 6-10 jam**

**Penyelenggaraan Bulanan:**
- Pemantauan: 1-2 jam
- Kemas kini: 1-2 jam
- Sokongan: 1-3 jam
- **Jumlah: 3-7 jam**

### Jumlah Kos Pemilikan (Tahunan)

**Sidang kecil (< 100 pengguna):**
- Infrastruktur: $240-720
- API: $0-100 (biasanya peringkat percuma)
- Perkhidmatan: $0-300
- Domain/SSL: $10-20
- **Jumlah: $250-1,140 + 48-96 jam/tahun**

Bandingkan ini dengan langganan perkhidmatan dihoskan sebelum membuat komitmen.

## Mendapatkan Bantuan

### Sokongan Komuniti

- GitHub Issues: [ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) dan [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- Baca isu sedia ada sebelum mencipta yang baharu
- Berikan maklumat terperinci apabila melaporkan masalah

### Apa yang Perlu Disertakan dalam Permintaan Sokongan

1. Nombor versi (backend dan frontend)
2. Persekitaran penempatan (Docker, tempatan, perkhidmatan pengehosan)
3. Mesej ralat (teks penuh)
4. Langkah untuk mengeluarkan semula
5. Tingkah laku yang dijangka vs sebenar
6. Log yang berkaitan
7. Konfigurasi (disanitasi, tiada rahsia)

### Sokongan Terhad

!!! info "Had Sokongan"
    Sokongan hos sendiri adalah berasaskan komuniti dan terhad. Pasukan Ministry Mapper mengutamakan perkhidmatan dihoskan. Untuk sokongan profesional, pertimbangkan menggunakan perkhidmatan dihoskan sebaliknya.

## Alternatif kepada Hos Sendiri

Sebelum hos sendiri, pertimbangkan:

1. **Gunakan Perkhidmatan Dihoskan Rasmi di [ministry-mapper.com](https://ministry-mapper.com)**
   - Tiada beban penyelenggaraan
   - Sokongan profesional
   - Kemas kini automatik
   - Kebolehpercayaan lebih baik
   - Pematuhan dikendalikan untuk anda
   - Tersedia sekarang!

2. **Minta Ciri**
   - Jika anda memerlukan penyesuaian, minta ciri dalam projek utama
   - Memberi manfaat kepada semua orang
   - Diselenggara oleh pasukan
   - Mungkin ditambah ke perkhidmatan dihoskan

3. **Sumbang kepada Projek**
   - Tingkatkan pangkalan kod utama
   - Bantu orang lain
   - Dapatkan sokongan komuniti

## Kesimpulan

Hos sendiri Ministry Mapper adalah mungkin tetapi memerlukan kepakaran, masa, dan sumber yang ketara. **Kami sangat mengesyorkan menggunakan perkhidmatan dihoskan di [ministry-mapper.com](https://ministry-mapper.com)** melainkan anda mempunyai keperluan khusus yang mewajarkan kerumitan tambahan.

### Gunakan Perkhidmatan Dihoskan Sebaliknya

Lawati **[ministry-mapper.com](https://ministry-mapper.com)** untuk:
- Persediaan serta-merta - tiada pengetahuan teknikal diperlukan
- Sokongan profesional apabila anda memerlukannya
- Sandaran automatik dan kemas kini keselamatan
- Bantuan pematuhan undang-undang
- Kebolehpercayaan dan masa aktif yang lebih baik
- Jumlah kos pemilikan yang lebih rendah

### Masih Mahu Hos Sendiri?

Jika anda meneruskan dengan hos sendiri:
- Ikuti semua amalan terbaik keselamatan
- Kekalkan sistem dikemas kini
- Kekalkan sandaran berkala
- Pastikan pematuhan undang-undang
- Pantau kesihatan sistem
- Bajetkan untuk kos berterusan
- Peruntukkan masa untuk penyelenggaraan

Pastikan anda telah membaca panduan ini sepenuhnya dan memahami implikasinya. Selamat berjaya!
