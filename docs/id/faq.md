# Pertanyaan yang Sering Diajukan (FAQ)

## Pertanyaan Umum

### Apa itu Ministry Mapper?

Ministry Mapper adalah aplikasi web modern yang membantu jemaat mengelola wilayah pelayanan lapangan secara digital. Daripada menggunakan slip wilayah kertas, Ministry Mapper memungkinkan Anda mengorganisir dan melacak segalanya secara digital dari perangkat apa pun dengan koneksi internet. Ini menggunakan React untuk frontend, PocketBase sebagai backend, dan Leaflet untuk pemetaan interaktif.

### Bagaimana cara mengakses Ministry Mapper?

**Disarankan**: Gunakan layanan hosted kami di **[ministry-mapper.com](https://ministry-mapper.com)**

Cukup:
1. Kunjungi [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akun menggunakan halaman pendaftaran
3. Verifikasi alamat email Anda
4. Hubungi administrator jemaat Anda untuk izin yang sesuai

**Alternatif**: Self-hosting (tidak disarankan) - lihat [Panduan Self-Hosting](self-hosting.md) untuk detailnya. Self-hosting memerlukan keahlian teknis yang signifikan dan pemeliharaan berkelanjutan.

### Apakah Ministry Mapper gratis digunakan?

**Layanan Hosted**: Kunjungi [ministry-mapper.com](https://ministry-mapper.com) untuk harga dan paket.

**Self-Hosting**: Kode sumber gratis dan open-source, tetapi Anda perlu menyediakan sendiri:
- Hosting untuk frontend (mis., Vercel, Netlify, AWS)
- Deployment backend PocketBase
- Opsional: Akun Sentry untuk pemantauan error

Catatan: Biaya self-hosting (server, biaya API, waktu pemeliharaan) sering kali melebihi biaya layanan hosted.

### Bahasa apa saja yang didukung Ministry Mapper?

Bahasa yang saat ini didukung:

- Bahasa Inggris
- Jepang (日本語)
- Korea (한국語)
- Tionghoa (中文)
- Indonesia (Bahasa Indonesia)
- Melayu (Bahasa Melayu)

Antarmuka secara otomatis mendeteksi bahasa browser Anda. Anda juga dapat mengganti bahasa secara manual di aplikasi.

### Apakah saya memerlukan pengetahuan teknis untuk menggunakan Ministry Mapper?

**Sebagai Penerbit/Pengguna**: Tidak diperlukan pengetahuan teknis! Jika Anda bisa menggunakan smartphone atau komputer, Anda bisa menggunakan Ministry Mapper melalui layanan hosted di [ministry-mapper.com](https://ministry-mapper.com).

**Sebagai Administrator Self-Hosting**: Ya, diperlukan pengetahuan teknis yang signifikan:

- Pengalaman dengan administrasi server Linux
- Pemahaman tentang Docker dan kontainerisasi
- Pengetahuan tentang variabel lingkungan dan konfigurasi
- Keakraban dengan sertifikat SSL/TLS
- Konfigurasi web server (Nginx/Apache)
- Konfigurasi DNS dan domain

**Kami sangat menyarankan menggunakan layanan hosted daripada self-hosting.** Lihat [Panduan Self-Hosting](self-hosting.md) untuk detail lengkap jika Anda masih ingin melakukan self-hosting.

### Bisakah Ministry Mapper bekerja offline?

Tidak, koneksi internet diperlukan agar aplikasi berfungsi dengan baik. Ini karena:

- Perlu terhubung ke backend PocketBase
- Pembaruan real-time antar pengguna memerlukan konektivitas

Aplikasi ini bekerja dengan baik pada data seluler jika WiFi tidak tersedia.

## Privasi dan Hukum

### Undang-undang privasi apa yang harus saya pertimbangkan?

**Penting**: Ministry Mapper melacak alamat tempat tinggal, yang mungkin tunduk pada undang-undang privasi data. Undang-undang ini sangat bervariasi antar negara dan wilayah:

- **Eropa**: GDPR (General Data Protection Regulation)
- **California**: CCPA (California Consumer Privacy Act)
- **Brazil**: LGPD (Lei Geral de Proteção de Dados)
- **Banyak Lainnya**: Berbagai peraturan lokal

**Harap tinjau secara menyeluruh peraturan lokal Anda dan pastikan kepatuhan sebelum menggunakan Ministry Mapper.** Konsultasikan dengan profesional hukum untuk saran hukum yang tepat sesuai daerah Anda.

**Layanan Hosted**: Layanan hosted di [ministry-mapper.com](https://ministry-mapper.com) mungkin memberikan bantuan kepatuhan, tetapi Anda tetap bertanggung jawab untuk mengikuti undang-undang lokal Anda.

**Self-Hosting**: Anda sepenuhnya bertanggung jawab atas semua kepatuhan undang-undang privasi.

### Data apa yang disimpan Ministry Mapper?

**Data Pengguna (dikelola oleh PocketBase):**

- Alamat email
- Nama
- Kata sandi (terenkripsi)
- Peran dan izin pengguna

**Data Wilayah:**

- Batas dan nama wilayah
- Alamat
- Informasi status
- Catatan tentang penghuni rumah
- Riwayat penugasan
- Timestamp pembaruan

**Data Teknis (jika Sentry diaktifkan):**

- Log error
- Metrik kinerja

Semua penyimpanan data mematuhi praktik keamanan terbaik. Untuk kebijakan data layanan hosted, periksa [ministry-mapper.com](https://ministry-mapper.com).

## Pengaturan Teknis (Self-Hosting)

!!! warning "Self-Hosting Tidak Disarankan"
    Bagian berikut untuk pengguna tingkat lanjut yang ingin melakukan self-hosting Ministry Mapper. **Kami sangat menyarankan menggunakan layanan hosted di [ministry-mapper.com](https://ministry-mapper.com) sebagai gantinya.** Self-hosting memerlukan keahlian teknis yang signifikan, pemeliharaan berkelanjutan, dan tanggung jawab keamanan.
    
    Untuk instruksi self-hosting lengkap, lihat [Panduan Self-Hosting](self-hosting.md).

### Apa persyaratan sistemnya?

**Untuk Menggunakan Layanan Hosted:**
- Browser web modern (Chrome, Firefox, Safari, Edge)
- Koneksi internet
- Alamat email
- Perangkat mobile atau komputer

**Untuk Pengembangan Self-Hosting:**

- Node.js >= 22.0.0
- npm package manager
- Git
- Docker (untuk backend)

**Untuk Produksi Self-Hosting:**

- Layanan hosting cloud (Railway, Render, DigitalOcean, AWS, dll.)
- Nama domain
- Sertifikat SSL/TLS
- Backend PocketBase (deployment terpisah)
- (Opsional) Akun Sentry untuk pemantauan
- (Opsional) Layanan email untuk notifikasi

### Variabel lingkungan apa yang diperlukan?

**Hanya untuk Self-Hosting** - Bagian ini hanya berlaku jika Anda melakukan self-hosting. Layanan hosted menangani semua konfigurasi secara otomatis.

**Frontend (file .env):**

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
VITE_SENTRY_DSN=your_sentry_dsn (optional)
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url
VITE_ABOUT_URL=your_about_page_url
```

**Untuk produksi:** Variabel yang sama di atas, tetapi dengan `VITE_SYSTEM_ENVIRONMENT=production`

Lihat [Panduan Pengaturan Frontend](frontend-setup.md) untuk detail lengkap.

### Bagaimana cara men-deploy frontend?

**Hanya untuk Self-Hosting** - Lewati ini jika Anda menggunakan layanan hosted.

1. **Build aplikasi:**

   ```bash
   npm run build
   ```

2. **Konfigurasi variabel lingkungan** seperti yang ditentukan di atas

3. **Deploy folder `build/`** ke penyedia hosting Anda:

   - Vercel: Hubungkan repo GitHub Anda dan konfigurasi variabel lingkungan
   - Netlify: Seret dan lepas folder build atau hubungkan melalui Git
   - AWS S3: Upload folder build dan konfigurasi hosting situs web statis

4. **Pastikan backend PocketBase di-deploy** dan dapat diakses di URL yang ditentukan dalam `VITE_POCKETBASE_URL`

Lihat [Panduan Pengaturan Frontend](frontend-setup.md) dan [Panduan Self-Hosting](self-hosting.md) untuk instruksi terperinci.

### Bagaimana cara menyiapkan backend PocketBase?

**Hanya untuk Self-Hosting** - Lewati ini jika Anda menggunakan layanan hosted.

Backend dikelola di repository terpisah: [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**Ikhtisar Singkat:**

1. Clone repository backend
2. Konfigurasi variabel lingkungan (lihat [Panduan Pengaturan Backend](backend-setup.md))
3. Deploy menggunakan Docker atau Railway/Render
4. Inisialisasi database dengan skema
5. Konfigurasi SMTP untuk notifikasi email (opsional)

**Instruksi terperinci:** Lihat [Panduan Pengaturan Backend](backend-setup.md) dan [Panduan Self-Hosting](self-hosting.md).

**Penting**: Backend harus di-deploy dan dapat diakses sebelum men-deploy frontend.

### Bagaimana cara menyiapkan pemantauan Sentry?

**Hanya untuk Self-Hosting** - Lewati ini jika Anda menggunakan layanan hosted.

1. Buat akun [Sentry](https://sentry.io/)
2. Buat proyek React
3. Pergi ke pengaturan dan ambil kunci DSN
4. Konfigurasi variabel lingkungan berikut:
   - `VITE_SENTRY_DSN`: DSN proyek Sentry Anda
   - `VITE_SYSTEM_ENVIRONMENT`: Atur ke "production" untuk produksi (mempengaruhi tingkat sampel tracing)
   - `VITE_VERSION`: Digunakan untuk pelacakan rilis di Sentry

Catatan: Sentry bersifat opsional tetapi disarankan untuk deployment produksi.

## Pengembangan

### Bagaimana cara menjalankan aplikasi secara lokal?

**Hanya untuk Pengembang** - Bagian ini untuk pengembang yang berkontribusi ke Ministry Mapper.

1. **Clone repository:**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **Install dependensi:**

   ```bash
   npm install
   ```

3. **Konfigurasi file .env** dengan variabel lingkungan yang diperlukan (lihat `.env.example`)

4. **Mulai server pengembangan:**
   ```bash
   npm start
   ```

Aplikasi akan berjalan di `http://localhost:3000`

Catatan: Anda memerlukan backend PocketBase yang berjalan agar aplikasi berfungsi. Lihat [Panduan Pengaturan Backend](backend-setup.md).

### Bagaimana cara menjalankan tes?

```bash
npm test
```

Tes ditulis menggunakan Vitest dan Testing Library. File tes berada di sebelah file sumber dengan ekstensi `.test.ts`.

### Bagaimana cara memformat kode?

**Periksa pemformatan:**

```bash
npm run prettier
```

**Terapkan perbaikan pemformatan:**

```bash
npm run prettier:fix
```

Proyek ini menggunakan Prettier untuk pemformatan kode dan memiliki pre-commit hooks yang dikonfigurasi melalui Husky.

### Apa tumpukan teknologinya?

- **Framework Frontend**: React 19 dengan TypeScript
- **Build Tool**: Vite
- **Library UI**: React Bootstrap (Bootstrap 5)
- **Peta**: Leaflet dengan OpenStreetMap
- **Manajemen State**: React Context + custom hooks
- **Routing**: Wouter
- **Styling**: SCSS
- **Backend**: PocketBase (repository terpisah)
- **Pemantauan**: Sentry
- **Pengujian**: Vitest + Testing Library

Lihat CLAUDE.md untuk informasi arsitektur terperinci.

## Pemecahan Masalah

### Aplikasi menampilkan "Cannot connect to backend"

**Untuk Pengguna Layanan Hosted**: Jika Anda melihat error ini, layanan mungkin mengalami masalah. Periksa halaman status atau hubungi dukungan.

**Untuk Pengguna Self-Hosting**:

**Kemungkinan penyebab:**

1. Backend PocketBase tidak berjalan atau tidak dapat diakses
2. Variabel lingkungan `VITE_POCKETBASE_URL` salah
3. Masalah CORS (backend tidak mengizinkan domain frontend)

**Solusi:**

- Verifikasi backend berjalan dan dapat diakses
- Periksa URL di variabel lingkungan Anda
- Konfigurasi pengaturan CORS di PocketBase untuk mengizinkan domain frontend Anda

### Perubahan tidak tercermin setelah rebuild

**Hanya untuk Self-Hosting**:

**Kemungkinan penyebab:**

1. Cache browser
2. Cache CDN (jika menggunakan)
3. Artefak build tidak di-deploy dengan benar

**Solusi:**

- Bersihkan cache browser atau gunakan mode incognito
- Invalidasi cache CDN jika berlaku
- Verifikasi folder build sepenuhnya diganti di layanan hosting Anda
- Periksa konsol browser untuk error

### Autentikasi pengguna tidak berfungsi

**Untuk Pengguna Layanan Hosted**: Hubungi dukungan jika Anda mengalami masalah autentikasi.

**Untuk Pengguna Self-Hosting**:

**Kemungkinan penyebab:**

1. Manajemen pengguna backend PocketBase tidak dikonfigurasi
2. Masalah konektivitas jaringan
3. URL PocketBase salah
4. Masalah konfigurasi CORS

**Solusi:**

- Verifikasi backend PocketBase dikonfigurasi dengan benar
- Periksa konsol browser untuk pesan error
- Uji endpoint API backend secara langsung
- Periksa pengaturan CORS di PocketBase

### Aplikasi lambat atau tidak responsif

**Kemungkinan penyebab:**

1. Dataset wilayah yang besar
2. Latensi jaringan
3. Sumber daya server tidak mencukupi (self-hosting)
4. Masalah kinerja browser

**Solusi:**

- Periksa koneksi internet Anda
- Coba gunakan browser yang berbeda
- Untuk self-hosting: Optimalkan data wilayah (kurangi field yang tidak perlu)
- Untuk self-hosting: Gunakan region hosting yang lebih dekat
- Untuk self-hosting: Upgrade sumber daya server jika diperlukan
- Bersihkan cache dan data browser
- Periksa konsol browser untuk peringatan kinerja

## Berkontribusi

### Bagaimana cara melaporkan bug?

1. Periksa apakah bug sudah dilaporkan di [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
2. Jika belum, buat issue baru dengan:
   - Deskripsi masalah yang jelas
   - Langkah-langkah untuk mereproduksi
   - Perilaku yang diharapkan vs aktual
   - Informasi browser dan perangkat
   - Screenshot atau pesan error
   - Konfigurasi lingkungan Anda (tanpa data sensitif)

### Bisakah saya meminta fitur?

Ya! Buat permintaan fitur di GitHub dengan:

- Deskripsi fitur yang jelas
- Kasus penggunaan dan manfaat
- Cara kerjanya
- Siapa yang akan mendapat manfaat

Catatan: Ini adalah proyek yang dikelola sukarelawan, jadi implementasi bergantung pada waktu dan sumber daya yang tersedia.

### Bisakah saya berkontribusi kode?

Tentu saja! Kontribusi diterima:

**Cara berkontribusi:**

1. Fork repository
2. Buat branch fitur
3. Buat perubahan Anda mengikuti gaya kode yang ada
4. Tulis tes untuk fungsionalitas baru
5. Jalankan `npm test` dan `npm run prettier` untuk memastikan kualitas
6. Submit pull request dengan deskripsi yang jelas

**Panduan kontribusi:**

- Ikuti praktik terbaik TypeScript dan React
- Gunakan pola yang ada di codebase
- Perbarui dokumentasi jika diperlukan
- Jaga perubahan tetap terfokus dan minimal
- Uji secara menyeluruh sebelum mengirimkan

Lihat CLAUDE.md untuk detail arsitektur dan konvensi pengkodean.

### Bisakah saya menerjemahkan aplikasi ke bahasa baru?

Ya! Aplikasi menggunakan internasionalisasi (i18n) untuk terjemahan:

1. Periksa direktori `src/i18n/` di [repository frontend](https://github.com/rimorin/ministry-mapper-v2) untuk terjemahan yang ada
2. Buat file bahasa baru mengikuti struktur yang ada
3. Tambahkan terjemahan Anda
4. Perbarui komponen pemilih bahasa
5. Submit pull request

### Bagaimana cara meng-upgrade ke versi baru?

**Untuk Pengguna Layanan Hosted**: Pembaruan dilakukan secara otomatis. Anda tidak perlu melakukan apa pun!

**Untuk Pengguna Self-Hosting**:

**Frontend:**

```bash
git pull origin main
npm install
npm run build
# Deploy build baru ke layanan hosting Anda
```

**Backend:**
Lihat [repository Ministry Mapper BE](https://github.com/rimorin/ministry-mapper-be) untuk instruksi upgrade backend.

**Penting:**

- Selalu backup data Anda terlebih dahulu
- Baca CHANGELOG.md untuk perubahan breaking
- Uji di lingkungan staging jika memungkinkan
- Perbarui variabel lingkungan jika diperlukan

## Dukungan

### Di mana saya bisa mendapatkan bantuan?

**Untuk Pengguna Layanan Hosted**: Hubungi dukungan melalui situs web [ministry-mapper.com](https://ministry-mapper.com).

**Untuk Semua Pengguna**:

1. **Dokumentasi**: Periksa situs dokumentasi ini, termasuk [Memulai](getting-started.md), [Panduan Pengguna](user-guide.md), dan panduan lainnya
2. **GitHub Issues**: Cari issue yang ada atau buat yang baru di [repository ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **GitHub Discussions**: Ajukan pertanyaan dan bagikan ide
4. **Code Review**: Baca kode dan komentar untuk detail implementasi (pengembang)

### Apakah ada dukungan komersial?

**Layanan Hosted**: Dukungan profesional tersedia melalui [ministry-mapper.com](https://ministry-mapper.com).

**Self-Hosting**: Tidak ada dukungan komersial resmi yang tersedia untuk instance self-hosted. Ini adalah proyek open-source yang dikelola sukarelawan. Namun:

- Dukungan komunitas melalui GitHub
- Dokumentasi yang komprehensif
- Anda dapat mempekerjakan pengembang independen untuk kustomisasi

### Seberapa sering aplikasi diperbarui?

**Layanan Hosted**: Pembaruan diterapkan secara otomatis saat tersedia.

**Self-Hosting**: Pembaruan bergantung pada ketersediaan sukarelawan. Periksa:

- **CHANGELOG.md** untuk riwayat versi
- **GitHub Releases** untuk catatan rilis
- **Riwayat commit** untuk perubahan terbaru

### Bisakah saya mengkustomisasi Ministry Mapper untuk kebutuhan saya?

Ya! Ini open source, jadi Anda bisa:

- Memodifikasi UI (warna, tata letak, branding)
- Menambahkan fitur kustom
- Mengubah alur kerja
- Berintegrasi dengan sistem lain

**Persyaratan:**

- Pengetahuan tentang React 19, TypeScript, dan PocketBase
- Pemahaman codebase (lihat CLAUDE.md di repository)
- Pengujian perubahan Anda yang tepat
- Pertimbangkan untuk berkontribusi perbaikan kembali ke proyek

**Catatan**: Kustomisasi memerlukan self-hosting. Layanan hosted menggunakan versi standar.

---

## Masih Punya Pertanyaan?

1. **Baca Dokumentasi**: 
   - [Panduan Memulai](getting-started.md)
   - [Panduan Pengguna](user-guide.md)
   - [Panduan Self-Hosting](self-hosting.md) (jika self-hosting)
   - [Panduan Pengaturan Backend](backend-setup.md) (jika self-hosting)
   - [Panduan Pengaturan Frontend](frontend-setup.md) (jika self-hosting)
2. **Cari** di [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **Tanya di** GitHub Discussions
4. **Hubungi Dukungan** (untuk pengguna layanan hosted)
5. **Buka issue baru** dengan pertanyaan Anda

Ingat: Ministry Mapper adalah open-source dan dikelola oleh sukarelawan. Bersabarlah dan hormati saat meminta bantuan!
