# Panduan Pengguna Ministry Mapper

## Pengenalan

Selamat datang ke Ministry Mapper, aplikasi web moden yang direka untuk membantu sidang mengurus wilayah khidmat lapangan dengan cekap. Panduan ini akan membimbing anda melalui segala yang perlu anda ketahui untuk bermula dan memanfaatkan aplikasi sepenuhnya.

**Apakah Ministry Mapper?**

Ministry Mapper adalah sistem pengurusan wilayah digital yang menggantikan kaedah berasaskan kertas tradisional. Ia membolehkan sidang untuk:

- Mengatur dan menugaskan wilayah secara digital
- Menjejak lawatan khidmat lapangan dalam masa nyata
- Menyelaras aktiviti merentas berbilang penerbit
- Mengakses wilayah dari mana-mana peranti dengan internet

**Faedah Utama:**

- ✓ Mesra alam - menghapuskan pembaziran kertas
- ✓ Kemas kini masa nyata melalui penyegerakan awan
- ✓ Berfungsi pada mana-mana peranti (desktop, tablet, mudah alih)
- ✓ Peta interaktif bersepadu untuk navigasi mudah
- ✓ Kawalan akses berasaskan peranan yang selamat

## Bermula

### Mencipta Akaun Anda

![Halaman Pendaftaran](assets/screenshots/sign_up.png)

*Rajah 1: Borang pendaftaran untuk mencipta akaun Ministry Mapper baharu*

**Langkah 1: Akses Halaman Pendaftaran**

![Log Masuk](assets/screenshots/sign_in.png)

*Rajah 2: Halaman log masuk dengan pilihan Daftar*

1. Lawati URL Ministry Mapper sidang anda
2. Klik butang **"Daftar"** pada halaman log masuk

**Langkah 2: Pilih Kaedah Pendaftaran**

| Pendaftaran Tradisional | Google OAuth (Disyorkan) |
|---------------------|---------------------------|
| Memerlukan e-mel, kata laluan (6+ aksara, 1 nombor, 1 huruf besar), dan pengesahan e-mel | Pendaftaran satu klik menggunakan akaun Google |
| Pengesahan e-mel manual diperlukan | Pengesahan e-mel automatik |
| Perlu ingat kata laluan lain | Tiada kata laluan untuk diurus |
| Terima Dasar Privasi & Terma | Keselamatan dipertingkatkan melalui Google |

**Pendaftaran Tradisional:**
1. Isikan: Nama, E-mel, Kata Laluan, Sahkan Kata Laluan
2. Terima Dasar Privasi dan Terma Perkhidmatan
3. Klik **"Cipta Akaun"**

**Pendaftaran Google OAuth:**
1. Klik butang **"Log masuk dengan Google"** di bawah "Atau teruskan dengan"
2. Pilih akaun Google anda
3. Benarkan Ministry Mapper akses profil asas
4. Akaun dicipta secara automatik

**Langkah 3: Sahkan E-mel Anda**

![E-mel Pengesahan Pendaftaran](assets/screenshots/sign_up_verification_email.png)

*Rajah 3: Mesej pengesahan e-mel dihantar selepas penciptaan akaun*

1. Semak peti masuk e-mel anda untuk mesej pengesahan dari Ministry Mapper
2. Klik pautan pengesahan dalam e-mel
3. Anda akan melihat pengesahan bahawa akaun anda telah disahkan

**Langkah 4: Tunggu Akses Sidang**

Selepas pengesahan:

1. Kembali ke halaman log masuk
2. Log masuk dengan e-mel dan kata laluan anda
3. Jika Kata Laluan Sekali (OTP) diaktifkan, semak e-mel anda untuk kod

![Halaman Pengesahan OTP](assets/screenshots/otp_verification_page.png)

*Rajah 4: Skrin pengesahan Kata Laluan Sekali (OTP)*

![E-mel Pengesahan OTP](assets/screenshots/otp_verification_email.png)

*Rajah 5: E-mel yang mengandungi kod OTP untuk pengesahan log masuk*

4. **Penting**: Anda tidak akan melihat sebarang wilayah lagi - pentadbir mesti memberikan anda akses ke sidang anda terlebih dahulu
5. Hubungi hamba wilayah atau pentadbir sidang anda untuk meminta akses

### Log Masuk ke Ministry Mapper

![Log Masuk Google OAuth](assets/screenshots/google_oauth_sign_in.png)

*Rajah 6: Pilihan log masuk Google OAuth untuk pengesahan lebih pantas dan selamat*

Sebaik sahaja akaun anda disahkan dan anda telah diberikan akses sidang:

**Log Masuk Standard:**
1. Navigasi ke URL Ministry Mapper sidang anda
2. Masukkan alamat e-mel dan kata laluan anda
3. Klik **"Log Masuk"**

**Log Masuk Google OAuth (Lebih Pantas):**
1. Klik butang **"Log masuk dengan Google"**
2. Pilih akaun Google anda
3. Log masuk secara automatik (tiada kata laluan diperlukan)

**Langkah 3: Lengkapkan Pengesahan OTP (Jika Diaktifkan)**

Jika sidang anda telah mengaktifkan keselamatan Kata Laluan Sekali:

1. Selepas memasukkan kelayakan, anda akan melihat skrin pengesahan OTP
2. Semak e-mel anda untuk kod pengesahan
3. Masukkan kod 6 digit dari e-mel
4. Klik **"Sahkan"** atau **"Hantar"**
5. Kod tamat tempoh dalam 5-10 minit

**Langkah 4: Akses Papan Pemuka Anda**

- **Penerbit**: Anda tidak akan melihat papan pemuka - gunakan pautan tugasan yang dihantar kepada anda
- **Baca Sahaja, Konduktor, Pentadbir**: Anda akan melihat papan pemuka khusus peranan anda

> **💡 Petua**: Kekal log masuk pada peranti yang dipercayai untuk kemudahan, tetapi sentiasa log keluar pada komputer yang dikongsi.

### Memahami Peranan Pengguna

Ministry Mapper menggunakan sistem kawalan akses empat peringkat. Peranan anda menentukan ciri yang boleh anda akses dan tindakan yang boleh anda lakukan.

#### Hierarki Peranan

```
Pentadbir (Akses Penuh)
    ↓
Konduktor (Urus Tugasan)
    ↓
Baca Sahaja (Lihat Sahaja)
    ↓
Penerbit (Akses Pautan Sahaja)
```

---

#### 👤 Penerbit

**Kaedah Akses**: Melalui pautan tugasan yang dihantar oleh pentadbir atau konduktor

**Apa yang Penerbit Boleh Lakukan:**

- ✓ Akses wilayah melalui pautan yang dikongsi
- ✓ Lihat peta wilayah dengan pemetaan interaktif
- ✓ Kemas kini status alamat selepas lawatan
  - Tandakan sebagai: Selesai, Tiada di Rumah, Jangan Panggil, Tidak Sah
- ✓ Tambah nota lawatan ke alamat
- ✓ Jejak bilangan percubaan "tiada di rumah"
- ✓ Lihat butiran alamat dan jujukan

**Apa yang Penerbit Tidak Boleh Lakukan:**

- ✗ Tiada akses papan pemuka
- ✗ Tidak boleh lihat semua wilayah sidang
- ✗ Tidak boleh cipta atau urus wilayah
- ✗ Tidak boleh akses pautan tugasan tamat tempoh selepas masa yang ditetapkan (lalai: 24 jam, boleh dikonfigurasi oleh pentadbir sidang)

**Paling Sesuai Untuk**: Penerbit biasa yang melakukan khidmat lapangan

---

#### 👓 Baca Sahaja

**Kaedah Akses**: Log masuk akaun pengguna dengan kebenaran baca sahaja

**Apa yang Pengguna Baca Sahaja Boleh Lakukan:**

- ✓ Lihat semua wilayah dalam sidang
- ✓ Lihat maklumat alamat lengkap
- ✓ Lihat kemajuan dan statistik wilayah
- ✓ Akses peta wilayah
- ✓ Lihat mesej sidang

**Apa yang Pengguna Baca Sahaja Tidak Boleh Lakukan:**

- ✗ Tidak boleh ubah suai sebarang data wilayah atau alamat
- ✗ Tidak boleh cipta atau padam wilayah
- ✗ Tidak boleh urus tugasan
- ✗ Tidak boleh tukar tetapan sidang

**Paling Sesuai Untuk**: Penyelia yang memerlukan keterlihatan tanpa keupayaan mengedit

---

#### 🎯 Konduktor

**Kaedah Akses**: Log masuk akaun pengguna dengan kebenaran konduktor

**Keupayaan Konduktor Termasuk:**

- ✓ **Semua yang Baca Sahaja boleh lakukan**, DITAMBAH:
- ✓ Cipta dan urus tugasan wilayah
- ✓ Jana pautan tugasan untuk penerbit
- ✓ Lihat semua sejarah tugasan
- ✓ Akses dan siarkan mesej sidang
- ✓ Urus pilihan sidang (jenis status isi rumah)
- ✓ Lihat status penyelesaian wilayah

**Apa yang Konduktor Tidak Boleh Lakukan:**

- ✗ Tidak boleh cipta atau padam wilayah
- ✗ Tidak boleh edit butiran alamat (alamat, unit, tingkat)
- ✗ Tidak boleh urus peranan atau kebenaran pengguna
- ✗ Tidak boleh ubah suai tetapan teras sidang

**Paling Sesuai Untuk**: Penyelaras khidmat lapangan dan penyelia kumpulan

---

#### 👑 Pentadbir (Hamba Wilayah)

**Kaedah Akses**: Log masuk akaun pengguna dengan kebenaran pentadbiran penuh

**Pentadbir Mempunyai Kawalan Lengkap:**

- ✓ **Semua yang Konduktor boleh lakukan**, DITAMBAH:
- ✓ Cipta, edit, dan padam wilayah
- ✓ Tambah, ubah suai, dan buang alamat
- ✓ Urus bangunan dan unit
- ✓ Konfigurasi tetapan sidang
  - Tetapkan percubaan tiada di rumah maksimum
  - Konfigurasi masa tamat pautan
  - Sediakan pilihan jenis isi rumah
- ✓ Jemput dan urus pengguna
- ✓ Tetapkan peranan dan kebenaran pengguna
- ✓ Tetapkan semula wilayah dan alamat
- ✓ Urus koordinat geolokasi

**Paling Sesuai Untuk**: Hamba wilayah dan mereka yang mengurus sistem wilayah sidang

---

> **💡 Nota**: Hubungi pentadbir sidang anda jika anda perlu menukar peranan anda atau jika anda tidak pasti peranan yang anda ada sekarang.

## Ciri Utama

### Gambaran Keseluruhan Papan Pemuka

![Papan Pemuka Pentadbir](assets/screenshots/admin_dashboard.png)

*Rajah 6: Papan pemuka pentadbir menunjukkan pemilih wilayah dan kawalan utama*

Antara muka papan pemuka berbeza berdasarkan peranan anda:

