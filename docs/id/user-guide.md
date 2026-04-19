# Panduan Pengguna Ministry Mapper

## Pendahuluan

Selamat datang di Ministry Mapper, aplikasi berbasis web modern yang dirancang untuk membantu jemaat mengelola wilayah pelayanan lapangan secara efisien. Panduan ini akan memandu Anda melalui semua yang perlu Anda ketahui untuk memulai dan memaksimalkan penggunaan aplikasi.

**Apa itu Ministry Mapper?**

Ministry Mapper adalah sistem manajemen wilayah digital yang menggantikan metode berbasis kertas tradisional. Ini memungkinkan jemaat untuk:

- Mengatur dan menetapkan wilayah secara digital
- Melacak kunjungan pelayanan lapangan secara real-time
- Mengkoordinasikan aktivitas di berbagai penginjil
- Mengakses wilayah dari perangkat apa pun dengan internet

**Manfaat Utama:**

- ✓ Ramah lingkungan - menghilangkan pemborosan kertas
- ✓ Pembaruan real-time melalui sinkronisasi cloud
- ✓ Berfungsi di perangkat apa pun (desktop, tablet, mobile)
- ✓ Peta interaktif terintegrasi untuk navigasi mudah
- ✓ Kontrol akses berbasis peran yang aman

## Memulai

### Membuat Akun Anda

![Halaman Pendaftaran](assets/screenshots/sign_up.png)

*Gambar 1: Formulir pendaftaran untuk membuat akun Ministry Mapper baru*

**Langkah 1: Akses Halaman Pendaftaran**

![Masuk](assets/screenshots/sign_in.png)

*Gambar 2: Halaman login dengan opsi Daftar*

1. Kunjungi URL Ministry Mapper jemaat Anda
2. Klik tombol **"Daftar"** di halaman login

**Langkah 2: Pilih Metode Pendaftaran**

| Pendaftaran Tradisional | Google OAuth (Disarankan) |
|-------------------------|---------------------------|
| Memerlukan email, kata sandi (6+ karakter, 1 angka, 1 huruf kapital), dan verifikasi email | Pendaftaran satu klik menggunakan akun Google |
| Verifikasi email manual diperlukan | Verifikasi email otomatis |
| Perlu mengingat kata sandi lain | Tidak ada kata sandi untuk dikelola |
| Terima Kebijakan Privasi & Ketentuan | Keamanan ditingkatkan melalui Google |

**Pendaftaran Tradisional:**
1. Isi: Nama, Email, Kata Sandi, Konfirmasi Kata Sandi
2. Terima Kebijakan Privasi dan Ketentuan Layanan
3. Klik **"Buat Akun"**

**Pendaftaran Google OAuth:**
1. Klik tombol **"Masuk dengan Google"** di bawah "Atau lanjutkan dengan"
2. Pilih akun Google Anda
3. Berikan akses profil dasar Ministry Mapper
4. Akun dibuat secara otomatis

**Langkah 3: Verifikasi Email Anda**

![Email Verifikasi Pendaftaran](assets/screenshots/sign_up_verification_email.png)

*Gambar 3: Pesan verifikasi email yang dikirim setelah pembuatan akun*

1. Periksa kotak masuk email Anda untuk pesan verifikasi dari Ministry Mapper
2. Klik tautan verifikasi di email
3. Anda akan melihat konfirmasi bahwa akun Anda terverifikasi

**Langkah 4: Tunggu Akses Jemaat**

Setelah verifikasi:

1. Kembali ke halaman login
2. Masuk dengan email dan kata sandi Anda
3. Jika Kata Sandi Satu Kali (OTP) diaktifkan, periksa email Anda untuk kodenya

![Halaman Verifikasi OTP](assets/screenshots/otp_verification_page.png)

*Gambar 4: Layar verifikasi Kata Sandi Satu Kali (OTP)*

![Email Verifikasi OTP](assets/screenshots/otp_verification_email.png)

*Gambar 5: Email berisi kode OTP untuk verifikasi login*

4. **Penting**: Anda belum akan melihat wilayah apa pun - administrator harus memberikan akses ke jemaat Anda terlebih dahulu
5. Hubungi pelayan wilayah atau administrator jemaat Anda untuk meminta akses

### Masuk ke Ministry Mapper

![Masuk Google OAuth](assets/screenshots/google_oauth_sign_in.png)

*Gambar 6: Opsi masuk Google OAuth untuk autentikasi yang lebih cepat dan aman*

Setelah akun Anda terverifikasi dan Anda telah diberikan akses jemaat:

**Login Standar:**
1. Navigasi ke URL Ministry Mapper jemaat Anda
2. Masukkan alamat email dan kata sandi Anda
3. Klik **"Masuk"**

**Login Google OAuth (Lebih Cepat):**
1. Klik tombol **"Masuk dengan Google"**
2. Pilih akun Google Anda
3. Otomatis masuk (tidak perlu kata sandi)

**Langkah 3: Lengkapi Verifikasi OTP (Jika Diaktifkan)**

Jika jemaat Anda telah mengaktifkan keamanan Kata Sandi Satu Kali:

1. Setelah memasukkan kredensial, Anda akan melihat layar verifikasi OTP
2. Periksa email Anda untuk kode verifikasi
3. Masukkan kode 6 digit dari email
4. Klik **"Verifikasi"** atau **"Kirim"**
5. Kode kedaluwarsa dalam 5-10 menit

**Langkah 4: Akses Dashboard Anda**

- **Penginjil**: Anda tidak akan melihat dashboard - gunakan tautan penugasan yang dikirimkan kepada Anda
- **Hanya-Baca, Konduktor, Administrator**: Anda akan melihat dashboard sesuai peran Anda

> **💡 Tips**: Tetap login di perangkat tepercaya untuk kenyamanan, tetapi selalu keluar di komputer bersama.

### Memahami Peran Pengguna

Ministry Mapper menggunakan sistem kontrol akses empat tingkat. Peran Anda menentukan fitur apa yang dapat Anda akses dan tindakan apa yang dapat Anda lakukan.

#### Hierarki Peran

```
Administrator (Akses Penuh)
    ↓
Konduktor (Kelola Penugasan)
    ↓
Hanya-Baca (Hanya Lihat)
    ↓
Penginjil (Akses Tautan Saja)
```

---

#### 👤 Penginjil

**Metode Akses**: Melalui tautan penugasan yang dikirim oleh administrator atau konduktor

**Apa yang Dapat Dilakukan Penginjil:**

- ✓ Mengakses wilayah melalui tautan yang dibagikan
- ✓ Melihat peta wilayah dengan pemetaan interaktif
- ✓ Memperbarui status alamat setelah kunjungan
  - Tandai sebagai: Selesai, Tidak di Rumah, Jangan Kunjungi, Tidak Valid
- ✓ Menambahkan catatan kunjungan ke alamat
- ✓ Melacak jumlah percobaan "tidak di rumah"
- ✓ Melihat detail alamat dan urutan

**Apa yang Tidak Dapat Dilakukan Penginjil:**

- ✗ Tidak ada akses dashboard
- ✗ Tidak dapat melihat semua wilayah jemaat
- ✗ Tidak dapat membuat atau mengelola wilayah
- ✗ Tautan penugasan kedaluwarsa setelah waktu yang ditentukan (default: 24 jam, dapat dikonfigurasi oleh administrator jemaat)

**Terbaik Untuk**: Penginjil reguler yang melakukan pelayanan lapangan

---

#### 👓 Hanya-Baca

**Metode Akses**: Login dashboard dengan izin hanya-baca

**Apa yang Dapat Dilakukan Pengguna Hanya-Baca:**

- ✓ Melihat semua wilayah di jemaat
- ✓ Melihat informasi alamat lengkap
- ✓ Melihat kemajuan dan statistik wilayah
- ✓ Mengakses peta wilayah
- ✓ Melihat pesan jemaat

**Apa yang Tidak Dapat Dilakukan Pengguna Hanya-Baca:**

- ✗ Tidak dapat memodifikasi data wilayah atau alamat apa pun
- ✗ Tidak dapat membuat atau menghapus wilayah
- ✗ Tidak dapat mengelola penugasan
- ✗ Tidak dapat mengubah pengaturan jemaat

**Terbaik Untuk**: Pengamat, auditor, anggota komite pelayanan

---

#### 🎯 Konduktor

**Metode Akses**: Login dashboard dengan izin konduktor

**Kemampuan Konduktor Meliputi:**

- ✓ **Semua yang dapat dilakukan Hanya-Baca**, DITAMBAH:
- ✓ Membuat dan mengelola penugasan wilayah
- ✓ Membuat tautan penugasan untuk penginjil
- ✓ Melihat semua riwayat penugasan
- ✓ Mengakses dan memposting pesan jemaat
- ✓ Mengelola opsi jemaat (jenis status rumah tangga)
- ✓ Melihat status penyelesaian wilayah

**Apa yang Tidak Dapat Dilakukan Konduktor:**

- ✗ Tidak dapat membuat atau menghapus wilayah
- ✗ Tidak dapat mengedit detail alamat (alamat, unit, lantai)
- ✗ Tidak dapat mengelola peran atau izin pengguna
- ✗ Tidak dapat memodifikasi pengaturan jemaat inti

**Terbaik Untuk**: Koordinator pelayanan lapangan dan pengawas kelompok

---

#### 👑 Administrator (Pelayan Wilayah)

**Metode Akses**: Login dashboard dengan izin administratif penuh

**Administrator Memiliki Kontrol Penuh:**

- ✓ **Semua yang dapat dilakukan Konduktor**, DITAMBAH:
- ✓ Membuat, mengedit, dan menghapus wilayah
- ✓ Menambah, memodifikasi, dan menghapus alamat
- ✓ Mengelola gedung dan unit
- ✓ Mengonfigurasi pengaturan jemaat
  - Mengatur maks percobaan "tidak di rumah"
  - Mengonfigurasi waktu kedaluwarsa tautan
  - Menyiapkan opsi jenis rumah tangga
- ✓ Mengundang dan mengelola pengguna
- ✓ Menetapkan peran dan izin pengguna
- ✓ Mengatur ulang wilayah dan alamat
- ✓ Mengelola koordinat geolokasi

**Terbaik Untuk**: Pelayan wilayah dan mereka yang mengelola sistem wilayah jemaat

---

> **💡 Catatan**: Hubungi administrator jemaat Anda jika Anda perlu mengubah peran atau jika Anda tidak yakin peran apa yang Anda miliki saat ini.

## Fitur Utama

### Ikhtisar Dashboard

![Dashboard Administrator](assets/screenshots/admin_dashboard.png)

*Gambar 6: Dashboard administrator menampilkan pemilih wilayah dan kontrol utama*

Antarmuka dashboard bervariasi berdasarkan peran Anda:

#### Untuk Penginjil

Penginjil **tidak memiliki akses dashboard**. Sebagai gantinya, mereka:

