# Ikhtisar Fitur

## Pendahuluan

Ministry Mapper adalah platform manajemen wilayah digital yang komprehensif yang dirancang untuk jemaat keagamaan. Halaman ini menyediakan ikhtisar rinci tentang semua fitur dan kemampuan.

---

## 🗺️ Manajemen Wilayah

### Mengatur Wilayah Geografis

**Membuat dan Mengelola Wilayah:**
- Membuat wilayah tanpa batas per jemaat
- Menetapkan kode dan deskripsi unik
- Mengatur wilayah berdasarkan prioritas dengan pengurutan seret-dan-lepas
- Melacak persentase penyelesaian secara otomatis
- Mengatur ulang status wilayah untuk memulai pelacakan kembali
- Memindahkan peta antar wilayah dengan mudah

**Statistik Wilayah:**
- Pelacakan penyelesaian agregat
- Indikator kemajuan visual
- Pembaruan statistik secara real-time
- Rincian per wilayah
- Pelacakan historis

**Kasus Penggunaan:** Membagi area pelayanan jemaat Anda menjadi wilayah-wilayah yang dapat dikelola untuk pekerjaan pelayanan lapangan yang terorganisir.

---

## 🏢 Manajemen Peta & Alamat

### Pelacakan Lokasi Interaktif

**Jenis Peta:**

**1. Alamat Publik (Gedung Bertingkat)**
- Dukungan untuk gedung bertingkat
- Menambah/menghapus lantai secara dinamis
- Setiap lantai berisi beberapa unit
- Penerapan kode alamat otomatis ke semua lantai
- Organisasi lantai per lantai

**2. Alamat Pribadi (Rumah Satu Lantai)**
- Rumah individual atau properti mandiri
- Struktur lebih sederhana tanpa subdivisi lantai
- Entri alamat cepat

**Fitur Peta:**
- Melampirkan koordinat GPS untuk lokasi yang tepat
- Menambahkan deskripsi terperinci
- Nomor urut untuk kunjungan yang terorganisir
- Integrasi peta visual dengan Leaflet/OpenStreetMap
- Mendapatkan petunjuk arah ke alamat mana pun
- Dukungan geolokasi

**Operasi Alamat:**
- Menambah/menghapus kode alamat
- Mengurutkan ulang alamat untuk rute optimal
- Operasi massal untuk efisiensi
- Menyalin alamat antar lantai
- Mengganti nama dan mengatur ulang

**Tambah Alamat Otomatis** *(v1.33+)*
- Penerbit dapat menambahkan alamat yang hilang langsung saat pemetaan melalui kartu tambah di akhir daftar alamat — tanpa perlu intervensi admin
- Kartu **"+"** muncul di akhir daftar alamat sebagai titik entri cepat
- Ideal untuk jemaat yang masih membangun catatan wilayah mereka

![Kartu "+" di akhir daftar alamat untuk menambahkan alamat yang hilang](../assets/screenshots/add_more_add.png)

---

## 📊 Pelacakan Unit/Rumah Tangga

### Manajemen Status Kunjungan yang Komprehensif

**Jenis Status:**

| Status | Deskripsi | Dampak pada Kemajuan |
|--------|-----------|---------------------|
| **Belum Selesai** | Status awal, belum dikunjungi | Dihitung dalam belum selesai |
| **Selesai** | Berhasil diselesaikan | Dihitung sebagai selesai |
| **Tidak di Rumah** | Tidak ada orang di rumah, dapat dicoba lagi | Dihitung setelah percobaan maksimum |
| **Jangan Kunjungi (DNC)** | Rumah tangga meminta tidak dikunjungi | Dikecualikan dari penghitungan |
| **Tidak Valid** | Unit tidak ada | Dikecualikan dari penghitungan |

**Pelacakan Tidak di Rumah:**
- Maksimum percobaan yang dapat dikonfigurasi per jemaat
- Penambahan penghitung otomatis
- Diperlakukan sebagai "selesai" setelah percobaan maksimum
- Mencegah kunjungan ulang tanpa batas