#### Untuk Penerbit

Penerbit **tidak mempunyai akses papan pemuka**. Sebaliknya, mereka:

- Menerima pautan tugasan melalui e-mel atau mesej
- Klik pautan untuk mengakses wilayah yang ditugaskan
- Bekerja terus dalam paparan wilayah
- Pautan tamat tempoh secara automatik selepas masa yang dikonfigurasi (lalai: 24 jam)

#### Untuk Konduktor dan Pentadbir

Papan pemuka menyediakan gambaran keseluruhan komprehensif:

**1. Pemilih Wilayah** (Dropdown Atas)

- Pilih wilayah mana yang hendak dilihat atau diurus
- Menunjukkan kod dan penerangan wilayah
- Navigasi pantas antara wilayah

**2. Butang Tindakan Utama**

- 📋 **Tugasan**: Lihat dan urus pautan tugasan
- 💬 **Mesej**: Siarkan dan lihat mesej sidang
- ⚙️ **Tetapan**: Akses konfigurasi sidang (Pentadbir sahaja)
- 👥 **Pengguna**: Urus peranan dan jemputan pengguna (Pentadbir sahaja)

**3. Panel Maklumat Wilayah**

- Kod dan penerangan wilayah
- Bar kemajuan menunjukkan peratusan penyelesaian
- Cap masa kemas kini terakhir
- Jumlah unit vs. unit selesai

**4. Pilihan Paparan Wilayah**

- 🗺️ **Paparan Peta**: Paparan peta interaktif
- 📋 **Paparan Senarai**: Paparan jadual semua alamat

**5. Menu Dail Pantas** (Butang Tindakan Terapung)

![Dail Pantas Halaman Pentadbir](assets/screenshots/speeddial_admin_page.png)

*Rajah 6a: Butang tindakan terapung dail pantas menyediakan akses pantas kepada tindakan biasa*

Butang **➕** (sudut kanan bawah) menyediakan akses pantas kepada:
- 🗺️ **Mod Paparan Peta**: Togol paparan peta skrin penuh
- 📋 **Pautan Pantas**: Cipta pautan tugasan dengan pantas (Konduktor & Pentadbir)
- Tindakan sensitif konteks berdasarkan paparan semasa

---

### Melihat Wilayah

#### Paparan Wilayah Penerbit

![Skrin Tugasan Penerbit](assets/screenshots/publisher_assignment_screen.png)

*Rajah 7: Paparan penerbit wilayah yang ditugaskan dengan peta dan senarai alamat*

**Mengakses Tugasan Anda:**

1. Klik pautan tugasan yang dihantar oleh pentadbir/konduktor anda
2. Wilayah dimuatkan secara automatik dengan:
   - Peta Google Interaktif menunjukkan semua alamat
   - Penanda boleh klik untuk setiap lokasi
   - Senarai alamat/unit untuk dilawati
   - Peratusan kemajuan semasa

**Maklumat Wilayah Dipaparkan:**

- **Kod Wilayah**: Pengecam (cth., "T-001")
- **Penerangan**: Nama atau kawasan wilayah
- **Bar Kemajuan**: Status penyelesaian visual
- **Peta**: Peta interaktif dengan penanda alamat
- **Senarai Alamat**: Semua alamat dengan status semasa

#### Paparan Wilayah Pentadbir/Konduktor

![Papan Pemuka Konduktor](assets/screenshots/conductor_dashboard.png)

*Rajah 8: Papan pemuka konduktor dengan pemilih wilayah dan pilihan pengurusan*

**Melihat Wilayah:**

1. Log masuk ke papan pemuka anda
2. Pilih wilayah dari menu dropdown
3. Lihat maklumat terperinci:
   - Kod dan penerangan wilayah
   - Statistik penyelesaian (cth., "15/20 selesai - 75%")
   - Peta interaktif dengan semua lokasi
   - Senarai alamat/unit lengkap dengan butiran
   - Butang pengurusan (Edit, Padam, Tetapkan Semula)

**Pilihan Pengurusan:**

- ✏️ **Edit Wilayah**: Tukar nama, kod, atau penerangan
- 🗑️ **Padam Wilayah**: Buang seluruh wilayah
- 🔄 **Tetapkan Semula Wilayah**: Kosongkan semua status alamat
- ➕ **Tambah Alamat**: Cipta alamat baharu dalam wilayah

### Bekerja Dengan Alamat

#### Memahami Maklumat Alamat

![Butiran Status Alamat](assets/screenshots/address_status_details.png)

*Rajah 9: Kad alamat menunjukkan semua butiran alamat dan status semasa*

Setiap alamat atau unit dalam Ministry Mapper memaparkan maklumat komprehensif:

**Maklumat Asas:**

- **Alamat/Nombor Unit**: Pengecam lokasi (cth., "#05-123")
- **Tingkat**: Tahap bangunan (untuk hartanah berbilang tingkat)
- **Jenis**: Klasifikasi isi rumah berdasarkan pilihan sidang
  - Contoh: Cina, Inggeris, Tamil, Sepanyol
  - Berbilang jenis boleh dipilih jika dikonfigurasi
- **Jujukan**: Nombor urutan lawatan

**Maklumat Status:**

- **Status**: Status lawatan semasa (lihat jenis status di bawah)
- **Kiraan Tiada di Rumah**: Bilangan percubaan lawatan tanpa jawapan
- **Tarikh Jangan Panggil**: Bila DNC ditandakan (jika berkenaan)

**Penjejakan Aktiviti:**

- **Nota**: Maklumat penting tentang lawatan atau penghuni rumah
- **Kemas Kini Terakhir**: Tarikh dan masa kemas kini terkini
- **Dikemas Kini Oleh**: Nama pengguna orang yang membuat perubahan terakhir

---

#### Jenis Status Alamat

Ministry Mapper menggunakan lima jenis status standard:

| Status          | Warna         | Penerangan            | Penggunaan                                      |
| --------------- | ------------- | ---------------------- | ------------------------------------------ |
| **Belum Selesai**    | Putih/Lalai | Belum dilawati        | Status awal untuk semua alamat           |
| **Selesai**        | Hijau         | Berjaya dihubungi | Penghuni rumah ada di rumah dan dihubungi         |
| **Tiada di Rumah**    | Kuning/Oren | Tiada yang menjawab        | Jejak sehingga percubaan maksimum (boleh dikonfigurasi)       |
| **Jangan Panggil** | Merah           | Meminta tiada lawatan    | Penghuni rumah meminta tiada hubungan lanjut   |
| **Tidak Sah**     | Kelabu          | Tidak boleh diakses           | Alamat tidak wujud atau tidak boleh dilawati |

> **💡 Petua**: Apabila "Tiada di Rumah" mencapai percubaan maksimum (ditetapkan oleh pentadbir), alamat secara automatik ditandakan sebagai selesai dalam pengiraan kemajuan.

---

#### Mengemas Kini Status Alamat

![Status Tiada di Rumah Alamat](assets/screenshots/address_not_home_status.png)

*Rajah 10: Modal kemas kini status menunjukkan semua medan dan pilihan*

**Proses Langkah demi Langkah:**

**1. Cari Alamat**

- Tatal melalui senarai alamat
- Atau klik penanda pada peta
- Alamat dikumpulkan mengikut tingkat untuk bangunan berbilang tingkat

**2. Buka Modal Kemas Kini**

- Klik pada kad alamat/unit
- Modal kemas kini akan dibuka dengan maklumat semasa

**3. Kemas Kini Status** (Diperlukan)

Pilih status yang sesuai:

**📗 Selesai** - Apabila berjaya dihubungi

- Pilih ini apabila seseorang menjawab
- Perbualan berlaku atau sastera diletakkan
- Kiraan "Tiada di Rumah" ditetapkan semula

**🏠 Tiada di Rumah** - Apabila tiada yang menjawab

- Secara automatik meningkatkan kiraan "Tiada di Rumah"
- Sistem menjejak bilangan percubaan
- Selepas mencapai percubaan maksimum, dianggap sebagai selesai

**🚫 Jangan Panggil** - Apabila diminta untuk tidak melawat

![Status Jangan Panggil Alamat](assets/screenshots/address_do_not_call_status.png)

*Rajah 11: Kemas kini status Jangan Panggil dengan pemilihan tarikh*

- Pilih status ini
- Pilihan tetapkan tarikh DNC (lalai kepada hari ini)
- Tambah nota menerangkan sebab jika sesuai
- **Penting**: Hormati hasrat penghuni rumah sentiasa

**❌ Tidak Sah** - Apabila alamat tidak boleh diakses

- Alamat tidak wujud
- Dalam pembinaan
- Ditutup secara kekal
- Tidak boleh diakses

**4. Kemas Kini Jenis Isi Rumah** (Jika Tersedia)

Jika sidang anda telah mengkonfigurasi jenis isi rumah:

- Pilih satu atau berbilang jenis
- Contoh: Keutamaan bahasa, keadaan khas
- Kosongkan jenis sedia ada jika diperlukan

**5. Tambah atau Kemas Kini Nota** (Pilihan tetapi Disyorkan)

**Amalan terbaik:**

- ✓ Fokus pada butiran hartanah, bukan individu
- ✓ Rekod arahan akses dan masa
- ✓ Ringkas dan hormat
- ✗ Jangan sekali-kali masukkan maklumat peribadi tentang penghuni rumah
- ✗ Jangan sekali-kali masukkan maklumat sensitif

**Contoh Baik:**

- "Hartanah berpagar - hubungi rumah pengawal dahulu"
- "Masa terbaik: Hujung minggu selepas 2 petang"
- "Pintu masuk sisi boleh diakses melalui lorong masuk"

**Contoh Buruk:**

- Butiran peribadi tentang penduduk
- Maklumat perubatan atau kewangan
- Nama atau penerangan individu

**6. Laraskan Kiraan Tiada di Rumah** (Jika Diperlukan)

Sistem secara automatik menjejak percubaan tiada di rumah, tetapi anda boleh melaraskan secara manual jika perlu.

**7. Tetapkan Tarikh Jangan Panggil** (Status DNC Sahaja)

Apabila menandakan sebagai Jangan Panggil:

- Sistem lalai kepada tarikh hari ini
- Laraskan jika diperlukan untuk permintaan DNC tertentu
- Tarikh membantu menjejak bila untuk berpotensi melawat semula

**8. Kemas Kini Geolokasi** (Pentadbir Sahaja)

Untuk wilayah satu tingkat:

- Klik butang "Kemas Kini Geolokasi"
- Gunakan peta untuk menetapkan lokasi tepat
- Membantu dengan navigasi dan ketepatan pemetaan

**9. Simpan Perubahan**

- Semak semua kemas kini
- Klik butang **"Simpan"**
- Perubahan disegerakkan serta-merta kepada semua pengguna
- Mesej kejayaan mengesahkan kemas kini

**10. Padam Hartanah** (Pentadbir Sahaja - Satu Tingkat)

