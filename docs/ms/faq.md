# Soalan Lazim (FAQ)

## Soalan Umum

### Apakah Ministry Mapper?

Ministry Mapper adalah aplikasi web moden yang membantu jemaah mengurus wilayah pelayanan lapangan secara digital. Daripada menggunakan slip wilayah kertas, Ministry Mapper membolehkan anda mengatur dan menjejak segala-galanya secara digital dari mana-mana peranti dengan sambungan internet. Ia menggunakan React untuk bahagian hadapan, PocketBase sebagai bahagian belakang, dan Leaflet untuk pemetaan interaktif.

### Bagaimana saya mengakses Ministry Mapper?

**Disyorkan**: Gunakan perkhidmatan yang dihoskan kami di **[ministry-mapper.com](https://ministry-mapper.com)**

Hanya:
1. Lawati [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akaun menggunakan halaman daftar
3. Sahkan alamat e-mel anda
4. Hubungi pentadbir jemaah anda untuk kebenaran yang betul

**Alternatif**: Pengehosan sendiri (tidak disyorkan) - lihat [Panduan Pengehosan Sendiri](self-hosting.md) untuk butiran. Pengehosan sendiri memerlukan kepakaran teknikal yang signifikan dan penyelenggaraan berterusan.

### Adakah Ministry Mapper percuma untuk digunakan?

**Perkhidmatan yang Dihoskan**: Lawati [ministry-mapper.com](https://ministry-mapper.com) untuk harga dan pelan.

**Pengehosan Sendiri**: Kod sumber adalah percuma dan sumber terbuka, tetapi anda perlu menyediakan sendiri:
- Pengehosan untuk bahagian hadapan (cth., Vercel, Netlify, AWS)
- Pelaksanaan bahagian belakang PocketBase
- Pilihan: Akaun Sentry untuk pemantauan ralat

Nota: Kos pengehosan sendiri (pelayan, yuran API, masa penyelenggaraan) selalunya melebihi yuran perkhidmatan yang dihoskan.

### Bahasa apa yang disokong Ministry Mapper?

Bahasa yang disokong sekarang:

- Inggeris
- Jepun (日本語)
- Korea (한국語)
- Cina (中文)
- Indonesia (Bahasa Indonesia)
- Melayu (Bahasa Melayu)

Antara muka secara automatik mengesan bahasa pelayar anda. Anda juga boleh menukar bahasa secara manual dalam aplikasi.

### Adakah saya perlu pengetahuan teknikal untuk menggunakan Ministry Mapper?

**Sebagai Penerbit/Pengguna**: Tiada pengetahuan teknikal diperlukan! Jika anda boleh menggunakan telefon pintar atau komputer, anda boleh menggunakan Ministry Mapper melalui perkhidmatan yang dihoskan di [ministry-mapper.com](https://ministry-mapper.com).

**Sebagai Pentadbir Pengehosan Sendiri**: Ya, pengetahuan teknikal yang signifikan diperlukan:

- Pengalaman dengan pentadbiran pelayan Linux
- Pemahaman tentang Docker dan kontenerisasi
- Pengetahuan tentang pembolehubah persekitaran dan konfigurasi
- Kebiasaan dengan sijil SSL/TLS
- Konfigurasi pelayan web (Nginx/Apache)
- Konfigurasi DNS dan domain

**Kami sangat mengesyorkan menggunakan perkhidmatan yang dihoskan dan bukannya pengehosan sendiri.** Lihat [Panduan Pengehosan Sendiri](self-hosting.md) untuk butiran lengkap jika anda masih mahu menghoskan sendiri.

### Bolehkah Ministry Mapper berfungsi tanpa talian?

Tidak, sambungan internet diperlukan agar aplikasi berfungsi dengan baik. Ini kerana:

- Ia perlu menyambung ke bahagian belakang PocketBase
- Kemas kini masa nyata antara pengguna memerlukan ketersambungan

Aplikasi ini berfungsi dengan baik pada data mudah alih jika WiFi tidak tersedia.

## Privasi dan Undang-undang

### Undang-undang privasi apa yang perlu saya pertimbangkan?

**Penting**: Ministry Mapper menjejak alamat kediaman, yang mungkin tertakluk kepada undang-undang privasi data. Undang-undang ini berbeza secara signifikan antara negara dan wilayah:

- **Eropah**: GDPR (Peraturan Perlindungan Data Am)
- **California**: CCPA (Akta Privasi Pengguna California)
- **Brazil**: LGPD (Lei Geral de Proteção de Dados)
- **Banyak Lagi**: Pelbagai peraturan tempatan

**Sila semak semula peraturan tempatan anda dengan teliti dan pastikan pematuhan sebelum menggunakan Ministry Mapper.** Berunding dengan profesional undang-undang untuk nasihat undang-undang yang khusus untuk kawasan anda.

**Perkhidmatan yang Dihoskan**: Perkhidmatan yang dihoskan di [ministry-mapper.com](https://ministry-mapper.com) mungkin menyediakan bantuan pematuhan, tetapi anda tetap bertanggungjawab untuk mematuhi undang-undang tempatan anda.

**Pengehosan Sendiri**: Anda sepenuhnya bertanggungjawab untuk semua pematuhan undang-undang privasi.

### Data apa yang disimpan Ministry Mapper?

**Data Pengguna (diurus oleh PocketBase):**

- Alamat e-mel
- Nama
- Kata laluan (disulitkan)
- Peranan dan kebenaran pengguna

**Data Wilayah:**

- Sempadan dan nama wilayah
- Alamat
- Maklumat status
- Nota tentang isi rumah
- Sejarah tugasan
- Cap masa kemas kini

**Data Teknikal (jika Sentry diaktifkan):**

- Log ralat
- Metrik prestasi

Semua penyimpanan data mematuhi amalan terbaik keselamatan. Untuk dasar data perkhidmatan yang dihoskan, semak [ministry-mapper.com](https://ministry-mapper.com).

## Persediaan Teknikal (Pengehosan Sendiri)

!!! warning "Pengehosan Sendiri Tidak Disyorkan"
    Bahagian berikut adalah untuk pengguna lanjutan yang mahu menghoskan sendiri Ministry Mapper. **Kami sangat mengesyorkan menggunakan perkhidmatan yang dihoskan di [ministry-mapper.com](https://ministry-mapper.com) sebaliknya.** Pengehosan sendiri memerlukan kepakaran teknikal yang signifikan, penyelenggaraan berterusan, dan tanggungjawab keselamatan.
    
    Untuk arahan pengehosan sendiri yang lengkap, lihat [Panduan Pengehosan Sendiri](self-hosting.md).

### Apakah keperluan sistem?

**Untuk Menggunakan Perkhidmatan yang Dihoskan:**
- Pelayar web moden (Chrome, Firefox, Safari, Edge)
- Sambungan internet
- Alamat e-mel
- Peranti mudah alih atau komputer

**Untuk Pembangunan Pengehosan Sendiri:**

- Node.js >= 22.0.0
- Pengurus pakej npm
- Git
- Docker (untuk bahagian belakang)

**Untuk Pengeluaran Pengehosan Sendiri:**

- Perkhidmatan pengehosan awan (Railway, Render, DigitalOcean, AWS, dll.)
- Nama domain
- Sijil SSL/TLS
- Bahagian belakang PocketBase (pelaksanaan berasingan)
- (Pilihan) Akaun Sentry untuk pemantauan
- (Pilihan) Perkhidmatan e-mel untuk pemberitahuan

### Pembolehubah persekitaran apa yang diperlukan?

**Untuk Pengehosan Sendiri Sahaja** - Bahagian ini hanya terpakai jika anda menghoskan sendiri. Perkhidmatan yang dihoskan mengendalikan semua konfigurasi secara automatik.

**Bahagian Hadapan (fail .env):**

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
VITE_SENTRY_DSN=your_sentry_dsn (pilihan)
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url
VITE_ABOUT_URL=your_about_page_url
```

**Untuk pengeluaran:** Pembolehubah yang sama seperti di atas, tetapi dengan `VITE_SYSTEM_ENVIRONMENT=production`

Lihat [Panduan Persediaan Bahagian Hadapan](frontend-setup.md) untuk butiran lengkap.

### Bagaimana saya melaksanakan bahagian hadapan?

**Untuk Pengehosan Sendiri Sahaja** - Langkau ini jika anda menggunakan perkhidmatan yang dihoskan.

1. **Bina aplikasi:**

   ```bash
   npm run build
   ```

2. **Konfigurasikan pembolehubah persekitaran** seperti yang dinyatakan di atas

3. **Laksanakan folder `build/`** ke pembekal pengehosan anda:

   - Vercel: Sambung repo GitHub anda dan konfigurasikan pembolehubah persekitaran
   - Netlify: Seret dan lepas folder binaan atau sambung melalui Git
   - AWS S3: Muat naik folder binaan dan konfigurasikan pengehosan laman web statik

4. **Pastikan bahagian belakang PocketBase dilaksanakan** dan boleh diakses di URL yang dinyatakan dalam `VITE_POCKETBASE_URL`

Lihat [Panduan Persediaan Bahagian Hadapan](frontend-setup.md) dan [Panduan Pengehosan Sendiri](self-hosting.md) untuk arahan terperinci.

### Bagaimana saya menyediakan bahagian belakang PocketBase?

**Untuk Pengehosan Sendiri Sahaja** - Langkau ini jika anda menggunakan perkhidmatan yang dihoskan.

Bahagian belakang diurus dalam repositori berasingan: [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**Gambaran Keseluruhan Pantas:**

1. Klon repositori bahagian belakang
2. Konfigurasikan pembolehubah persekitaran (lihat [Panduan Persediaan Bahagian Belakang](backend-setup.md))
3. Laksanakan menggunakan Docker atau Railway/Render
4. Inisialisasikan pangkalan data dengan skema
5. Konfigurasikan SMTP untuk pemberitahuan e-mel (pilihan)

**Arahan terperinci:** Lihat [Panduan Persediaan Bahagian Belakang](backend-setup.md) dan [Panduan Pengehosan Sendiri](self-hosting.md).

**Penting**: Bahagian belakang mesti dilaksanakan dan boleh diakses sebelum melaksanakan bahagian hadapan.

### Bagaimana saya menyediakan pemantauan Sentry?

**Untuk Pengehosan Sendiri Sahaja** - Langkau ini jika anda menggunakan perkhidmatan yang dihoskan.

1. Buat akaun di [Sentry](https://sentry.io/)
2. Buat projek React
3. Pergi ke tetapan dan dapatkan kunci DSN
4. Konfigurasikan pembolehubah persekitaran berikut:
   - `VITE_SENTRY_DSN`: DSN projek Sentry anda
   - `VITE_SYSTEM_ENVIRONMENT`: Tetapkan ke "production" untuk pengeluaran (mempengaruhi kadar pensampelan penjejakan)
   - `VITE_VERSION`: Digunakan untuk penjejakan keluaran dalam Sentry

Nota: Sentry adalah pilihan tetapi disyorkan untuk pelaksanaan pengeluaran.

## Pembangunan

### Bagaimana saya menjalankan aplikasi secara tempatan?

**Untuk Pembangun Sahaja** - Bahagian ini adalah untuk pembangun yang menyumbang kepada Ministry Mapper.

1. **Klon repositori:**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **Pasang dependensi:**

   ```bash
   npm install
   ```

3. **Konfigurasikan fail .env** dengan pembolehubah persekitaran yang diperlukan (lihat `.env.example`)

4. **Mulakan pelayan pembangunan:**
   ```bash
   npm start
   ```

Aplikasi akan berjalan pada `http://localhost:3000`

Nota: Anda memerlukan bahagian belakang PocketBase yang berjalan agar aplikasi berfungsi. Lihat [Panduan Persediaan Bahagian Belakang](backend-setup.md).

### Bagaimana saya menjalankan ujian?

```bash
npm test
```

Ujian ditulis menggunakan Vitest dan Testing Library. Fail ujian terletak bersama fail sumber dengan sambungan `.test.ts`.

### Bagaimana saya memformat kod?

**Semak pemformatan:**

```bash
npm run prettier
```

**Terapkan pembetulan pemformatan:**

```bash
npm run prettier:fix
```

Projek ini menggunakan Prettier untuk pemformatan kod dan mempunyai cangkuk pra-komit yang dikonfigurasi melalui Husky.

### Apakah timbunan teknologi?

- **Rangka Kerja Bahagian Hadapan**: React 19 dengan TypeScript
- **Alat Binaan**: Vite
- **Perpustakaan UI**: React Bootstrap (Bootstrap 5)
- **Peta**: Leaflet dengan OpenStreetMap
- **Pengurusan Keadaan**: Konteks React + cangkuk tersuai
- **Penghalaan**: Wouter
- **Penggayaan**: SCSS
- **Bahagian Belakang**: PocketBase (repositori berasingan)
- **Pemantauan**: Sentry
- **Pengujian**: Vitest + Testing Library

## Penyelesaian Masalah

### Aplikasi menunjukkan "Cannot connect to backend"

**Untuk Pengguna Perkhidmatan yang Dihoskan**: Jika anda melihat ralat ini, perkhidmatan mungkin mengalami masalah. Semak halaman status atau hubungi sokongan.

**Untuk Pengguna Pengehosan Sendiri**:

**Kemungkinan punca:**

1. Bahagian belakang PocketBase tidak berjalan atau tidak boleh diakses
2. Pembolehubah persekitaran `VITE_POCKETBASE_URL` tidak betul
3. Isu CORS (bahagian belakang tidak membenarkan domain bahagian hadapan)

**Penyelesaian:**

- Sahkan bahagian belakang berjalan dan boleh diakses
- Semak URL dalam pembolehubah persekitaran anda
- Konfigurasikan tetapan CORS dalam PocketBase untuk membenarkan domain bahagian hadapan anda

### Perubahan tidak ditunjukkan selepas membina semula

**Untuk Pengehosan Sendiri Sahaja**:

**Kemungkinan punca:**

1. Cache pelayar
2. Cache CDN (jika menggunakan)
3. Artifak binaan tidak dilaksanakan dengan betul

**Penyelesaian:**

- Kosongkan cache pelayar atau gunakan mod inkognito
- Batalkan sah cache CDN jika berkenaan
- Sahkan folder binaan telah digantikan sepenuhnya pada perkhidmatan pengehosan anda
- Semak konsol pelayar untuk ralat

### Pengesahan pengguna tidak berfungsi

**Kemungkinan punca:**

1. Pengurusan pengguna bahagian belakang PocketBase tidak dikonfigurasi
2. Isu ketersambungan rangkaian
3. URL PocketBase tidak betul
4. Isu konfigurasi CORS

**Penyelesaian:**

- Sahkan bahagian belakang PocketBase dikonfigurasi dengan betul
- Semak konsol pelayar untuk mesej ralat
- Uji titik akhir API bahagian belakang secara langsung
- Semak tetapan CORS dalam PocketBase

### Aplikasi perlahan atau tidak responsif

**Kemungkinan punca:**

1. Set data wilayah yang besar
2. Latensi rangkaian
3. Sumber pelayan tidak mencukupi (pengehosan sendiri)
4. Isu prestasi pelayar

**Penyelesaian:**

- Semak sambungan internet anda
- Cuba menggunakan pelayar yang berbeza
- Untuk pengehosan sendiri: Optimumkan data wilayah
- Untuk pengehosan sendiri: Gunakan wilayah pengehosan yang lebih dekat
- Untuk pengehosan sendiri: Naik taraf sumber pelayan jika perlu
- Kosongkan cache dan data pelayar

## Menyumbang

### Bagaimana saya melaporkan pepijat?

1. Semak jika pepijat sudah dilaporkan dalam [Isu GitHub](https://github.com/rimorin/ministry-mapper-v2/issues)
2. Jika tidak, buat isu baru dengan:
   - Penerangan yang jelas tentang masalah
   - Langkah untuk menghasilkan semula
   - Tingkah laku dijangka berbanding sebenar
   - Maklumat pelayar dan peranti
   - Tangkap layar atau mesej ralat
   - Konfigurasi persekitaran anda (tanpa data sensitif)

### Bolehkah saya meminta ciri?

Ya! Buat permintaan ciri di GitHub dengan:

- Penerangan yang jelas tentang ciri
- Kes penggunaan dan manfaat
- Bagaimana ia akan berfungsi
- Siapa yang akan mendapat manfaat

Nota: Ini adalah projek yang diselenggara secara sukarela, jadi pelaksanaan bergantung pada masa dan sumber yang ada.

### Bolehkah saya menyumbang kod?

Sudah tentu! Sumbangan dialu-alukan:

**Cara menyumbang:**

1. Fork repositori
2. Buat cawangan ciri
3. Buat perubahan anda mengikut gaya kod yang sedia ada
4. Tulis ujian untuk fungsi baru
5. Jalankan `npm test` dan `npm run prettier` untuk memastikan kualiti
6. Hantar permintaan tarikan dengan penerangan yang jelas

**Garis panduan sumbangan:**

- Ikuti amalan terbaik TypeScript dan React
- Gunakan corak yang sedia ada dalam pangkalan kod
- Kemas kini dokumentasi jika perlu
- Pastikan perubahan fokus dan minimum
- Uji dengan teliti sebelum menghantar

### Bolehkah saya menterjemahkan aplikasi ke bahasa baru?

Ya! Aplikasi menggunakan pengantarabangsaan (i18n) untuk terjemahan:

1. Semak direktori `src/i18n/` dalam [repositori bahagian hadapan](https://github.com/rimorin/ministry-mapper-v2) untuk terjemahan yang sedia ada
2. Buat fail bahasa baru mengikut struktur yang sedia ada
3. Tambah terjemahan anda
4. Kemas kini komponen pemilih bahasa
5. Hantar permintaan tarikan

### Bagaimana saya menaik taraf ke versi baru?

**Untuk Pengguna Perkhidmatan yang Dihoskan**: Kemas kini adalah automatik. Anda tidak perlu melakukan apa-apa!

**Untuk Pengguna Pengehosan Sendiri**:

**Bahagian Hadapan:**

```bash
git pull origin main
npm install
npm run build
# Laksanakan binaan baru ke perkhidmatan pengehosan anda
```

**Bahagian Belakang:**
Rujuk [repositori Ministry Mapper BE](https://github.com/rimorin/ministry-mapper-be) untuk arahan naik taraf bahagian belakang.

**Penting:**

- Sentiasa sandarkan data anda terlebih dahulu
- Baca CHANGELOG.md untuk perubahan pecah
- Uji dalam persekitaran peringkat jika boleh
- Kemas kini pembolehubah persekitaran jika perlu

## Sokongan

### Di mana saya boleh mendapatkan bantuan?

**Untuk Pengguna Perkhidmatan yang Dihoskan**: Hubungi sokongan melalui laman web [ministry-mapper.com](https://ministry-mapper.com).

**Untuk Semua Pengguna**:

1. **Dokumentasi**: Semak laman dokumentasi ini, termasuk [Memulakan](getting-started.md), [Panduan Pengguna](user-guide.md), dan panduan lain
2. **Isu GitHub**: Cari isu yang sedia ada atau buat yang baru di [repositori ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **Perbincangan GitHub**: Tanya soalan dan kongsi idea
4. **Semakan Kod**: Baca kod dan komen untuk butiran pelaksanaan (pembangun)

### Adakah sokongan komersial tersedia?

**Perkhidmatan yang Dihoskan**: Sokongan profesional tersedia melalui [ministry-mapper.com](https://ministry-mapper.com).

**Pengehosan Sendiri**: Tiada sokongan komersial rasmi tersedia untuk instance yang dihoskan sendiri. Ini adalah projek sumber terbuka yang diselenggara oleh sukarela.

### Berapa kerap aplikasi dikemas kini?

**Perkhidmatan yang Dihoskan**: Kemas kini diterapkan secara automatik apabila tersedia.

**Pengehosan Sendiri**: Kemas kini bergantung pada ketersediaan sukarela. Semak:

- **CHANGELOG.md** untuk sejarah versi
- **Keluaran GitHub** untuk nota keluaran
- **Sejarah komit** untuk perubahan terkini

### Bolehkah saya menyesuaikan Ministry Mapper untuk keperluan saya?

Ya! Ia sumber terbuka, jadi anda boleh:

- Mengubah suai UI (warna, tata letak, penjenamaan)
- Menambah ciri tersuai
- Mengubah aliran kerja
- Mengintegrasikan dengan sistem lain

**Keperluan:**

- Pengetahuan tentang React 19, TypeScript, dan PocketBase
- Pemahaman tentang pangkalan kod
- Pengujian perubahan anda yang betul
- Pertimbangkan menyumbang penambahbaikan kembali ke projek

**Nota**: Penyesuaian memerlukan pengehosan sendiri. Perkhidmatan yang dihoskan menggunakan versi standard.

---

## Masih Ada Soalan?

1. **Baca Dokumentasi**: 
   - [Panduan Memulakan](getting-started.md)
   - [Panduan Pengguna](user-guide.md)
   - [Panduan Pengehosan Sendiri](self-hosting.md) (jika pengehosan sendiri)
   - [Panduan Persediaan Bahagian Belakang](backend-setup.md) (jika pengehosan sendiri)
   - [Panduan Persediaan Bahagian Hadapan](frontend-setup.md) (jika pengehosan sendiri)
2. **Cari** [Isu GitHub](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **Tanya dalam** Perbincangan GitHub
4. **Hubungi Sokongan** (untuk pengguna perkhidmatan yang dihoskan)
5. **Buka isu baru** dengan soalan anda

Ingat: Ministry Mapper adalah sumber terbuka dan diselenggara oleh sukarela. Bersabarlah dan hormat apabila meminta bantuan!