- Menerima tautan penugasan melalui email atau pesan
- Klik tautan untuk mengakses wilayah yang ditugaskan
- Bekerja langsung dalam tampilan wilayah
- Tautan kedaluwarsa secara otomatis setelah waktu yang dikonfigurasi (default: 24 jam)

#### Untuk Konduktor dan Administrator

Dashboard menyediakan ikhtisar komprehensif:

**1. Pemilih Wilayah** (Dropdown Atas)

- Pilih wilayah mana yang akan dilihat atau dikelola
- Menampilkan kode dan deskripsi wilayah
- Navigasi cepat antar wilayah

**2. Tombol Aksi Utama**

- 📋 **Penugasan**: Lihat dan kelola tautan penugasan
- 💬 **Pesan**: Posting dan lihat pesan jemaat
- ⚙️ **Pengaturan**: Akses konfigurasi jemaat (hanya Administrator)
- 👥 **Pengguna**: Kelola peran dan undangan pengguna (hanya Administrator)

**3. Panel Informasi Wilayah**

- Kode dan deskripsi wilayah
- Bilah kemajuan menunjukkan persentase penyelesaian
- Timestamp terakhir diperbarui
- Total unit vs. unit selesai

**4. Opsi Tampilan Wilayah**

- 🗺️ **Tampilan Peta**: Tampilan peta interaktif
- 📋 **Tampilan Daftar**: Tampilan tabular semua alamat

**5. Menu Speed Dial** (Tombol Aksi Mengambang)

![Speed Dial Halaman Admin](assets/screenshots/speeddial_admin_page.png)

*Gambar 6a: Tombol aksi mengambang speed dial menyediakan akses cepat ke tindakan umum*

Tombol **➕** (pojok kanan bawah) menyediakan akses cepat ke:
- 🗺️ **Mode Tampilan Peta**: Beralih tampilan peta layar penuh
- 📋 **Tautan Cepat**: Membuat tautan penugasan dengan cepat (Konduktor & Administrator)
- Tindakan sadar konteks berdasarkan tampilan saat ini

---

### Melihat Wilayah

#### Tampilan Wilayah Penginjil

![Layar Penugasan Penginjil](assets/screenshots/publisher_assignment_screen.png)

*Gambar 7: Tampilan penginjil dari wilayah yang ditugaskan dengan peta dan daftar alamat*

**Mengakses Penugasan Anda:**

1. Klik tautan penugasan yang dikirim oleh administrator/konduktor Anda
2. Wilayah otomatis dimuat dengan:
   - Google Map interaktif menampilkan semua alamat
   - Penanda yang dapat diklik untuk setiap lokasi
   - Daftar alamat/unit yang akan dikunjungi
   - Persentase kemajuan saat ini

**Informasi Wilayah yang Ditampilkan:**

- **Kode Wilayah**: Pengidentifikasi (mis. "T-001")
- **Deskripsi**: Nama atau area wilayah
- **Bilah Kemajuan**: Status penyelesaian visual
- **Peta**: Peta interaktif dengan penanda alamat
- **Daftar Alamat**: Semua alamat dengan status saat ini

#### Tampilan Wilayah Administrator/Konduktor

![Dashboard Konduktor](assets/screenshots/conductor_dashboard.png)

*Gambar 8: Dashboard konduktor dengan pemilih wilayah dan opsi manajemen*

**Melihat Wilayah:**

1. Login ke dashboard Anda
2. Pilih wilayah dari menu dropdown
3. Lihat informasi detail:
   - Kode dan deskripsi wilayah
   - Statistik penyelesaian (mis. "15/20 selesai - 75%")
   - Peta interaktif dengan semua lokasi
   - Daftar alamat/unit lengkap dengan detail
   - Tombol manajemen (Edit, Hapus, Atur Ulang)

**Opsi Manajemen:**

- ✏️ **Edit Wilayah**: Ubah nama, kode, atau deskripsi
- 🗑️ **Hapus Wilayah**: Hapus seluruh wilayah
- 🔄 **Atur Ulang Wilayah**: Bersihkan semua status alamat
- ➕ **Tambah Alamat**: Buat alamat baru di wilayah

### Bekerja Dengan Alamat

#### Memahami Informasi Alamat

![Detail Status Alamat](assets/screenshots/address_status_details.png)

*Gambar 9: Kartu alamat menampilkan semua detail alamat dan status saat ini*

Setiap alamat atau unit di Ministry Mapper menampilkan informasi komprehensif:

**Informasi Dasar:**

- **Alamat/Nomor Unit**: Pengidentifikasi lokasi (mis. "#05-123")
- **Lantai**: Tingkat gedung (untuk properti bertingkat)
- **Jenis**: Klasifikasi rumah tangga berdasarkan opsi jemaat
  - Contoh: Tionghoa, Inggris, Tamil, Spanyol
  - Beberapa jenis dapat dipilih jika dikonfigurasi
- **Urutan**: Nomor urut kunjungan

**Informasi Status:**

- **Status**: Status kunjungan saat ini (lihat jenis status di bawah)
- **Jumlah Tidak di Rumah**: Jumlah percobaan kunjungan tidak dijawab
- **Tanggal Jangan Kunjungi**: Kapan DNC ditandai (jika berlaku)

**Pelacakan Aktivitas:**

- **Catatan**: Informasi penting tentang kunjungan atau penghuni
- **Terakhir Diperbarui**: Tanggal dan waktu pembaruan terbaru
- **Diperbarui Oleh**: Nama pengguna orang yang membuat perubahan terakhir

---

#### Jenis Status Alamat

Ministry Mapper menggunakan lima jenis status standar:

| Status          | Warna         | Deskripsi            | Penggunaan                                      |
| --------------- | ------------- | -------------------- | ----------------------------------------------- |
| **Belum Selesai**| Putih/Default | Belum dikunjungi     | Status awal untuk semua alamat                  |
| **Selesai**     | Hijau         | Berhasil dihubungi   | Penghuni ada di rumah dan dihubungi            |
| **Tidak di Rumah**| Kuning/Oranye| Tidak ada yang menjawab | Lacak hingga maks percobaan (dapat dikonfigurasi) |
| **Jangan Kunjungi**| Merah      | Meminta tidak dikunjungi | Penghuni meminta tidak ada kontak lebih lanjut |
| **Tidak Valid** | Abu-abu       | Tidak dapat diakses  | Alamat tidak ada atau tidak dapat dikunjungi   |

> **💡 Tips**: Setelah "Tidak di Rumah" mencapai percobaan maksimum (diatur oleh administrator), alamat otomatis ditandai sebagai selesai dalam perhitungan kemajuan.

---

#### Memperbarui Status Alamat

![Status Tidak di Rumah Alamat](assets/screenshots/address_not_home_status.png)

*Gambar 10: Modal perbarui status menampilkan semua field dan opsi*

**Proses Langkah demi Langkah:**

**1. Temukan Alamat**

- Gulir melalui daftar alamat
- Atau klik penanda di peta
- Alamat dikelompokkan berdasarkan lantai untuk gedung bertingkat

**2. Buka Modal Pembaruan**

- Klik pada kartu alamat/unit
- Modal pembaruan akan terbuka dengan informasi saat ini

**3. Perbarui Status** (Wajib)

Pilih status yang sesuai:

**📗 Selesai** - Ketika berhasil dihubungi

- Pilih ini ketika seseorang menjawab
- Percakapan terjadi atau literatur ditempatkan
- Penghitungan "Tidak di Rumah" direset

**🏠 Tidak di Rumah** - Ketika tidak ada yang menjawab

- Otomatis menambah penghitungan "Tidak di Rumah"
- Sistem melacak jumlah percobaan
- Setelah mencapai maks percobaan, diperlakukan sebagai selesai

**🚫 Jangan Kunjungi** - Ketika diminta untuk tidak mengunjungi

![Status Jangan Kunjungi Alamat](assets/screenshots/address_do_not_call_status.png)

*Gambar 11: Pembaruan status Jangan Kunjungi dengan pemilihan tanggal*

- Pilih status ini
- Opsional atur tanggal DNC (default ke hari ini)
- Tambahkan catatan yang menjelaskan alasan jika sesuai
- **Penting**: Selalu hormati keinginan penghuni

**❌ Tidak Valid** - Ketika alamat tidak dapat diakses

- Alamat tidak ada
- Sedang dalam konstruksi
- Tutup permanen
- Tidak dapat diakses

**4. Perbarui Jenis Rumah Tangga** (Jika Tersedia)

Jika jemaat Anda telah mengonfigurasi jenis rumah tangga:

- Pilih satu atau beberapa jenis
- Contoh: Preferensi bahasa, keadaan khusus
- Bersihkan jenis yang ada jika diperlukan

**5. Tambah atau Perbarui Catatan** (Opsional tetapi Disarankan)

**Praktik terbaik:**

- ✓ Fokus pada detail properti, bukan individu
- ✓ Catat instruksi akses dan waktu
- ✓ Ringkas dan sopan
- ✗ Jangan pernah menyertakan informasi pribadi tentang penghuni
- ✗ Jangan pernah menyertakan informasi sensitif

**Contoh Baik:**

- "Properti berpagar - hubungi pos jaga terlebih dahulu"
- "Waktu terbaik: Akhir pekan setelah jam 2 siang"
- "Pintu samping dapat diakses melalui jalan masuk"

**Contoh Buruk:**

- Detail pribadi tentang penghuni
- Informasi medis atau keuangan
- Nama atau deskripsi individu

**6. Sesuaikan Penghitungan Tidak di Rumah** (Jika Diperlukan)

Sistem otomatis melacak percobaan tidak di rumah, tetapi Anda dapat menyesuaikan secara manual jika diperlukan.

**7. Atur Tanggal Jangan Kunjungi** (Hanya Status DNC)

Saat menandai sebagai Jangan Kunjungi:

- Sistem default ke tanggal hari ini
- Sesuaikan jika diperlukan untuk permintaan DNC tertentu
- Tanggal membantu melacak kapan berpotensi mengunjungi kembali

**8. Perbarui Geolokasi** (Hanya Administrator)

Untuk wilayah satu lantai:

- Klik tombol "Perbarui Geolokasi"
- Gunakan peta untuk mengatur lokasi yang tepat
- Membantu navigasi dan akurasi pemetaan

**9. Simpan Perubahan**

- Tinjau semua pembaruan
- Klik tombol **"Simpan"**
- Perubahan disinkronkan langsung ke semua pengguna
- Pesan sukses mengonfirmasi pembaruan

**10. Hapus Properti** (Hanya Administrator - Satu Lantai)

Untuk properti pribadi yang perlu dihapus:

- Klik tombol "Hapus Properti" di bagian bawah
- Konfirmasi penghapusan
- **Peringatan**: Tindakan ini tidak dapat dibatalkan

---

### Menggunakan Fitur Peta

![Petunjuk Arah Peta](assets/screenshots/map_directions.png)