Untuk hartanah persendirian yang perlu dibuang:

- Klik butang "Padam Hartanah" di bahagian bawah
- Sahkan pemadaman
- **Amaran**: Tindakan ini tidak boleh dibatalkan

---

### Menggunakan Ciri Peta

![Arah Peta](assets/screenshots/map_directions.png)

*Rajah 12: Paparan peta interaktif menunjukkan penanda dan kawalan navigasi*

Ministry Mapper mengintegrasikan pemetaan interaktif untuk navigasi wilayah intuitif.

#### Ciri Peta

**Penanda Interaktif:**

- Setiap alamat ditandakan pada peta
- 🔴 Penanda merah menunjukkan destinasi
- 🔵 Penanda biru berkelip menunjukkan lokasi semasa
- Klik mana-mana penanda untuk melihat/edit alamat tersebut

**Kawalan Peta:**

- **Zum**: Butang + dan - atau gerak isyarat cubit
- **Sorot**: Klik dan seret untuk bergerak
- **Paparan Satelit**: Togol antara peta dan imejan satelit
- **Skrin Penuh**: Kembangkan peta ke skrin penuh (atau gunakan Dail Pantas → Mod Paparan Peta)
- **Pusatkan pada Wilayah**: Tetapkan semula paparan untuk menunjukkan semua alamat

**Mod Paparan Peta Skrin Penuh** (Pentadbir & Konduktor):

![Mod Paparan Peta Pentadbir](assets/screenshots/admin_map_view_mode.png)

*Rajah 12a: Mod paparan peta skrin penuh diakses melalui menu dail pantas*

Akses melalui Dail Pantas (➕) → ikon Paparan Peta. Ideal untuk:
- Perancangan laluan sebelum khidmat lapangan
- Analisis liputan wilayah
- Persembahan mesyuarat
- Mengenal pasti kelompok alamat

#### Petua Navigasi

1. **Sebelum Meninggalkan Rumah:**

   - Lihat peta untuk merancang laluan anda
   - Kenal pasti kelompok alamat
   - Perhatikan sebarang keperluan akses khas dari nota

2. **Dalam Lapangan:**

   - Gunakan peta untuk navigasi antara alamat
   - Ikut nombor jujukan untuk penghalaan optimum
   - Ketik penanda untuk mengemas kini status dengan pantas selepas setiap lawatan

3. **Menggunakan dengan GPS Telefon:**
   - Aktifkan perkhidmatan lokasi
   - Peta menunjukkan kedudukan semasa anda
   - Navigasi terus ke alamat seterusnya
   - Berfungsi luar talian jika jubin peta dicache (terhad)

---

### Tugasan Wilayah (Konduktor & Pentadbir)

Ministry Mapper menggunakan sistem tugasan berasaskan pautan. Konduktor dan Pentadbir mencipta pautan boleh dikongsi yang penerbit gunakan untuk mengakses wilayah.

#### Mengapa Tugasan Berasaskan Pautan?

- ✓ **Pengedaran Mudah**: Hantar pautan melalui e-mel, mesej, atau teks
- ✓ **Tamat Tempoh Automatik**: Pautan tamat tempoh selepas masa yang ditetapkan (lalai: 24 jam)
- ✓ **Tiada Akaun Diperlukan**: Penerbit bekerja terus melalui pautan
- ✓ **Keselamatan**: Pautan tamat tempoh tidak boleh diakses
- ✓ **Penjejakan**: Lihat siapa mengakses apa dan bila

#### Mencipta Tugasan

**Dua Cara untuk Mencipta Tugasan:**

![Pautan Pantas Konduktor Pentadbir](assets/screenshots/admin_conductor_quicklink.png)

*Rajah 13a: Antara muka penciptaan pautan pantas diakses melalui menu dail pantas*

| Kaedah | Akses | Paling Sesuai Untuk |
|--------|--------|----------|
| **Pautan Pantas** | Dail Pantas (➕) → Pautan Pantas | Penciptaan pantas untuk wilayah semasa |
| **Standard** | Butang Tugasan → Cipta Baharu | Pilihan penuh dan penyesuaian |

**Langkah 1: Akses Penciptaan Tugasan**

- **Pautan Pantas**: Klik Dail Pantas (➕) → ikon Pautan Pantas (pra-isi wilayah semasa)
- **Standard**: Klik **"Tugasan"** → **"Cipta Tugasan Baharu"** atau **"+"**

**Langkah 2: Isi Borang Tugasan**

| Medan | Penerangan | Diperlukan |
|-------|-------------|----------|
| **Jenis** | Tugasan Biasa atau Slip Peribadi | Ya |
| **Wilayah** | Pilih dari wilayah sidang | Ya |
| **Nama Penerbit** | Untuk penjejakan (pautan berfungsi tanpa nama) | Pilihan |
| **Tamat Tempoh Pautan** | Jam sehingga pautan tamat tempoh (lalai: 24) | Ya |

**Langkah 3: Jana dan Kongsi**

1. Klik **"Cipta Tugasan"**
2. Sistem menjana pautan unik
3. Kongsi pautan dengan penerbit melalui:
   - E-mel
   - Mesej teks
   - Aplikasi pemesejan segera
   - Sebarang kaedah komunikasi

**Contoh Pautan Tugasan:**

```
https://your-ministry-mapper.com/map/abc123xyz456
```

#### Penciptaan Tugasan Khusus Peta

Cipta tugasan terus dari paparan peta tanpa menavigasi ke modal tugasan pusat.

**Akses:**

1. Navigasi ke wilayah yang anda mahu tugaskan
2. Klik sama ada:
   - Butang **"Tugaskan"** - cipta tugasan wilayah biasa
   - Butang **"Peribadi"** - cipta tugasan slip peribadi

**Tugasan Wilayah Biasa:**

![Menugaskan Penerbit Peta](assets/screenshots/assigning_publisher_map.png)

*Rajah 13b: Borang penciptaan tugasan biasa khusus peta*

Klik **"Tugaskan"** untuk mencipta tugasan wilayah biasa. Borang memaparkan:

- Maklumat wilayah dalam pengepala
- Medan **Nama Penerbit** - masukkan nama penerbit yang ditugaskan
- Butang **Batal** dan **Sahkan**

**Tugasan Slip Peribadi:**

![Tugasan Peribadi Penerbit Peta](assets/screenshots/personal_assign_publisher_map.png)

*Rajah 13c: Borang penciptaan slip peribadi khusus peta dengan kalendar*

Klik **"Peribadi"** untuk mencipta tugasan slip peribadi. Borang termasuk:

- Maklumat wilayah dalam pengepala
- **Pemilih kalendar** - pilih tarikh tugasan
- Medan **Nama Penerbit** - masukkan nama penerbit yang ditugaskan
- Butang **Batal** dan **Sahkan**

**Faedah:**

- ✓ Penciptaan tugasan pantas semasa melihat wilayah
- ✓ Tiada perlu tukar ke modal tugasan
- ✓ Konteks segera wilayah yang ditugaskan

#### Mengurus Tugasan

![Pengurusan Tugasan](assets/screenshots/assignment_management.png)

*Rajah 14: Modal pengurusan tugasan menunjukkan semua tugasan aktif merentas wilayah*

Antara muka Pengurusan Tugasan membolehkan konduktor dan pentadbir melihat dan mengurus semua tugasan sedia ada dalam sidang.

**Lihat Semua Tugasan:**

1. Klik butang **"Tugasan"** dari papan pemuka
2. Modal Tugasan dibuka memaparkan semua pautan tugasan aktif
3. Lihat tugasan untuk semua wilayah dalam senarai berpusat

**Maklumat Tugasan Dipaparkan:**

Untuk setiap tugasan, anda boleh lihat:

- **Kod Wilayah dan Lokasi**: Pengecam wilayah dengan penerangan lokasi (cth., "187A, Marsiling Road (M01)")
- **Jenis Tugasan**: "Tugaskan" untuk tugasan biasa
- **Nama Penerbit**: Nama orang yang ditugaskan (cth., Jon, Erli, Pety)
- **Tarikh/Masa Dicipta**: Bila tugasan dicipta (cth., "Dec 22, 2025, 9:38 PM")
- **Tarikh/Masa Tamat Tempoh**: Bila pautan tugasan tamat tempoh (cth., "Dec 23, 2025, 9:38 AM" atau "11:38 PM")
- **Butang Padam**: Ikon tong sampah (🗑️) untuk membuang tugasan individu

**Padam Tugasan:**

1. Cari tugasan dalam senarai
2. Klik butang **ikon tong sampah** (🗑️) di sebelah kanan tugasan
3. Sahkan pemadaman jika diminta
4. Pautan tugasan serta-merta menjadi tidak boleh diakses kepada penerbit

**Bila untuk Memadam:**

- Wilayah dikembalikan awal oleh penerbit
- Pautan salah dicipta atau dihantar
- Penerbit tidak lagi memerlukan akses
- Tugasan perlu ditugaskan semula kepada orang lain
- Kebimbangan keselamatan atau kompromi

**Menutup Modal Tugasan:**

- Klik butang **"Batal"** di bahagian bawah untuk menutup modal dan kembali ke papan pemuka

#### Pengurusan Tugasan Khusus Peta

Lihat dan urus tugasan untuk wilayah individu terus dari paparan peta.

**Akses:**

1. Navigasi ke wilayah
2. Klik sama ada:
   - **"Pautan Tugasan"** - untuk tugasan wilayah biasa
   - **"Pautan Peribadi"** - untuk tugasan slip peribadi

![Tugasan Peta Biasa](assets/screenshots/map_assign_links.png)

*Rajah 14a: Modal Pautan Tugasan untuk wilayah tertentu*

![Tugasan Peta Peribadi](assets/screenshots/map_personal_links.png)

*Rajah 14b: Modal Pautan Peribadi untuk wilayah tertentu*

**Maklumat Dipaparkan:**

- Nama penerbit
- Tarikh/masa dicipta dan tamat tempoh
- Butang padam (🗑️) untuk membuang tugasan

**Bila untuk Menggunakan:**

- Semak siapa yang kini mempunyai wilayah ditugaskan
- Buang tugasan semasa melihat wilayah
- Urus tugasan biasa dan peribadi secara berasingan

#### Amalan Terbaik untuk Tugasan

**Sebelum Mencipta:**

- ✓ Sahkan wilayah sedia (alamat dikemas kini, arahan jelas)
- ✓ Semak tetapan sidang untuk masa tamat tempoh lalai
- ✓ Rancang tempoh tamat tempoh yang sesuai untuk saiz wilayah

**Semasa Berkongsi:**

- ✓ Sertakan arahan dalam mesej anda
- ✓ Ingatkan penerbit tentang masa tamat tempoh
- ✓ Berikan kenalan anda untuk soalan
- ✓ Hantar pada waktu yang munasabah

**Pemantauan:**

- ✓ Semakan berkala untuk tugasan tamat tempoh
- ✓ Bersihkan tugasan lama secara berkala
- ✓ Susulan jika wilayah tidak dikembalikan
- ✓ Jejak kadar penyelesaian

