# Gambaran Keseluruhan Ciri

## Pengenalan

Ministry Mapper adalah platform pengurusan wilayah digital yang komprehensif yang direka untuk sidang keagamaan. Halaman ini menyediakan gambaran keseluruhan terperinci tentang semua ciri dan keupayaan.

---

## 🗺️ Pengurusan Wilayah

### Mengatur Kawasan Geografi

**Cipta dan Urus Wilayah:**
- Cipta wilayah tanpa had bagi setiap sidang
- Tetapkan kod dan penerangan unik
- Atur wilayah mengikut keutamaan dengan penjujukan seret dan lepas
- Jejak peratusan penyelesaian secara automatik
- Tetapkan semula status wilayah untuk memulakan semula penjejakan
- Pindahkan peta antara wilayah dengan mudah

**Statistik Wilayah:**
- Penjejakan penyelesaian agregat
- Penunjuk kemajuan visual
- Kemas kini statistik masa nyata
- Pecahan mengikut wilayah
- Penjejakan sejarah

**Kes Penggunaan:** Bahagikan kawasan perkhidmatan sidang anda kepada wilayah yang boleh diurus untuk kerja khidmat lapangan yang teratur.

---

## 🏢 Pengurusan Peta & Alamat

### Penjejakan Lokasi Interaktif

**Jenis Peta:**

**1. Alamat Awam (Bangunan Berbilang Tingkat)**
- Sokongan untuk bangunan berbilang tingkat
- Tambah/buang tingkat secara dinamik
- Setiap tingkat mengandungi berbilang unit
- Aplikasi kod alamat automatik ke semua tingkat
- Organisasi mengikut tingkat

**2. Alamat Persendirian (Kediaman Satu Tingkat)**
- Rumah individu atau hartanah berdiri sendiri
- Struktur lebih mudah tanpa pembahagian tingkat
- Kemasukan alamat pantas

**Ciri Peta:**
- Lampirkan koordinat GPS untuk lokasi tepat
- Tambah penerangan terperinci
- Nombor jujukan untuk lawatan teratur
- Integrasi peta visual dengan Leaflet/OpenStreetMap
- Dapatkan arah ke mana-mana alamat
- Sokongan geolokasi

**Operasi Alamat:**
- Tambah/buang kod alamat
- Jujukan semula alamat untuk laluan optimum
- Operasi pukal untuk kecekapan
- Salin alamat merentas tingkat
- Namakan semula dan atur semula

**Tambah Alamat Semasa Pemetaan** *(v1.33+)*
- Penerbit boleh menambah alamat yang tiada terus semasa pemetaan melalui kad tambah di hujung senarai alamat — tiada campur tangan pentadbir diperlukan
- Kad **"+"** muncul di hujung senarai alamat sebagai titik masuk tambah pantas
- Sesuai untuk jemaat yang masih membina rekod wilayah mereka

![Kad "+" di hujung senarai alamat untuk menambah alamat yang tiada](../assets/screenshots/add_more_add.png)

---

## 📊 Penjejakan Unit/Isi Rumah

### Pengurusan Status Lawatan Komprehensif

**Jenis Status:**

| Status | Penerangan | Kesan pada Kemajuan |
|--------|-------------|-------------------|
| **Belum Selesai** | Keadaan awal, belum dilawati | Dikira dalam belum selesai |
| **Selesai** | Berjaya diselesaikan | Dikira sebagai selesai |
| **Tiada di Rumah** | Tiada orang di rumah, boleh cuba semula | Dikira selepas percubaan maksimum |
| **Jangan Panggil (DNC)** | Isi rumah meminta tiada lawatan | Dikecualikan dari pengiraan |
| **Tidak Sah** | Unit tidak wujud | Dikecualikan dari pengiraan |

**Penjejakan Tiada di Rumah:**
- Percubaan maksimum yang boleh dikonfigurasi bagi setiap sidang
- Kenaikan kaunter automatik
- Dianggap sebagai "selesai" selepas percubaan maksimum
- Menghalang lawatan semula tanpa had

**Logik Penyelesaian:**
```
Selesai apabila:
  - Status = Selesai, ATAU
  - Status = Tiada di Rumah DAN percubaan >= percubaan_maksimum

Kemajuan % = Selesai / Jumlah Unit Boleh Dikira × 100
```

