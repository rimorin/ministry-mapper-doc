# Panduan Persediaan Frontend

!!! warning "Hos Sendiri Tidak Disyorkan"
    Panduan ini adalah untuk pengguna lanjutan yang ingin mengehoskan sendiri Ministry Mapper. Kami tidak menggalakkan hos sendiri kerana kerumitan teknikal, beban penyelenggaraan, dan tanggungjawab keselamatan yang terlibat.

    **Kebanyakan pengguna sepatutnya**: Gunakan perkhidmatan dihoskan kami di **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Mencari Dokumentasi Pengguna?"
    Jika anda pengguna biasa yang cuba mempelajari cara menggunakan Ministry Mapper, lihat [Bermula](getting-started.md) atau [Panduan Pengguna](user-guide.md) sebaliknya.

## Gambaran Keseluruhan

Frontend Ministry Mapper (ministry-mapper-v2) adalah aplikasi web berasaskan React yang menyediakan:

- Pengesahan pengguna dan pengurusan akaun
- Antara muka pengurusan wilayah interaktif
- Fungsi pemetaan interaktif
- Penyegerakan data masa nyata
- Reka bentuk responsif mudah alih
- Sokongan berbilang bahasa (7 bahasa)
- Keupayaan Aplikasi Web Progresif

**Panduan ini mengandaikan anda mempunyai**:
- Pengalaman dengan Node.js dan npm
- Pemahaman tentang pemboleh ubah persekitaran
- Kebiasaan dengan penempatan laman statik
- Pengetahuan tentang keselamatan aplikasi web

Untuk arahan hos sendiri lengkap, lihat [Panduan Hos Sendiri](self-hosting.md).

## Rujukan Pantas

Halaman ini menyediakan rujukan pantas untuk konfigurasi frontend. **Untuk arahan persediaan lengkap, lihat [Panduan Hos Sendiri](self-hosting.md).**

## Rujukan Pantas

Halaman ini menyediakan rujukan pantas untuk konfigurasi frontend. **Untuk arahan persediaan lengkap, lihat [Panduan Hos Sendiri](self-hosting.md).**

### Tindanan Teknologi

- **React 19**: Pustaka UI moden
- **TypeScript**: JavaScript selamat jenis
- **Vite 7**: Alat bina dan pelayan dev pantas
- **Bootstrap 5**: Rangka kerja CSS responsif
- **Leaflet**: Pemetaan interaktif
- **PocketBase SDK**: Sambungan backend
- **Sentry**: Penjejakan ralat
- **i18next**: Sokongan berbilang bahasa
- **Vite PWA**: Ciri Aplikasi Web Progresif

---

!!! note "Arahan Persediaan Lengkap"
    Bahagian di bawah menyediakan rujukan teknikal untuk konfigurasi frontend. Untuk arahan persediaan langkah demi langkah dengan pilihan penempatan dan penyelesaian masalah, sila lihat **[Panduan Hos Sendiri](self-hosting.md)**.

---

## Pemboleh Ubah Persekitaran

Cipta fail `.env` dalam direktori akar dengan tetapan ini:

### Pemboleh Ubah Diperlukan

```bash
# Persekitaran Sistem - menentukan persekitaran penempatan
VITE_SYSTEM_ENVIRONMENT=production

# Versi - secara automatik menggunakan versi package.json
VITE_VERSION=$npm_package_version

# URL Backend PocketBase - tiada garis miring di hujung
VITE_POCKETBASE_URL=https://your-backend-url.com

# Halaman Undang-undang - diperlukan untuk pengeluaran
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

### Pemboleh Ubah Pilihan

```bash
# Penjejakan Ralat (Sentry) - disyorkan untuk pengeluaran
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
# Token Sentry masa bina untuk muat naik peta sumber
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# Penghalaan & Geokod
VITE_OPENROUTE_API_KEY=your_openrouteservice_api_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_api_key