---

### Mesej dan Arahan

![Mesej Konduktor Pentadbir](assets/screenshots/admin_conductor_messages.png)

*Rajah 15: Modal mesej menunjukkan mesej yang disiarkan dengan pilihan penyematan*

Pentadbir dan Konduktor boleh menyiarkan mesej yang kelihatan kepada kumpulan pengguna tertentu.

#### Jenis Mesej

**Mesej Penerbit:**

- Kelihatan kepada semua penerbit dengan pautan tugasan
- Arahan untuk khidmat lapangan
- Panduan khusus wilayah
- Pengumuman umum

**Mesej Konduktor:**

- Kelihatan kepada Konduktor dan Pentadbir
- Maklumat penyelarasan
- Nota pentadbiran
- Maklumat perancangan

**Mesej Pentadbir:**

- Kelihatan kepada Pentadbir sahaja
- Nota pentadbiran sistem
- Kemas kini kritikal
- Peringatan pengurusan

#### Menyiarkan Mesej

1. Klik butang **"Mesej"**
2. Klik **"Mesej Baharu"** atau **"+"**
3. Taip mesej anda
4. Pilih jenis mesej (Penerbit/Konduktor/Pentadbir)
5. Pilihan **semat** mesej penting ke atas
6. Klik **"Siarkan"**

#### Ciri Mesej

**Penyematan:**

- Semat mesej penting untuk mengekalkannya di atas
- Hanya satu mesej disemat bagi setiap jenis
- Lepaskan semat apabila tidak lagi kritikal

**Pengeditan:**

- Edit mesej selepas menyiarkan
- Kemas kini serta-merta untuk semua penonton

**Pemadaman:**

- Buang mesej lapuk
- Bersihkan selepas acara berlalu

**Status Pembacaan:**

- Lihat siapa yang telah membaca mesej (paparan pentadbir)
- Jejak pengakuan kemas kini penting

---

### Penyegerakan Data Masa Nyata

Ministry Mapper menggunakan langganan masa nyata PocketBase untuk kemas kini segera:

#### Cara Ia Berfungsi

**Penyegerakan Automatik:**

- Perubahan disegerakkan serta-merta merentas semua peranti yang disambungkan
- Tiada muat semula manual diperlukan
- Kemas kini muncul serta-merta untuk semua pengguna yang melihat wilayah yang sama

**Apa yang Disegerakkan:**

- ✓ Perubahan status alamat
- ✓ Nota dan kemas kini baharu
- ✓ Kemajuan wilayah
- ✓ Mesej baharu
- ✓ Perubahan tugasan

**Pengendalian Sambungan:**

- Sistem mengesan kehilangan sambungan secara automatik
- Menyambung semula apabila internet dipulihkan
- Menunjukkan penunjuk status sambungan
- Barisan kemas kini jika luar talian (terhad)

**Prestasi:**

- Kemas kini hanya apabila wilayah/halaman dibuka
- Pembersihan automatik apabila halaman ditutup
- Penggunaan jalur lebar minimum
- Dioptimumkan untuk data mudah alih

> **💡 Nota**: Untuk kemas kini masa nyata berfungsi, pastikan tab pelayar anda aktif semasa bekerja pada wilayah.

---

### Pengurusan Pengguna (Pentadbir Sahaja)

![Senarai Pengurusan Pengguna](assets/screenshots/manage_users_list.png)

*Rajah 16: Panel pengurusan pengguna menunjukkan senarai pengguna dengan peranan*

Pentadbir mempunyai kawalan penuh ke atas akaun dan kebenaran pengguna dalam sidang mereka.

#### Melihat Pengguna

1. Klik **ikon profil** atau **menu pengguna** anda
2. Pilih **"Pengurusan Pengguna"** atau **"Pengguna"**
3. Lihat senarai lengkap pengguna sidang menunjukkan:
   - Nama pengguna
   - Alamat e-mel
   - Status pengesahan (✓ disahkan / ✗ tidak disahkan)
   - Lencana peranan semasa
   - Aktiviti terakhir

#### Menjemput Pengguna Baharu

![Jemput Pengguna](assets/screenshots/invite_users.png)

*Rajah 17: Dialog jemputan pengguna untuk menambah ahli sidang baharu*

**Langkah 1: Buka Dialog Jemput**

1. Dalam Pengurusan Pengguna, klik **"Jemput Pengguna"** atau **"+"**
2. Modal jemput pengguna dibuka

**Langkah 2: Masukkan Maklumat Pengguna**

- **Alamat E-mel**: E-mel pengguna (mesti sah)
- **Penetapan Peranan**: Pilih satu daripada:
  - Penerbit
  - Baca Sahaja
  - Konduktor
  - Pentadbir

**Langkah 3: Hantar Jemputan**

1. Klik **"Hantar Jemputan"**
2. Sistem menghantar e-mel jemputan kepada pengguna
3. E-mel mengandungi:
   - Pautan untuk mencipta akaun
   - Maklumat sidang
   - Butiran penetapan peranan
   - Arahan untuk bermula

**Langkah 4: Pengguna Melengkapkan Pendaftaran**

- Pengguna menerima e-mel
- Klik pautan untuk mendaftar
- Cipta akaun dengan kata laluan
- Sahkan alamat e-mel
- Secara automatik ditambah ke sidang dengan peranan yang ditetapkan

#### Menukar Peranan Pengguna

![Butiran Pengurusan Pengguna](assets/screenshots/manage_users_details.png)

*Rajah 18: Antara muka butiran dan pengurusan peranan pengguna*

1. Cari pengguna dalam senarai pengguna
2. Klik pada pengguna atau butang **"Edit"**
3. Pilih peranan baharu dari dropdown:
   - **Penerbit**: Akses wilayah asas melalui pautan
   - **Baca Sahaja**: Akses papan pemuka lihat sahaja
   - **Konduktor**: Boleh mencipta tugasan dan mengurus mesej
   - **Pentadbir**: Kawalan penuh
4. Klik **"Simpan"** atau **"Kemas Kini"**
5. Perubahan berkuat kuasa serta-merta

> **⚠️ Penting**: Pengguna mesti log keluar dan log masuk semula untuk melihat kebenaran baharu mereka dicerminkan.

#### Membuang Akses Pengguna

**Pembuangan Sementara:**

1. Tukar peranan pengguna kepada **"Tiada Akses"** atau **"Padam Akses"**
2. Pengguna kehilangan semua kebenaran
3. Akaun kekal tetapi tidak boleh mengakses data sidang

**Pembuangan Kekal:**

1. Klik butang **"Padam"** untuk pengguna
2. Sahkan pemadaman
3. Pengguna dibuang sepenuhnya dari sidang
4. Pengguna boleh dijemput semula jika diperlukan

#### Status Pengesahan Pengguna

**Pengguna Disahkan (✓):**

- Alamat e-mel disahkan
- Akses penuh kepada kebenaran yang ditetapkan
- Boleh log masuk secara normal

**Pengguna Tidak Disahkan (✗):**

- E-mel belum disahkan
- Akses terhad atau tiada
- Perlu menyemak e-mel dan klik pautan pengesahan

**Untuk Menghantar Semula Pengesahan:**

- Sesetengah sistem membenarkan menghantar semula e-mel pengesahan
- Atau minta pengguna menggunakan ciri "Lupa Kata Laluan"

---

### Tetapan Sidang (Pentadbir Sahaja)

![Tetapan Sidang](assets/screenshots/congregation_settings.png)

*Rajah 19: Halaman tetapan sidang dengan semua pilihan konfigurasi*

Konfigurasi cara Ministry Mapper berfungsi untuk sidang anda.

#### Mengakses Tetapan

1. Klik butang **"Tetapan"** atau ikon ⚙️
2. Lihat panel konfigurasi sidang

#### Tetapan Utama

**1. Percubaan "Tiada di Rumah" Maksimum**

- Lalai: 1
- Julat: 1-4 percubaan
- Apabila dicapai, alamat dianggap selesai untuk pengiraan kemajuan
- Mempengaruhi bila wilayah menunjukkan sebagai selesai

**Contoh:** Jika ditetapkan kepada 3:

- "Tiada di Rumah" pertama: Kiraan = 1
- "Tiada di Rumah" kedua: Kiraan = 2
- "Tiada di Rumah" ketiga: Kiraan = 3, ditandakan selesai

**2. Tamat Tempoh Pautan Tugasan (Jam)**

- Lalai: 24 jam
- Julat: 1-168 jam (1 minggu)
- Berapa lama pautan tugasan kekal aktif
- Terpakai kepada pautan yang baru dicipta

**3. Asal/Lokasi Sidang**

- Tetapkan lokasi sidang anda
- Digunakan untuk pemetaan dan arah pusat
- Boleh jadi nama bandar atau koordinat
- Membantu dengan perancangan laluan

**4. OTP (Kata Laluan Sekali)**

- Aktifkan/lumpuhkan OTP e-mel untuk log masuk
- Menambah lapisan keselamatan tambahan
- Pengguna menerima kod melalui e-mel semasa log masuk
- Disyorkan untuk data sidang sensitif

#### Pilihan Sidang (Jenis Isi Rumah)

![Tetapan Pilihan Isi Rumah](assets/screenshots/household_options_settings.png)

*Rajah 20: Pengurusan pilihan sidang menunjukkan konfigurasi jenis isi rumah*

Konfigurasi jenis klasifikasi isi rumah tersuai untuk wilayah anda.

**Apakah Pilihan Sidang?**

- Kategori tersuai untuk mengklasifikasikan isi rumah
- Contoh: Kumpulan bahasa (Cina, Inggeris, Tamil)
- Boleh mewakili sebarang sistem klasifikasi yang sidang anda gunakan
- Berbilang jenis boleh ditetapkan kepada isi rumah tunggal jika dikonfigurasi

**Mengurus Pilihan:**

1. **Lihat Pilihan**

   - Dalam Tetapan, cari bahagian "Pilihan Sidang"
   - Lihat senarai semua jenis yang dikonfigurasi

2. **Tambah Pilihan Baharu**

   - Klik "Tambah Pilihan" atau "+"
   - Isikan:
     - **Kod**: Pengecam pendek (cth., "CHI", "ENG")
     - **Penerangan**: Nama penuh (cth., "Cina", "Inggeris")
     - **Boleh Dikira**: Tandakan jika perlu dikira ke arah kemajuan wilayah
     - **Adalah Lalai**: Tandakan jika perlu menjadi pilihan lalai
     - **Jujukan**: Nombor urutan paparan
   - Klik "Simpan"

3. **Edit Pilihan**

   - Klik pada pilihan sedia ada
   - Ubah suai medan
   - Simpan perubahan

4. **Padam Pilihan**
   - Klik butang padam untuk pilihan
   - Sahkan pemadaman
   - **Amaran**: Mempengaruhi semua alamat yang menggunakan jenis ini