**Logika Penyelesaian:**
```
Selesai ketika:
  - Status = Selesai, ATAU
  - Status = Tidak di Rumah DAN percobaan >= max_tries

Progress % = Selesai / Total Unit yang Dapat Dihitung × 100
```

**Opsi Rumah Tangga:**
- Klasifikasi alamat yang dapat disesuaikan
- Jenis yang dapat dihitung vs tidak dapat dihitung
- Opsi default untuk alamat baru
- Beberapa opsi per alamat
- Mempengaruhi perhitungan kemajuan

**Catatan Kunjungan:**
- Menambahkan catatan terperinci per alamat
- Pelacakan timestamp otomatis
- Melacak siapa yang melakukan pembaruan
- Notifikasi email untuk perubahan catatan
- Tampilan catatan historis

---

## 🔗 Sistem Penugasan (Tautan Penginjil)

### Akses Aman dengan Batas Waktu

**Fitur Tautan:**
- **Terbatas Waktu:** Kedaluwarsa yang dapat dikonfigurasi (default 24 jam)
- **Berbasis Token:** Token akses aman 20 karakter
- **Tidak Perlu Akun:** Penginjil mengakses hanya melalui tautan
- **Dua Jenis:**
  - Penugasan normal untuk pekerjaan wilayah reguler
  - Penugasan pribadi untuk pelayanan pribadi

**Algoritma Penugasan Cerdas:**

Saat meminta penugasan baru, sistem:

1. **Faktor Utama:** Memilih peta dengan penugasan aktif paling sedikit
   - Mencegah kelebihan beban pada peta tunggal
   - Menyeimbangkan beban kerja

2. **Faktor Sekunder:** Lebih memilih peta yang lebih dekat secara geografis
   - Menggunakan koordinat GPS
   - Ambang batas 50 meter
   - Mengurangi waktu perjalanan

3. **Faktor Tersier:** Memprioritaskan kemajuan penyelesaian yang lebih rendah
   - Memastikan penyelesaian wilayah yang seimbang
   - Mencegah beberapa area terabaikan

**Alur Kerja Penugasan:**
```
1. Konduktor membuat tautan penugasan
2. Tautan dibagikan dengan penginjil (QR, email, teks)
3. Penginjil mengakses /map/:assignmentId
4. Sistem memvalidasi token dan kedaluwarsa
5. Penginjil memperbarui status alamat secara real-time
6. Tautan kedaluwarsa secara otomatis
7. Admin menerima pembaruan secara real-time
```

**Manajemen Penugasan:**
- Melihat penugasan aktif per peta
- Melihat siapa yang memiliki wilayah mana
- Melacak riwayat penugasan
- Penghapusan penugasan manual
- Pembersihan otomatis tautan kedaluwarsa (setiap 5 menit)

---

## 👥 Manajemen Pengguna & Kontrol Akses

### Sistem Izin Berbasis Peran

**Empat Peran Pengguna:**

#### 1. Penginjil (Paling Terbatas)
**Metode Akses:** Hanya tautan penugasan terbatas waktu

**Izin:**
- ✅ Melihat peta yang ditugaskan saja
- ✅ Memperbarui status alamat/unit
- ✅ Menambahkan catatan kunjungan
- ✅ Mengirim pesan umpan balik
- ❌ Tidak dapat melihat wilayah lain
- ❌ Tidak dapat membuat wilayah
- ❌ Tidak dapat mengelola pengguna

**Kasus Penggunaan:** Penginjil pelayanan lapangan yang mengerjakan wilayah

---

#### 2. Hanya-Baca
**Metode Akses:** Akun pengguna dengan peran hanya-baca

**Izin:**
- ✅ Melihat semua data jemaat
- ✅ Melihat wilayah dan peta
- ✅ Melihat informasi alamat
- ✅ Melihat pesan
- ❌ Tidak dapat memodifikasi apa pun
- ❌ Tidak dapat membuat penugasan
- ❌ Tidak dapat mengelola pengguna

**Kasus Penggunaan:** Pengamat, auditor, anggota komite pelayanan

---

#### 3. Konduktor (Tingkat Menengah)
**Metode Akses:** Akun pengguna dengan peran konduktor