**Pilihan Isi Rumah:**
- Klasifikasi alamat yang boleh disesuaikan
- Jenis boleh dikira vs tidak boleh dikira
- Pilihan lalai untuk alamat baharu
- Berbilang pilihan bagi setiap alamat
- Mempengaruhi pengiraan kemajuan

**Nota Lawatan:**
- Tambah nota terperinci bagi setiap alamat
- Penjejakan cap masa automatik
- Jejak siapa yang membuat kemas kini
- Pemberitahuan e-mel untuk perubahan nota
- Paparan nota sejarah

---

## 🔗 Sistem Tugasan (Pautan Penerbit)

### Akses Selamat, Terhad Masa

**Ciri Pautan:**
- **Terhad Masa:** Tamat tempoh yang boleh dikonfigurasi (lalai 24 jam)
- **Berasaskan Token:** Token akses selamat 20 aksara
- **Tiada Akaun Diperlukan:** Penerbit mengakses melalui pautan sahaja
- **Dua Jenis:**
  - Tugasan biasa untuk kerja wilayah biasa
  - Tugasan peribadi untuk pelayanan peribadi

**Algoritma Tugasan Pintar:**

Apabila meminta tugasan baharu, sistem:

1. **Faktor Utama:** Memilih peta dengan tugasan aktif paling sedikit
   - Menghalang lebihan beban peta tunggal
   - Mengimbangi beban kerja

2. **Faktor Sekunder:** Mengutamakan peta yang lebih dekat secara geografi
   - Menggunakan koordinat GPS
   - Ambang 50 meter
   - Mengurangkan masa perjalanan

3. **Faktor Tertier:** Mengutamakan kemajuan penyelesaian yang lebih rendah
   - Memastikan penyelesaian wilayah yang seimbang
   - Menghalang sesetengah kawasan diabaikan

**Aliran Kerja Tugasan:**
```
1. Konduktor menjana pautan tugasan
2. Pautan dikongsi dengan penerbit (QR, e-mel, mesej)
3. Penerbit mengakses /map/:assignmentId
4. Sistem mengesahkan token dan tamat tempoh
5. Penerbit mengemas kini status alamat dalam masa nyata
6. Pautan tamat tempoh secara automatik
7. Pentadbir menerima kemas kini masa nyata
```

**Pengurusan Tugasan:**
- Lihat tugasan aktif bagi setiap peta
- Lihat siapa yang mempunyai wilayah mana
- Jejak sejarah tugasan
- Pemadaman tugasan manual
- Pembersihan automatik pautan tamat tempoh (setiap 5 minit)

---

## 👥 Pengurusan Pengguna & Kawalan Akses

### Sistem Kebenaran Berasaskan Peranan

**Empat Peranan Pengguna:**

#### 1. Penerbit (Paling Terhad)
**Kaedah Akses:** Pautan tugasan terhad masa sahaja

**Kebenaran:**
- ✅ Lihat peta yang ditugaskan sahaja
- ✅ Kemas kini status alamat/unit
- ✅ Tambah nota lawatan
- ✅ Hantar mesej maklum balas
- ❌ Tidak boleh lihat wilayah lain
- ❌ Tidak boleh cipta wilayah
- ❌ Tidak boleh urus pengguna

**Kes Penggunaan:** Penerbit khidmat lapangan yang bekerja di wilayah

---

#### 2. Baca Sahaja
**Kaedah Akses:** Akaun pengguna dengan peranan baca sahaja

**Kebenaran:**
- ✅ Lihat semua data sidang
- ✅ Lihat wilayah dan peta
- ✅ Lihat maklumat alamat
- ✅ Lihat mesej
- ❌ Tidak boleh ubah suai apa-apa
- ❌ Tidak boleh cipta tugasan
- ❌ Tidak boleh urus pengguna

**Kes Penggunaan:** Pemerhati, juruaudit, ahli jawatankuasa perkhidmatan

---

#### 3. Konduktor (Peringkat Pertengahan)
**Kaedah Akses:** Akaun pengguna dengan peranan konduktor