**Bendera Pilihan:**

**Boleh Dikira:**
- Mengawal sama ada alamat dengan jenis ini dikira ke arah kemajuan wilayah
- **Ditandakan**: Disertakan dalam pengiraan kemajuan (cth., Cina, Inggeris, Tamil)
- **Tidak Ditandakan**: Dikecualikan dari kemajuan (cth., Perniagaan, Dalam Pembinaan)
- Contoh: 100 jumlah alamat, 10 ditandakan "Perniagaan" (tidak boleh dikira) = kemajuan berdasarkan 90 alamat sahaja

**Adalah Lalai:**
- Pilih automatik jenis ini semasa mencipta alamat baharu
- Hanya satu pilihan perlu menjadi lalai
- Gunakan untuk jenis isi rumah anda yang paling biasa

**Jujukan:**
- Mengawal urutan paparan dalam menu dropdown
- Nombor lebih rendah muncul dahulu (1, 2, 3...)
- **Diurus dengan seret dan lepas** dalam tetapan - hanya seret pilihan untuk menyusun semula
- Mempengaruhi urutan dalam dropdown isi rumah alamat
- Petua: Susun dari paling biasa ke paling kurang biasa

**Konfigurasi Pemilihan Berbilang:**

- Aktifkan jika isi rumah boleh mempunyai berbilang jenis
- Contoh: Isi rumah bercakap kedua-dua Cina dan Inggeris
- Apabila dilumpuhkan, hanya satu jenis bagi setiap isi rumah

#### Konfigurasi Peta

![Konfigurasi Peta](assets/screenshots/map_configurations.png)

*Rajah 34: Pilihan konfigurasi peta lanjutan untuk pentadbir*

Konfigurasi cara peta dipaparkan dan berkelakuan dalam sidang anda. Pentadbir mempunyai akses kepada fungsi pengurusan peta yang berkuasa untuk penyelenggaraan dan organisasi wilayah.

**Untuk Mengakses Konfigurasi Peta:**

1. Pergi ke **Tetapan** (Pentadbir sahaja)
2. Pilih wilayah dari dropdown
3. Buka paparan peta untuk wilayah yang dipilih
4. Akses menu konfigurasi peta (biasanya melalui ikon tetapan atau menu)

**Fungsi Konfigurasi Peta Tersedia:**

**1. Tukar Lokasi**
   - Pindahkan penanda peta ke alamat berbeza
   - Mengemas kini lokasi geografi unit wilayah
   - Berguna apabila alamat telah berubah atau lokasi awal tidak betul
   - Hanya pilih lokasi baharu pada peta

**2. Tukar Wilayah**
   - Pindahkan alamat atau unit ke wilayah berbeza
   - Membantu menyusun semula sempadan wilayah
   - Berguna untuk mengimbangi saiz wilayah
   - Mengekalkan semua data dan sejarah alamat semasa pemindahan

**3. Tukar Jujukan**
   - Ubah suai nombor urutan lawatan untuk alamat
   - Mengoptimumkan laluan untuk kecekapan khidmat lapangan
   - Nombor lebih rendah dilawati dahulu
   - Membantu mencipta corak lawatan logik

##### Kemas Kini Jujukan Peta (Seret & Lepas)

![Kemas Kini Jujukan Peta](assets/screenshots/update_map_sequence.png)

*Antara muka seret dan lepas untuk menyusun semula semua jujukan peta dalam wilayah*

**Akses:** Menu alamat → "Tukar Jujukan"

**Cara Ia Berfungsi:**
1. Setiap kad menunjukkan alamat dengan nombor jujukan semasa
2. Seret dan lepas untuk menyusun semula - nombor dikemas kini secara automatik
3. Klik **"Simpan"** untuk menggunakan atau **"Batal"** untuk membuang

**Amalan Terbaik:**
- Minimumkan pengulangan dengan mengumpulkan alamat berdekatan
- Kumpulkan tingkat bersama dalam bangunan berbilang tingkat
- Cipta aliran logik dari satu hujung ke hujung yang lain
- Semak paparan peta selepas penjujukan

**4. Namakan Semula**
   - Tukar nama atau pengecam alamat/unit
   - Kemas kini nama bangunan atau nombor unit
   - Pastikan data terkini dengan perubahan dunia sebenar
   - Berguna untuk membetulkan ralat kemasukan awal

**5. Tambah No. Unit**
   - Tambah nombor unit baharu ke alamat sedia ada
   - Kembangkan bangunan berbilang tingkat dengan unit tambahan
   - Berguna apabila apartmen baharu ditambah ke bangunan
   - Mengekalkan organisasi struktur bangunan

**6. Tambah Tingkat Lebih Tinggi**
   - Kembangkan bangunan ke atas dengan tingkat tambahan
   - Untuk bangunan yang telah dikembangkan atau pada awalnya dikurangkan anggaran
   - Secara automatik mencipta unit untuk tingkat baharu berdasarkan corak bangunan
   - Membantu memastikan data wilayah terkini dengan perubahan pembinaan

**7. Tambah Tingkat Lebih Rendah**
   - Tambah tingkat di bawah tingkat terendah semasa
   - Berguna untuk tahap bawah tanah atau tingkat bawah yang baru boleh diakses
   - Boleh menambah nombor tingkat negatif (cth., B1, B2)
   - Mengekalkan sistem penomboran tingkat yang konsisten

**8. Tetapkan Semula Status**
   - Kosongkan status alamat kembali ke "Belum Selesai"
   - Membuang status "Selesai" dan "Tiada di Rumah" sahaja
   - TIDAK membuang status "Jangan Panggil" atau "Tidak Sah"
   - Mengekalkan nota dan maklumat alamat lain
   - Berguna apabila memulakan semula kerja pada alamat yang sebelumnya selesai
   - Tidak mempengaruhi alamat lain dalam wilayah

**9. Padam**
   - Buang secara kekal alamat, unit, atau tingkat dari wilayah
   - Tidak boleh dibatalkan - gunakan dengan berhati-hati
   - Membantu membuang entri pendua atau alamat yang tidak wujud
   - Sahkan dengan teliti sebelum memadam

**Kes Penggunaan Biasa:**
- Membetulkan ralat: Namakan Semula dan Tukar Lokasi
- Pengimbangan semula wilayah: Tukar Wilayah
- Kemas kini bangunan: Tambah Tingkat Lebih Tinggi/Rendah
- Pengoptimuman laluan: Tukar Jujukan
- Pembersihan data: Padam pendua
- Kemas kini bermusim: Tetapkan Semula Status

> **⚠️ Amaran**: Perubahan mempengaruhi semua pengguna serta-merta. Padam adalah kekal dan tidak boleh dibatalkan.

---

### Pengurusan Wilayah (Pentadbir Sahaja)

![Cipta Wilayah Baharu](assets/screenshots/create_new_territory.png)

*Rajah 21: Antara muka penciptaan wilayah untuk menambah wilayah baharu*

Pentadbir mempunyai kawalan penuh ke atas mencipta, mengedit, dan mengurus wilayah.

#### Mencipta Wilayah Baharu

**Langkah 1: Akses Penciptaan Wilayah**

1. Klik dropdown **pemilih wilayah**
2. Pilih **"Cipta Wilayah Baharu"** atau **"Wilayah Baharu"**
3. Borang penciptaan wilayah dibuka

**Langkah 2: Masukkan Maklumat Wilayah**

- **Kod Wilayah**: Pengecam pendek (cth., "T-001", "M-12", "W-05")
  - Pastikan ia pendek dan bermakna
  - Gunakan konvensyen penamaan yang konsisten
  - Maksimum disyorkan: 10 aksara
- **Penerangan**: Nama penuh atau penerangan kawasan
  - Contoh: "Komersial Pusat Bandar", "Kediaman Utara"
  - Jadi deskriptif untuk pengenalan mudah
  - Maksimum disyorkan: 100 aksara

**Langkah 3: Cipta**

1. Klik **"Cipta Wilayah"**
2. Wilayah baharu dicipta dan dipilih
3. Sedia untuk menambah alamat

#### Mengedit Butiran Wilayah

**Tukar Kod Wilayah:**

1. Pilih wilayah
2. Klik ✏️ **"Edit"** atau **"Tukar Kod Wilayah"**
3. Masukkan kod baharu
4. Simpan perubahan
5. **Amaran**: Kemas kini sebarang rujukan kepada kod lama

**Tukar Penerangan Wilayah:**

1. Pilih wilayah
2. Klik **"Tukar Nama Wilayah"** atau pilihan edit
3. Kemas kini penerangan
4. Simpan perubahan

**Tukar Jujukan Wilayah:**

##### Kemas Kini Jujukan Wilayah (Seret & Lepas)

![Kemas Kini Jujukan Wilayah](assets/screenshots/territory_sequence_change.png)

*Antara muka seret dan lepas untuk menyusun semula semua wilayah dalam sidang*

**Akses:** Dropdown Wilayah → "Tukar Jujukan"

**Cara Ia Berfungsi:**
1. Setiap kad menunjukkan kod dan penerangan wilayah
2. Seret dan lepas untuk menyusun semula - nombor jujukan dikemas kini secara automatik
3. Klik **"Simpan"** untuk menggunakan atau **"Batal"** untuk membuang
4. Mengawal urutan dalam dropdown dan senarai pemilihan

**Amalan Terbaik:**
- Susun mengikut kedekatan geografi
- Kumpulkan mengikut jenis (kediaman, komersial, perniagaan)
- Pertimbangkan tugasan kumpulan khidmat lapangan
- Gunakan untuk perancangan putaran wilayah

#### Pilihan Konfigurasi Wilayah

![Pilihan Konfigurasi Wilayah](assets/screenshots/territory_configurations.png)

*Menu dropdown wilayah menunjukkan pilihan konfigurasi*

Pentadbir boleh mengakses pilihan konfigurasi wilayah melalui butang **Dropdown Wilayah** dalam bar navigasi atas. Klik butang untuk mendedahkan pilihan berikut:

**Pilihan Tersedia:**

- **Cipta Baharu**: Cipta wilayah baharu dari awal
- **Tukar Kod**: Ubah suai pengecam unik wilayah
- **Tukar Nama**: Kemas kini penerangan wilayah
- **Tukar Jujukan**: Susun semula cara wilayah muncul dalam senarai pemilihan
- **Padam Semasa**: Buang secara kekal wilayah yang sedang dipilih
- **Tetapkan Semula Status**: Kosongkan semua status alamat kembali ke "Belum Selesai"

Pilihan ini menyediakan akses pantas kepada tugas pengurusan wilayah biasa tanpa menavigasi melalui berbilang menu.

#### Operasi Wilayah

**Tetapkan Semula Wilayah:**

![Cipta Peta Baharu](assets/screenshots/create_new_map.png)

*Rajah 22: Antara muka operasi dan pengurusan wilayah*

Menetapkan semula semua alamat dalam wilayah kepada status "Belum Selesai":

