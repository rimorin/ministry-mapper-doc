# Panduan Setup Frontend

!!! warning "Self-Hosting Tidak Disarankan"
    Panduan ini untuk pengguna tingkat lanjut yang ingin meng-host sendiri Ministry Mapper. Kami tidak menyarankan self-hosting karena kompleksitas teknis, beban pemeliharaan, dan tanggung jawab keamanan yang terlibat.

    **Sebagian besar pengguna sebaiknya**: Menggunakan layanan yang kami host di **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Mencari Dokumentasi Pengguna?"
    Jika Anda pengguna reguler yang ingin mempelajari cara menggunakan Ministry Mapper, lihat [Memulai](getting-started.md) atau [Panduan Pengguna](user-guide.md) sebagai gantinya.

## Ikhtisar

Frontend Ministry Mapper (ministry-mapper-v2) adalah aplikasi web berbasis React yang menyediakan:

- Autentikasi pengguna dan manajemen akun
- Antarmuka manajemen wilayah interaktif
- Fungsionalitas pemetaan interaktif
- Sinkronisasi data real-time
- Desain responsif mobile
- Dukungan multi-bahasa (7 bahasa)
- Kemampuan Progressive Web App

**Panduan ini mengasumsikan Anda memiliki**:
- Pengalaman dengan Node.js dan npm
- Pemahaman tentang variabel lingkungan
- Keakraban dengan deployment situs statis
- Pengetahuan tentang keamanan aplikasi web

Untuk instruksi self-hosting lengkap, lihat [Panduan Self-Hosting](self-hosting.md).

## Referensi Cepat

Halaman ini menyediakan referensi cepat untuk konfigurasi frontend. **Untuk instruksi setup lengkap, lihat [Panduan Self-Hosting](self-hosting.md).**

## Referensi Cepat

Halaman ini menyediakan referensi cepat untuk konfigurasi frontend. **Untuk instruksi setup lengkap, lihat [Panduan Self-Hosting](self-hosting.md).**

### Stack Teknologi

- **React 19**: Library UI modern
- **TypeScript**: JavaScript dengan type-safe
- **Vite 7**: Build tool dan dev server yang cepat
- **Bootstrap 5**: Framework CSS responsif
- **Leaflet**: Pemetaan interaktif
- **PocketBase SDK**: Koneksi backend
- **Sentry**: Pelacakan error
- **i18next**: Dukungan multi-bahasa
- **Vite PWA**: Fitur Progressive Web App

---

!!! note "Instruksi Setup Lengkap"
    Bagian di bawah ini menyediakan referensi teknis untuk konfigurasi frontend. Untuk instruksi setup langkah demi langkah dengan opsi deployment dan pemecahan masalah, silakan lihat **[Panduan Self-Hosting](self-hosting.md)**.

---

## Variabel Lingkungan

Buat file `.env` di direktori root dengan pengaturan berikut:

### Variabel Wajib

```bash
# Lingkungan Sistem - menentukan lingkungan deployment
VITE_SYSTEM_ENVIRONMENT=production

# Versi - secara otomatis menggunakan versi package.json
VITE_VERSION=$npm_package_version

# URL Backend PocketBase - tanpa trailing slash
VITE_POCKETBASE_URL=https://your-backend-url.com

# Halaman Legal - diperlukan untuk produksi
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about

# Peta & Rute
VITE_OPENROUTE_API_KEY=your_openrouteservice_api_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_api_key
```

### Variabel Opsional

```bash
# Pelacakan Error (Sentry) - disarankan untuk produksi
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
# Token Sentry saat build untuk upload source map
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# Mode Pemeliharaan - menampilkan banner pemeliharaan kepada pengguna
VITE_MAINTENANCE_MODE=false
```

### Detail Variabel

#### VITE_SYSTEM_ENVIRONMENT

- **Tujuan**: Menentukan lingkungan di mana aplikasi berjalan
- **Nilai**:
  - `local` - Pengembangan di mesin lokal Anda
  - `production` - Deployment live
- **Efek**: Mempengaruhi konfigurasi pelacakan error Sentry dan logging

#### VITE_VERSION