**Kebenaran:**
- ✅ Semua kebenaran Baca Sahaja
- ✅ Cipta dan urus wilayah
- ✅ Cipta dan urus peta
- ✅ Tambah/kemas kini alamat
- ✅ Jana pautan tugasan penerbit
- ✅ Lihat semua data sidang
- ✅ Urus pilihan isi rumah
- ✅ Tetapkan semula wilayah
- ❌ Tidak boleh urus peranan pengguna
- ❌ Tidak boleh konfigurasi tetapan sidang
- ❌ Tidak boleh padam pengguna

**Kes Penggunaan:** Penyelia khidmat lapangan, penyelaras wilayah

---

#### 4. Pentadbir (Akses Penuh)
**Kaedah Akses:** Akaun pengguna dengan peranan pentadbir

**Kebenaran:**
- ✅ Semua kebenaran Konduktor
- ✅ Cipta dan urus pengguna
- ✅ Tetapkan peranan pengguna
- ✅ Konfigurasi tetapan sidang
- ✅ Urus pilihan isi rumah
- ✅ Padam wilayah dan peta
- ✅ Padam akaun pengguna
- ✅ Lihat semua tugasan dan mesej
- ✅ Konfigurasi percubaan tiada di rumah maksimum
- ✅ Tetapkan tempoh tamat pautan
- ✅ Akses log sistem

**Kes Penggunaan:** Hamba wilayah, pentadbir sistem

---

### Pengurusan Akaun Pengguna

**Ciri:**
- Pengesahan berasaskan e-mel
- Pengesahan e-mel diperlukan
- Fungsi tetapan semula kata laluan
- Pengesahan OAuth2 Google
- Sokongan pengesahan berbilang faktor (MFA)
- Sokongan kata laluan sekali (OTP)
- Lumpuhkan/aktifkan akaun
- Penjejakan log masuk terakhir
- Sokongan berbilang sidang (peranan berbeza bagi setiap sidang)

---

## 🛠️ Tetapan Sidang

### Konfigurasi Boleh Disesuaikan

**Tetapan Teras:**

**1. Percubaan Tiada di Rumah Maksimum**
- Tetapkan berapa banyak lawatan "tiada di rumah" sebelum dianggap selesai
- Biasa: 2-3 percubaan
- Mempengaruhi pengiraan kemajuan
- Khusus sidang

**2. Tamat Tempoh Pautan Tugasan**
- Konfigurasi berapa lama pautan kekal sah
- Lalai: 24 jam
- Boleh disesuaikan bagi setiap sidang
- Pembersihan automatik pautan tamat tempoh

**3. Pilihan Isi Rumah**
- Cipta klasifikasi alamat tersuai
- Contoh: "Kediaman", "Perniagaan", "Tinggi", "Bahasa: Sepanyol"
- Tandakan pilihan sebagai "boleh dikira" (mempengaruhi penyelesaian %)
- Tetapkan pilihan lalai untuk alamat baharu
- Susun semula dengan seret dan lepas
- Lihat alamat mana yang mempunyai setiap pilihan

**4. Tetapan Serantau**
- Konfigurasi zon masa
- Pemilihan negara/rantau
- Mempengaruhi pengiraan tarikh dan laporan

---

## 💬 Komunikasi & Pemesejan

### Sistem Komunikasi Dalam Aplikasi

**Jenis Mesej:**

**1. Mesej Penerbit**
- Dari penerbit kepada konduktor/pentadbir
- Dilampirkan kepada peta tertentu
- Maklum balas khusus wilayah
- Penghantaran masa nyata

**2. Mesej Konduktor**
- Komunikasi pentadbiran
- Arahan khusus peta
- Mesej penyelarasan

**3. Mesej Pentadbir**
- Boleh disemat untuk keterlihatan
- Penjejakan status baca
- Pengumuman penting
- Siaran kepada penerbit

**4. Mesej Disemat**
- Kekal kelihatan sehingga dilepaskan
- Penjejakan baca bagi setiap pengguna
- E-mel automatik kepada pengguna berkaitan
- Paparan keutamaan

**Pemberitahuan E-mel:**

Ringkasan e-mel automatik dihantar untuk:
- **Setiap 30 minit:** Mesej belum dibaca kepada pentadbir
- **Setiap 30 minit:** Mesej pentadbir disemat kepada penerbit
- **Setiap 1 jam:** Kemas kini nota kepada pentadbir
- **Bulanan:** Laporan sidang dengan lampiran Excel