1. Pilih wilayah
2. Klik butang **"Tetapkan Semula Wilayah"**
3. Sahkan tindakan (ini mengosongkan semua data lawatan!)
4. Semua alamat kembali ke "Belum Selesai"
5. Kiraan tiada di rumah ditetapkan semula kepada 0
6. Nota dikekalkan
7. Kemajuan ditetapkan semula kepada 0%

**Gunakan Bila:**

- Wilayah sepenuhnya dikerjakan dan sedia untuk ditugaskan semula
- Memulakan pusingan lawatan baharu
- Membersihkan data ujian

**⚠️ Amaran**: Tidak boleh dibatalkan. Semua kemas kini status akan hilang.

**Padam Wilayah:**

![Senarai Pemilihan Wilayah](assets/screenshots/territory_selection_list.png)

*Rajah 23: Senarai pemilihan wilayah menunjukkan semua wilayah yang tersedia*

Membuang secara kekal wilayah dan semua datanya:

1. Pilih wilayah untuk dipadam
2. Klik butang **"Padam Wilayah"**
3. Baca mesej amaran dengan teliti
4. Taip pengesahan jika diperlukan
5. Sahkan pemadaman

**Memadam:**

- Rekod wilayah
- Semua alamat dalam wilayah
- Semua unit dan tingkat
- Semua sejarah tugasan
- Semua data berkaitan

**⚠️ Amaran Kritikal**: Tindakan ini TIDAK BOLEH dibatalkan. Pertimbangkan untuk mengeksport data terlebih dahulu.

---

### Pengurusan Alamat (Pentadbir Sahaja)

![Peta Satu Tingkat](assets/screenshots/single_story_map.png)

*Rajah 24: Antara muka pengurusan alamat untuk hartanah persendirian/satu tingkat*

Pentadbir boleh menambah dan mengurus alamat dalam wilayah.

#### Jenis Alamat

Ministry Mapper menyokong dua jenis alamat:

**1. Alamat Awam (Berbilang Tingkat)**

- Bangunan apartmen, kondominium
- Berbilang tingkat dan unit
- Contoh: Flat HDB, kompleks apartmen
- Setiap tingkat mempunyai berbilang unit

**2. Alamat Persendirian (Satu Tingkat)**

- Rumah individu, rumah kedai
- Hartanah tunggal dengan satu alamat
- Contoh: Hartanah bertanah, bangunan berdiri sendiri
- Tiada struktur tingkat/unit

#### Menambah Alamat Awam (Berbilang Tingkat)

**Langkah 1: Mulakan Penciptaan**

1. Pilih wilayah
2. Klik butang **"Tambah Alamat"** atau **"+"**
3. Pilih jenis **"Alamat Awam"**

**Langkah 2: Masukkan Maklumat Bangunan**

- **Poskod/Alamat**: Pengecam bangunan
  - Masukkan poskod atau alamat jalan
  - Sistem mungkin mengisi lokasi secara automatik
  - Digunakan untuk geokod dan paparan peta
- **Nama Bangunan**: Nama bangunan pilihan
  - Contoh: "Blok 123A", "Sunny Heights"
  - Membantu penerbit mengenal pasti bangunan

**Langkah 3: Konfigurasi Tingkat**

![Peta Berbilang Tingkat](assets/screenshots/multi_story_map.png)

*Rajah 25: Pengurusan bangunan berbilang tingkat dengan organisasi tingkat dan unit*

- **Tingkat Mula**: Nombor tingkat terendah
  - Boleh negatif untuk tahap bawah tanah
  - Contoh: -2 (B2), 1 (Bawah), 0
- **Tingkat Atas**: Nombor tingkat tertinggi
  - Maksimum: 50
  - Contoh: 10, 25, 40
- **Pemilihan Tingkat**: Pilih tingkat tertentu
  - Langkau tingkat tanpa unit (cth., tingkat mekanikal)
  - Biasa: Semua tingkat dari mula ke atas

**Langkah 4: Konfigurasi Unit**

- **Unit Setiap Tingkat**: Bilangan unit pada setiap tingkat
  - Contoh: 8, 12, 16
  - Mencipta unit secara automatik
- **Format Nombor Unit**: Cara menomborkan unit
  - Corak: Tingkat + unit (cth., 01-01, 01-02)
  - Tersuai: Masukkan nombor unit secara manual kemudian

**Langkah 5: Cipta dan Isi**

1. Klik **"Cipta"**
2. Sistem menjana semua tingkat dan unit
3. Alamat muncul dalam wilayah dengan semua unit

**Contoh:**

- Bangunan: Blok 123
- Tingkat: 1 hingga 12
- Unit setiap tingkat: 8
- Hasil: 96 unit dicipta (12 tingkat × 8 unit)

#### Menambah Alamat Persendirian (Satu Tingkat)

**Langkah 1: Mulakan Penciptaan**

1. Pilih wilayah
2. Klik **"Tambah Alamat"** atau **"+"**
3. Pilih jenis **"Alamat Persendirian"**

**Langkah 2: Masukkan Maklumat Hartanah**

- **Poskod/Alamat Hartanah**: Pengecam unik
  - Alamat jalan atau poskod
  - Setiap hartanah adalah satu rekod
- **Nama Hartanah**: Nama rumah pilihan
  - Contoh: "123 Jalan Utama", "Villa Sunshine"

**Langkah 3: Tetapkan Lokasi (Pilihan)**

- Klik **"Tetapkan Geolokasi"**
- Gunakan peta untuk menunjukkan lokasi tepat
- Membantu dengan navigasi
- Boleh dikemas kini kemudian

**Langkah 4: Cipta**

1. Klik **"Cipta Hartanah"**
2. Unit alamat tunggal dicipta
3. Muncul dalam senarai wilayah

#### Mengurus Alamat Sedia Ada

**Edit Nama Alamat:**

1. Pilih wilayah dengan alamat
2. Klik pilihan edit untuk alamat
3. Kemas kini nama/poskod
4. Simpan perubahan

**Tukar Poskod:**

1. Akses mod edit alamat
2. Kemas kini medan poskod
3. Mungkin mempengaruhi geokod
4. Simpan dan sahkan lokasi peta

**Tetapkan Semula Alamat:**

- Mengosongkan semua status unit dalam alamat
- Nota dikekalkan
- Gunakan apabila alamat sepenuhnya dikerjakan

**Padam Alamat:**

- Membuang keseluruhan alamat dan semua unit
- Tidak boleh dibatalkan
- Sahkan dengan teliti sebelum memadam

#### Mengurus Unit (Alamat Awam Sahaja)

**Tambah Unit:**

1. Pilih alamat
2. Klik **"Tambah Unit"**
3. Nyatakan nombor unit untuk ditambah
4. Unit dicipta secara automatik

**Padam Unit:**

1. Klik pada unit tertentu
2. Klik butang **"Padam Unit"** dalam modal
3. Sahkan pemadaman
4. Unit dibuang dari alamat

**Tukar Jujukan Unit:**

1. Buka modal edit unit
2. Kemas kini nombor jujukan
3. Mempengaruhi urutan lawatan
4. Nombor lebih rendah dilawati dahulu

**Tambah/Padam Tingkat:**

1. Akses pengurusan tingkat
2. Tambah nombor tingkat baharu
3. Atau buang keseluruhan tingkat
4. Semua unit pada tingkat terjejas

**Kemas Kini Geolokasi Unit:**

- Tetapkan koordinat khusus untuk unit
- Berguna untuk bangunan besar
- Membantu dengan navigasi tepat
- Ciri pilihan

---

---

## Penggunaan Mudah Alih

![Paparan Peta Tugasan Penerbit](assets/screenshots/publisher_assignment_map_view.png)

*Rajah 26: Antara muka responsif mudah alih dioptimumkan untuk khidmat lapangan*

### Menggunakan Ministry Mapper pada Telefon Anda

Ministry Mapper sepenuhnya responsif dan dioptimumkan untuk peranti mudah alih, menjadikannya sempurna untuk khidmat lapangan.

#### Mengakses pada Mudah Alih

**Akses Pelayar:**

1. Buka pelayar mudah alih anda:
   - **iOS**: Safari, Chrome
   - **Android**: Chrome, Firefox, Samsung Internet
2. Navigasi ke URL Ministry Mapper sidang anda
3. Log masuk atau klik pautan tugasan
4. Antara muka menyesuaikan secara automatik kepada saiz skrin anda

**Ciri pada Mudah Alih:**

- ✓ Butang dan kawalan mesra sentuh
- ✓ Gerak isyarat leret untuk navigasi
- ✓ Susun atur dioptimumkan untuk skrin kecil
- ✓ Sasaran ketik lebih besar untuk pemilihan mudah
- ✓ Akses penuh kepada semua ciri desktop
- ✓ Integrasi peta interaktif dengan GPS

#### Pemasangan Aplikasi Web Progresif (PWA)

![Mod Paparan Peta Satu Tingkat](assets/screenshots/single_story_map_view_mode.png)

*Rajah 27: Mod paparan peta skrin penuh tersedia dalam pemasangan PWA*

Pasang Ministry Mapper sebagai aplikasi untuk prestasi lebih baik:

**Faedah Pemasangan:**

- 🚀 Pemuatan lebih pantas dengan sumber dicache
- 📱 Ikon aplikasi pada skrin utama
- 🎯 Pengalaman skrin penuh (tiada UI pelayar)
- ⚡ Prestasi dipertingkatkan
- 🔔 Integrasi lebih baik dengan peranti

**Pemasangan iOS (Safari):**

1. Buka Ministry Mapper dalam Safari
2. Ketik butang **Kongsi** (📤)
3. Tatal ke bawah dan ketik **"Tambah ke Skrin Utama"**
4. Edit nama jika dikehendaki
5. Ketik **"Tambah"**
6. Ikon aplikasi muncul pada skrin utama

**Pemasangan Android (Chrome):**

1. Buka Ministry Mapper dalam Chrome
2. Ketik butang menu (⋮)
3. Pilih **"Pasang aplikasi"** atau **"Tambah ke Skrin Utama"**
4. Sahkan pemasangan
5. Ikon aplikasi muncul pada skrin utama atau laci aplikasi

**Menggunakan Aplikasi yang Dipasang:**

- Lancar dari skrin utama seperti mana-mana aplikasi
- Tiada bar alamat pelayar
- Pengalaman aplikasi yang lancar
- Kemas kini secara automatik

#### Amalan Terbaik Mudah Alih

**Sebelum Keluar:**

1. ✓ Semak pautan tugasan belum tamat tempoh
2. ✓ Semak wilayah dan peta
3. ✓ Perhatikan sebarang arahan khas
4. ✓ Pastikan sambungan internet stabil
5. ✓ Cas peranti anda sepenuhnya
6. ✓ Pertimbangkan pengecas mudah alih

**Semasa dalam Khidmat Lapangan:**