**Izin:**
- ✅ Semua izin Hanya-Baca
- ✅ Membuat dan mengelola wilayah
- ✅ Membuat dan mengelola peta
- ✅ Menambah/memperbarui alamat
- ✅ Membuat tautan penugasan penginjil
- ✅ Melihat semua data jemaat
- ✅ Mengelola opsi rumah tangga
- ✅ Mengatur ulang wilayah
- ❌ Tidak dapat mengelola peran pengguna
- ❌ Tidak dapat mengonfigurasi pengaturan jemaat
- ❌ Tidak dapat menghapus pengguna

**Kasus Penggunaan:** Pengawas pelayanan lapangan, koordinator wilayah

---

#### 4. Administrator (Akses Penuh)
**Metode Akses:** Akun pengguna dengan peran administrator

**Izin:**
- ✅ Semua izin Konduktor
- ✅ Membuat dan mengelola pengguna
- ✅ Menetapkan peran pengguna
- ✅ Mengonfigurasi pengaturan jemaat
- ✅ Mengelola opsi rumah tangga
- ✅ Menghapus wilayah dan peta
- ✅ Menghapus akun pengguna
- ✅ Melihat semua penugasan dan pesan
- ✅ Mengonfigurasi maksimum percobaan tidak di rumah
- ✅ Mengatur durasi kedaluwarsa tautan
- ✅ Mengakses log sistem

**Kasus Penggunaan:** Pelayan wilayah, administrator sistem

---

### Manajemen Akun Pengguna

**Fitur:**
- Autentikasi berbasis email
- Verifikasi email diperlukan
- Fungsionalitas reset kata sandi
- Autentikasi OAuth2 Google
- Dukungan autentikasi multi-faktor (MFA)
- Dukungan kata sandi satu kali (OTP)
- Nonaktif/aktifkan akun
- Pelacakan login terakhir
- Dukungan multi-jemaat (peran berbeda per jemaat)

---

## 🛠️ Pengaturan Jemaat

### Konfigurasi yang Dapat Disesuaikan

**Pengaturan Inti:**

**1. Maksimum Percobaan Tidak di Rumah**
- Mengatur berapa kali kunjungan "tidak di rumah" sebelum dianggap selesai
- Tipikal: 2-3 percobaan
- Mempengaruhi perhitungan kemajuan
- Spesifik per jemaat

**2. Kedaluwarsa Tautan Penugasan**
- Mengonfigurasi berapa lama tautan tetap valid
- Default: 24 jam
- Dapat disesuaikan per jemaat
- Pembersihan otomatis tautan kedaluwarsa

**3. Opsi Rumah Tangga**
- Membuat klasifikasi alamat kustom
- Contoh: "Perumahan", "Bisnis", "Gedung Tinggi", "Bahasa: Spanyol"
- Menandai opsi sebagai "dapat dihitung" (mempengaruhi % penyelesaian)
- Mengatur opsi default untuk alamat baru
- Mengurutkan ulang dengan seret-dan-lepas
- Melihat alamat mana yang memiliki setiap opsi

**4. Pengaturan Regional**
- Konfigurasi zona waktu
- Pemilihan negara/wilayah
- Mempengaruhi perhitungan tanggal dan laporan

---

## 💬 Komunikasi & Pesan

### Sistem Komunikasi Dalam Aplikasi

**Jenis Pesan:**

**1. Pesan Penginjil**
- Dari penginjil ke konduktor/admin
- Dilampirkan ke peta tertentu
- Umpan balik spesifik wilayah
- Pengiriman real-time

**2. Pesan Konduktor**
- Komunikasi administratif
- Instruksi spesifik peta
- Pesan koordinasi

**3. Pesan Admin**
- Dapat disematkan untuk visibilitas
- Pelacakan status baca
- Pengumuman penting
- Siaran ke penginjil

**4. Pesan Disematkan**
- Tetap terlihat hingga dilepas
- Pelacakan baca per pengguna
- Email otomatis ke pengguna terkait
- Tampilan prioritas

**Notifikasi Email:**