**Templat E-mel:**
- Templat HTML profesional
- Reka bentuk responsif mudah alih
- Termasuk pautan berkaitan
- Kandungan boleh disesuaikan

---

## 🔄 Kerjasama Masa Nyata

### Penyegerakan Data Langsung

**Ciri Masa Nyata:**

**Langganan SSE:**
- Kemas kini langsung melalui Server-Sent Events (SSE)
- Penyambungan semula automatik apabila terputus
- Kependaman rendah (<100ms biasa)

**Pengeditan Serentak:**
- Berbilang pengguna boleh bekerja serentak
- Tiada konflik atau kehilangan data
- Strategi tulis terakhir menang
- Penyelesaian berasaskan cap masa

**Segar Semula Automatik:**
- Data dikemas kini serta-merta apabila berubah
- Tiada muat semula halaman manual diperlukan
- Penunjuk visual untuk kemas kini
- Kemas kini UI optimistik

**Pengesanan Keterlihatan:**
- Menyegar semula secara automatik apabila tab menjadi kelihatan
- Menjimatkan jalur lebar apabila tab tersembunyi
- Menghalang data lapuk

**Senario Kerjasama:**
- Pentadbir mencipta wilayah semasa konduktor melihat senarai → Muncul serta-merta
- Penerbit menandakan unit selesai → Pentadbir melihat kemas kini kemajuan langsung
- Berbilang konduktor mengurus wilayah berbeza → Tiada konflik
- Kemas kini statistik masa nyata semasa kerja berjalan

---

## 📱 Aplikasi Web Progresif (PWA)

### Pengalaman Aplikasi Asli

**Keupayaan PWA:**

**Boleh Dipasang:**
- Tambah ke skrin utama (mudah alih)
- Pasang sebagai aplikasi desktop
- Muncul dalam pelancar aplikasi
- Mod skrin penuh
- Ikon aplikasi tersuai

**Pemasangan Desktop:**
```
Lawati URL → Menu pelayar → "Pasang aplikasi"
```

**Pemasangan Mudah Alih:**
```
Lawati URL → Kongsi → "Tambah ke skrin utama"
```

**Sokongan Luar Talian:**
- Aset statik dicache melalui service worker
- Terus melihat data yang dicache apabila luar talian
- Penyegerakan automatik apabila sambungan dipulihkan
- Kemas kini latar belakang

**Faedah Prestasi:**
- Masa muat lebih pantas (aset dicache)
- Penggunaan data dikurangkan
- Berfungsi pada sambungan perlahan
- Animasi lancar

**Ciri Asli:**
- Pemberitahuan tolak (dirancang)
- Penyegerakan latar belakang
- Pengendalian fail asli
- Integrasi API kongsi

---

## 🌍 Pengantarabangsaan

### Sokongan Berbilang Bahasa

**Bahasa Disokong:**
1. **English** (en) - Bahasa utama
2. **Spanish** (es) - Español
3. **Indonesian** (id) - Bahasa Indonesia
4. **Japanese** (ja) - 日本語
5. **Korean** (ko) - 한국어
6. **Malay** (ms) - Bahasa Melayu
7. **Chinese** (zh) - 中文
8. **Tamil** (ta) - தமிழ்

**Ciri i18n:**
- Pemilih bahasa dalam navigasi
- Keutamaan berterusan (localStorage)
- Pengesanan bahasa pelayar
- Terjemahan UI lengkap
- Penukaran dinamik (tanpa muat semula)
- Mudah untuk menambah bahasa baharu

**Nota Keluaran Setempat:**
- Log perubahan dalam aplikasi dipaparkan dalam bahasa yang dipilih pengguna
- Menyokong kesemua 8 bahasa
- Modal dipaparkan secara automatik apabila versi aplikasi baharu dikesan, supaya penerbit sentiasa mengetahui perkara baharu tanpa meninggalkan aplikasi

**Menambah Bahasa Baharu:**
1. Cipta fail terjemahan dalam `src/i18n/locales/[lang].json`
2. Salin struktur dari `en.json`
3. Terjemahkan semua rentetan
4. Daftarkan dalam `src/i18n/index.ts`

---

## 🌙 Mod Gelap

### Tema Sedar Sistem