1. ✓ Kemas kini alamat serta-merta selepas lawatan
2. ✓ Tambah nota semasa maklumat masih segar
3. ✓ Gunakan navigasi GPS pada peta
4. ✓ Ikut nombor jujukan untuk penghalaan cekap
5. ✓ Jimat bateri dengan meredupkan skrin apabila tidak diperlukan

**Petua Penggunaan Data:**

- Ministry Mapper menggunakan data minimum
- Jubin peta mungkin menggunakan lebih banyak data
- Kebanyakan kemas kini adalah < 1KB setiap satu
- Sesuai untuk penggunaan data mudah alih

### Keupayaan dan Had Luar Talian

**Sambungan Internet Diperlukan:**

Ministry Mapper memerlukan internet aktif untuk:

- ✗ Memuatkan data wilayah
- ✗ Menyimpan kemas kini status
- ✗ Penyegerakan masa nyata
- ✗ Memaparkan peta
- ✗ Pengesahan pengguna

**Ciri Luar Talian Terhad:**

- Aset statik dicache (shell aplikasi)
- Wilayah yang dimuatkan sebelumnya mungkin dipaparkan
- **Tidak boleh membuat kemas kini luar talian**
- **Kemas kini tidak dibarisi untuk penyegerakan kemudian**

**Mengendalikan Kehilangan Sambungan:**

- Sistem mengesan kehilangan sambungan
- Memaparkan amaran status sambungan
- Menyambung semula secara automatik apabila tersedia
- Sambung semula kerja apabila sambungan dipulihkan

**Pengesyoran:**

- ✓ Pastikan sambungan yang boleh dipercayai sebelum bermula
- ✓ Uji sambungan di lokasi wilayah
- ✓ Ada pelan sandaran untuk kawasan tanpa internet
- ✓ Pertimbangkan hotspot WiFi mudah alih jika diperlukan
- ✓ Kemas kini alamat semasa sambungan aktif

---

---

## Tetapan Akaun dan Profil

### Pengurusan Profil Peribadi

![Log Masuk](assets/screenshots/sign_in.png)

*Rajah 28: Antara muka log masuk untuk mengakses akaun Ministry Mapper anda*

**Mengakses Profil Anda:**

1. Klik **nama/ikon profil** anda (sudut kanan atas)
2. Pilih **"Profil"** dari menu dropdown
3. Lihat dan urus tetapan akaun anda

**Pilihan Profil Tersedia:**

- Lihat maklumat akaun (nama, e-mel)
- Tukar kata laluan
- Lihat keahlian sidang
- Akses pengurusan pengguna (Pentadbir sahaja)
- Log keluar

### Menukar Kata Laluan Anda

**Keperluan Keselamatan:**

- Minimum 6 aksara
- Sekurang-kurangnya satu nombor
- Sekurang-kurangnya satu huruf besar
- Mesti sepadan dengan pengesahan

**Langkah untuk Menukar:**

1. Pergi ke profil anda
2. Klik **"Tukar Kata Laluan"**
3. Masukkan **kata laluan semasa** anda
4. Masukkan **kata laluan baharu**
5. **Sahkan kata laluan baharu**
6. Klik **"Simpan"** atau **"Tukar Kata Laluan"**
7. Mesej kejayaan mengesahkan perubahan

> **💡 Petua**: Gunakan kata laluan yang kuat dan unik. Pertimbangkan untuk menggunakan pengurus kata laluan.

### Pemulihan Kata Laluan

![Lupa Kata Laluan](assets/screenshots/forgot_password.png)

*Rajah 29: Halaman pemulihan kata laluan untuk menetapkan semula kata laluan yang dilupakan*

Lupa kata laluan anda? Proses pemulihan mudah:

1. Pergi ke halaman log masuk
2. Klik pautan **"Lupa kata laluan anda?"**
3. Masukkan **alamat e-mel berdaftar** anda
4. Klik **"Teruskan"** atau **"Hantar Pautan Tetapan Semula"**
5. Semak peti masuk e-mel anda

![E-mel Tetapan Semula Kata Laluan](assets/screenshots/reset_password_email.png)

*Rajah 30: E-mel tetapan semula kata laluan dengan pautan selamat untuk mencipta kata laluan baharu*

6. Klik pautan tetapan semula kata laluan (sah untuk masa terhad)
7. Cipta kata laluan baharu memenuhi keperluan
8. Sahkan kata laluan baharu
9. Log masuk dengan kata laluan baharu

**Jika anda tidak menerima e-mel:**

- Semak folder spam/sampah
- Sahkan anda memasukkan e-mel yang betul
- Tunggu beberapa minit dan cuba lagi
- Hubungi pentadbir jika masalah berterusan

### Pemilihan Bahasa

![Senarai Pemilihan Bahasa](assets/screenshots/language_selection_list.png)

*Rajah 31: Antara muka pemilihan bahasa menunjukkan semua bahasa yang disokong*

Ministry Mapper menyokong berbilang bahasa untuk sidang antarabangsa.

**Bahasa Disokong:**

- 🇬🇧 English (en)
- 🇯🇵 Japanese (ja / 日本語)
- 🇰🇷 Korean (ko / 한국어)
- 🇨🇳 Chinese (zh / 中文)
- 🇮🇩 Indonesian (id / Bahasa Indonesia)
- 🇲🇾 Malay (ms / Bahasa Melayu)

**Cara Bahasa Ditentukan:**

- Dikesan secara automatik dari tetapan bahasa pelayar
- Menggunakan keutamaan bahasa sistem pengendalian anda
- Tiada pemilihan manual diperlukan dalam kebanyakan kes

**Untuk Menukar Bahasa:**

![Tetapan Tema Gelap Terang](assets/screenshots/dark_light_theme_settings.png)

*Rajah 32: Tetapan tema dan penampilan termasuk keutamaan bahasa*

Tukar tetapan bahasa pelayar (Chrome: Tetapan → Bahasa | Safari: Keutamaan Sistem → Bahasa & Rantau | Firefox: Tetapan → Umum → Bahasa), kemudian muat semula Ministry Mapper. Semua elemen antara muka, mesej, dan teks bantuan diterjemahkan sepenuhnya.

### Tetapan Penampilan dan Tema

Ministry Mapper menyokong kedua-dua tema terang dan gelap untuk memenuhi keutamaan anda dan mengurangkan ketegangan mata.

**Menukar Tema:**

Ikon profil → Tetapan Tema/Penampilan → Pilih:
- **Mod Terang**: Antara muka terang untuk siang hari
- **Mod Gelap**: Kecerahan dikurangkan untuk cahaya rendah (menjimatkan bateri pada OLED)
- **Lalai Sistem**: Sepadan dengan tetapan peranti secara automatik

---

## Amalan Terbaik

### Untuk Penerbit

| Fasa | Amalan Terbaik |
|-------|---------------|
| **Sebelum Bermula** | Semak wilayah pada peta • Semak nota/arahan • Rancang laluan menggunakan jujukan • Perhatikan alamat "Jangan Panggil" • Pastikan peranti dicas |
| **Semasa Perkhidmatan** | Kemas kini serta-merta selepas setiap lawatan • Tambah nota terperinci dan hormat • Ikut nombor jujukan • Gunakan peta untuk navigasi • Tandakan "Tiada di Rumah" dengan tepat |
| **Selepas Selesai** | Semak semua kemas kini • Tambah pemerhatian akhir • Maklumkan pentadbir jika selesai • Laporkan masalah alamat |

### Untuk Konduktor

- Tetapkan masa tamat pautan yang sesuai berdasarkan saiz wilayah
- Sertakan nama penerbit untuk penjejakan
- Bersihkan tugasan tamat tempoh secara berkala
- Pantau penyelesaian wilayah dan susulan tugasan yang tertunggak
- Siarkan mesej dan arahan yang jelas

### Untuk Pentadbir

| Kawasan | Amalan Terbaik |
|------|---------------|
| **Persediaan Wilayah** | Penamaan konsisten • Kod pendek dan bermakna • Penerangan jelas • Sahkan lokasi peta • Jujukan logik |
| **Pengurusan Data** | Pembersihan berkala • Tetapkan semula wilayah selesai • Sahkan ketepatan • Sandaran luaran • Latih pengguna |
| **Pengurusan Pengguna** | Penetapan peranan yang sesuai • Buang pengguna tidak aktif • Respons segera kepada permintaan • Komunikasi jelas |

### Prinsip Umum

- **Rancang Lebih Awal**: Semak sebelum keluar
- **Bekerja Secara Sistematik**: Selesaikan satu bahagian pada satu masa
- **Kemas Kini Segera**: Jangan tunggu untuk merekod maklumat
- **Berkomunikasi dengan Jelas**: Tulis nota yang boleh difahami dan hormat
- **Jadi Teliti**: Liputi semua alamat dengan gigih
- **Kekal Fleksibel**: Sesuaikan dengan keperluan wilayah

---

## Penyelesaian Masalah Isu Biasa

### Masalah Log Masuk

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Kata Laluan Salah** | Ralat "Invalid credentials" | Sahkan e-mel • Semak Caps Lock • Gunakan "Lupa Kata Laluan" → Tetapkan semula melalui e-mel |
| **Akaun Tidak Disahkan** | Mesej "E-mel tidak disahkan" | Semak peti masuk/spam untuk e-mel pengesahan • Klik pautan • Minta yang baharu jika tamat tempoh |
| **Tiada Akses Sidang** | Log masuk tetapi tiada wilayah ditunjukkan | Hubungi pentadbir untuk menjemput anda dan menetapkan peranan • Log keluar/masuk selepas akses diberikan |
| **Masalah OTP** | Tidak menerima/kod OTP tidak sah | Semak spam • Kod tamat tempoh dalam 5-10 minit • Minta kod baharu • Sahkan alamat e-mel |

---

### Masalah Kemas Kini Data

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Perubahan Tidak Disimpan** | Simpan tidak berterusan, hilang selepas muat semula | Semak sambungan internet • Muat semula halaman (Ctrl/Cmd + R) • Kosongkan cache pelayar • Cuba pelayar berbeza • Semak pengeditan serentak |
| **Kemas Kini Masa Nyata Tidak Muncul** | Perubahan oleh orang lain tidak ditunjukkan | Pastikan internet aktif • Kekalkan halaman dibuka • Muat semula untuk memaksa kemas kini • Semak status sambungan |

---

### Masalah Peta dan Navigasi

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Peta Tidak Dimuatkan** | Kotak kelabu, ralat, peta beku | Muat semula halaman (F5) • Tunggu 10-15 saat • Semak kelajuan internet • Kosongkan cache • Cuba pelayar berbeza • Aktifkan JavaScript |
| **Lokasi Salah** | Penanda tersalah letak | Pentadbir: Kemas kini geolokasi • Sahkan poskod • Gunakan "Kemas Kini Geolokasi" • Gunakan lat/long jika diperlukan |
| **Arah Tidak Berfungsi** | Masalah navigasi | Sahkan lokasi asal sidang • Semak alamat mempunyai koordinat sah |

---