Ringkasan email otomatis dikirim untuk:
- **Setiap 30 menit:** Pesan belum dibaca ke administrator
- **Setiap 30 menit:** Pesan admin disematkan ke penginjil
- **Setiap 1 jam:** Pembaruan catatan ke administrator
- **Bulanan:** Laporan jemaat dengan lampiran Excel

**Template Email:**
- Template HTML profesional
- Desain responsif mobile
- Menyertakan tautan yang relevan
- Konten yang dapat disesuaikan

---

## 🔄 Kolaborasi Real-Time

### Sinkronisasi Data Langsung

**Fitur Real-Time:**

**Langganan SSE:**
- Pembaruan langsung melalui Server-Sent Events (SSE)
- Koneksi ulang otomatis saat terputus
- Latensi rendah (<100ms tipikal)

**Pengeditan Bersamaan:**
- Beberapa pengguna dapat bekerja secara bersamaan
- Tidak ada konflik atau kehilangan data
- Strategi tulis-terakhir-menang
- Resolusi berbasis timestamp

**Penyegaran Otomatis:**
- Data diperbarui segera saat diubah
- Tidak perlu menyegarkan halaman secara manual
- Indikator visual untuk pembaruan
- Pembaruan UI optimis

**Deteksi Visibilitas:**
- Secara otomatis menyegarkan saat tab menjadi terlihat
- Menghemat bandwidth saat tab tersembunyi
- Mencegah data basi

**Skenario Kolaborasi:**
- Admin membuat wilayah saat konduktor melihat daftar → Muncul langsung
- Penginjil menandai unit selesai → Admin melihat pembaruan kemajuan secara langsung
- Beberapa konduktor mengelola wilayah berbeda → Tidak ada konflik
- Pembaruan statistik real-time saat pekerjaan berlangsung

---

## 📱 Progressive Web App (PWA)

### Pengalaman Aplikasi Native

**Kemampuan PWA:**

**Dapat Diinstal:**
- Tambahkan ke layar beranda (mobile)
- Instal sebagai aplikasi desktop
- Muncul di peluncur aplikasi
- Mode layar penuh
- Ikon aplikasi kustom

**Instalasi Desktop:**
```
Kunjungi URL → Menu browser → "Instal aplikasi"
```

**Instalasi Mobile:**
```
Kunjungi URL → Bagikan → "Tambahkan ke layar beranda"
```

**Dukungan Offline:**
- Aset statis di-cache melalui service worker
- Lanjutkan melihat data yang di-cache saat offline
- Sinkronisasi otomatis saat koneksi dipulihkan
- Pembaruan latar belakang

**Manfaat Kinerja:**
- Waktu muat lebih cepat (aset di-cache)
- Penggunaan data berkurang
- Bekerja pada koneksi lambat
- Animasi halus

**Fitur Native:**
- Notifikasi push (direncanakan)
- Sinkronisasi latar belakang
- Penanganan file native
- Integrasi API Bagikan

---

## 🌍 Internasionalisasi

### Dukungan Multi-Bahasa

**Bahasa yang Didukung:**
1. **Inggris** (en) - Bahasa utama
2. **Spanyol** (es) - Español
3. **Indonesia** (id) - Bahasa Indonesia
4. **Jepang** (ja) - 日本語
5. **Korea** (ko) - 한국어
6. **Melayu** (ms) - Bahasa Melayu
7. **Tionghoa** (zh) - 中文
8. **Tamil** (ta) - தமிழ்

**Fitur i18n:**
- Pemilih bahasa di navigasi
- Preferensi persisten (localStorage)
- Deteksi bahasa browser
- Terjemahan UI lengkap
- Pergantian dinamis (tanpa reload)
- Mudah menambahkan bahasa baru

**Catatan Rilis yang Dilokalisasi:**
- Catatan rilis dalam aplikasi ditampilkan dalam bahasa yang dipilih pengguna
- Mendukung 8 bahasa
- Modal ditampilkan secara otomatis saat versi aplikasi baru terdeteksi, sehingga penerbit selalu mengetahui hal baru tanpa meninggalkan aplikasi