**Pilihan Tema:**
- **Mod Terang:** Tema terang tradisional
- **Mod Gelap:** Tema gelap mesra mata
- **Sistem:** Mengikut keutamaan OS

**Faedah:**
- Mengurangkan ketegangan mata
- Hayat bateri lebih baik (skrin OLED)
- Pematuhan kebolehcapaian
- Pengalaman pengguna moden

**Pelaksanaan:**
- Sifat tersuai CSS
- Peralihan lancar
- Keutamaan berterusan
- Tiada kilat tema salah

---

## 📈 Pelaporan & Analitik

### Statistik Komprehensif

**Laporan Sidang Bulanan:**
- **Jadual:** 1hb setiap bulan pada tengah malam
- **Format:** Buku kerja Excel (.xlsx)
- **Penghantaran:** E-mel kepada semua pentadbir

**Kandungan Laporan:**

**1. Gambaran Keseluruhan Sidang**
- Nama dan butiran sidang
- Tempoh pelaporan
- Statistik keseluruhan

**2. Pecahan Wilayah**
- Peratusan penyelesaian bagi setiap wilayah
- Bilangan peta bagi setiap wilayah
- Pengedaran alamat
- Kemajuan dari semasa ke semasa

**3. Statistik Alamat**
- Jumlah alamat mengikut jenis
- Pengedaran status (selesai, belum selesai, dll.)
- Pecahan boleh dikira vs tidak boleh dikira
- Kiraan DNC dan tidak sah

**4. Tugasan Penerbit**
- Kiraan tugasan aktif
- Sejarah tugasan
- Liputan wilayah

**5. Penjejakan Kemajuan**
- Perbandingan bulan ke bulan
- Trend dan pandangan
- Kawasan yang memerlukan perhatian

**Ciri Excel:**
- Pemformatan profesional
- Carta dan graf
- Data sedia pivot
- Susun atur mesra cetak

**Papan Pemuka Masa Nyata:**
- Statistik langsung pada panel pentadbir
- Penjejakan penyelesaian wilayah
- Pengedaran status alamat
- Pemantauan tugasan aktif
- Suapan aktiviti terkini

**Ringkasan Laporan AI:**
- Laporan jemaah bulanan boleh menyertakan ringkasan trend utama yang dijana AI berdasarkan nota wilayah dan mesej
- Dikuasakan oleh **OpenAI gpt-5.4-mini**
- Dikawal oleh bendera ciri `enable-report-ai-summary` — pilihan masuk bagi setiap penempatan supaya jemaat memilih sama ada untuk mengaktifkannya
- Memerlukan `OPENAI_API_KEY` dikonfigurasi dalam persekitaran penempatan

---

## 🔐 Ciri Keselamatan

### Keselamatan Gred Perusahaan

**Kaedah Pengesahan:**

**1. E-mel/Kata Laluan**
- Pencincangan kata laluan Bcrypt (kos: 10)
- Minimum 6 aksara
- Pengesahan e-mel diperlukan
- Tetapan semula kata laluan melalui e-mel

**2. OAuth2 (Google)**
- Aliran PKCE untuk keselamatan
- Penciptaan pengguna automatik
- Tiada pengurusan kata laluan diperlukan
- Pengendalian token selamat

**3. Kata Laluan Sekali (OTP)**
- Kod 4 digit pilihan
- Kesahan 180 saat
- Lapisan keselamatan tambahan
- Penghantaran e-mel

**4. Pengesahan Berbilang Faktor (MFA)**
- Sokongan TOTP
- Keselamatan dipertingkatkan untuk pentadbir
- Sesi 1800 saat
- Kod sandaran

**Kawalan Akses:**

**Kawalan Akses Berasaskan Peranan (RBAC):**
- Kebenaran terperinci
- Pengasingan tahap sidang
- Seni bina berbilang penyewa
- Selamat secara lalai

**Akses Berasaskan Pautan:**
- Akses tanpa nama sementara
- Pengesahan berasaskan token
- Tamat tempoh automatik
- Akses boleh dibatalkan

**Amalan Terbaik Keselamatan:**
- Penguatkuasaan HTTPS
- Konfigurasi CORS
- Pengehadan kadar
- Pencegahan suntikan SQL (pertanyaan berparameter)
- Perlindungan XSS
- Perlindungan CSRF
- Pengesahan input
- Sanitasi output