### Masalah Pautan Tugasan

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Pautan Tamat Tempoh** | "Pautan telah tamat tempoh", ralat 404 | Hubungi pentadbir/konduktor untuk pautan baharu (pautan tamat tempoh selepas masa yang ditetapkan, biasanya 24 jam - ini adalah ciri keselamatan) |
| **Pautan Tidak Berfungsi** | Tidak akan dibuka, mesej ralat | Pastikan keseluruhan pautan disalin (semak pemecahan baris) • Salin-tampal ke pelayar (jangan taip) • Sahkan tidak tamat tempoh • Hubungi pentadbir |

---

### Masalah Kebenaran dan Akses

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Kebenaran Dinafikan** | "Insufficient permissions", ciri dikelabukan | Sahkan peranan anda dengan pentadbir • Log keluar, kosongkan cache, log masuk semula • Minta naik taraf peranan jika diperlukan |
| **Data Sidang Salah** | Wilayah/data tidak dikenali | Sahkan akaun yang betul • Semak pemilih sidang • Log keluar/masuk • Hubungi pentadbir |

---

### Masalah Prestasi

| Isu | Gejala | Penyelesaian |
|-------|----------|-----------|
| **Pemuatan Perlahan** | Masa muat lama, lag, kelewatan | Semak kelajuan internet • Tutup tab yang tidak perlu • Kosongkan cache • Mulakan semula pelayar • Cuba masa berbeza • Laporkan jika berterusan |
| **Ranap/Beku** | Aplikasi berhenti bertindak balas | Muat semula halaman • Kosongkan cache/kuki • Kemas kini pelayar • Cuba pelayar berbeza • Mulakan semula peranti • Semak memori |

---

### Keserasian Pelayar

#### Keserasian Pelayar

**Disokong:** Chrome, Firefox, Safari, Edge (versi terkini) • iOS Safari (iOS 13+) • Android Chrome

**Tidak Disokong:** Internet Explorer, pelayar lapuk

**Jika mengalami masalah:** Kemas kini pelayar • Aktifkan JavaScript • Benarkan kuki • Lumpuhkan pencegahan penjejakan ketat • Cuba pelayar berbeza

---

## Mendapatkan Bantuan dan Sokongan

### Saluran Sokongan

**1. Pentadbir Sidang Anda**

- **Titik hubungan pertama** untuk kebanyakan isu
- Boleh membantu dengan:
  - Akses dan peranan akaun
  - Soalan wilayah
  - Pautan tugasan
  - Konfigurasi tempatan

**2. Dokumentasi Rasmi**

- **GitHub Wiki**: https://github.com/rimorin/ministry-mapper/wiki
- Panduan komprehensif untuk semua peranan
- Dokumentasi persediaan dan keselamatan
- Soalan lazim dan penyelesaian biasa

**3. Masalah Teknikal**

- **GitHub Issues**: https://github.com/rimorin/ministry-mapper/issues
- Laporkan pepijat dan masalah teknikal
- Semak isu sedia ada terlebih dahulu
- Cari masalah yang serupa

### Melaporkan Masalah dengan Berkesan

**Sertakan dalam Laporan Anda:**
- Apa yang anda cuba lakukan
- Apa yang berlaku sebaliknya (mesej ralat, tangkapan skrin)
- Langkah untuk mengeluarkan semula masalah
- Pelayar dan versi
- Peranti dan OS
- Jenis akaun (Penerbit/Konduktor/Pentadbir)

**Contoh Laporan Baik:**
```
Isu: Tidak boleh menyimpan kemas kini status alamat
Langkah: Buka pautan → Klik #05-123 → Tukar ke "Selesai" → Klik Simpan → Ralat: "Failed to update"
Pelayar: Chrome 120 | Peranti: iPhone 12, iOS 17 | Akaun: Pautan penerbit
Tangkapan skrin: [dilampirkan]
```

### Hubungan Kecemasan

Untuk isu mendesak:

- Hubungi pentadbir sidang anda secara langsung
- Sediakan nombor telefon/e-mel
- Terangkan kecemasan dengan jelas
- Sediakan butiran berkaitan

---

---

## Rujukan Pantas

### Pintasan Papan Kekunci

Ministry Mapper menggunakan pintasan pelayar standard:

| Pintasan         | Tindakan                                      |
| ---------------- | ------------------------------------------- |
| `Escape`         | Tutup modal/dialog terbuka                   |
| `Tab`            | Navigasi antara medan borang                |
| `Enter`          | Hantar borang atau sahkan tindakan             |
| `Ctrl/Cmd + R`   | Muat semula halaman                                |
| Pengeditan standard | Salin, tampal, pilih semua berfungsi dalam medan teks |

### Rujukan Pantas Status

| Status          | Simbol | Bila untuk Digunakan                           |
| --------------- | ------ | ------------------------------------- |
| **Belum Selesai**    | ⚪     | Alamat belum dilawati (lalai)     |
| **Selesai**        | ✅     | Berjaya menghubungi penghuni rumah    |
| **Tiada di Rumah**    | 🏠     | Tiada yang menjawab pintu              |
| **Jangan Panggil** | 🚫     | Penghuni rumah meminta tiada lawatan       |
| **Tidak Sah**     | ❌     | Alamat tidak wujud atau tidak boleh diakses |

### Rujukan Pantas Keupayaan Peranan

| Ciri                  | Penerbit | Baca Sahaja | Konduktor | Pentadbir |
| ------------------------ | :-------: | :-------: | :-------: | :-----------: |
| Lihat melalui pautan tugasan |     ✓     |     -     |     -     |       -       |
| Lihat semua wilayah     |     -     |     ✓     |     ✓     |       ✓       |
| Kemas kini status alamat    |     ✓     |     -     |     -     |       ✓       |
| Cipta tugasan       |     -     |     -     |     ✓     |       ✓       |
| Siarkan mesej            |     -     |     -     |     ✓     |       ✓       |
| Urus wilayah       |     -     |     -     |     -     |       ✓       |
| Urus pengguna             |     -     |     -     |     -     |       ✓       |
| Konfigurasi tetapan       |     -     |     -     |     -     |       ✓       |

---



## Privasi dan Keselamatan

### Melindungi Maklumat

Ministry Mapper mengendalikan maklumat alamat dan peribadi yang sensitif. Sila patuhi garis panduan ini:

**Keselamatan Akaun:**

- ✓ **Jangan sekali-kali berkongsi kelayakan log masuk** dengan sesiapa
- ✓ **Gunakan kata laluan yang kuat dan unik** (minimum 6 aksara dengan nombor dan huruf besar)
- ✓ **Log keluar pada peranti yang dikongsi** sentiasa
- ✓ **Aktifkan OTP jika tersedia** untuk keselamatan tambahan
- ✓ **Laporkan aktiviti mencurigakan** serta-merta

**Privasi Data:**

- ✓ **Rekod hanya maklumat yang perlu** dalam nota
- ✓ **Jadi hormat dan faktual** dalam semua penerangan
- ✓ **Tiada data peribadi sensitif** (perubatan, kewangan, dll.)
- ✓ **Ikut permintaan penghuni rumah** untuk privasi
- ✓ **Patuhi undang-undang privasi** (GDPR, CCPA, peraturan tempatan)

**Pematuhan Undang-undang:**

- ⚠️ **GDPR (Eropah)**: Keperluan perlindungan data peribadi
- ⚠️ **CCPA (California)**: Hak privasi pengguna
- ⚠️ **LGPD (Brazil)**: Peraturan perlindungan data
- ⚠️ **Undang-undang Tempatan**: Semak keperluan rantau anda

### Maklumat Apa yang Disimpan

**Data Pengguna:**

- Butiran akaun (nama, e-mel, status pengesahan)
- Penetapan peranan sidang
- Pautan tugasan dicipta/diakses
- Cap masa aktiviti

**Data Wilayah:**

- Maklumat alamat dan unit
- Kemas kini dan sejarah status
- Nota dan maklumat lawatan
- Koordinat geolokasi
- Penjejakan kemajuan

**Data Sistem:**

- Sesi log masuk
- Langganan masa nyata
- Sejarah mesej
- Tetapan konfigurasi

### Penyimpanan dan Keselamatan Data

| Komponen | Butiran |
|-----------|---------|
| **Backend** | Pangkalan data PocketBase diurus oleh pentadbir • Lokasi pengehosan ditentukan oleh sidang • Kawalan akses berasaskan peranan |
| **Penyegerakan Masa Nyata** | Langganan PocketBase • Sambungan HTTPS disulitkan • Penyambungan semula automatik • Pengurusan sesi |
| **Sisi Pelanggan** | Service worker mencache aset statik sahaja • Tiada data sensitif disimpan secara tempatan • Kemas kini automatik pada versi baharu |
| **Tanggungjawab Pentadbir** | Laksanakan prosedur sandaran • Jalankan audit keselamatan • Kekalkan backend dikemas kini • Pantau log akses |

---

## Kesimpulan

Terima kasih kerana menggunakan Ministry Mapper untuk menyokong aktiviti khidmat lapangan sidang anda. Penyelesaian moden berasaskan web ini membawa kecekapan, kerjasama, dan faedah alam sekitar kepada pengurusan wilayah.

### Perkara Utama

| Peranan | Tanggungjawab Utama |
|------|---------------------|
| **Penerbit** | Akses melalui pautan • Kemas kini serta-merta selepas lawatan • Tulis nota yang hormat • Ikut jujukan |
| **Konduktor** | Cipta tugasan • Pantau kemajuan • Siarkan mesej • Selaraskan aktiviti |
| **Pentadbir** | Konfigurasi tetapan • Urus wilayah • Tetapkan peranan • Pastikan keselamatan |

### Ciri Sistem

**Tindanan Teknologi:**

- ✓ Frontend React 19 + TypeScript
- ✓ Backend PocketBase untuk pengurusan data
- ✓ Leaflet dengan OpenStreetMap untuk navigasi
- ✓ Penyegerakan masa nyata
- ✓ PWA responsif mudah alih
- ✓ Sokongan berbilang bahasa
- ✓ Kawalan akses berasaskan peranan
- ✓ Pemantauan ralat Sentry

**Faedah:**

- 🌱 **Mesra Alam**: Menghapuskan pembaziran kertas
- ⚡ **Masa Nyata**: Kemas kini segera merentas semua peranti
- 📱 **Mudah Alih Dahulu**: Berfungsi pada mana-mana peranti dengan internet
- 🗺️ **Peta Bersepadu**: Pemetaan interaktif untuk navigasi mudah
- 🔒 **Selamat**: Kebenaran berasaskan peranan dan sokongan OTP
- 🌍 **Berbilang Bahasa**: Sokongan untuk 7+ bahasa
- 💾 **Boleh Dipercayai**: Backend PocketBase dengan penyegerakan masa nyata

---

**Versi**: Rujuk versi penempatan anda
**Kemas Kini Terakhir**: 2024

Untuk sokongan teknikal, hubungi pentadbir sidang anda atau lawati wiki GitHub.

---