*Gambar 12: Tampilan peta interaktif menampilkan penanda dan kontrol navigasi*

Ministry Mapper mengintegrasikan pemetaan interaktif untuk navigasi wilayah yang intuitif.

#### Fitur Peta

**Penanda Interaktif:**

- Setiap alamat ditandai di peta
- 🔴 Penanda merah menunjukkan tujuan
- 🔵 Penanda biru berkedip menunjukkan lokasi saat ini
- Klik penanda mana pun untuk melihat/mengedit alamat tersebut

**Kontrol Peta:**

- **Zoom**: Tombol + dan - atau gerakan cubit
- **Pan**: Klik dan seret untuk bergerak
- **Tampilan Satelit**: Beralih antara peta dan citra satelit
- **Layar Penuh**: Perluas peta ke layar penuh (atau gunakan Speed Dial → Mode Tampilan Peta)
- **Pusat pada Wilayah**: Reset tampilan untuk menampilkan semua alamat

**Mode Tampilan Peta Layar Penuh** (Administrator & Konduktor):

![Mode Tampilan Peta Admin](assets/screenshots/admin_map_view_mode.png)

*Gambar 12a: Mode tampilan peta layar penuh diakses melalui menu speed dial*

Akses melalui Speed Dial (➕) → ikon Tampilan Peta. Ideal untuk:
- Perencanaan rute sebelum pelayanan lapangan
- Analisis cakupan wilayah
- Presentasi pertemuan
- Mengidentifikasi kluster alamat

#### Tips Navigasi

1. **Sebelum Meninggalkan Rumah:**

   - Lihat peta untuk merencanakan rute Anda
   - Identifikasi kluster alamat
   - Catat persyaratan akses khusus dari catatan

2. **Di Lapangan:**

   - Gunakan peta untuk bernavigasi antar alamat
   - Ikuti nomor urut untuk rute optimal
   - Ketuk penanda untuk memperbarui status dengan cepat setelah setiap kunjungan

3. **Menggunakan dengan GPS Ponsel:**
   - Aktifkan layanan lokasi
   - Peta menunjukkan posisi Anda saat ini
   - Navigasi langsung ke alamat berikutnya
   - Berfungsi offline jika tile peta di-cache (terbatas)

---

### Menambahkan Alamat yang Hilang

Jika Anda menemukan alamat yang belum terdaftar saat pemetaan, Anda dapat menambahkannya langsung tanpa bantuan admin.

> **Catatan:** Fitur ini tersedia untuk jemaat yang masih membangun catatan wilayah mereka.

![Kartu "+" di akhir daftar alamat untuk menambahkan alamat yang hilang](assets/screenshots/add_more_add.png)

*Gambar: Kartu tambah di akhir daftar alamat untuk menambahkan alamat yang hilang*

**Langkah-langkah:**

1. Gulir ke bawah daftar alamat hingga ke bagian bawah
2. Ketuk kartu **"+"** yang muncul di akhir daftar
3. Masukkan detail alamat (nomor atau nama)
4. Ketuk **"Simpan"** untuk menyimpan alamat baru

Alamat yang ditambahkan akan langsung muncul di daftar dan dapat segera diperbarui statusnya.

---

### Mendapatkan Petunjuk Arah

Ministry Mapper memiliki layanan rute bawaan untuk membantu Anda menavigasi ke alamat.

![Panel rute menampilkan opsi mode perjalanan dan rute yang diplot di peta](assets/screenshots/map_routing.png)

*Gambar: Panel rute dengan pilihan mode perjalanan*

**Langkah-langkah:**

1. Ketuk alamat yang ingin Anda kunjungi
2. Ketuk **"Petunjuk Arah"** di modal alamat
3. Pilih mode perjalanan:
   - 🚗 **Berkendara** — untuk kendaraan bermotor
   - 🚶 **Berjalan** — untuk jalan kaki
   - 🚲 **Bersepeda** — untuk sepeda
4. Rute ditampilkan di peta dengan petunjuk arah turn-by-turn

> **💡 Tip:** Gunakan mode **Berjalan** untuk area padat atau wilayah yang berdekatan agar rute lebih akurat.

---

### Penugasan Wilayah (Konduktor & Administrator)

Ministry Mapper menggunakan sistem penugasan berbasis tautan. Konduktor dan Administrator membuat tautan yang dapat dibagikan yang digunakan penginjil untuk mengakses wilayah.

#### Mengapa Penugasan Berbasis Tautan?

- ✓ **Distribusi Sederhana**: Kirim tautan melalui email, pesan, atau teks
- ✓ **Kedaluwarsa Otomatis**: Tautan kedaluwarsa setelah waktu yang ditentukan (default: 24 jam)
- ✓ **Tidak Perlu Akun**: Penginjil bekerja langsung melalui tautan
- ✓ **Keamanan**: Tautan kedaluwarsa tidak dapat diakses
- ✓ **Pelacakan**: Lihat siapa yang mengakses apa dan kapan

#### Membuat Penugasan

**Dua Cara Membuat Penugasan:**

![Tautan Cepat Admin Konduktor](assets/screenshots/admin_conductor_quicklink.png)

*Gambar 13a: Antarmuka pembuatan tautan cepat diakses melalui menu speed dial*

| Metode | Akses | Terbaik Untuk |
|--------|-------|---------------|
| **Tautan Cepat** | Speed Dial (➕) → Tautan Cepat | Pembuatan cepat untuk wilayah saat ini |
| **Standar** | Tombol Penugasan → Buat Baru | Opsi lengkap dan kustomisasi |

**Langkah 1: Akses Pembuatan Penugasan**

- **Tautan Cepat**: Klik Speed Dial (➕) → ikon Tautan Cepat (pra-isi wilayah saat ini)
- **Standar**: Klik **"Penugasan"** → **"Buat Penugasan Baru"** atau **"+"**

**Langkah 2: Isi Formulir Penugasan**

| Field | Deskripsi | Wajib |
|-------|-----------|-------|
| **Jenis** | Penugasan Normal atau Slip Pribadi | Ya |
| **Wilayah** | Pilih dari wilayah jemaat | Ya |
| **Nama Penginjil** | Untuk pelacakan (tautan berfungsi tanpa nama) | Opsional |
| **Kedaluwarsa Tautan** | Jam hingga tautan kedaluwarsa (default: 24) | Ya |

**Langkah 3: Buat dan Bagikan**

1. Klik **"Buat Penugasan"**
2. Sistem menghasilkan tautan unik
3. Bagikan tautan dengan penginjil melalui:
   - Email
   - Pesan teks
   - Aplikasi pesan instan
   - Metode komunikasi apa pun

**Contoh Tautan Penugasan:**

```
https://your-ministry-mapper.com/map/abc123xyz456
```

#### Pembuatan Penugasan Spesifik Peta

Buat penugasan langsung dari tampilan peta tanpa menavigasi ke modal penugasan pusat.

**Akses:**

1. Navigasi ke wilayah yang ingin Anda tugaskan
2. Klik salah satu:
   - Tombol **"Tugaskan"** - buat penugasan wilayah normal
   - Tombol **"Pribadi"** - buat penugasan slip pribadi

**Penugasan Wilayah Normal:**

![Menugaskan Peta Penginjil](assets/screenshots/assigning_publisher_map.png)

*Gambar 13b: Formulir pembuatan penugasan normal spesifik peta*

Klik **"Tugaskan"** untuk membuat penugasan wilayah normal. Formulir menampilkan:

- Informasi wilayah di header
- Field **Nama Penginjil** - masukkan nama penginjil yang ditugaskan
- Tombol **Batal** dan **Konfirmasi**

**Penugasan Slip Pribadi:**

![Peta Tugaskan Penginjil Pribadi](assets/screenshots/personal_assign_publisher_map.png)

*Gambar 13c: Formulir pembuatan slip pribadi spesifik peta dengan kalender*

Klik **"Pribadi"** untuk membuat penugasan slip pribadi. Formulir meliputi:

- Informasi wilayah di header
- **Pemilih kalender** - pilih tanggal penugasan
- Field **Nama Penginjil** - masukkan nama penginjil yang ditugaskan
- Tombol **Batal** dan **Konfirmasi**

**Manfaat:**

- ✓ Pembuatan penugasan cepat saat melihat wilayah
- ✓ Tidak perlu beralih ke modal penugasan
- ✓ Konteks langsung dari wilayah yang ditugaskan

#### Mengelola Penugasan

![Manajemen Penugasan](assets/screenshots/assignment_management.png)

*Gambar 14: Modal manajemen penugasan menampilkan semua penugasan aktif di seluruh wilayah*

Antarmuka Manajemen Penugasan memungkinkan konduktor dan administrator untuk melihat dan mengelola semua penugasan yang ada di jemaat.

**Lihat Semua Penugasan:**

1. Klik tombol **"Penugasan"** dari dashboard
2. Modal Penugasan terbuka menampilkan semua tautan penugasan aktif
3. Lihat penugasan untuk semua wilayah dalam daftar terpusat

**Informasi Penugasan yang Ditampilkan:**

Untuk setiap penugasan, Anda dapat melihat:

- **Kode Wilayah dan Lokasi**: Pengidentifikasi wilayah dengan deskripsi lokasi (mis. "187A, Marsiling Road (M01)")
- **Jenis Penugasan**: "Tugaskan" untuk penugasan normal
- **Nama Penginjil**: Nama orang yang ditugaskan (mis. Jon, Erli, Pety)
- **Tanggal/Waktu Dibuat**: Kapan penugasan dibuat (mis. "22 Des 2025, 9:38 PM")
- **Tanggal/Waktu Kedaluwarsa**: Kapan tautan penugasan kedaluwarsa (mis. "23 Des 2025, 9:38 AM" atau "11:38 PM")
- **Tombol Hapus**: Ikon tempat sampah (🗑️) untuk menghapus penugasan individual

**Menghapus Penugasan:**

1. Temukan penugasan dalam daftar
2. Klik tombol **ikon tempat sampah** (🗑️) di sisi kanan penugasan
3. Konfirmasi penghapusan jika diminta
4. Tautan penugasan langsung menjadi tidak dapat diakses oleh penginjil

**Kapan Menghapus:**

- Wilayah dikembalikan lebih awal oleh penginjil
- Tautan yang salah dibuat atau dikirim
- Penginjil tidak lagi memerlukan akses
- Penugasan perlu ditugaskan ulang ke orang lain
- Masalah keamanan atau kompromi

**Menutup Modal Penugasan:**

- Klik tombol **"Batal"** di bagian bawah untuk menutup modal dan kembali ke dashboard

#### Manajemen Penugasan Spesifik Peta

Lihat dan kelola penugasan untuk wilayah individual langsung dari tampilan peta.

**Akses:**

1. Navigasi ke wilayah
2. Klik salah satu:
   - **"Tautan Tugaskan"** - untuk penugasan wilayah normal
   - **"Tautan Pribadi"** - untuk penugasan slip pribadi