**Jejak Audit:**
- Pengelogan tindakan pengguna
- Penjejakan perubahan
- Siapa mengemas kini apa bila
- Pengelogan alamat IP
- Percubaan pengesahan

**Pengurusan Kitaran Hayat Pengguna:**

Pengendalian automatik akaun pengguna tidak aktif dan tidak diperuntukkan, sejajar dengan kawalan keselamatan **NIST AC-2** dan **AC-2(3)**:

- **Pengguna Tidak Diperuntukkan:** Akaun yang belum pernah menyelesaikan persediaan menerima e-mel amaran berbilang peringkat sebelum dilumpuhkan secara automatik dan akhirnya dipadamkan
- **Pengguna Tidak Aktif:** Akaun tanpa aktiviti log masuk untuk tempoh yang boleh dikonfigurasi akan diperingatkan, kemudian dilumpuhkan
- Kerja latar belakang berjalan setiap hari untuk menilai dan menguatkuasakan dasar kitaran hayat — tiada campur tangan manual diperlukan
- Mengekalkan senarai pengguna jemaat yang tepat dan meminimumkan permukaan serangan dari akaun yang tidak aktif

**Penjejakan Ralat:**
- Integrasi Sentry
- Pemantauan ralat masa nyata
- Jejak tindanan untuk penyahpepijatan
- Penjejakan keluaran
- Pemantauan prestasi

---

## 🗺️ Pemetaan Interaktif

### Integrasi Leaflet + OpenStreetMap

**Ciri Pemetaan:**

**Ciri Asas:**
- Sorot dan zum interaktif
- Perincian tahap jalan
- Pilihan imejan satelit
- Penanda tersuai untuk alamat
- Visualisasi sempadan wilayah
- Penanda kelompok untuk kawasan padat

**Navigasi:**
- Dapatkan arah ke mana-mana alamat
- Integrasi dengan aplikasi peta peranti
- Pengiraan jarak
- Perancangan laluan optimum
- Sokongan lokasi GPS

**Perkhidmatan Laluan (Arah Pusingan demi Pusingan):**
- Laluan terbina dalam dikuasakan oleh **OpenRouteService**
- Menyokong tiga mod perjalanan: **memandu**, **berjalan**, dan **berbasikal**
- Penerbit boleh meminta arah ke mana-mana alamat terus dari paparan peta
- Geokod alamat dikendalikan oleh **LocationIQ**

![Panel laluan menunjukkan pilihan mod perjalanan dan laluan yang diplotkan pada peta](../assets/screenshots/map_routing.png)

**Geolokasi:**
- Kesan lokasi semasa
- Pusatkan peta pada pengguna
- Tugasan berasaskan kedekatan
- Jarak ke wilayah

**Penanda Tersuai:**
- Pengekodan warna berasaskan status
- Ikon tersuai
- Popup maklumat
- Interaksi klik
- Pengelompokan penanda

**Prestasi:**
- Pemuatan jubin secara malas
- Rendering penanda cekap
- Pemuatan berasaskan viewport
- Dioptimumkan untuk mudah alih

**Kelebihan:**
- Tiada had API
- Percuma untuk digunakan
- Tiada pengebilan diperlukan
- Data OpenStreetMap
- Caching jubin luar talian penuh mungkin

---

## 📧 Sistem E-mel

### Pemberitahuan E-mel Automatik

**Perkhidmatan E-mel:** MailerSend API v1

**Jenis E-mel:**

**1. Pemberitahuan Mesej**
- Jadual: Setiap 30 minit
- Penerima: Pentadbir
- Kandungan: Mesej penerbit belum dibaca
- Templat: HTML profesional

**2. Arahan Pentadbir**
- Jadual: Setiap 30 minit
- Penerima: Penerbit (pengguna bukan pentadbir)
- Kandungan: Mesej pentadbir disemat
- Templat: Format arahan

**3. Kemas Kini Nota**
- Jadual: Setiap 1 jam
- Penerima: Pentadbir
- Kandungan: Perubahan nota alamat
- Templat: Format ringkasan nota

