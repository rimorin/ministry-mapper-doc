# Selamat Datang ke Ministry Mapper

## Apakah Ministry Mapper?

Ministry Mapper adalah aplikasi web sumber terbuka yang moden untuk membantu jemaah mengurus wilayah pelayanan lapangan mereka dengan cekap. Daripada menggunakan slip wilayah kertas, Ministry Mapper menyediakan penyelesaian digital yang komprehensif untuk mengatur, menjejak, dan mengurus kerja pelayanan lapangan dari mana-mana peranti dengan sambungan internet.

## Kisah Kami

Kami memulakan Ministry Mapper pada tahun 2022 sebagai projek ringkas untuk membantu jemaah tempatan mengurus wilayah dengan lebih berkesan. Apabila lebih banyak jemaah mula melihat manfaat pengurusan wilayah digital, kami terus meningkatkan platform ini. Hari ini, Ministry Mapper digunakan oleh jemaah di Singapura dan Malaysia, membantu para penerbit kekal teratur dalam kerja pelayanan mereka dengan ciri-ciri seperti penyegerakan masa nyata, pemetaan interaktif, dan sokongan berbilang bahasa.

## Apa Yang Ministry Mapper Lakukan

Ministry Mapper menyediakan keupayaan pengurusan wilayah yang komprehensif:

### Ciri-Ciri Utama

- **🗺️ Pengurusan Wilayah**: Susun kawasan geografi kepada wilayah dengan penjejakan penyelesaian automatik
- **🏢 Pemetaan Interaktif**: Peta berasaskan Leaflet dengan integrasi OpenStreetMap, arah, dan geolokasi
- **📱 Kerjasama Masa Nyata**: Penyegerakan data langsung merentasi semua peranti melalui SSE (Server-Sent Events)
- **🌍 Sokongan Berbilang Bahasa**: 7 bahasa (Inggeris, Sepanyol, Indonesia, Jepun, Korea, Melayu, Cina)
- **👥 Akses Berasaskan Peranan**: Peranan Penerbit, Konduktor, Pentadbir, dan Baca Sahaja dengan kebenaran terperinci
- **🔗 Pautan Tugasan**: Pautan berkongsi yang selamat dan terhad masa untuk akses wilayah tanpa akaun pengguna
- **📊 Penjejakan Kemajuan**: Status penyelesaian automatik dengan penunjuk visual dan statistik
- **✉️ Pemberitahuan E-mel**: Templat e-mel HTML untuk mesej, nota, dan laporan bulanan
- **📈 Pelaporan Excel**: Laporan jemaah bulanan dengan statistik terperinci
- **🔐 Pengesahan Pelbagai Faktor**: Sokongan OTP, MFA, dan OAuth2 (Google)
- **🌙 Mod Gelap**: Tema sedar sistem dengan pilihan terang/gelap/sistem
- **📲 Aplikasi Web Progresif**: Boleh dipasang pada mudah alih dan desktop dengan caching aset luar talian

### Untuk Pelbagai Pengguna

- **Pelayan Wilayah**: Urus semua rekod wilayah secara digital dengan statistik dan pelaporan yang komprehensif
- **Konduktor Pelayanan Lapangan**: Tugaskan wilayah dengan cepat menggunakan algoritma pintar dan jejak aktiviti penerbit
- **Penerbit**: Akses peta yang ditugaskan melalui pautan mudah, kemas kini status alamat dalam masa nyata, dan bekerjasama dengan berkesan
- **Pentadbir**: Kawalan sistem penuh dengan pengurusan pengguna, tetapan jemaah, dan jejak audit

## Masalah Yang Ministry Mapper Selesaikan

### Beralih kepada Digital

Slip wilayah kertas tradisional sering hilang, rosak, atau sukar dibaca lama kelamaan. Ministry Mapper menyimpan segala-galanya secara digital dan selamat di awan. Anda boleh mengakses wilayah anda dari telefon, tablet, atau komputer pada bila-bila masa.

### Organisasi Lebih Baik

Apabila wilayah dikongsi antara beberapa penerbit, mudah terjadi pengerjaan alamat yang sama secara tidak sengaja. Ministry Mapper membantu semua orang melihat apa yang berlaku dalam masa nyata, supaya anda tidak membuang masa mengulang kawasan yang sama.

### Kemas Kini Segera

Apabila seseorang mengemas kini wilayah, semua orang akan melihat perubahan tersebut dengan serta-merta. Tiada tunggu slip diserahkan atau mesyuarat untuk berkongsi kemas kini.

### Penyimpanan Rekod Lebih Mudah

Daripada menulis setiap perubahan secara manual pada slip kertas, Ministry Mapper menjejak segala-galanya secara automatik. Ini menjimatkan berjam-jam kerja dan mencegah kesilapan.

## Cara Ministry Mapper Berfungsi

### Timbunan Teknologi Moden

Ministry Mapper memanfaatkan teknologi web terkini untuk prestasi optimum:

**Bahagian Hadapan (ministry-mapper-v2):**

- **React 19.2.4**: Perpustakaan UI terbaru dengan pengoptimuman pengkompil automatik
- **TypeScript 5.9.3**: Pembangunan selamat jenis untuk mengurangkan pepijat
- **Vite 8.0.2**: Alat binaan dan pelayan pembangunan yang pantas
- **Leaflet 1.9.4**: Pemetaan interaktif dengan OpenStreetMap (tiada had API)
- **Bootstrap 5.3.8**: Rangka kerja UI moden dan responsif
- **Wouter 3.9.0**: Penghalaan ringan (3KB berbanding 40KB alternatif)