- **Tujuan**: Melacak versi aplikasi dalam laporan error
- **Nilai**: `$npm_package_version` (otomatis membaca dari package.json)
- **Versi Saat Ini**: 1.9.1 (sesuai package.json)
- **Efek**: Ditampilkan dalam laporan Sentry dan membantu melacak versi mana yang bermasalah

#### VITE_POCKETBASE_URL

- **Tujuan**: URL server backend PocketBase Anda
- **Format**: URL lengkap tanpa trailing slash
- **Contoh**: `https://api.ministry-mapper.com` atau `http://localhost:8090` untuk lokal
- **Penting**: Harus dapat diakses dari mana pun frontend di-deploy

#### VITE_PRIVACY_URL

- **Tujuan**: Tautan ke halaman kebijakan privasi Anda
- **Wajib**: Ya, untuk kepatuhan hukum (GDPR, CCPA, dll.)
- **Contoh**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **Tujuan**: Tautan ke halaman syarat layanan Anda
- **Wajib**: Ya, untuk kepatuhan hukum
- **Contoh**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **Tujuan**: Tautan ke informasi tentang instance Ministry Mapper Anda
- **Wajib**: Tidak, tetapi disarankan
- **Contoh**: `https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **Tujuan**: Mengirim error JavaScript ke Sentry untuk pemantauan
- **Dapatkan Dari**: [sentry.io](https://sentry.io) (tier gratis tersedia)
- **Wajib**: Tidak, tetapi sangat disarankan untuk produksi
- **Manfaat**: Lacak error, pantau kinerja, dapatkan notifikasi masalah
- **Catatan**: Hanya aktif saat VITE_SYSTEM_ENVIRONMENT adalah "production"

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **Tujuan**: Digunakan selama `npm run build` untuk mengunggah source maps ke Sentry sehingga stack trace yang diminifikasi dapat dikembalikan ke kode sumber asli Anda
- **Wajib**: Tidak — source maps dihasilkan secara lokal jika ini tidak ada, tetapi mengunggahnya ke Sentry sangat meningkatkan debugging error di produksi
- **Cara Mendapatkan**: Buat auth token di pengaturan organisasi Sentry Anda di **Settings → Auth Tokens**

#### VITE_OPENROUTE_API_KEY

- **Tujuan**: Mengaktifkan routing turn-by-turn dan petunjuk arah (berkendara, berjalan, bersepeda) menggunakan OpenRouteService pada peta interaktif
- **Dapatkan Dari**: [openrouteservice.org](https://openrouteservice.org) (tier gratis tersedia)
- **Wajib**: Ya

#### VITE_LOCATIONIQ_API_KEY

- **Tujuan**: Geocoding alamat ke koordinat dan reverse geocoding (koordinat menjadi alamat) menggunakan LocationIQ
- **Dapatkan Dari**: [locationiq.com](https://locationiq.com) (tier gratis tersedia)
- **Wajib**: Ya

#### VITE_MAINTENANCE_MODE

- **Tujuan**: Saat diatur ke `true`, menampilkan banner pemeliharaan kepada pengguna
- **Nilai**: `true` atau `false`
- **Default**: `false`
- **Kasus Penggunaan**: Atur ke `true` sementara selama upgrade backend atau downtime terencana

## Setup Pengembangan Lokal

### Prasyarat

Anda memerlukan Node.js **>=24.0.0**:

```bash
# Periksa versi Node.js Anda
node --version
```

Jika Anda tidak memiliki Node.js >=24.0.0, unduh dari: [nodejs.org](https://nodejs.org)

### Langkah-langkah Setup

#### 1. Clone Repository

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. Instal Dependensi

```bash
npm install
```

Ini mengunduh semua paket yang diperlukan. Mungkin memakan waktu beberapa menit tergantung pada koneksi internet Anda.

#### 3. Buat File Lingkungan

```bash
# Salin file lingkungan contoh
cp .env.example .env
```

Edit `.env` dengan pengaturan Anda:

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_OPENROUTE_API_KEY=
VITE_LOCATIONIQ_API_KEY=
VITE_MAINTENANCE_MODE=false
```

**Catatan**: Anda memerlukan backend PocketBase yang berjalan di port 8090. Lihat repositori ministry-mapper-be untuk setup backend.