![Penugasan Peta Normal](assets/screenshots/map_assign_links.png)

*Gambar 14a: Modal Tautan Tugaskan untuk wilayah tertentu*

![Penugasan Peta Pribadi](assets/screenshots/map_personal_links.png)

*Gambar 14b: Modal Tautan Pribadi untuk wilayah tertentu*

**Informasi yang Ditampilkan:**

- Nama penginjil
- Tanggal/waktu dibuat dan kedaluwarsa
- Tombol hapus (🗑️) untuk menghapus penugasan

**Kapan Menggunakan:**

- Memeriksa siapa yang saat ini memiliki wilayah yang ditugaskan
- Menghapus penugasan saat melihat wilayah
- Mengelola penugasan normal dan pribadi secara terpisah

#### Praktik Terbaik untuk Penugasan

**Sebelum Membuat:**

- ✓ Verifikasi wilayah siap (alamat diperbarui, instruksi jelas)
- ✓ Periksa pengaturan jemaat untuk waktu kedaluwarsa default
- ✓ Rencanakan durasi kedaluwarsa yang sesuai untuk ukuran wilayah

**Saat Membagikan:**

- ✓ Sertakan instruksi dalam pesan Anda
- ✓ Ingatkan penginjil tentang waktu kedaluwarsa
- ✓ Berikan kontak Anda untuk pertanyaan
- ✓ Kirim selama jam yang wajar

**Pemantauan:**

- ✓ Pemeriksaan rutin untuk penugasan kedaluwarsa
- ✓ Bersihkan penugasan lama secara berkala
- ✓ Tindak lanjuti jika wilayah tidak dikembalikan
- ✓ Lacak tingkat penyelesaian

---

### Pesan dan Instruksi

![Pesan Admin Konduktor](assets/screenshots/admin_conductor_messages.png)

*Gambar 15: Modal pesan menampilkan pesan yang diposting dengan opsi penyematan*

Administrator dan Konduktor dapat memposting pesan yang terlihat oleh kelompok pengguna tertentu.

#### Jenis Pesan

**Pesan Penginjil:**

- Terlihat oleh semua penginjil dengan tautan penugasan
- Instruksi untuk pelayanan lapangan
- Panduan spesifik wilayah
- Pengumuman umum

**Pesan Konduktor:**

- Terlihat oleh Konduktor dan Administrator
- Informasi koordinasi
- Catatan administratif
- Informasi perencanaan

**Pesan Administrator:**

- Hanya terlihat oleh Administrator
- Catatan administrasi sistem
- Pembaruan kritis
- Pengingat manajemen

#### Memposting Pesan

1. Klik tombol **"Pesan"**
2. Klik **"Pesan Baru"** atau **"+"**
3. Ketik pesan Anda
4. Pilih jenis pesan (Penginjil/Konduktor/Administrator)
5. Opsional **sematkan** pesan penting ke atas
6. Klik **"Posting"**

#### Fitur Pesan

**Penyematan:**

- Sematkan pesan penting untuk menjaganya di atas
- Hanya satu pesan yang disematkan per jenis
- Lepas sematkan saat tidak lagi kritis

**Pengeditan:**

- Edit pesan setelah diposting
- Pembaruan langsung untuk semua penonton

**Penghapusan:**

- Hapus pesan yang sudah usang
- Bersihkan setelah acara berlalu

**Status Baca:**

- Lihat siapa yang telah membaca pesan (tampilan administrator)
- Lacak pengakuan pembaruan penting

---

### Sinkronisasi Data Real-Time

Ministry Mapper menggunakan langganan real-time PocketBase untuk pembaruan instan:

#### Cara Kerjanya

**Sinkronisasi Otomatis:**

- Perubahan disinkronkan langsung di semua perangkat yang terhubung
- Tidak perlu penyegaran manual
- Pembaruan muncul langsung untuk semua pengguna yang melihat wilayah yang sama

**Apa yang Disinkronkan:**

- ✓ Perubahan status alamat
- ✓ Catatan dan pembaruan baru
- ✓ Kemajuan wilayah
- ✓ Pesan baru
- ✓ Perubahan penugasan

**Penanganan Koneksi:**

- Sistem otomatis mendeteksi kehilangan koneksi
- Terhubung kembali saat internet dipulihkan
- Menampilkan indikator status koneksi
- Mengantri pembaruan jika offline (terbatas)

**Kinerja:**

- Pembaruan hanya saat wilayah/halaman terbuka
- Pembersihan otomatis saat halaman ditutup
- Penggunaan bandwidth minimal
- Dioptimalkan untuk data mobile

> **💡 Catatan**: Agar pembaruan real-time berfungsi, jaga tab browser Anda tetap aktif saat bekerja di wilayah.

---

### Manajemen Pengguna (Hanya Administrator)

![Daftar Kelola Pengguna](assets/screenshots/manage_users_list.png)

*Gambar 16: Panel manajemen pengguna menampilkan daftar pengguna dengan peran*

Administrator memiliki kontrol penuh atas akun dan izin pengguna dalam jemaat mereka.

#### Melihat Pengguna

1. Klik **ikon profil** atau **menu pengguna** Anda
2. Pilih **"Manajemen Pengguna"** atau **"Pengguna"**
3. Lihat daftar lengkap pengguna jemaat menampilkan:
   - Nama pengguna
   - Alamat email
   - Status verifikasi (✓ terverifikasi / ✗ tidak terverifikasi)
   - Lencana peran saat ini
   - Aktivitas terakhir

#### Mengundang Pengguna Baru

![Undang Pengguna](assets/screenshots/invite_users.png)

*Gambar 17: Dialog undangan pengguna untuk menambahkan anggota jemaat baru*

**Langkah 1: Buka Dialog Undang**

1. Di Manajemen Pengguna, klik **"Undang Pengguna"** atau **"+"**
2. Modal undang pengguna terbuka

**Langkah 2: Masukkan Informasi Pengguna**

- **Alamat Email**: Email pengguna (harus valid)
- **Penetapan Peran**: Pilih salah satu:
  - Penginjil
  - Hanya-Baca
  - Konduktor
  - Administrator

**Langkah 3: Kirim Undangan**

1. Klik **"Kirim Undangan"**
2. Sistem mengirim email undangan ke pengguna
3. Email berisi:
   - Tautan untuk membuat akun
   - Informasi jemaat
   - Detail penetapan peran
   - Instruksi untuk memulai

**Langkah 4: Pengguna Menyelesaikan Pendaftaran**

- Pengguna menerima email
- Klik tautan untuk mendaftar
- Membuat akun dengan kata sandi
- Memverifikasi alamat email
- Otomatis ditambahkan ke jemaat dengan peran yang ditetapkan

#### Mengubah Peran Pengguna

![Detail Kelola Pengguna](assets/screenshots/manage_users_details.png)

*Gambar 18: Antarmuka detail pengguna dan manajemen peran*

1. Temukan pengguna dalam daftar pengguna
2. Klik pada pengguna atau tombol **"Edit"**
3. Pilih peran baru dari dropdown:
   - **Penginjil**: Akses wilayah dasar melalui tautan
   - **Hanya-Baca**: Akses dashboard hanya-lihat
   - **Konduktor**: Dapat membuat penugasan dan mengelola pesan
   - **Administrator**: Kontrol penuh
4. Klik **"Simpan"** atau **"Perbarui"**
5. Perubahan berlaku segera

> **⚠️ Penting**: Pengguna harus keluar dan masuk kembali untuk melihat izin baru mereka tercermin.

#### Menghapus Akses Pengguna

**Penghapusan Sementara:**

1. Ubah peran pengguna ke **"Tanpa Akses"** atau **"Hapus Akses"**
2. Pengguna kehilangan semua izin
3. Akun tetap ada tetapi tidak dapat mengakses data jemaat

**Penghapusan Permanen:**

1. Klik tombol **"Hapus"** untuk pengguna
2. Konfirmasi penghapusan
3. Pengguna sepenuhnya dihapus dari jemaat
4. Pengguna dapat diundang kembali jika diperlukan

#### Status Verifikasi Pengguna

**Pengguna Terverifikasi (✓):**

- Alamat email dikonfirmasi
- Akses penuh ke izin yang ditetapkan
- Dapat login secara normal

**Pengguna Tidak Terverifikasi (✗):**

- Email belum dikonfirmasi
- Akses terbatas atau tidak ada
- Perlu memeriksa email dan klik tautan verifikasi

**Untuk Mengirim Ulang Verifikasi:**

- Beberapa sistem memungkinkan pengiriman ulang email verifikasi
- Atau minta pengguna menggunakan fitur "Lupa Kata Sandi"

---

### Pengaturan Jemaat (Hanya Administrator)

![Pengaturan Jemaat](assets/screenshots/congregation_settings.png)

*Gambar 19: Halaman pengaturan jemaat dengan semua opsi konfigurasi*

Konfigurasikan cara Ministry Mapper bekerja untuk jemaat Anda.

#### Mengakses Pengaturan

1. Klik tombol **"Pengaturan"** atau ikon ⚙️
2. Lihat panel konfigurasi jemaat

#### Pengaturan Utama

**1. Maksimum Percobaan "Tidak di Rumah"**

- Default: 1
- Rentang: 1-4 percobaan
- Saat tercapai, alamat dianggap selesai untuk perhitungan kemajuan
- Mempengaruhi kapan wilayah ditampilkan sebagai selesai

**Contoh:** Jika diatur ke 3:

- "Tidak di Rumah" pertama: Hitung = 1
- "Tidak di Rumah" kedua: Hitung = 2
- "Tidak di Rumah" ketiga: Hitung = 3, tandai selesai

**2. Kedaluwarsa Tautan Penugasan (Jam)**

- Default: 24 jam
- Rentang: 1-168 jam (1 minggu)
- Berapa lama tautan penugasan tetap aktif
- Berlaku untuk tautan yang baru dibuat

**3. Asal/Lokasi Jemaat**

- Atur lokasi jemaat Anda
- Digunakan untuk pemusatan peta dan petunjuk arah
- Dapat berupa nama kota atau koordinat
- Membantu perencanaan rute

**4. OTP (Kata Sandi Satu Kali)**

- Aktifkan/nonaktifkan OTP email untuk login
- Menambahkan lapisan keamanan ekstra
- Pengguna menerima kode melalui email saat login
- Disarankan untuk data jemaat yang sensitif

#### Opsi Jemaat (Jenis Rumah Tangga)

![Pengaturan Opsi Rumah Tangga](assets/screenshots/household_options_settings.png)

*Gambar 20: Manajemen opsi jemaat menampilkan konfigurasi jenis rumah tangga*

Konfigurasikan jenis klasifikasi rumah tangga kustom untuk wilayah Anda.

**Apa itu Opsi Jemaat?**

