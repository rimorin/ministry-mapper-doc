# Selamat Datang di Ministry Mapper

## Apa itu Ministry Mapper?

Ministry Mapper adalah aplikasi web modern berbasis open-source yang membantu jemaat mengelola wilayah pelayanan lapangan mereka secara efisien. Daripada menggunakan slip wilayah kertas, Ministry Mapper menyediakan solusi digital yang komprehensif untuk mengorganisir, melacak, dan mengelola pekerjaan pelayanan lapangan dari perangkat apa pun dengan koneksi internet.

## Kisah Kami

Kami memulai Ministry Mapper pada tahun 2022 sebagai proyek sederhana untuk membantu jemaat lokal kami mengelola wilayah dengan lebih efektif. Seiring semakin banyak jemaat yang menemukan manfaat pengelolaan wilayah digital, kami terus meningkatkan platform ini. Saat ini, Ministry Mapper digunakan oleh jemaat di Singapura dan Malaysia, membantu para penerbit tetap terorganisir dalam pekerjaan pelayanan mereka dengan fitur seperti sinkronisasi real-time, pemetaan interaktif, dan dukungan multibahasa.

## Apa yang Dilakukan Ministry Mapper

Ministry Mapper menyediakan kemampuan manajemen wilayah yang komprehensif:

### Fitur Utama

- **🗺️ Manajemen Wilayah**: Mengorganisir wilayah geografis ke dalam teritori dengan pelacakan penyelesaian otomatis
- **🏢 Pemetaan Interaktif**: Peta berbasis Leaflet dengan integrasi OpenStreetMap, petunjuk arah, dan geolokasi
- **📱 Kolaborasi Real-Time**: Sinkronisasi data langsung di semua perangkat melalui SSE (Server-Sent Events)
- **🌍 Dukungan Multibahasa**: 8 bahasa (Inggris, Spanyol, Indonesia, Jepang, Korea, Melayu, Tionghoa, Tamil)
- **👥 Akses Berbasis Peran**: Peran Penerbit, Konduktor, Administrator, dan Hanya-Baca dengan izin yang detail
- **🔗 Tautan Penugasan**: Tautan berbagi yang aman dan terbatas waktu untuk akses wilayah tanpa akun pengguna
- **📊 Pelacakan Kemajuan**: Status penyelesaian otomatis dengan indikator visual dan statistik
- **✉️ Notifikasi Email**: Template email HTML untuk pesan, catatan, dan laporan bulanan
- **📈 Pelaporan Excel**: Laporan jemaat bulanan dengan statistik detail
- **🔐 Autentikasi Multi-Faktor**: Dukungan OTP, MFA, dan OAuth2 (Google)
- **🌙 Mode Gelap**: Tema yang disesuaikan sistem dengan opsi terang/gelap/sistem
- **📲 Progressive Web App**: Dapat dipasang di perangkat mobile dan desktop dengan caching aset offline

### Untuk Pengguna yang Berbeda

- **Pelayan Wilayah**: Mengelola semua catatan wilayah secara digital dengan statistik dan pelaporan yang komprehensif
- **Konduktor Pelayanan Lapangan**: Menugaskan wilayah dengan cepat menggunakan algoritma cerdas dan melacak aktivitas penerbit
- **Penerbit**: Mengakses peta yang ditugaskan melalui tautan sederhana, memperbarui status alamat secara real-time, dan berkolaborasi secara efektif
- **Administrator**: Kontrol sistem penuh dengan manajemen pengguna, pengaturan jemaat, dan jejak audit

## Masalah yang Diselesaikan Ministry Mapper

### Beralih ke Digital

Slip wilayah kertas tradisional mudah hilang, rusak, atau sulit dibaca seiring waktu. Ministry Mapper menyimpan semuanya secara digital dan aman di cloud. Anda dapat mengakses wilayah dari ponsel, tablet, atau komputer kapan saja.

### Organisasi yang Lebih Baik

Ketika wilayah dibagikan di antara beberapa penerbit, mudah untuk secara tidak sengaja mengerjakan alamat yang sama. Ministry Mapper membantu semua orang melihat apa yang terjadi secara real-time, sehingga Anda tidak membuang waktu mengerjakan area yang sama.

### Pembaruan Instan

Ketika seseorang memperbarui wilayah, semua orang langsung melihat perubahan tersebut. Tidak perlu menunggu kertas diedarkan atau rapat untuk berbagi pembaruan.

### Pencatatan yang Lebih Mudah

Daripada menulis setiap perubahan secara manual di slip kertas, Ministry Mapper secara otomatis melacak segalanya. Ini menghemat waktu berjam-jam dan mencegah kesalahan.

## Cara Kerja Ministry Mapper

### Tumpukan Teknologi Modern

Ministry Mapper memanfaatkan teknologi web terkini untuk kinerja optimal:

**Frontend (ministry-mapper-v2):**

- **React 19.2.4**: Library UI terbaru dengan optimasi kompiler otomatis
- **TypeScript 5.9.3**: Pengembangan type-safe untuk lebih sedikit bug
- **Vite 8.0.2**: Build tool dan dev server yang sangat cepat
- **Leaflet 1.9.4**: Pemetaan interaktif dengan OpenStreetMap (tanpa batas API)
- **Bootstrap 5.3.8**: Framework UI modern dan responsif
- **Wouter 3.9.0**: Routing ringan (3KB vs 40KB alternatif)