**Menambahkan Bahasa Baru:**
1. Buat file terjemahan di `src/i18n/locales/[lang].json`
2. Salin struktur dari `en.json`
3. Terjemahkan semua string
4. Daftarkan di `src/i18n/index.ts`

---

## 🌙 Mode Gelap

### Tema Sadar Sistem

**Opsi Tema:**
- **Mode Terang:** Tema terang tradisional
- **Mode Gelap:** Tema gelap ramah mata
- **Sistem:** Mengikuti preferensi OS

**Manfaat:**
- Mengurangi ketegangan mata
- Masa pakai baterai lebih baik (layar OLED)
- Kepatuhan aksesibilitas
- Pengalaman pengguna modern

**Implementasi:**
- Properti kustom CSS
- Transisi halus
- Preferensi persisten
- Tidak ada kilatan tema yang salah

---

## 📈 Pelaporan & Analitik

### Statistik Komprehensif

**Laporan Bulanan Jemaat:**
- **Jadwal:** Tanggal 1 setiap bulan pada tengah malam
- **Format:** Workbook Excel (.xlsx)
- **Pengiriman:** Email ke semua administrator

**Konten Laporan:**

**1. Ikhtisar Jemaat**
- Nama dan detail jemaat
- Periode pelaporan
- Statistik keseluruhan

**2. Rincian Wilayah**
- Persentase penyelesaian per wilayah
- Jumlah peta per wilayah
- Distribusi alamat
- Kemajuan dari waktu ke waktu

**3. Statistik Alamat**
- Total alamat berdasarkan jenis
- Distribusi status (selesai, belum selesai, dll.)
- Rincian dapat dihitung vs tidak dapat dihitung
- Jumlah DNC dan tidak valid

**4. Penugasan Penginjil**
- Jumlah penugasan aktif
- Riwayat penugasan
- Cakupan wilayah

**5. Pelacakan Kemajuan**
- Perbandingan bulan ke bulan
- Tren dan wawasan
- Area yang perlu perhatian

**Fitur Excel:**
- Format profesional
- Bagan dan grafik
- Data siap pivot
- Tata letak ramah cetak

**Dashboard Real-Time:**
- Statistik langsung di panel admin
- Pelacakan penyelesaian wilayah
- Distribusi status alamat
- Pemantauan penugasan aktif
- Feed aktivitas terbaru

**Ringkasan Laporan yang Dihasilkan AI:**
- Laporan jemaat bulanan dapat menyertakan ringkasan yang dihasilkan AI tentang tren utama dari catatan dan pesan wilayah
- Didukung oleh **OpenAI gpt-4o-mini**
- Dikontrol oleh feature flag `enable-report-ai-summary` — opt-in per deployment sehingga jemaat memilih apakah akan mengaktifkannya
- Memerlukan `OPENAI_API_KEY` yang dikonfigurasi di lingkungan deployment

---

## 🔐 Fitur Keamanan

### Keamanan Tingkat Enterprise

**Metode Autentikasi:**

**1. Email/Kata Sandi**
- Hashing kata sandi Bcrypt (biaya: 10)
- Minimum 6 karakter
- Verifikasi email diperlukan
- Reset kata sandi melalui email

**2. OAuth2 (Google)**
- Alur PKCE untuk keamanan
- Pembuatan pengguna otomatis
- Tidak perlu manajemen kata sandi
- Penanganan token aman

**3. Kata Sandi Satu Kali (OTP)**
- Kode 4 digit opsional
- Validitas 180 detik
- Lapisan keamanan tambahan
- Pengiriman email

**4. Autentikasi Multi-Faktor (MFA)**
- Dukungan TOTP
- Keamanan yang ditingkatkan untuk admin
- Sesi 1800 detik
- Kode cadangan

**Kontrol Akses:**

**Kontrol Akses Berbasis Peran (RBAC):**
- Izin yang terperinci
- Isolasi tingkat jemaat
- Arsitektur multi-tenant
- Aman secara default

**Akses Berbasis Tautan:**
- Akses anonim sementara
- Autentikasi berbasis token
- Kedaluwarsa otomatis
- Akses yang dapat dicabut