- Kategori kustom untuk mengklasifikasikan rumah tangga
- Contoh: Kelompok bahasa (Tionghoa, Inggris, Tamil)
- Dapat mewakili sistem klasifikasi apa pun yang digunakan jemaat Anda
- Beberapa jenis dapat ditetapkan ke satu rumah tangga jika dikonfigurasi

**Mengelola Opsi:**

1. **Lihat Opsi**

   - Di Pengaturan, temukan bagian "Opsi Jemaat"
   - Lihat daftar semua jenis yang dikonfigurasi

2. **Tambah Opsi Baru**

   - Klik "Tambah Opsi" atau "+"
   - Isi:
     - **Kode**: Pengidentifikasi pendek (mis. "CHI", "ENG")
     - **Deskripsi**: Nama lengkap (mis. "Tionghoa", "Inggris")
     - **Dapat Dihitung**: Centang jika harus dihitung ke kemajuan wilayah
     - **Adalah Default**: Centang jika harus menjadi pilihan default
     - **Urutan**: Nomor urut tampilan
   - Klik "Simpan"

3. **Edit Opsi**

   - Klik pada opsi yang ada
   - Modifikasi field
   - Simpan perubahan

4. **Hapus Opsi**
   - Klik tombol hapus untuk opsi
   - Konfirmasi penghapusan
   - **Peringatan**: Mempengaruhi semua alamat yang menggunakan jenis ini

**Flag Opsi:**

**Dapat Dihitung:**
- Mengontrol apakah alamat dengan jenis ini dihitung ke kemajuan wilayah
- **Dicentang**: Termasuk dalam perhitungan kemajuan (mis. Tionghoa, Inggris, Tamil)
- **Tidak Dicentang**: Dikecualikan dari kemajuan (mis. Bisnis, Sedang Konstruksi)
- Contoh: 100 total alamat, 10 ditandai "Bisnis" (tidak dapat dihitung) = kemajuan berdasarkan 90 alamat saja

**Adalah Default:**
- Auto-pilih jenis ini saat membuat alamat baru
- Hanya satu opsi yang harus default
- Gunakan untuk jenis rumah tangga Anda yang paling umum

**Urutan:**
- Mengontrol urutan tampilan di menu dropdown
- Angka lebih rendah muncul lebih dulu (1, 2, 3...)
- **Dikelola dengan seret dan lepas** di pengaturan - cukup seret opsi untuk mengurutkan ulang
- Mempengaruhi urutan di dropdown rumah tangga alamat
- Tips: Urutkan dari yang paling umum ke yang paling jarang

**Konfigurasi Pilihan Ganda:**

- Aktifkan jika rumah tangga dapat memiliki beberapa jenis
- Contoh: Rumah tangga berbicara Tionghoa dan Inggris
- Saat dinonaktifkan, hanya satu jenis per rumah tangga

#### Konfigurasi Peta

![Konfigurasi Peta](assets/screenshots/map_configurations.png)

*Gambar 34: Opsi konfigurasi peta lanjutan untuk administrator*

Konfigurasikan bagaimana peta ditampilkan dan berperilaku di jemaat Anda. Administrator memiliki akses ke fungsi manajemen peta yang kuat untuk pemeliharaan dan organisasi wilayah.

**Untuk Mengakses Konfigurasi Peta:**

1. Pergi ke **Pengaturan** (hanya Administrator)
2. Pilih wilayah dari dropdown
3. Buka tampilan peta untuk wilayah yang dipilih
4. Akses menu konfigurasi peta (biasanya melalui ikon pengaturan atau menu)

**Fungsi Konfigurasi Peta yang Tersedia:**

**1. Ubah Lokasi**
   - Pindahkan penanda peta ke alamat berbeda
   - Memperbarui lokasi geografis unit wilayah
   - Berguna saat alamat telah berubah atau lokasi awal salah
   - Cukup pilih lokasi baru di peta

**2. Ubah Wilayah**
   - Pindahkan alamat atau unit ke wilayah berbeda
   - Membantu mengatur ulang batas wilayah
   - Berguna untuk menyeimbangkan ukuran wilayah
   - Mempertahankan semua data alamat dan riwayat selama transfer

**3. Ubah Urutan**
   - Modifikasi nomor urut kunjungan untuk alamat
   - Mengoptimalkan rute untuk efisiensi pelayanan lapangan
   - Angka lebih rendah dikunjungi lebih dulu
   - Membantu membuat pola kunjungan yang logis

##### Perbarui Urutan Peta (Seret & Lepas)

![Perbarui Urutan Peta](assets/screenshots/update_map_sequence.png)

*Antarmuka seret dan lepas untuk mengurutkan ulang semua urutan peta di wilayah*

**Akses:** Menu alamat → "Ubah Urutan"

**Cara Kerjanya:**
1. Setiap kartu menampilkan alamat dengan nomor urut saat ini
2. Seret dan lepas untuk mengurutkan ulang - nomor diperbarui secara otomatis
3. Klik **"Simpan"** untuk menerapkan atau **"Batal"** untuk membuang

**Praktik Terbaik:**
- Minimalkan backtracking dengan mengelompokkan alamat terdekat
- Kelompokkan lantai bersama di gedung bertingkat
- Buat alur logis dari satu ujung ke ujung lainnya
- Tinjau tampilan peta setelah pengurutan

**4. Ganti Nama**
   - Ubah nama atau pengidentifikasi alamat/unit
   - Perbarui nama gedung atau nomor unit
   - Menjaga data tetap terkini dengan perubahan dunia nyata
   - Berguna untuk mengoreksi kesalahan entri awal

**5. Tambah No. Unit**
   - Tambahkan nomor unit baru ke alamat yang ada
   - Perluas gedung bertingkat dengan unit tambahan
   - Berguna saat apartemen baru ditambahkan ke gedung
   - Mempertahankan organisasi struktur gedung

**6. Tambah Lantai Atas**
   - Perluas gedung ke atas dengan lantai tambahan
   - Untuk gedung yang telah diperluas atau awalnya diperkirakan kurang
   - Otomatis membuat unit untuk lantai baru berdasarkan pola gedung
   - Membantu menjaga data wilayah tetap terkini dengan perubahan konstruksi

**7. Tambah Lantai Bawah**
   - Tambahkan lantai di bawah lantai terendah saat ini
   - Berguna untuk tingkat basement atau lantai bawah yang baru dapat diakses
   - Dapat menambahkan nomor lantai negatif (mis. B1, B2)
   - Mempertahankan sistem penomoran lantai yang konsisten

**8. Atur Ulang Status**
   - Bersihkan status alamat kembali ke "Belum Selesai"
   - Menghapus status "Selesai" dan "Tidak di Rumah" saja
   - TIDAK menghapus status "Jangan Kunjungi" atau "Tidak Valid"
   - Mempertahankan catatan dan informasi alamat lainnya
   - Berguna saat memulai ulang pekerjaan di alamat yang sebelumnya selesai
   - Tidak mempengaruhi alamat lain di wilayah

**9. Hapus**
   - Hapus alamat, unit, atau lantai secara permanen dari wilayah
   - Tidak dapat dibatalkan - gunakan dengan hati-hati
   - Membantu menghapus entri duplikat atau alamat yang tidak ada
   - Konfirmasi dengan hati-hati sebelum menghapus

**Kasus Penggunaan Umum:**
- Mengoreksi kesalahan: Ganti Nama dan Ubah Lokasi
- Penyeimbangan wilayah: Ubah Wilayah
- Pembaruan gedung: Tambah Lantai Atas/Bawah
- Optimasi rute: Ubah Urutan
- Pembersihan data: Hapus duplikat
- Pembaruan musiman: Atur Ulang Status

> **⚠️ Peringatan**: Perubahan mempengaruhi semua pengguna segera. Hapus bersifat permanen dan tidak dapat dibatalkan.

---

### Manajemen Wilayah (Hanya Administrator)

![Buat Wilayah Baru](assets/screenshots/create_new_territory.png)

*Gambar 21: Antarmuka pembuatan wilayah untuk menambahkan wilayah baru*

Administrator memiliki kontrol penuh atas pembuatan, pengeditan, dan pengelolaan wilayah.

#### Membuat Wilayah Baru

**Langkah 1: Akses Pembuatan Wilayah**

1. Klik dropdown **pemilih wilayah**
2. Pilih **"Buat Wilayah Baru"** atau **"Wilayah Baru"**
3. Formulir pembuatan wilayah terbuka

**Langkah 2: Masukkan Informasi Wilayah**

- **Kode Wilayah**: Pengidentifikasi pendek (mis. "T-001", "M-12", "W-05")
  - Jaga tetap pendek dan bermakna
  - Gunakan konvensi penamaan yang konsisten
  - Maksimum yang disarankan: 10 karakter
- **Deskripsi**: Nama lengkap atau deskripsi area
  - Contoh: "Pusat Kota Komersial", "Utara Perumahan"
  - Jadikan deskriptif untuk identifikasi mudah
  - Maksimum yang disarankan: 100 karakter

**Langkah 3: Buat**

1. Klik **"Buat Wilayah"**
2. Wilayah baru dibuat dan dipilih
3. Siap untuk menambahkan alamat

#### Mengedit Detail Wilayah

**Ubah Kode Wilayah:**

1. Pilih wilayah
2. Klik ✏️ **"Edit"** atau **"Ubah Kode Wilayah"**
3. Masukkan kode baru
4. Simpan perubahan
5. **Peringatan**: Perbarui referensi apa pun ke kode lama

**Ubah Deskripsi Wilayah:**

1. Pilih wilayah
2. Klik **"Ubah Nama Wilayah"** atau opsi edit
3. Perbarui deskripsi
4. Simpan perubahan

**Ubah Urutan Wilayah:**

##### Perbarui Urutan Wilayah (Seret & Lepas)

![Perbarui Urutan Wilayah](assets/screenshots/territory_sequence_change.png)

*Antarmuka seret dan lepas untuk mengurutkan ulang semua wilayah di jemaat*

**Akses:** Dropdown wilayah → "Ubah Urutan"

**Cara Kerjanya:**
1. Setiap kartu menampilkan kode dan deskripsi wilayah
2. Seret dan lepas untuk mengurutkan ulang - nomor urutan diperbarui secara otomatis
3. Klik **"Simpan"** untuk menerapkan atau **"Batal"** untuk membuang
4. Mengontrol urutan di dropdown pemilihan dan daftar

**Praktik Terbaik:**
- Atur berdasarkan kedekatan geografis
- Kelompokkan berdasarkan jenis (perumahan, komersial, bisnis)
- Pertimbangkan penugasan kelompok pelayanan lapangan
- Gunakan untuk perencanaan rotasi wilayah

#### Opsi Konfigurasi Wilayah

![Opsi Konfigurasi Wilayah](assets/screenshots/territory_configurations.png)

*Menu dropdown wilayah menampilkan opsi konfigurasi*

Administrator dapat mengakses opsi konfigurasi wilayah melalui tombol **Dropdown Wilayah** di bilah navigasi atas. Klik tombol untuk mengungkapkan opsi berikut:

**Opsi yang Tersedia:**

- **Buat Baru**: Buat wilayah baru dari awal
- **Ubah Kode**: Modifikasi pengidentifikasi unik wilayah
- **Ubah Nama**: Perbarui deskripsi wilayah
- **Ubah Urutan**: Urutkan ulang bagaimana wilayah muncul di daftar pilihan
- **Hapus Saat Ini**: Hapus wilayah yang saat ini dipilih secara permanen
- **Atur Ulang Status**: Bersihkan semua status alamat kembali ke "Belum Selesai"

Opsi ini menyediakan akses cepat ke tugas manajemen wilayah umum tanpa menavigasi melalui beberapa menu.

#### Operasi Wilayah

**Atur Ulang Wilayah:**

![Buat Peta Baru](assets/screenshots/create_new_map.png)

*Gambar 22: Operasi wilayah dan antarmuka manajemen*

Mengatur ulang semua alamat di wilayah ke status "Belum Selesai":

1. Pilih wilayah
2. Klik tombol **"Atur Ulang Wilayah"**
3. Konfirmasi tindakan (ini membersihkan semua data kunjungan!)
4. Semua alamat kembali ke "Belum Selesai"
5. Penghitungan tidak di rumah reset ke 0
6. Catatan dipertahankan
7. Kemajuan reset ke 0%

**Gunakan Kapan:**

- Wilayah sepenuhnya dikerjakan dan siap ditugaskan ulang
- Memulai putaran kunjungan baru
- Membersihkan data tes

**⚠️ Peringatan**: Tidak dapat dibatalkan. Semua pembaruan status akan hilang.

**Hapus Wilayah:**

![Daftar Pilihan Wilayah](assets/screenshots/territory_selection_list.png)

*Gambar 23: Daftar pilihan wilayah menampilkan semua wilayah yang tersedia*

Menghapus wilayah dan semua datanya secara permanen:

1. Pilih wilayah yang akan dihapus
2. Klik tombol **"Hapus Wilayah"**
3. Baca pesan peringatan dengan hati-hati
4. Ketik konfirmasi jika diperlukan
5. Konfirmasi penghapusan

**Menghapus:**

- Catatan wilayah
- Semua alamat di wilayah
- Semua unit dan lantai
- Semua riwayat penugasan
- Semua data terkait

**⚠️ Peringatan Kritis**: Tindakan ini TIDAK DAPAT dibatalkan. Pertimbangkan mengekspor data terlebih dahulu.

---

### Manajemen Alamat (Hanya Administrator)

![Peta Satu Lantai](assets/screenshots/single_story_map.png)

*Gambar 24: Antarmuka manajemen alamat untuk properti pribadi/satu lantai*

Administrator dapat menambahkan dan mengelola alamat dalam wilayah.

#### Jenis Alamat

Ministry Mapper mendukung dua jenis alamat:

**1. Alamat Publik (Bertingkat)**

- Gedung apartemen, kondominium
- Beberapa lantai dan unit
- Contoh: Flat HDB, kompleks apartemen
- Setiap lantai memiliki beberapa unit

**2. Alamat Pribadi (Satu Lantai)**

- Rumah individual, ruko
- Properti tunggal dengan satu alamat
- Contoh: Properti landed, gedung mandiri
- Tidak ada struktur lantai/unit

#### Menambahkan Alamat Publik (Bertingkat)

**Langkah 1: Mulai Pembuatan**

1. Pilih wilayah
2. Klik tombol **"Tambah Alamat"** atau **"+"**
3. Pilih jenis **"Alamat Publik"**

**Langkah 2: Masukkan Informasi Gedung**

- **Kode Pos/Alamat**: Pengidentifikasi gedung
  - Masukkan kode pos atau alamat jalan
  - Sistem mungkin auto-populasi lokasi
  - Digunakan untuk geocoding dan tampilan peta
- **Nama Gedung**: Nama gedung opsional
  - Contoh: "Blok 123A", "Sunny Heights"
  - Membantu penginjil mengidentifikasi gedung

**Langkah 3: Konfigurasikan Lantai**

![Peta Bertingkat](assets/screenshots/multi_story_map.png)

*Gambar 25: Manajemen gedung bertingkat dengan organisasi lantai dan unit*

- **Lantai Mulai**: Nomor lantai terendah
  - Dapat negatif untuk tingkat basement
  - Contoh: -2 (B2), 1 (Dasar), 0
- **Lantai Atas**: Nomor lantai tertinggi
  - Maksimum: 50
  - Contoh: 10, 25, 40
- **Pemilihan Lantai**: Pilih lantai tertentu
  - Lewati lantai tanpa unit (mis. lantai mekanis)
  - Tipikal: Semua lantai dari mulai ke atas

**Langkah 4: Konfigurasikan Unit**

- **Unit Per Lantai**: Jumlah unit di setiap lantai
  - Contoh: 8, 12, 16
  - Membuat unit secara otomatis
- **Format Nomor Unit**: Cara menomori unit
  - Pola: Lantai + unit (mis. 01-01, 01-02)
  - Kustom: Masukkan nomor unit secara manual nanti

**Langkah 5: Buat dan Isi**

1. Klik **"Buat"**
2. Sistem menghasilkan semua lantai dan unit
3. Alamat muncul di wilayah dengan semua unit

**Contoh:**

- Gedung: Blok 123
- Lantai: 1 sampai 12
- Unit per lantai: 8
- Hasil: 96 unit dibuat (12 lantai × 8 unit)

#### Menambahkan Alamat Pribadi (Satu Lantai)

**Langkah 1: Mulai Pembuatan**

1. Pilih wilayah
2. Klik **"Tambah Alamat"** atau **"+"**
3. Pilih jenis **"Alamat Pribadi"**

**Langkah 2: Masukkan Informasi Properti**

- **Kode Pos/Alamat Properti**: Pengidentifikasi unik
  - Alamat jalan atau kode pos
  - Setiap properti adalah satu catatan
- **Nama Properti**: Nama rumah opsional
  - Contoh: "123 Jalan Utama", "Villa Sunshine"

**Langkah 3: Atur Lokasi (Opsional)**

- Klik **"Atur Geolokasi"**
- Gunakan peta untuk menentukan lokasi yang tepat
- Membantu navigasi
- Dapat diperbarui nanti

**Langkah 4: Buat**

1. Klik **"Buat Properti"**
2. Unit alamat tunggal dibuat
3. Muncul di daftar wilayah

#### Mengelola Alamat yang Ada

**Edit Nama Alamat:**

1. Pilih wilayah dengan alamat
2. Klik opsi edit untuk alamat
3. Perbarui nama/pos
4. Simpan perubahan

**Ubah Kode Pos:**

1. Akses mode edit alamat
2. Perbarui field kode pos
3. Mungkin mempengaruhi geocoding
4. Simpan dan verifikasi lokasi peta

**Atur Ulang Alamat:**

- Membersihkan semua status unit di alamat
- Catatan dipertahankan
- Gunakan saat alamat sepenuhnya dikerjakan

**Hapus Alamat:**

- Menghapus seluruh alamat dan semua unit
- Tidak dapat dibatalkan
- Konfirmasi dengan hati-hati sebelum menghapus

#### Mengelola Unit (Hanya Alamat Publik)

**Tambah Unit:**

1. Pilih alamat
2. Klik **"Tambah Unit"**
3. Tentukan nomor unit yang akan ditambahkan
4. Unit dibuat secara otomatis

**Hapus Unit:**

1. Klik pada unit tertentu
2. Klik tombol **"Hapus Unit"** di modal
3. Konfirmasi penghapusan
4. Unit dihapus dari alamat

**Ubah Urutan Unit:**

1. Buka modal edit unit
2. Perbarui nomor urutan
3. Mempengaruhi urutan kunjungan
4. Nomor lebih rendah dikunjungi lebih dulu

**Tambah/Hapus Lantai:**

1. Akses manajemen lantai
2. Tambahkan nomor lantai baru
3. Atau hapus seluruh lantai
4. Semua unit di lantai terpengaruh

**Perbarui Geolokasi Unit:**

- Atur koordinat spesifik untuk unit
- Berguna untuk gedung besar
- Membantu navigasi yang tepat
- Fitur opsional

---

---

## Penggunaan Mobile

![Tampilan Peta Penugasan Penginjil](assets/screenshots/publisher_assignment_map_view.png)

*Gambar 26: Antarmuka responsif mobile yang dioptimalkan untuk pelayanan lapangan*

### Menggunakan Ministry Mapper di Ponsel Anda

Ministry Mapper sepenuhnya responsif dan dioptimalkan untuk perangkat mobile, menjadikannya sempurna untuk pelayanan lapangan.

#### Mengakses di Mobile

**Akses Browser:**

1. Buka browser mobile Anda:
   - **iOS**: Safari, Chrome
   - **Android**: Chrome, Firefox, Samsung Internet
2. Navigasi ke URL Ministry Mapper jemaat Anda
3. Login atau klik tautan penugasan
4. Antarmuka otomatis menyesuaikan dengan ukuran layar Anda

**Fitur di Mobile:**

- ✓ Tombol dan kontrol ramah sentuh
- ✓ Gerakan geser untuk navigasi
- ✓ Tata letak yang dioptimalkan untuk layar kecil
- ✓ Target ketuk lebih besar untuk pemilihan mudah
- ✓ Akses penuh ke semua fitur desktop
- ✓ Integrasi peta interaktif dengan GPS

#### Instalasi Progressive Web App (PWA)

![Mode Tampilan Peta Satu Lantai](assets/screenshots/single_story_map_view_mode.png)

*Gambar 27: Mode tampilan peta layar penuh tersedia dalam instalasi PWA*

Instal Ministry Mapper sebagai aplikasi untuk kinerja lebih baik:

**Manfaat Menginstal:**

- 🚀 Pemuatan lebih cepat dengan sumber daya yang di-cache
- 📱 Ikon aplikasi di layar beranda
- 🎯 Pengalaman layar penuh (tanpa UI browser)
- ⚡ Kinerja yang ditingkatkan
- 🔔 Integrasi lebih baik dengan perangkat

**Instalasi iOS (Safari):**

1. Buka Ministry Mapper di Safari
2. Ketuk tombol **Bagikan** (📤)
3. Gulir ke bawah dan ketuk **"Tambahkan ke Layar Beranda"**
4. Edit nama jika diinginkan
5. Ketuk **"Tambahkan"**
6. Ikon aplikasi muncul di layar beranda

**Instalasi Android (Chrome):**

1. Buka Ministry Mapper di Chrome
2. Ketuk tombol menu (⋮)
3. Pilih **"Instal aplikasi"** atau **"Tambahkan ke Layar Beranda"**
4. Konfirmasi instalasi
5. Ikon aplikasi muncul di layar beranda atau laci aplikasi

**Menggunakan Aplikasi yang Diinstal:**