# Mod Penyelenggaraan - tunjukkan sepanduk penyelenggaraan kepada pengguna
VITE_MAINTENANCE_MODE=false
```

### Butiran Pemboleh Ubah

#### VITE_SYSTEM_ENVIRONMENT

- **Tujuan**: Menentukan persekitaran di mana aplikasi berjalan
- **Nilai**:
  - `local` - Pembangunan pada mesin tempatan anda
  - `production` - Penempatan langsung
- **Kesan**: Mempengaruhi konfigurasi penjejakan ralat Sentry dan pengelogan

#### VITE_VERSION

- **Tujuan**: Menjejak versi aplikasi dalam laporan ralat
- **Nilai**: `$npm_package_version` (secara automatik membaca dari package.json)
- **Versi Semasa**: 1.9.1 (mengikut package.json)
- **Kesan**: Ditunjukkan dalam laporan Sentry dan membantu menjejak versi mana yang mempunyai masalah

#### VITE_POCKETBASE_URL

- **Tujuan**: URL pelayan backend PocketBase anda
- **Format**: URL penuh tanpa garis miring di hujung
- **Contoh**: `https://api.ministry-mapper.com` atau `http://localhost:8090` untuk tempatan
- **Penting**: Mesti boleh diakses dari mana sahaja frontend ditempatkan

#### VITE_PRIVACY_URL

- **Tujuan**: Pautan ke halaman dasar privasi anda
- **Diperlukan**: Ya, untuk pematuhan undang-undang (GDPR, CCPA, dll.)
- **Contoh**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **Tujuan**: Pautan ke halaman terma perkhidmatan anda
- **Diperlukan**: Ya, untuk pematuhan undang-undang
- **Contoh**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **Tujuan**: Pautan ke maklumat tentang contoh Ministry Mapper anda
- **Diperlukan**: Tidak, tetapi disyorkan
- **Contoh**: `https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **Tujuan**: Menghantar ralat JavaScript ke Sentry untuk pemantauan
- **Dapatkan Dari**: [sentry.io](https://sentry.io) (peringkat percuma tersedia)
- **Diperlukan**: Tidak, tetapi sangat disyorkan untuk pengeluaran
- **Faedah**: Jejak ralat, pantau prestasi, dapatkan pemberitahuan masalah
- **Nota**: Hanya aktif apabila VITE_SYSTEM_ENVIRONMENT adalah "production"

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **Tujuan**: Digunakan semasa `npm run build` untuk memuat naik peta sumber ke Sentry supaya jejak tindanan yang dikecilkan boleh diselesaikan kembali ke kod sumber asal anda
- **Diperlukan**: Tidak — peta sumber dijana secara tempatan jika ini tiada, tetapi memuat naiknya ke Sentry meningkatkan penyahpepijatan ralat dalam pengeluaran dengan ketara
- **Cara Mendapatkan**: Cipta token pengesahan dalam tetapan organisasi Sentry anda di bawah **Settings → Auth Tokens**

#### VITE_OPENROUTE_API_KEY

- **Tujuan**: Menggerakkan penghalaan belokan demi belokan dan arah pada peta interaktif
- **Dapatkan Dari**: [openrouteservice.org](https://openrouteservice.org) (peringkat percuma tersedia)
- **Diperlukan**: Tidak — navigasi peta berfungsi tanpanya, tetapi arah tidak akan tersedia

#### VITE_LOCATIONIQ_API_KEY

- **Tujuan**: Geokod (tukar alamat kepada koordinat) dan geokod terbalik (koordinat kepada alamat)
- **Dapatkan Dari**: [locationiq.com](https://locationiq.com) (peringkat percuma tersedia)
- **Diperlukan**: Tidak — aplikasi berfungsi tanpanya, tetapi carian alamat dan isi automatik tidak akan tersedia

#### VITE_MAINTENANCE_MODE

- **Tujuan**: Apabila ditetapkan kepada `true`, menunjukkan sepanduk penyelenggaraan kepada pengguna
- **Nilai**: `true` atau `false`
- **Lalai**: `false`
- **Kes Penggunaan**: Tetapkan kepada `true` sementara semasa naik taraf backend atau masa henti yang dirancang

## Persediaan Pembangunan Tempatan

### Prasyarat

Anda memerlukan Node.js versi 24 atau lebih tinggi:

```bash
# Semak versi Node.js anda
node --version
```

Jika anda tidak mempunyai Node.js 24+, muat turun dari: [nodejs.org](https://nodejs.org)

### Langkah Persediaan

#### 1. Klon Repositori

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. Pasang Kebergantungan

```bash
npm install
```

Ini memuat turun semua pakej yang diperlukan. Mungkin mengambil beberapa minit bergantung pada sambungan internet anda.

#### 3. Cipta Fail Persekitaran

```bash
# Salin fail persekitaran contoh
cp .env.example .env
```

Edit `.env` dengan tetapan anda:

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

**Nota**: Anda memerlukan backend PocketBase berjalan pada port 8090. Lihat repositori ministry-mapper-be untuk persediaan backend.

#### 4. Mulakan Pelayan Pembangunan

```bash
npm start
```

Aplikasi akan dibuka di `http://localhost:3000` (port 3000 dikonfigurasi dalam vite.config.js).