**4. Laporan Bulanan**
- Jadual: 1hb bulan
- Penerima: Pentadbir
- Kandungan: Laporan statistik dengan ringkasan wilayah dijana AI pilihan
- Lampiran: Buku kerja Excel
- Ringkasan AI: Apabila diaktifkan melalui bendera ciri `enable-report-ai-summary` dan `OPENAI_API_KEY` dikonfigurasi, setiap helaian wilayah termasuk gambaran keseluruhan dijana AI tentang trend utama dan pemerhatian penting

**5. E-mel Sistem**
- Pengesahan e-mel
- Tetapan semula kata laluan
- Amaran akaun
- Pemberitahuan pengesahan

**Ciri Templat:**
- HTML responsif mudah alih
- Reka bentuk profesional
- CSS sebaris untuk keserasian
- Sandaran teks biasa
- Pautan nyahlanggan
- Piksel penjejakan (pilihan)

---

## 🔄 Kerja Latar Belakang

### Pemprosesan Tugas Automatik

**Penjadual Kerja:** Berasaskan Cron dengan bendera ciri LaunchDarkly

**Kerja Berjadual:**

| Kerja | Jadual | Tujuan |
|-----|----------|---------|
| Pembersihan Tugasan | Setiap 5 min | Padam tugasan tamat tempoh |
| Agregat Wilayah | Setiap 10 min | Kemas kini statistik kemajuan |
| Pemprosesan Mesej | Setiap 30 min | Hantar pemberitahuan mesej |
| Arahan | Setiap 30 min | Hantar mesej pentadbir |
| Kemas Kini Nota | Setiap 1 jam | Maklumkan perubahan nota |
| Laporan Bulanan | 1hb bulan | Jana laporan Excel (dengan ringkasan AI pilihan) |
| Pengguna Tidak Diperuntukkan | Harian 01:00 UTC | Kuatkuasa kitaran hayat pengguna (amaran → lumpuhkan → padam) |
| Pengguna Tidak Aktif | Harian 01:30 UTC | Amaran dan lumpuhkan akaun tidak aktif |

**Kawalan Bendera Ciri:**
- Aktifkan/lumpuhkan kerja tanpa penempatan
- Keupayaan pelancaran berperingkat
- Ujian A/B
- Suis mati kecemasan
- Kawalan bagi setiap sidang

**Pemantauan Kerja:**
- Log pelaksanaan
- Penjejakan kejayaan/kegagalan
- Metrik tempoh
- Amaran ralat
- Logik cuba semula

---

## 🎨 Antara Muka Pengguna

### Reka Bentuk Moden, Responsif

**Rangka Kerja UI:** Bootstrap 5.3.8 + Komponen Tersuai

**Prinsip Reka Bentuk:**
- Pendekatan mudah alih dahulu
- Interaksi mesra sentuh
- Pematuhan kebolehcapaian (WCAG 2.1)
- Bahasa visual konsisten
- Navigasi intuitif

**Elemen UI Utama:**

**Navigasi:**
- Bar navigasi responsif
- Pemilih sidang
- Dail pantas (butang tindakan terapung)
- Navigasi jejak remah
- Pautan pantas

**Borang:**
- Pengesahan sebaris
- Pemesejan ralat
- Simpan draf automatik
- Pintasan papan kekunci
- Pendedahan progresif

**Jadual:**
- Lajur boleh diisih
- Pengeditan sebaris
- Tindakan pukal
- Penomboran halaman
- Cari dan tapis
- Keupayaan eksport

**Modal:**
- 24+ modal khusus
- Navigasi papan kekunci
- Pengurusan fokus
- Sokongan bertindih
- Dioptimumkan untuk mudah alih

**Maklum Balas:**
- Pemberitahuan toast
- Penunjuk pemuatan
- Bar kemajuan
- Pengesahan kejayaan
- Amaran ralat

**Titik Putus Responsif:**
- Mudah alih: <768px
- Tablet: 768px-1024px
- Desktop: >1024px
- Desktop besar: >1440px

---

## 🚀 Prestasi

### Dioptimumkan untuk Kelajuan

**Pengoptimuman Frontend:**
- Pemisahan kod mengikut laluan
- Pemuatan komponen secara malas
- Pengoptimuman imej
- Pemampatan CSS
- Pengumpulan JavaScript
- Penggoncangan pokok
- Caching service worker

**Pengkompil React 19:**
- Memoisasi automatik
- Tiada pengoptimuman manual diperlukan
- Saiz bundle lebih kecil
- Rendering lebih pantas