- Luncurkan dari layar beranda seperti aplikasi biasa
- Tidak ada bilah alamat browser
- Pengalaman aplikasi yang mulus
- Pembaruan otomatis

#### Praktik Terbaik Mobile

**Sebelum Keluar:**

1. ✓ Periksa tautan penugasan belum kedaluwarsa
2. ✓ Tinjau wilayah dan peta
3. ✓ Catat instruksi khusus apa pun
4. ✓ Pastikan koneksi internet stabil
5. ✓ Isi daya perangkat Anda sepenuhnya
6. ✓ Pertimbangkan pengisi daya portabel

**Saat di Pelayanan Lapangan:**

1. ✓ Perbarui alamat segera setelah kunjungan
2. ✓ Tambahkan catatan saat informasi masih segar
3. ✓ Gunakan navigasi GPS di peta
4. ✓ Ikuti nomor urutan untuk rute efisien
5. ✓ Hemat baterai dengan meredupkan layar saat tidak diperlukan

**Tips Penggunaan Data:**

- Ministry Mapper menggunakan data minimal
- Tile peta mungkin menggunakan lebih banyak data
- Sebagian besar pembaruan < 1KB masing-masing
- Cocok untuk penggunaan data mobile

### Kemampuan dan Batasan Offline

**Koneksi Internet Diperlukan:**

Ministry Mapper memerlukan internet aktif untuk:

- ✗ Memuat data wilayah
- ✗ Menyimpan pembaruan status
- ✗ Sinkronisasi real-time
- ✗ Menampilkan peta
- ✗ Autentikasi pengguna

**Fitur Offline Terbatas:**

- Aset statis di-cache (shell aplikasi)
- Wilayah yang dimuat sebelumnya mungkin ditampilkan
- **Tidak dapat membuat pembaruan offline**
- **Pembaruan tidak diantrekan untuk sinkronisasi nanti**

**Menangani Kehilangan Koneksi:**

- Sistem mendeteksi kehilangan koneksi
- Menampilkan peringatan status koneksi
- Otomatis terhubung kembali saat tersedia
- Lanjutkan pekerjaan saat koneksi dipulihkan

**Rekomendasi:**

- ✓ Pastikan koneksi andal sebelum memulai
- ✓ Uji koneksi di lokasi wilayah
- ✓ Miliki rencana cadangan untuk area tanpa internet
- ✓ Pertimbangkan hotspot WiFi portabel jika diperlukan
- ✓ Perbarui alamat saat koneksi aktif

---

---

## Pengaturan Akun dan Profil

### Manajemen Profil Pribadi

![Masuk](assets/screenshots/sign_in.png)

*Gambar 28: Antarmuka login untuk mengakses akun Ministry Mapper Anda*

**Mengakses Profil Anda:**

1. Klik **nama/ikon profil** Anda (pojok kanan atas)
2. Pilih **"Profil"** dari menu dropdown
3. Lihat dan kelola pengaturan akun Anda

**Opsi Profil yang Tersedia:**

- Lihat informasi akun (nama, email)
- Ubah kata sandi
- Lihat keanggotaan jemaat
- Akses manajemen pengguna (hanya Administrator)
- Keluar

### Mengubah Kata Sandi Anda

**Persyaratan Keamanan:**

- Minimum 6 karakter
- Setidaknya satu angka
- Setidaknya satu huruf kapital
- Harus cocok dengan konfirmasi

**Langkah untuk Mengubah:**

1. Pergi ke profil Anda
2. Klik **"Ubah Kata Sandi"**
3. Masukkan **kata sandi saat ini** Anda
4. Masukkan **kata sandi baru**
5. **Konfirmasi kata sandi baru**
6. Klik **"Simpan"** atau **"Ubah Kata Sandi"**
7. Pesan sukses mengonfirmasi perubahan

> **💡 Tips**: Gunakan kata sandi yang kuat dan unik. Pertimbangkan menggunakan pengelola kata sandi.

### Pemulihan Kata Sandi

![Lupa Kata Sandi](assets/screenshots/forgot_password.png)

*Gambar 29: Halaman pemulihan kata sandi untuk mengatur ulang kata sandi yang terlupa*

Lupa kata sandi Anda? Proses pemulihan mudah:

1. Pergi ke halaman login
2. Klik tautan **"Lupa kata sandi?"**
3. Masukkan **alamat email terdaftar** Anda
4. Klik **"Lanjutkan"** atau **"Kirim Tautan Reset"**
5. Periksa kotak masuk email Anda

![Email Reset Kata Sandi](assets/screenshots/reset_password_email.png)

*Gambar 30: Email reset kata sandi dengan tautan aman untuk membuat kata sandi baru*

6. Klik tautan reset kata sandi (valid untuk waktu terbatas)
7. Buat kata sandi baru yang memenuhi persyaratan
8. Konfirmasi kata sandi baru
9. Login dengan kata sandi baru

**Jika Anda tidak menerima email:**

- Periksa folder spam/sampah
- Verifikasi Anda memasukkan email yang benar
- Tunggu beberapa menit dan coba lagi
- Hubungi administrator jika masalah berlanjut

### Pemilihan Bahasa

![Daftar Pemilihan Bahasa](assets/screenshots/language_selection_list.png)

*Gambar 31: Antarmuka pemilihan bahasa menampilkan semua bahasa yang didukung*

Ministry Mapper mendukung beberapa bahasa untuk jemaat internasional.

**Bahasa yang Didukung:**

- 🇬🇧 Inggris (en)
- 🇯🇵 Jepang (ja / 日本語)
- 🇰🇷 Korea (ko / 한국어)
- 🇨🇳 Tionghoa (zh / 中文)
- 🇮🇩 Indonesia (id / Bahasa Indonesia)
- 🇲🇾 Melayu (ms / Bahasa Melayu)

**Bagaimana Bahasa Ditentukan:**

- Otomatis terdeteksi dari pengaturan bahasa browser
- Menggunakan preferensi bahasa sistem operasi Anda
- Tidak perlu pemilihan manual dalam kebanyakan kasus

**Untuk Mengubah Bahasa:**

![Pengaturan Tema Terang Gelap](assets/screenshots/dark_light_theme_settings.png)

*Gambar 32: Pengaturan tema dan tampilan termasuk preferensi bahasa*

Ubah pengaturan bahasa browser (Chrome: Settings → Languages | Safari: System Preferences → Language & Region | Firefox: Settings → General → Language), lalu segarkan Ministry Mapper. Semua elemen antarmuka, pesan, dan teks bantuan sepenuhnya diterjemahkan.

### Pengaturan Tampilan dan Tema

Ministry Mapper mendukung tema terang dan gelap untuk menyesuaikan preferensi Anda dan mengurangi ketegangan mata.

**Mengubah Tema:**

Ikon profil → Pengaturan tema/tampilan → Pilih:
- **Mode Terang**: Antarmuka cerah untuk siang hari
- **Mode Gelap**: Kecerahan dikurangi untuk cahaya rendah (hemat baterai di OLED)
- **Default Sistem**: Mengikuti pengaturan perangkat secara otomatis

---

## Praktik Terbaik

### Untuk Penginjil

| Fase | Praktik Terbaik |
|------|-----------------|
| **Sebelum Memulai** | Tinjau wilayah di peta • Periksa catatan/instruksi • Rencanakan rute menggunakan urutan • Catat alamat "Jangan Kunjungi" • Pastikan perangkat terisi daya |
| **Selama Pelayanan** | Perbarui segera setelah setiap kunjungan • Tambahkan catatan terperinci dan sopan • Ikuti nomor urutan • Gunakan peta untuk navigasi • Tandai "Tidak di Rumah" secara akurat |
| **Setelah Selesai** | Tinjau semua pembaruan • Tambahkan observasi akhir • Beri tahu administrator jika selesai • Laporkan masalah alamat |

### Untuk Konduktor

- Atur waktu kedaluwarsa tautan yang sesuai berdasarkan ukuran wilayah
- Sertakan nama penginjil untuk pelacakan
- Bersihkan penugasan kedaluwarsa secara teratur
- Pantau penyelesaian wilayah dan tindak lanjuti penugasan yang terlambat
- Posting pesan dan instruksi yang jelas

### Untuk Administrator

| Area | Praktik Terbaik |
|------|-----------------|
| **Setup Wilayah** | Penamaan konsisten • Kode pendek dan bermakna • Deskripsi jelas • Verifikasi lokasi peta • Urutan logis |
| **Manajemen Data** | Pembersihan teratur • Atur ulang wilayah yang selesai • Verifikasi akurasi • Backup eksternal • Latih pengguna |
| **Manajemen Pengguna** | Penetapan peran yang sesuai • Hapus pengguna tidak aktif • Respons cepat terhadap permintaan • Komunikasi yang jelas |

### Prinsip Umum

- **Rencanakan Lebih Dulu**: Tinjau sebelum keluar
- **Bekerja Sistematis**: Selesaikan satu bagian pada satu waktu
- **Perbarui Segera**: Jangan menunggu untuk mencatat informasi
- **Komunikasikan dengan Jelas**: Tulis catatan yang dapat dipahami dan sopan
- **Jadilah Menyeluruh**: Cakup semua alamat dengan tekun
- **Tetap Fleksibel**: Beradaptasi dengan kebutuhan wilayah

---

## Pemecahan Masalah Umum

### Masalah Login

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Kata Sandi Salah** | Error "Invalid credentials" | Verifikasi email • Periksa Caps Lock • Gunakan "Lupa Kata Sandi" → Reset melalui email |
| **Akun Tidak Terverifikasi** | Pesan "Email not verified" | Periksa inbox/spam untuk email verifikasi • Klik tautan • Minta yang baru jika kedaluwarsa |
| **Tidak Ada Akses Jemaat** | Login tetapi tidak ada wilayah yang ditampilkan | Hubungi administrator untuk mengundang Anda dan menetapkan peran • Keluar/masuk setelah akses diberikan |
| **Masalah OTP** | Tidak menerima/kode OTP tidak valid | Periksa spam • Kode kedaluwarsa dalam 5-10 menit • Minta kode baru • Verifikasi alamat email |

---

### Masalah Pembaruan Data

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Perubahan Tidak Tersimpan** | Simpan tidak bertahan, hilang setelah refresh | Periksa koneksi internet • Segarkan halaman (Ctrl/Cmd + R) • Bersihkan cache browser • Coba browser berbeda • Periksa pengeditan bersamaan |
| **Pembaruan Real-Time Tidak Muncul** | Perubahan oleh orang lain tidak ditampilkan | Pastikan internet aktif • Jaga halaman tetap terbuka • Segarkan untuk memaksa pembaruan • Periksa status koneksi |

---