**Praktik Terbaik Keamanan:**
- Penegakan HTTPS
- Konfigurasi CORS
- Pembatasan laju
- Pencegahan injeksi SQL (kueri parameter)
- Perlindungan XSS
- Perlindungan CSRF
- Validasi input
- Sanitasi output

**Jejak Audit:**
- Pencatatan tindakan pengguna
- Pelacakan perubahan
- Siapa memperbarui apa kapan
- Pencatatan alamat IP
- Percobaan autentikasi

**Manajemen Siklus Hidup Pengguna:**

Penanganan otomatis akun pengguna tidak aktif dan belum disediakan, selaras dengan kontrol keamanan **NIST AC-2** dan **AC-2(3)**:

- **Pengguna Belum Disediakan:** Akun yang belum pernah menyelesaikan pengaturan menerima email peringatan bertahap sebelum otomatis dinonaktifkan dan akhirnya dihapus
- **Pengguna Tidak Aktif:** Akun tanpa aktivitas login selama periode yang dapat dikonfigurasi diperingatkan, kemudian dinonaktifkan
- Job latar belakang berjalan setiap hari untuk mengevaluasi dan menerapkan kebijakan siklus hidup — tidak diperlukan intervensi manual
- Menjaga daftar pengguna jemaat tetap akurat dan meminimalkan permukaan serangan dari akun yang tidak aktif

**Pelacakan Error:**
- Integrasi Sentry
- Pemantauan error real-time
- Stack trace untuk debugging
- Pelacakan rilis
- Pemantauan kinerja

---

## 🗺️ Pemetaan Interaktif

### Integrasi Leaflet + OpenStreetMap

**Fitur Pemetaan:**

**Fitur Dasar:**
- Pan dan zoom interaktif
- Detail tingkat jalan
- Opsi citra satelit
- Penanda kustom untuk alamat
- Visualisasi batas wilayah
- Penanda kluster untuk area padat

**Navigasi:**
- Mendapatkan petunjuk arah ke alamat mana pun
- Integrasi dengan aplikasi peta perangkat
- Perhitungan jarak
- Perencanaan rute optimal
- Dukungan lokasi GPS

**Layanan Rute (Petunjuk Arah Turn-by-Turn):**
- Rute bawaan melalui **OpenRouteService**
- Mendukung tiga mode perjalanan: **berkendara**, **berjalan**, dan **bersepeda**
- Penerbit dapat meminta petunjuk arah ke alamat mana pun langsung dari tampilan peta
- Geocoding alamat ditangani oleh **LocationIQ**

![Panel rute menampilkan opsi mode perjalanan dan rute yang diplot di peta](../assets/screenshots/map_routing.png)

**Geolokasi:**
- Mendeteksi lokasi saat ini
- Pusatkan peta pada pengguna
- Penugasan berbasis kedekatan
- Jarak ke wilayah

**Penanda Kustom:**
- Kode warna berbasis status
- Ikon kustom
- Popup info
- Interaksi klik
- Pengelompokan penanda

**Kinerja:**
- Pemuatan tile lazy
- Rendering penanda yang efisien
- Pemuatan berbasis viewport
- Dioptimalkan untuk mobile

**Keuntungan:**
- Tidak ada batas API
- Gratis untuk digunakan
- Tidak perlu penagihan
- Data OpenStreetMap
- Caching tile offline penuh dimungkinkan

---

## 📧 Sistem Email

### Notifikasi Email Otomatis

**Layanan Email:** MailerSend API v1

**Jenis Email:**

**1. Notifikasi Pesan**
- Jadwal: Setiap 30 menit
- Penerima: Administrator
- Konten: Pesan penginjil yang belum dibaca
- Template: HTML profesional

**2. Instruksi Admin**
- Jadwal: Setiap 30 menit
- Penerima: Penginjil (pengguna non-admin)
- Konten: Pesan administrator yang disematkan
- Template: Format instruksi

**3. Pembaruan Catatan**
- Jadwal: Setiap 1 jam
- Penerima: Administrator
- Konten: Perubahan catatan alamat
- Template: Format ringkasan catatan