#### 4. Mulai Server Pengembangan

```bash
npm start
```

Aplikasi akan terbuka di `http://localhost:3000` (port 3000 dikonfigurasi di vite.config.js).

### Fitur Pengembangan

- **Hot Module Replacement (HMR)**: Perubahan muncul langsung tanpa reload halaman penuh
- **Pengecekan TypeScript**: Error ditampilkan di terminal dan konsol browser
- **Source Maps**: Debugging lebih mudah dengan referensi kode sumber asli
- **Fast Refresh**: Komponen React diperbarui tanpa kehilangan state

### Perintah Pengembangan

```bash
# Mulai server pengembangan (port 3000)
npm start

# Jalankan test dengan Vitest
npm test

# Periksa format kode
npm run prettier

# Perbaiki masalah format secara otomatis
npm run prettier:fix

# Build untuk produksi
npm run build

# Preview build produksi secara lokal
npm run serve
```

### Alat Kualitas Kode

**Prettier** (format kode):

- Dikonfigurasi di `.prettierrc`
- Otomatis memformat saat commit melalui Husky
- Jalankan manual dengan `npm run prettier:fix`

**ESLint** (linting kode):

- Dikonfigurasi di `eslint.config.mjs`
- Aturan React dan TypeScript diaktifkan
- Memeriksa error umum dan masalah kualitas kode

**Husky** (git hooks):

- Menjalankan Prettier pada file staged sebelum commit
- Memastikan format kode yang konsisten
- Memvalidasi pesan commit dengan commitlint

## Deployment Produksi

### Build untuk Produksi

```bash
# Build file produksi yang dioptimalkan
npm run build
```

**Apa yang terjadi selama build:**

- TypeScript dikompilasi ke JavaScript
- Kode React dioptimalkan dan diminifikasi
- CSS diproses dan diminifikasi
- Source maps dihasilkan
- Kode dipecah menjadi chunks untuk pemuatan lebih cepat
- Output masuk ke direktori `build/`
- Service worker dihasilkan untuk dukungan PWA

**Output Build:**

- `build/index.html` - File HTML utama
- `build/assets/` - JavaScript, CSS, dan aset lainnya
- Nama file menyertakan hash konten untuk caching
- Ukuran total biasanya di bawah 1MB (tidak termasuk dependensi eksternal)

### Opsi Deployment

Frontend Ministry Mapper adalah aplikasi web statis. Anda dapat men-deploy-nya ke layanan hosting statis mana pun.

#### Opsi 1: Vercel (Disarankan)

Cepat, tier gratis, deployment otomatis.

**Setup:**