### Masalah Peta dan Navigasi

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Peta Tidak Dimuat** | Kotak abu-abu, error, peta beku | Segarkan halaman (F5) • Tunggu 10-15 detik • Periksa kecepatan internet • Bersihkan cache • Coba browser berbeda • Aktifkan JavaScript |
| **Lokasi Salah** | Penanda salah tempat | Administrator: Perbarui geolokasi • Verifikasi kode pos • Gunakan "Perbarui Geolokasi" • Gunakan lat/long jika diperlukan |
| **Petunjuk Arah Tidak Berfungsi** | Masalah navigasi | Verifikasi lokasi asal jemaat • Periksa alamat memiliki koordinat valid |

---

### Masalah Tautan Penugasan

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Tautan Kedaluwarsa** | "Link has expired", error 404 | Hubungi administrator/konduktor untuk tautan baru (tautan kedaluwarsa setelah waktu yang ditentukan, biasanya 24 jam - ini adalah fitur keamanan) |
| **Tautan Tidak Berfungsi** | Tidak terbuka, pesan error | Pastikan seluruh tautan disalin (periksa line break) • Salin-tempel ke browser (jangan ketik) • Verifikasi tidak kedaluwarsa • Hubungi administrator |

---

### Masalah Izin dan Akses

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Izin Ditolak** | "Insufficient permissions", fitur abu-abu | Verifikasi peran Anda dengan administrator • Keluar, bersihkan cache, masuk kembali • Minta upgrade peran jika diperlukan |
| **Data Jemaat Salah** | Wilayah/data tidak dikenal | Verifikasi akun yang benar • Periksa pemilih jemaat • Keluar/masuk • Hubungi administrator |

---

### Masalah Kinerja

| Masalah | Gejala | Solusi |
|---------|--------|--------|
| **Pemuatan Lambat** | Waktu muat lama, lag, penundaan | Periksa kecepatan internet • Tutup tab yang tidak perlu • Bersihkan cache • Restart browser • Coba waktu berbeda • Laporkan jika berlanjut |
| **Crash/Freeze** | Aplikasi berhenti merespons | Segarkan halaman • Bersihkan cache/cookies • Perbarui browser • Coba browser berbeda • Restart perangkat • Periksa memori |

---

### Kompatibilitas Browser

#### Kompatibilitas Browser

**Didukung:** Chrome, Firefox, Safari, Edge (versi terbaru) • iOS Safari (iOS 13+) • Android Chrome

**Tidak Didukung:** Internet Explorer, browser usang

**Jika ada masalah:** Perbarui browser • Aktifkan JavaScript • Izinkan cookies • Nonaktifkan pencegahan pelacakan ketat • Coba browser berbeda

---

## Mendapatkan Bantuan dan Dukungan

### Saluran Dukungan

**1. Administrator Jemaat Anda**

- **Titik kontak pertama** untuk sebagian besar masalah
- Dapat membantu dengan:
  - Akses akun dan peran
  - Pertanyaan wilayah
  - Tautan penugasan
  - Konfigurasi lokal

**2. Dokumentasi Resmi**

- **GitHub Wiki**: https://github.com/rimorin/ministry-mapper/wiki
- Panduan komprehensif untuk semua peran
- Dokumentasi setup dan keamanan
- FAQ dan solusi umum

**3. Masalah Teknis**

- **GitHub Issues**: https://github.com/rimorin/ministry-mapper/issues
- Laporkan bug dan masalah teknis
- Periksa masalah yang ada terlebih dahulu
- Cari masalah serupa

### Melaporkan Masalah Secara Efektif

**Sertakan dalam Laporan Anda:**
- Apa yang Anda coba lakukan
- Apa yang terjadi sebaliknya (pesan error, screenshot)
- Langkah untuk mereproduksi masalah
- Browser dan versi
- Perangkat dan OS
- Jenis akun (Penginjil/Konduktor/Administrator)

**Contoh Laporan yang Baik:**
```
Masalah: Tidak dapat menyimpan pembaruan status alamat
Langkah: Buka tautan → Klik #05-123 → Ubah ke "Selesai" → Klik Simpan → Error: "Failed to update"
Browser: Chrome 120 | Perangkat: iPhone 12, iOS 17 | Akun: Tautan penginjil
Screenshot: [terlampir]
```

### Kontak Darurat

Untuk masalah mendesak:

- Hubungi administrator jemaat Anda secara langsung
- Siapkan nomor telepon/email
- Jelaskan urgensi dengan jelas
- Siapkan detail yang relevan

---

---

## Referensi Cepat

### Pintasan Keyboard

Ministry Mapper menggunakan pintasan browser standar:

| Pintasan         | Aksi                                        |
| ---------------- | ------------------------------------------- |
| `Escape`         | Tutup modal/dialog yang terbuka             |
| `Tab`            | Navigasi antar field formulir               |
| `Enter`          | Kirim formulir atau konfirmasi aksi         |
| `Ctrl/Cmd + R`   | Segarkan halaman                            |
| Pengeditan standar | Salin, tempel, pilih semua berfungsi di field teks |

### Referensi Cepat Status

| Status          | Simbol | Kapan Menggunakan                     |
| --------------- | ------ | ------------------------------------- |
| **Belum Selesai**| ⚪     | Alamat belum dikunjungi (default)     |
| **Selesai**     | ✅     | Berhasil menghubungi penghuni         |
| **Tidak di Rumah**| 🏠   | Tidak ada yang menjawab pintu         |
| **Jangan Kunjungi**| 🚫  | Penghuni meminta tidak dikunjungi     |
| **Tidak Valid** | ❌     | Alamat tidak ada atau tidak dapat diakses |

### Referensi Cepat Kemampuan Peran

| Fitur                  | Penginjil | Hanya-Baca | Konduktor | Administrator |
| ---------------------- | :-------: | :--------: | :-------: | :-----------: |
| Lihat melalui tautan penugasan |     ✓     |     -     |     -     |       -       |
| Lihat semua wilayah    |     -     |     ✓     |     ✓     |       ✓       |
| Perbarui status alamat |     ✓     |     -     |     -     |       ✓       |
| Buat penugasan         |     -     |     -     |     ✓     |       ✓       |
| Posting pesan          |     -     |     -     |     ✓     |       ✓       |
| Kelola wilayah         |     -     |     -     |     -     |       ✓       |
| Kelola pengguna        |     -     |     -     |     -     |       ✓       |
| Konfigurasikan pengaturan |     -     |     -     |     -     |       ✓       |

---



## Privasi dan Keamanan

### Melindungi Informasi

Ministry Mapper menangani alamat sensitif dan informasi pribadi. Harap perhatikan panduan berikut:

**Keamanan Akun:**

- ✓ **Jangan pernah bagikan kredensial login** dengan siapa pun
- ✓ **Gunakan kata sandi yang kuat dan unik** (minimum 6 karakter dengan angka dan huruf kapital)
- ✓ **Keluar di perangkat bersama** selalu
- ✓ **Aktifkan OTP jika tersedia** untuk keamanan ekstra
- ✓ **Laporkan aktivitas mencurigakan** segera

**Privasi Data:**

- ✓ **Catat hanya informasi yang diperlukan** dalam catatan
- ✓ **Jadilah sopan dan faktual** dalam semua deskripsi
- ✓ **Tidak ada data pribadi sensitif** (medis, keuangan, dll.)
- ✓ **Ikuti permintaan penghuni** untuk privasi
- ✓ **Patuhi undang-undang privasi** (GDPR, CCPA, peraturan lokal)

**Kepatuhan Hukum:**

- ⚠️ **GDPR (Eropa)**: Persyaratan perlindungan data pribadi
- ⚠️ **CCPA (California)**: Hak privasi konsumen
- ⚠️ **LGPD (Brazil)**: Peraturan perlindungan data
- ⚠️ **Hukum Lokal**: Periksa persyaratan wilayah Anda

### Informasi Apa yang Disimpan

**Data Pengguna:**

- Detail akun (nama, email, status verifikasi)
- Penetapan peran jemaat
- Tautan penugasan yang dibuat/diakses
- Timestamp aktivitas

**Data Wilayah:**

- Informasi alamat dan unit
- Pembaruan dan riwayat status
- Catatan dan informasi kunjungan
- Koordinat geolokasi
- Pelacakan kemajuan

**Data Sistem:**

- Sesi login
- Langganan real-time
- Riwayat pesan
- Pengaturan konfigurasi

### Penyimpanan dan Keamanan Data

| Komponen | Detail |
|----------|--------|
| **Backend** | Database PocketBase yang dikelola oleh administrator • Lokasi hosting ditentukan oleh jemaat • Kontrol akses berbasis peran |
| **Sinkronisasi Real-time** | Langganan PocketBase • Koneksi HTTPS terenkripsi • Koneksi ulang otomatis • Manajemen sesi |
| **Sisi Klien** | Service worker hanya meng-cache aset statis • Tidak ada data sensitif disimpan secara lokal • Auto-update pada versi baru |
| **Tanggung Jawab Administrator** | Implementasikan prosedur backup • Lakukan audit keamanan • Jaga backend tetap terbaru • Pantau log akses |

---

## Kesimpulan

Terima kasih telah menggunakan Ministry Mapper untuk mendukung aktivitas pelayanan lapangan jemaat Anda. Solusi berbasis web modern ini membawa efisiensi, kolaborasi, dan manfaat lingkungan ke manajemen wilayah.

### Poin-poin Utama

| Peran | Tanggung Jawab Utama |
|-------|---------------------|
| **Penginjil** | Akses melalui tautan • Perbarui segera setelah kunjungan • Tulis catatan sopan • Ikuti urutan |
| **Konduktor** | Buat penugasan • Pantau kemajuan • Posting pesan • Koordinasikan aktivitas |
| **Administrator** | Konfigurasikan pengaturan • Kelola wilayah • Tetapkan peran • Pastikan keamanan |

### Fitur Sistem

**Stack Teknologi:**

- ✓ Frontend React 19 + TypeScript
- ✓ Backend PocketBase untuk manajemen data
- ✓ Leaflet dengan OpenStreetMap untuk navigasi
- ✓ Sinkronisasi real-time
- ✓ PWA responsif mobile
- ✓ Dukungan multi-bahasa
- ✓ Kontrol akses berbasis peran
- ✓ Pemantauan error Sentry

**Manfaat:**

- 🌱 **Ramah Lingkungan**: Menghilangkan pemborosan kertas
- ⚡ **Real-Time**: Pembaruan instan di semua perangkat
- 📱 **Mobile-First**: Berfungsi di perangkat apa pun dengan internet
- 🗺️ **Peta Terintegrasi**: Pemetaan interaktif untuk navigasi mudah
- 🔒 **Aman**: Izin berbasis peran dan dukungan OTP
- 🌍 **Multi-Bahasa**: Dukungan untuk 7+ bahasa
- 💾 **Andal**: Backend PocketBase dengan sinkronisasi real-time

---

**Versi**: Rujuk ke versi deployment Anda
**Terakhir Diperbarui**: 2024

Untuk dukungan teknis, hubungi administrator jemaat Anda atau kunjungi wiki GitHub.

---