**Pengoptimuman Backend:**
- Pengindeksan pangkalan data (25+ indeks)
- Pengoptimuman pertanyaan
- Pengumpulan sambungan
- Caching agregat
- Pengumpulan transaksi
- Pemampatan Gzip

**Strategi Caching:**
- Caching pelayar (aset statik)
- Service worker (aset luar talian)
- Caching tepi CDN
- Caching pertanyaan pangkalan data

**Sasaran Prestasi:**
- **First Contentful Paint:** <1.5s
- **Time to Interactive:** <3s
- **Skor Lighthouse:** >90
- **Core Web Vitals:** Semua hijau

---

## 🔌 API & Integrasi

### API RESTful + SSE

**Ciri API:**
- Titik akhir RESTful
- Muatan JSON
- Pengesahan JWT
- Sokongan penomboran halaman
- Penapisan dan pengisihan
- Pengehadan kadar
- Sokongan CORS

**SSE (Server-Sent Events):**
- Langganan masa nyata
- Penyambungan semula automatik
- Kependaman rendah
- Kemas kini dipacu peristiwa

**Pilihan Integrasi:**
- PocketBase SDK (JavaScript/TypeScript)
- Permintaan HTTP langsung
- Klien SSE
- Integrasi OAuth2
- Sokongan Webhook (dirancang)

---

## 📊 Pengurusan Data

### Lapisan Data Teguh

**Pangkalan Data:** SQLite melalui PocketBase

**Ciri:**
- Transaksi ACID
- Kekangan kunci asing
- Pemadaman lata
- Sokongan medan JSON
- Carian teks penuh
- Sandaran automatik

**Import/Eksport Data:**
- Eksport Excel (laporan)
- Eksport CSV (dirancang)
- Eksport JSON melalui API
- Sandaran/pemulihan

**Integriti Data:**
- Integriti rujukan
- Peraturan pengesahan
- Kekangan unik
- Kekangan semakan
- ID dijana automatik

---

## 🎯 Pelan Hala Tuju Masa Depan

### Ciri Dirancang

**Jangka Pendek (S1-S2 2025):**
- Pemberitahuan tolak
- Import/eksport CSV
- Penapisan lanjutan
- Laporan tersuai
- Operasi kelompok
- Susun atur cetak

**Jangka Sederhana (S3-S4 2025):**
- Aplikasi mudah alih (iOS/Android)
- Integrasi webhook
- Medan tersuai
- Analitik lanjutan
- Alat kerjasama pasukan
- Pasaran integrasi

**Jangka Panjang (2026+):**
- Pandangan dikuasakan AI
- Analitik ramalan
- Arahan suara
- Navigasi realiti terimbuh
- Jejak audit blockchain
- Hierarki berbilang organisasi

---

## 📚 Dokumentasi & Sokongan

### Sumber Komprehensif

**Dokumentasi:**
- [Bermula](getting-started.md)
- [Panduan Pengguna](user-guide.md)
- [Seni Bina](architecture.md)
- [Panduan Penempatan](deployment.md)
- [Soalan Lazim](faq.md)

**Saluran Sokongan:**
- GitHub Issues
- Forum Komuniti
- Sokongan E-mel (perkhidmatan dihoskan)
- Laman Dokumentasi
- Tutorial Video (dirancang)

---

## ✅ Kesimpulan

Ministry Mapper menyediakan penyelesaian lengkap dan moden untuk pengurusan wilayah digital dengan:

- ✅ Pengurusan wilayah dan alamat komprehensif
- ✅ Kerjasama masa nyata
- ✅ Kawalan akses berasaskan peranan yang selamat
- ✅ Sokongan berbilang bahasa
- ✅ Keupayaan aplikasi web progresif
- ✅ Pelaporan dan pemberitahuan automatik
- ✅ Pemetaan interaktif
- ✅ Keselamatan gred perusahaan
- ✅ Sumber terbuka dan boleh dihoskan sendiri
- ✅ Pembangunan dan sokongan aktif

**Bersedia untuk bermula?**
- [Panduan Mula Pantas](getting-started.md)
- [Lihat Demo](https://ministry-mapper.com)
- [Arahan Hos Sendiri](deployment.md)