**4. Laporan Bulanan**
- Jadwal: Tanggal 1 bulan
- Penerima: Administrator
- Konten: Laporan statistik dengan ringkasan wilayah yang dihasilkan AI opsional
- Lampiran: Workbook Excel
- Ringkasan AI: Saat diaktifkan melalui flag fitur `enable-report-ai-summary` dan `OPENAI_API_KEY` dikonfigurasi, setiap lembar wilayah menyertakan ikhtisar yang dihasilkan AI tentang tren utama dan observasi penting

**5. Email Sistem**
- Verifikasi email
- Reset kata sandi
- Peringatan akun
- Notifikasi autentikasi

**Fitur Template:**
- HTML responsif mobile
- Desain profesional
- CSS inline untuk kompatibilitas
- Fallback teks biasa
- Tautan berhenti berlangganan
- Piksel pelacakan (opsional)

---

## 🔄 Pekerjaan Latar Belakang

### Pemrosesan Tugas Otomatis

**Penjadwal Pekerjaan:** Berbasis Cron dengan flag fitur LaunchDarkly

**Pekerjaan Terjadwal:**

| Pekerjaan | Jadwal | Tujuan |
|-----------|--------|--------|
| Pembersihan Penugasan | Setiap 5 menit | Menghapus penugasan kedaluwarsa |
| Agregat Wilayah | Setiap 10 menit | Memperbarui statistik kemajuan |
| Pemrosesan Pesan | Setiap 30 menit | Mengirim notifikasi pesan |
| Instruksi | Setiap 30 menit | Mengirim pesan admin |
| Pembaruan Catatan | Setiap 1 jam | Memberi tahu perubahan catatan |
| Laporan Bulanan | Tanggal 1 bulan | Menghasilkan laporan Excel (dengan ringkasan AI opsional) |
| Pengguna Belum Disediakan | Harian 01:00 UTC | Menegakkan siklus hidup pengguna (peringatan → nonaktif → hapus) |
| Pengguna Tidak Aktif | Harian 01:30 UTC | Memperingatkan dan menonaktifkan akun tidak aktif |

**Kontrol Flag Fitur:**
- Aktifkan/nonaktifkan pekerjaan tanpa deployment
- Kemampuan peluncuran bertahap
- Pengujian A/B
- Tombol darurat mati
- Kontrol per jemaat

**Pemantauan Pekerjaan:**
- Log eksekusi
- Pelacakan sukses/gagal
- Metrik durasi
- Peringatan error
- Logika percobaan ulang

---

## 🎨 Antarmuka Pengguna

### Desain Modern dan Responsif

**Framework UI:** Bootstrap 5.3.8 + Komponen Kustom

**Prinsip Desain:**
- Pendekatan mobile-first
- Interaksi ramah sentuh
- Kepatuhan aksesibilitas (WCAG 2.1)
- Bahasa visual yang konsisten
- Navigasi intuitif

**Elemen UI Utama:**

**Navigasi:**
- Navbar responsif
- Pemilih jemaat
- Speed dial (tombol aksi mengambang)
- Navigasi breadcrumb
- Tautan cepat

**Formulir:**
- Validasi inline
- Pesan error
- Simpan otomatis draft
- Pintasan keyboard
- Pengungkapan progresif

**Tabel:**
- Kolom yang dapat diurutkan
- Pengeditan inline
- Aksi massal
- Paginasi
- Pencarian dan filter
- Kemampuan ekspor

**Modal:**
- 24+ modal khusus
- Navigasi keyboard
- Manajemen fokus
- Dukungan penumpukan
- Dioptimalkan untuk mobile

**Umpan Balik:**
- Notifikasi toast
- Indikator pemuatan
- Bilah kemajuan
- Konfirmasi sukses
- Peringatan error

**Breakpoint Responsif:**
- Mobile: <768px
- Tablet: 768px-1024px
- Desktop: >1024px
- Desktop besar: >1440px

---

## 🚀 Kinerja

### Dioptimalkan untuk Kecepatan

**Optimasi Frontend:**
- Pemisahan kode berdasarkan rute
- Pemuatan komponen lazy
- Optimasi gambar
- Minifikasi CSS
- Bundling JavaScript
- Tree shaking
- Caching service worker