**Backend (ministry-mapper-be):**

- **Go 1.25.0**: Bahasa yang dikompilasi berkinerja tinggi
- **PocketBase 0.36.7**: Backend-as-a-service self-hosted dengan SQLite
- **SQLite 1.40.2+**: Database yang andal dan mandiri (implementasi Go murni)
- **Echo v5**: Framework web minimalis berkinerja tinggi
- **Sentry 0.43.0**: Pelacakan dan pemantauan error real-time
- **MailerSend 1.6.3**: Layanan email transaksional
- **LaunchDarkly 7.14.6**: Manajemen feature flag
- **Excelize 2.10.1**: Pembuatan laporan Excel
- **OpenAI GPT**: Ringkasan bertenaga AI untuk laporan dan digest pesan

### Sorotan Arsitektur

- **Self-Hosted**: Tanpa vendor lock-in, kontrol penuh atas data Anda
- **Multi-Tenant**: Isolasi data di tingkat jemaat untuk keamanan
- **Sinkronisasi Real-Time**: Pembaruan langsung berbasis SSE di semua klien
- **Progressive Web App**: Dapat dipasang di semua perangkat dengan dukungan aset offline
- **Kontainerisasi Docker**: Deployment dan penskalaan yang mudah
- **Desain API-First**: API RESTful dengan dukungan SDK PocketBase yang komprehensif
- **Wawasan Bertenaga AI**: Integrasi OpenAI GPT opsional untuk ringkasan laporan cerdas dan digest pesan

## Pertimbangan Penting

### Privasi Pertama

Ministry Mapper melacak alamat tempat tinggal, yang berarti Anda perlu mengikuti undang-undang privasi di wilayah Anda. Negara yang berbeda memiliki aturan yang berbeda (seperti GDPR di Eropa atau CCPA di California). Pastikan Anda memahami dan mengikuti peraturan lokal Anda sebelum menggunakan Ministry Mapper.

### Internet Diperlukan

Ministry Mapper bekerja melalui internet, jadi Anda memerlukan koneksi untuk menggunakannya. Jika daerah Anda memiliki internet yang tidak dapat diandalkan, ini mungkin menimbulkan tantangan. Aplikasi ini bekerja dengan baik pada data seluler jika WiFi tidak tersedia.

### Kurva Pembelajaran

Berpindah dari kertas ke digital adalah perubahan, terutama bagi mereka yang kurang nyaman dengan teknologi. Kami menyediakan bantuan dan dukungan untuk membuat transisi lebih mudah bagi semua orang di jemaat Anda.

## Memulai

### Untuk Pengguna

Cara termudah untuk menggunakan Ministry Mapper adalah melalui layanan yang kami hosting:

**[ministry-mapper.com](https://ministry-mapper.com)**

Cukup:

1. Kunjungi [ministry-mapper.com](https://ministry-mapper.com)
2. Buat akun menggunakan halaman pendaftaran
3. Verifikasi alamat email Anda
4. Hubungi administrator jemaat Anda untuk izin yang sesuai

**Tautan Cepat:**

- **[Mulai →](getting-started.md)**
- **[Ikhtisar Fitur →](features.md)**
- **[Panduan Pengguna →](user-guide.md)**
- **[FAQ →](faq.md)**

### Untuk Administrator

Jika Anda mempertimbangkan Ministry Mapper untuk jemaat Anda:

- **Disarankan**: Gunakan layanan yang kami hosting di [ministry-mapper.com](https://ministry-mapper.com)

  - Tidak diperlukan pengaturan teknis
  - Dukungan profesional tersedia
  - Pembaruan dan pemeliharaan otomatis
  - Keandalan dan keamanan yang lebih baik

- **Alternatif**: Tinjau [Panduan Deployment](deployment.md) (hanya untuk pengguna tingkat lanjut)

Kami tidak mendorong self-hosting karena kompleksitas teknis dan persyaratan pemeliharaan yang berkelanjutan. Layanan yang di-hosting di ministry-mapper.com memberikan solusi yang lebih sederhana dan andal.

**Sumber Daya Administrator:**

- **[Panduan Memulai →](getting-started.md)**
- **[Opsi Deployment →](deployment.md)**
- **[Panduan Self-Hosting →](self-hosting.md)**

### Untuk Pengembang

Jika Anda tertarik dengan aspek teknis atau ingin berkontribusi:

**Dokumentasi Teknis:**

- **[Ikhtisar Arsitektur →](architecture.md)** - Desain sistem dan keputusan teknologi
- **[Model Data & Skema →](data-models.md)** - Struktur database dan hubungan
- **[Pengaturan Backend →](backend-setup.md)** - Konfigurasi PocketBase
- **[Pengaturan Frontend →](frontend-setup.md)** - Pengaturan aplikasi React

**Pengembangan:**

- **Frontend Repository:** [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
- **Backend Repository:** [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
- **Issues:** GitHub Issues pada repository masing-masing
- **Berkontribusi:** Lihat file CONTRIBUTING.md di repository