1. Buat akun di [vercel.com](https://vercel.com)
2. Klik "New Project"
3. Import repositori GitHub Anda
4. Konfigurasikan:
   - Framework Preset: Vite
   - Build Command: `npm run build`
   - Output Directory: `build`
   - Install Command: `npm install`
5. Tambahkan variabel lingkungan di dashboard Vercel
6. Deploy

**Fitur Vercel:**

- Deployment otomatis saat git push
- Deployment preview untuk pull request
- Domain kustom
- HTTPS secara default
- CDN global
- Gratis untuk proyek personal

#### Opsi 2: Netlify

Deployment sederhana dengan fitur hebat.

**Setup:**

1. Buat akun di [netlify.com](https://netlify.com)
2. "Add new site" → "Import an existing project"
3. Hubungkan repositori GitHub
4. Pengaturan build:
   - Build Command: `npm run build`
   - Publish Directory: `build`
5. Tambahkan variabel lingkungan
6. Deploy

**Fitur Netlify:**

- Deployment otomatis
- Penanganan form
- Fungsi serverless (jika diperlukan)
- Domain kustom
- HTTPS secara default

#### Opsi 3: Cloudflare Pages

CDN cepat dengan tier gratis yang murah hati.

**Setup:**

1. Buat akun di [cloudflare.com](https://cloudflare.com)
2. Pergi ke Pages → "Create a project"
3. Hubungkan repositori GitHub
4. Pengaturan build:
   - Framework preset: Vite
   - Build command: `npm run build`
   - Build output directory: `build`
5. Tambahkan variabel lingkungan
6. Deploy

**Fitur Cloudflare:**

- Jaringan CDN global
- Perlindungan DDoS
- Analitik
- Web Analytics tanpa pelacakan client-side

#### Opsi 4: AWS S3 + CloudFront

Lebih banyak kontrol, bagus untuk deployment besar.

**Setup:**

1. Buat bucket S3
2. Aktifkan hosting website statis
3. Upload konten `build/`
4. Buat distribusi CloudFront
5. Konfigurasikan domain kustom
6. Siapkan sertifikat SSL

**Pertimbangan AWS:**

- Setup lebih kompleks
- Bayar per penggunaan (biasanya sangat murah)
- Lebih banyak opsi konfigurasi
- Bagus untuk deployment enterprise

#### Opsi 5: Self-Hosted

Host di server Anda sendiri.

**Menggunakan Nginx:**

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Aktifkan kompresi gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Cache aset statis
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**Menggunakan Apache:**

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # Aktifkan React Router
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**Penting untuk Self-Hosting:**

- Sajikan melalui HTTPS (diperlukan untuk fitur PWA)
- Konfigurasikan header CORS yang tepat jika backend di domain berbeda
- Atur header caching yang tepat untuk aset statis
- Pastikan routing SPA berfungsi (semua rute menyajikan index.html)

### Variabel Lingkungan dalam Deployment

**Penting**: Variabel lingkungan harus diatur pada waktu build, bukan runtime, karena Vite menyuntikkannya pada waktu build.

**Untuk Vercel/Netlify/Cloudflare:**

- Tambahkan variabel lingkungan di dashboard platform
- Mereka akan tersedia selama proses build

**Untuk Self-Hosted:**

- Atur variabel lingkungan sebelum menjalankan `npm run build`
- Atau buat file `.env.production` dengan pengaturan Anda

## Menyiapkan Sentry (Opsional tetapi Disarankan)

Sentry memantau error JavaScript dan kinerja.

### 1. Buat Akun Sentry

1. Pergi ke [sentry.io](https://sentry.io)
2. Daftar (tier gratis yang murah hati tersedia)
3. Buat proyek baru
4. Pilih platform: "React"

### 2. Dapatkan DSN Anda

1. Setelah membuat proyek, Anda akan melihat DSN
2. Atau pergi ke Settings → Projects → [Proyek Anda] → Client Keys (DSN)
3. Salin URL DSN

### 3. Tambahkan ke Lingkungan

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**Catatan**: Sentry hanya aktif saat `VITE_SYSTEM_ENVIRONMENT` diatur ke `production`.

### 4. Konfigurasikan Sentry (Opsional)

**Source Maps**: Proses build secara otomatis menghasilkan source maps, yang membantu men-debug kode produksi yang diminifikasi.

**Peringatan**: Siapkan di dashboard Sentry:

- Notifikasi email untuk masalah baru
- Integrasi Slack/Discord
- Aturan peringatan kustom untuk error kritis
- Pelacakan rilis

## Dukungan Multi-Bahasa

Ministry Mapper menyertakan 7 bahasa secara langsung.

### Bahasa yang Didukung

- Inggris (`en`)
- Spanyol (`es` - Español)
- Jepang (`ja` - 日本語)
- Korea (`ko` - 한국어)
- Tionghoa (`zh` - 中文)
- Indonesia (`id` - Bahasa Indonesia)
- Melayu (`ms` - Bahasa Melayu)

### Cara Kerja Deteksi Bahasa

- Menggunakan `i18next-browser-languagedetector`
- Otomatis mendeteksi dari pengaturan browser pada kunjungan pertama
- Pengguna dapat secara manual mengganti bahasa melalui pemilih bahasa di bilah navigasi
- Pilihan disimpan di `localStorage` dan diprioritaskan di atas pengaturan browser
- Fallback ke Inggris jika bahasa yang dipilih tidak didukung

### Menambahkan Bahasa Baru

1. **Buat File Terjemahan**

   - Pergi ke `src/i18n/locales/`
   - Buat file baru: `[kode-bahasa].json` (mis. `fr.json` untuk Prancis)
   - Salin struktur dari `en.json`

2. **Terjemahkan Semua String**

   - Terjemahkan semua pasangan key-value
   - Pertahankan struktur JSON yang sama
   - Pertahankan variabel placeholder seperti `{{variable}}`

3. **Daftarkan Bahasa**
   - Edit `src/i18n/index.ts`
   - Import file terjemahan Anda
   - Tambahkan ke objek resources

Contoh untuk Prancis:

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... bahasa lainnya
}
```

## Progressive Web App (PWA)

Ministry Mapper menyertakan dukungan PWA melalui plugin Vite PWA.

### Fitur PWA

- **Dapat Diinstal**: Dapat diinstal di layar beranda mobile/desktop
- **Aset Offline**: File statis di-cache untuk pemuatan lebih cepat
- **Service Worker**: Dihasilkan dan didaftarkan secara otomatis
- **Strategi Cache**: StaleWhileRevalidate untuk aset eksternal, CacheFirst untuk font

### Konfigurasi PWA

Konfigurasi di `vite.config.js`:

```javascript
VitePWA({
  registerType: "autoUpdate", // Auto-update service worker
  manifest: false, // Tidak ada manifest (dapat ditambahkan jika diperlukan)
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // Aktifkan service worker baru segera
    clientsClaim: true, // Ambil alih klien segera
    cleanupOutdatedCaches: true, // Hapus cache lama
  },
});
```

### Strategi Caching

**Aset Eksternal** (mis. dari assets.ministry-mapper.com):

- Strategi: StaleWhileRevalidate
- Nama cache: "external-assets"
- Kedaluwarsa: 7 hari, maks 50 entri

**Font**:

- Strategi: CacheFirst
- Nama cache: "fonts"
- Kedaluwarsa: 30 hari, maks 30 entri

**Catatan**: Aplikasi memerlukan internet untuk operasi data (koneksi PocketBase). Hanya aset statis yang di-cache offline.

## Menguji Deployment Anda

### Daftar Periksa Fungsionalitas

- [ ] Halaman beranda dimuat dengan benar
- [ ] Halaman pendaftaran berfungsi
- [ ] Verifikasi email berfungsi
- [ ] Halaman login berfungsi
- [ ] Autentikasi OTP berfungsi (jika diaktifkan)
- [ ] Reset kata sandi berfungsi
- [ ] Pemilih wilayah ditampilkan (untuk konduktor/admin)
- [ ] Peta dimuat dan ditampilkan dengan benar
- [ ] Dapat melihat wilayah
- [ ] Dapat memperbarui status alamat
- [ ] Tautan penugasan berfungsi
- [ ] Tautan kedaluwarsa dengan benar
- [ ] Tampilan mobile responsif
- [ ] Deteksi bahasa berfungsi
- [ ] Semua modal terbuka dan tertutup dengan benar

### Pemeriksaan Kinerja

- [ ] Pemuatan halaman awal di bawah 3 detik
- [ ] Skor Lighthouse > 90
- [ ] Tidak ada error konsol
- [ ] Gambar dimuat dengan cepat
- [ ] Peta responsif
- [ ] Scrolling dan navigasi lancar

### Pemeriksaan Keamanan

- [ ] HTTPS ditegakkan (tidak ada akses HTTP)
- [ ] Tautan kebijakan privasi berfungsi
- [ ] Tautan syarat layanan berfungsi
- [ ] Kunci API tidak terekspos dalam kode browser
- [ ] CORS dikonfigurasi dengan benar untuk backend
- [ ] Koneksi PocketBase aman

### Pengujian Browser

Uji pada beberapa browser:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] Browser mobile

## Pemecahan Masalah

### Koneksi Backend Gagal

**Masalah**: "Cannot connect to server" atau "Network Error"

**Solusi:**

- Verifikasi `VITE_POCKETBASE_URL` sudah benar (tanpa trailing slash)
- Periksa apakah backend PocketBase berjalan
- Uji URL backend langsung di browser
- Periksa pengaturan CORS di PocketBase (harus mengizinkan domain frontend Anda)
- Buka DevTools browser → tab Network untuk melihat error yang tepat
- Pastikan backend dapat diakses dari lokasi deployment Anda

### Error Build

**Masalah**: `npm run build` gagal

**Solusi:**

- Pastikan semua variabel lingkungan diatur dengan benar
- Jalankan `npm install` untuk menyegarkan dependensi
- Hapus `node_modules` dan instal ulang: `rm -rf node_modules && npm install`
- Periksa versi Node.js: `node --version` (perlu 24+)
- Periksa error TypeScript: Lihat pesan error spesifik
- Coba `npm run prettier:fix` untuk memperbaiki masalah format
- Bersihkan cache Vite: `rm -rf node_modules/.vite`

### Error TypeScript

**Masalah**: Error type selama build

**Solusi:**

- Periksa `src/utils/interface.ts` untuk definisi type
- Pastikan import sudah benar
- Jalankan `npx tsc --noEmit` untuk melihat semua error type
- Verifikasi semua dependensi terinstal

### Kinerja Lambat

**Masalah**: Aplikasi lambat atau laggy

**Solusi:**

- Periksa kecepatan koneksi internet
- Bersihkan cache browser dan reload
- Pastikan backend PocketBase merespons dengan cepat
- Periksa konsol browser untuk error JavaScript
- Uji di perangkat/browser berbeda untuk mengisolasi masalah
- Periksa skor kinerja Lighthouse
- Verifikasi service worker berfungsi: DevTools → Application → Service Workers

### Masalah Instalasi PWA

**Masalah**: "Add to Home Screen" tidak muncul

**Solusi:**

- Harus disajikan melalui HTTPS
- Periksa registrasi service worker di DevTools
- Verifikasi persyaratan PWA terpenuhi (manifest, service worker, dll.)
- Coba browser berbeda (Safari, Chrome memiliki dukungan PWA yang berbeda)

### Pembaruan Real-time Tidak Berfungsi

**Masalah**: Perubahan tidak muncul untuk pengguna lain

**Solusi:**

- Verifikasi langganan realtime PocketBase berfungsi
- Periksa konsol browser untuk error SSE (Server-Sent Events)
- Pastikan endpoint SSE backend dapat diakses
- Periksa apakah beberapa pengguna benar-benar melihat wilayah yang sama
- Segarkan halaman untuk memaksa koneksi ulang

## Pemeliharaan

### Menjaga Dependensi Tetap Terbaru

```bash
# Pull perubahan terbaru dari repositori
git pull origin main

# Instal dependensi yang diperbarui
npm install

# Periksa kerentanan keamanan
npm audit

# Perbaiki masalah keamanan secara otomatis (jika memungkinkan)
npm audit fix

# Periksa paket yang sudah usang
npm outdated

# Update paket tertentu
npm update package-name
```

### Pemantauan

**Dashboard Sentry:**

- Periksa mingguan untuk error baru
- Tinjau tren error
- Perbaiki masalah kritis dengan cepat
- Siapkan peringatan untuk error prioritas tinggi

**Google Cloud Console:**

- Pantau penggunaan API agar tetap dalam kuota
- Periksa lonjakan penggunaan yang tidak biasa
- Tinjau peringatan penagihan
- Pastikan API tetap diaktifkan

**Kinerja:**

- Jalankan audit Lighthouse secara berkala
- Pantau waktu muat halaman
- Periksa Core Web Vitals
- Uji pada koneksi lambat

**Umpan Balik Pengguna:**

- Dengarkan anggota jemaat
- Lacak masalah umum
- Dokumentasikan solusi
- Perbarui dokumentasi sesuai kebutuhan

### Pembaruan Versi

Ministry Mapper menggunakan semantic versioning:

- **Versi major** (x.0.0): Perubahan breaking
- **Versi minor** (1.x.0): Fitur baru
- **Versi patch** (1.9.x): Perbaikan bug

## Langkah Selanjutnya

**Untuk Pengguna:**

- Baca [Panduan Pengguna](user-guide.md) untuk mempelajari cara menggunakan aplikasi

**Untuk Administrator:**

- Siapkan backend PocketBase (lihat repositori ministry-mapper-be)
- Konfigurasikan pengaturan jemaat
- Undang pengguna dan tetapkan peran
- Buat wilayah dan tambahkan alamat