### Ciri Pembangunan

- **Hot Module Replacement (HMR)**: Perubahan muncul serta-merta tanpa muat semula halaman penuh
- **Semakan TypeScript**: Ralat ditunjukkan dalam terminal dan konsol pelayar
- **Peta Sumber**: Penyahpepijatan lebih mudah dengan rujukan kod sumber asal
- **Fast Refresh**: Komponen React dikemas kini tanpa kehilangan keadaan

### Arahan Pembangunan

```bash
# Mulakan pelayan pembangunan (port 3000)
npm start

# Jalankan ujian dengan Vitest
npm test

# Semak pemformatan kod
npm run prettier

# Betulkan masalah pemformatan secara automatik
npm run prettier:fix

# Bina untuk pengeluaran
npm run build

# Pratonton binaan pengeluaran secara tempatan
npm run serve
```

### Alat Kualiti Kod

**Prettier** (pemformatan kod):

- Dikonfigurasi dalam `.prettierrc`
- Memformat secara automatik pada komit melalui Husky
- Jalankan secara manual dengan `npm run prettier:fix`

**ESLint** (linting kod):

- Dikonfigurasi dalam `eslint.config.mjs`
- Peraturan React dan TypeScript diaktifkan
- Menyemak ralat biasa dan isu kualiti kod

**Husky** (cangkuk git):

- Menjalankan Prettier pada fail yang diletakkan sebelum komit
- Memastikan pemformatan kod yang konsisten
- Mengesahkan mesej komit dengan commitlint

## Penempatan Pengeluaran

### Membina untuk Pengeluaran

```bash
# Bina fail pengeluaran yang dioptimumkan
npm run build
```

**Apa yang berlaku semasa bina:**

- TypeScript dikompil kepada JavaScript
- Kod React dioptimumkan dan dikecilkan
- CSS diproses dan dikecilkan
- Peta sumber dijana
- Kod dipecahkan kepada ketulan untuk pemuatan lebih pantas
- Output pergi ke direktori `build/`
- Service worker dijana untuk sokongan PWA

**Output Binaan:**

- `build/index.html` - Fail HTML utama
- `build/assets/` - JavaScript, CSS, dan aset lain
- Nama fail termasuk cincangan kandungan untuk caching
- Jumlah saiz biasanya di bawah 1MB (tidak termasuk kebergantungan luaran)

### Pilihan Penempatan

Frontend Ministry Mapper adalah aplikasi web statik. Anda boleh menempatkannya ke mana-mana perkhidmatan pengehosan statik.

#### Pilihan 1: Vercel (Disyorkan)

Pantas, peringkat percuma, penempatan automatik.

**Persediaan:**