**Kompiler React 19:**
- Memoization otomatis
- Tidak perlu optimasi manual
- Ukuran bundle lebih kecil
- Rendering lebih cepat

**Optimasi Backend:**
- Pengindeksan database (25+ indeks)
- Optimasi kueri
- Connection pooling
- Caching agregat
- Batching transaksi
- Kompresi Gzip

**Strategi Caching:**
- Caching browser (aset statis)
- Service worker (aset offline)
- Caching edge CDN
- Caching kueri database

**Target Kinerja:**
- **First Contentful Paint:** <1.5 detik
- **Time to Interactive:** <3 detik
- **Skor Lighthouse:** >90
- **Core Web Vitals:** Semua hijau

---

## 🔌 API & Integrasi

### API RESTful + SSE

**Fitur API:**
- Endpoint RESTful
- Payload JSON
- Autentikasi JWT
- Dukungan paginasi
- Penyaringan dan pengurutan
- Pembatasan laju
- Dukungan CORS

**SSE (Server-Sent Events):**
- Langganan real-time
- Koneksi ulang otomatis
- Latensi rendah
- Pembaruan berbasis event

**Opsi Integrasi:**
- PocketBase SDK (JavaScript/TypeScript)
- Permintaan HTTP langsung
- Klien SSE
- Integrasi OAuth2
- Dukungan Webhook (direncanakan)

---

## 📊 Manajemen Data

### Lapisan Data yang Tangguh

**Database:** SQLite melalui PocketBase

**Fitur:**
- Transaksi ACID
- Constraint foreign key
- Penghapusan cascade
- Dukungan field JSON
- Pencarian teks penuh
- Backup otomatis

**Import/Ekspor Data:**
- Ekspor Excel (laporan)
- Ekspor CSV (direncanakan)
- Ekspor JSON melalui API
- Backup/restore

**Integritas Data:**
- Integritas referensial
- Aturan validasi
- Constraint unik
- Constraint cek
- ID yang dihasilkan otomatis

---

## 🎯 Roadmap Masa Depan

### Fitur yang Direncanakan

**Jangka Pendek (Q1-Q2 2025):**
- Notifikasi push
- Import/ekspor CSV
- Penyaringan lanjutan
- Laporan kustom
- Operasi batch
- Tata letak cetak

**Jangka Menengah (Q3-Q4 2025):**
- Aplikasi mobile (iOS/Android)
- Integrasi webhook
- Field kustom
- Analitik lanjutan
- Alat kolaborasi tim
- Marketplace integrasi

**Jangka Panjang (2026+):**
- Wawasan bertenaga AI
- Analitik prediktif
- Perintah suara
- Navigasi realitas tertambah
- Jejak audit blockchain
- Hierarki multi-organisasi

---

## 📚 Dokumentasi & Dukungan

### Sumber Daya Komprehensif

**Dokumentasi:**
- [Memulai](getting-started.md)
- [Panduan Pengguna](user-guide.md)
- [Arsitektur](architecture.md)
- [Panduan Deployment](deployment.md)
- [FAQ](faq.md)

**Saluran Dukungan:**
- GitHub Issues
- Forum Komunitas
- Dukungan Email (layanan yang dihosting)
- Situs Dokumentasi
- Tutorial Video (direncanakan)

---

## ✅ Kesimpulan

Ministry Mapper menyediakan solusi lengkap dan modern untuk manajemen wilayah digital dengan:

- ✅ Manajemen wilayah dan alamat yang komprehensif
- ✅ Kolaborasi real-time
- ✅ Kontrol akses berbasis peran yang aman
- ✅ Dukungan multi-bahasa
- ✅ Kemampuan progressive web app
- ✅ Pelaporan dan notifikasi otomatis
- ✅ Pemetaan interaktif
- ✅ Keamanan tingkat enterprise
- ✅ Open-source dan dapat di-self-host
- ✅ Pengembangan dan dukungan aktif

**Siap untuk memulai?**
- [Panduan Memulai Cepat](getting-started.md)
- [Lihat Demo](https://ministry-mapper.com)
- [Instruksi Self-Host](deployment.md)