**Bahagian Belakang (ministry-mapper-be):**

- **Go 1.25.0**: Bahasa terkompil berprestasi tinggi
- **PocketBase 0.36.7**: Bahagian belakang sebagai perkhidmatan yang dihoskan sendiri dengan SQLite
- **SQLite 1.40.2+**: Pangkalan data yang boleh dipercayai dan berdikari (pelaksanaan Go tulen)
- **Echo v5**: Rangka kerja web ringkas berprestasi tinggi
- **Sentry 0.43.0**: Penjejakan ralat dan pemantauan masa nyata
- **MailerSend 1.6.3**: Perkhidmatan e-mel transaksi
- **LaunchDarkly 7.14.6**: Pengurusan bendera ciri
- **Excelize 2.10.1**: Penjanaan laporan Excel
- **OpenAI GPT**: Ringkasan berkuasa AI untuk laporan dan ringkasan mesej

### Sorotan Seni Bina

- **Dihoskan Sendiri**: Tiada kunci vendor, kawalan penuh ke atas data anda
- **Berbilang Penyewa**: Pengasingan data peringkat jemaah untuk keselamatan
- **Penyegerakan Masa Nyata**: Kemas kini langsung berasaskan SSE merentasi semua klien
- **Aplikasi Web Progresif**: Boleh dipasang pada semua peranti dengan sokongan aset luar talian
- **Kontenerisasi Docker**: Pelaksanaan dan penskalaan mudah
- **Reka Bentuk API-Pertama**: API RESTful dengan sokongan SDK PocketBase yang komprehensif
- **Cerapan Berkuasa AI**: Integrasi OpenAI GPT pilihan untuk ringkasan laporan pintar dan ringkasan mesej

## Pertimbangan Penting

### Privasi Didahulukan

Ministry Mapper menjejak alamat kediaman, bermakna anda perlu mematuhi undang-undang privasi di kawasan anda. Negara yang berbeza mempunyai peraturan berbeza (seperti GDPR di Eropah atau CCPA di California). Pastikan anda memahami dan mengikuti peraturan tempatan sebelum menggunakan Ministry Mapper.

### Internet Diperlukan

Ministry Mapper beroperasi melalui internet, jadi anda memerlukan sambungan untuk menggunakannya. Jika kawasan anda mempunyai internet yang tidak stabil, ini mungkin menimbulkan cabaran. Aplikasi ini berfungsi dengan baik pada data mudah alih jika WiFi tidak tersedia.

### Keluk Pembelajaran

Peralihan dari kertas ke digital adalah satu perubahan, terutamanya bagi mereka yang kurang selesa dengan teknologi. Kami menyediakan bantuan dan sokongan untuk memudahkan peralihan bagi semua orang dalam jemaah anda.

## Mulai Sekarang

### Untuk Pengguna

Cara termudah untuk menggunakan Ministry Mapper adalah melalui perkhidmatan yang dihoskan kami:

**[ministry-mapper.com](https://ministry-mapper.com)**

Hanya:

1. Lawati [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akaun menggunakan halaman daftar
3. Sahkan alamat e-mel anda
4. Hubungi pentadbir jemaah anda untuk kebenaran yang sesuai

**Pautan Pantas:**

- **[Mulai →](getting-started.md)**
- **[Gambaran Keseluruhan Ciri →](features.md)**
- **[Panduan Pengguna →](user-guide.md)**
- **[Soalan Lazim →](faq.md)**

### Untuk Pentadbir

Jika anda sedang mempertimbangkan Ministry Mapper untuk jemaah anda:

- **Disyorkan**: Gunakan perkhidmatan yang dihoskan kami di [ministry-mapper.com](https://ministry-mapper.com)

  - Tiada persediaan teknikal diperlukan
  - Sokongan profesional tersedia
  - Kemas kini dan penyelenggaraan automatik
  - Kebolehpercayaan dan keselamatan yang lebih baik

- **Alternatif**: Semak [Panduan Pelaksanaan](deployment.md) (untuk pengguna lanjutan sahaja)

Kami tidak menggalakkan pengehosan sendiri kerana kerumitan teknikal dan keperluan penyelenggaraan berterusan. Perkhidmatan yang dihoskan di ministry-mapper.com menyediakan penyelesaian yang lebih mudah dan lebih boleh dipercayai.

**Sumber Pentadbir:**

- **[Panduan Memulakan →](getting-started.md)**
- **[Pilihan Pelaksanaan →](deployment.md)**
- **[Panduan Pengehosan Sendiri →](self-hosting.md)**

### Untuk Pembangun

Jika anda berminat dengan aspek teknikal atau menyumbang:

**Dokumentasi Teknikal:**

- **[Gambaran Keseluruhan Seni Bina →](architecture.md)** - Reka bentuk sistem dan keputusan teknologi
- **[Model Data & Skema →](data-models.md)** - Struktur pangkalan data dan hubungan
- **[Persediaan Bahagian Belakang →](backend-setup.md)** - Konfigurasi PocketBase
- **[Persediaan Bahagian Hadapan →](frontend-setup.md)** - Persediaan aplikasi React

**Pembangunan:**

- **Repositori Bahagian Hadapan:** [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
- **Repositori Bahagian Belakang:** [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
- **Isu:** Isu GitHub pada repositori masing-masing
- **Penyumbangan:** Lihat fail CONTRIBUTING.md repositori