1. Cipta akaun di [vercel.com](https://vercel.com)
2. Klik "New Project"
3. Import repositori GitHub anda
4. Konfigurasi:
   - Framework Preset: Vite
   - Build Command: `npm run build`
   - Output Directory: `build`
   - Install Command: `npm install`
5. Tambah pemboleh ubah persekitaran dalam papan pemuka Vercel
6. Tempatkan

**Ciri Vercel:**

- Penempatan automatik pada git push
- Penempatan pratonton untuk permintaan tarik
- Domain tersuai
- HTTPS secara lalai
- CDN global
- Percuma untuk projek peribadi

#### Pilihan 2: Netlify

Penempatan mudah dengan ciri hebat.

**Persediaan:**

1. Cipta akaun di [netlify.com](https://netlify.com)
2. "Add new site" → "Import an existing project"
3. Sambungkan repositori GitHub
4. Tetapan binaan:
   - Build Command: `npm run build`
   - Publish Directory: `build`
5. Tambah pemboleh ubah persekitaran
6. Tempatkan

**Ciri Netlify:**

- Penempatan automatik
- Pengendalian borang
- Fungsi tanpa pelayan (jika diperlukan)
- Domain tersuai
- HTTPS secara lalai

#### Pilihan 3: Cloudflare Pages

CDN pantas dengan peringkat percuma murah hati.

**Persediaan:**

1. Cipta akaun di [cloudflare.com](https://cloudflare.com)
2. Pergi ke Pages → "Create a project"
3. Sambungkan repositori GitHub
4. Tetapan binaan:
   - Framework preset: Vite
   - Build command: `npm run build`
   - Build output directory: `build`
5. Tambah pemboleh ubah persekitaran
6. Tempatkan

**Ciri Cloudflare:**

- Rangkaian CDN global
- Perlindungan DDoS
- Analitik
- Web Analytics tanpa penjejakan sisi pelanggan

#### Pilihan 4: AWS S3 + CloudFront

Lebih kawalan, baik untuk penempatan besar.

**Persediaan:**

1. Cipta baldi S3
2. Aktifkan pengehosan laman web statik
3. Muat naik kandungan `build/`
4. Cipta pengedaran CloudFront
5. Konfigurasi domain tersuai
6. Sediakan sijil SSL

**Pertimbangan AWS:**

- Persediaan lebih kompleks
- Bayar mengikut penggunaan (biasanya sangat murah)
- Lebih pilihan konfigurasi
- Baik untuk penempatan perusahaan

#### Pilihan 5: Hos Sendiri

Hos pada pelayan anda sendiri.

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

    # Aktifkan pemampatan gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Cache aset statik
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

**Penting untuk Hos Sendiri:**

- Hidangkan melalui HTTPS (diperlukan untuk ciri PWA)
- Konfigurasi pengepala CORS yang betul jika backend pada domain berbeza
- Sediakan pengepala caching yang betul untuk aset statik
- Pastikan penghalaan SPA berfungsi (semua laluan menghidangkan index.html)

### Pemboleh Ubah Persekitaran dalam Penempatan

**Penting**: Pemboleh ubah persekitaran mesti ditetapkan semasa masa bina, bukan masa jalan, kerana Vite menyuntiknya pada masa bina.

**Untuk Vercel/Netlify/Cloudflare:**

- Tambah pemboleh ubah persekitaran dalam papan pemuka platform
- Ia akan tersedia semasa proses binaan

**Untuk Hos Sendiri:**

- Tetapkan pemboleh ubah persekitaran sebelum menjalankan `npm run build`
- Atau cipta fail `.env.production` dengan tetapan anda

## Menyediakan Sentry (Pilihan tetapi Disyorkan)

Sentry memantau ralat dan prestasi JavaScript.

### 1. Cipta Akaun Sentry

1. Pergi ke [sentry.io](https://sentry.io)
2. Daftar (peringkat percuma murah hati tersedia)
3. Cipta projek baharu
4. Pilih platform: "React"

### 2. Dapatkan DSN Anda

1. Selepas mencipta projek, anda akan melihat DSN
2. Atau pergi ke Settings → Projects → [Projek Anda] → Client Keys (DSN)
3. Salin URL DSN

### 3. Tambah ke Persekitaran

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**Nota**: Sentry hanya aktif apabila `VITE_SYSTEM_ENVIRONMENT` ditetapkan kepada `production`.

### 4. Konfigurasi Sentry (Pilihan)

**Peta Sumber**: Proses binaan secara automatik menjana peta sumber, yang membantu menyahpepijat kod pengeluaran yang dikecilkan.

**Amaran**: Sediakan dalam papan pemuka Sentry:

- Pemberitahuan e-mel untuk isu baharu
- Integrasi Slack/Discord
- Peraturan amaran tersuai untuk ralat kritikal
- Penjejakan keluaran

## Sokongan Berbilang Bahasa

Ministry Mapper termasuk 7 bahasa secara lalai.

### Bahasa Disokong

- English (`en`)
- Spanish (`es` - Español)
- Japanese (`ja` - 日本語)
- Korean (`ko` - 한국어)
- Chinese (`zh` - 中文)
- Indonesian (`id` - Bahasa Indonesia)
- Malay (`ms` - Bahasa Melayu)

### Cara Pengesanan Bahasa Berfungsi

- Menggunakan `i18next-browser-languagedetector`
- Mengesan secara automatik dari tetapan pelayar pada lawatan pertama
- Pengguna boleh menukar bahasa secara manual melalui pemilih bahasa dalam bar navigasi
- Pilihan disimpan dalam `localStorage` dan mengambil keutamaan berbanding tetapan pelayar
- Kembali ke Bahasa Inggeris jika bahasa yang dipilih tidak disokong

### Menambah Bahasa Baharu

1. **Cipta Fail Terjemahan**

   - Pergi ke `src/i18n/locales/`
   - Cipta fail baharu: `[kod-bahasa].json` (cth., `fr.json` untuk Perancis)
   - Salin struktur dari `en.json`

2. **Terjemahkan Semua Rentetan**

   - Terjemahkan semua pasangan kunci-nilai
   - Kekalkan struktur JSON yang sama
   - Kekalkan pemboleh ubah pemegang tempat seperti `{{variable}}`

3. **Daftarkan Bahasa**
   - Edit `src/i18n/index.ts`
   - Import fail terjemahan anda
   - Tambah ke objek resources

Contoh untuk Perancis:

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... bahasa lain
}
```

## Aplikasi Web Progresif (PWA)

Ministry Mapper termasuk sokongan PWA melalui pemalam Vite PWA.

### Ciri PWA

- **Boleh Dipasang**: Boleh dipasang pada skrin utama mudah alih/desktop
- **Aset Luar Talian**: Fail statik dicache untuk pemuatan lebih pantas
- **Service Worker**: Dijana dan didaftarkan secara automatik
- **Strategi Cache**: StaleWhileRevalidate untuk aset luaran, CacheFirst untuk fon

### Konfigurasi PWA

Konfigurasi dalam `vite.config.js`:

```javascript
VitePWA({
  registerType: "autoUpdate", // Kemas kini service worker secara automatik
  manifest: false, // Tiada manifest (boleh ditambah jika diperlukan)
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // Aktifkan service worker baharu serta-merta
    clientsClaim: true, // Ambil kawalan pelanggan serta-merta
    cleanupOutdatedCaches: true, // Buang cache lama
  },
});
```

### Strategi Caching

**Aset Luaran** (cth., dari assets.ministry-mapper.com):

- Strategi: StaleWhileRevalidate
- Nama cache: "external-assets"
- Tamat tempoh: 7 hari, maksimum 50 entri

**Fon**:

- Strategi: CacheFirst
- Nama cache: "fonts"
- Tamat tempoh: 30 hari, maksimum 30 entri

**Nota**: Aplikasi memerlukan internet untuk operasi data (sambungan PocketBase). Hanya aset statik yang dicache luar talian.

## Menguji Penempatan Anda

### Senarai Semak Fungsi

- [ ] Halaman utama dimuatkan dengan betul
- [ ] Halaman pendaftaran berfungsi
- [ ] Pengesahan e-mel berfungsi
- [ ] Halaman log masuk berfungsi
- [ ] Pengesahan OTP berfungsi (jika diaktifkan)
- [ ] Tetapan semula kata laluan berfungsi
- [ ] Pemilih wilayah dipaparkan (untuk konduktor/pentadbir)
- [ ] Peta dimuatkan dan dipaparkan dengan betul
- [ ] Boleh melihat wilayah
- [ ] Boleh mengemas kini status alamat
- [ ] Pautan tugasan berfungsi
- [ ] Pautan tamat tempoh dengan betul
- [ ] Paparan mudah alih responsif
- [ ] Pengesanan bahasa berfungsi
- [ ] Semua modal dibuka dan ditutup dengan betul

### Semakan Prestasi

- [ ] Muat halaman awal di bawah 3 saat
- [ ] Skor Lighthouse > 90
- [ ] Tiada ralat konsol
- [ ] Imej dimuatkan dengan pantas
- [ ] Peta responsif
- [ ] Menatal dan navigasi lancar

### Semakan Keselamatan

- [ ] HTTPS dikuatkuasakan (tiada akses HTTP)
- [ ] Pautan dasar privasi berfungsi
- [ ] Pautan terma perkhidmatan berfungsi
- [ ] Kunci API tidak terdedah dalam kod pelayar
- [ ] CORS dikonfigurasi dengan betul untuk backend
- [ ] Sambungan PocketBase selamat

### Ujian Pelayar

Uji pada berbilang pelayar:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] Pelayar mudah alih

## Penyelesaian Masalah

### Sambungan Backend Gagal

**Masalah**: "Cannot connect to server" atau "Network Error"

**Penyelesaian:**

- Sahkan `VITE_POCKETBASE_URL` betul (tiada garis miring di hujung)
- Semak jika backend PocketBase berjalan
- Uji URL backend secara langsung dalam pelayar
- Semak tetapan CORS dalam PocketBase (mesti membenarkan domain frontend anda)
- Buka DevTools pelayar → tab Network untuk melihat ralat tepat
- Pastikan backend boleh diakses dari lokasi penempatan anda

### Ralat Binaan

**Masalah**: `npm run build` gagal

**Penyelesaian:**

- Pastikan semua pemboleh ubah persekitaran ditetapkan dengan betul
- Jalankan `npm install` untuk menyegar semula kebergantungan
- Padam `node_modules` dan pasang semula: `rm -rf node_modules && npm install`
- Semak versi Node.js: `node --version` (memerlukan 24+)
- Semak ralat TypeScript: Lihat mesej ralat tertentu
- Cuba `npm run prettier:fix` untuk membetulkan masalah pemformatan
- Kosongkan cache Vite: `rm -rf node_modules/.vite`

### Ralat TypeScript

**Masalah**: Ralat jenis semasa binaan

**Penyelesaian:**

- Semak `src/utils/interface.ts` untuk definisi jenis
- Pastikan import betul
- Jalankan `npx tsc --noEmit` untuk melihat semua ralat jenis
- Sahkan semua kebergantungan dipasang

### Prestasi Perlahan

**Masalah**: Aplikasi perlahan atau lag

**Penyelesaian:**

- Semak kelajuan sambungan internet
- Kosongkan cache pelayar dan muat semula
- Pastikan backend PocketBase bertindak balas dengan pantas
- Semak konsol pelayar untuk ralat JavaScript
- Uji pada peranti/pelayar berbeza untuk mengasingkan masalah
- Semak skor prestasi Lighthouse
- Sahkan service worker berfungsi: DevTools → Application → Service Workers

### Masalah Pemasangan PWA

**Masalah**: "Add to Home Screen" tidak muncul

**Penyelesaian:**

- Mesti dihidangkan melalui HTTPS
- Semak pendaftaran service worker dalam DevTools
- Sahkan keperluan PWA dipenuhi (manifest, service worker, dll.)
- Cuba pelayar berbeza (Safari, Chrome mempunyai sokongan PWA berbeza)

### Kemas Kini Masa Nyata Tidak Berfungsi

**Masalah**: Perubahan tidak muncul untuk pengguna lain

**Penyelesaian:**

- Sahkan langganan masa nyata PocketBase berfungsi
- Semak konsol pelayar untuk ralat SSE (Server-Sent Events)
- Pastikan titik akhir SSE backend boleh diakses
- Semak jika berbilang pengguna sebenarnya melihat wilayah yang sama
- Segar semula halaman untuk memaksa penyambungan semula

## Penyelenggaraan

### Mengekalkan Kebergantungan Dikemas Kini

```bash
# Tarik perubahan terkini dari repositori
git pull origin main

# Pasang kebergantungan yang dikemas kini
npm install

# Semak kerentanan keselamatan
npm audit

# Betulkan isu keselamatan secara automatik (apabila mungkin)
npm audit fix

# Semak pakej yang lapuk
npm outdated

# Kemas kini pakej tertentu
npm update package-name
```

### Pemantauan

**Papan Pemuka Sentry:**

- Semak mingguan untuk ralat baharu
- Semak trend ralat
- Betulkan isu kritikal dengan segera
- Sediakan amaran untuk ralat keutamaan tinggi

**Google Cloud Console:**

- Pantau penggunaan API untuk kekal dalam kuota
- Semak lonjakan luar biasa dalam penggunaan
- Semak amaran pengebilan
- Pastikan API kekal diaktifkan

**Prestasi:**

- Jalankan audit Lighthouse secara berkala
- Pantau masa muat halaman
- Semak Core Web Vitals
- Uji pada sambungan perlahan

**Maklum Balas Pengguna:**

- Dengar ahli sidang
- Jejak masalah biasa
- Dokumentasikan penyelesaian
- Kemas kini dokumentasi mengikut keperluan

### Kemas Kini Versi

Ministry Mapper menggunakan penomboran versi semantik:

- **Versi utama** (x.0.0): Perubahan pecah
- **Versi minor** (1.x.0): Ciri baharu
- **Versi tampalan** (1.9.x): Pembetulan pepijat

## Langkah Seterusnya

**Untuk Pengguna:**

- Baca [Panduan Pengguna](user-guide.md) untuk mempelajari cara menggunakan aplikasi

**Untuk Pentadbir:**

- Sediakan backend PocketBase (lihat repositori ministry-mapper-be)
- Konfigurasi tetapan sidang
- Jemput pengguna dan tetapkan peranan
- Cipta wilayah dan tambah alamat
