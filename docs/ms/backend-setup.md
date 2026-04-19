# Panduan Persediaan Bahagian Belakang

!!! warning "Pengehosan Sendiri Tidak Disyorkan"
    Panduan ini adalah untuk pengguna lanjutan yang mahu menghoskan sendiri Ministry Mapper. Kami tidak menggalakkan pengehosan sendiri kerana kerumitan teknikal, beban penyelenggaraan, dan tanggungjawab keselamatan yang terlibat.
    
    **Kebanyakan pengguna sepatutnya**: Gunakan perkhidmatan yang dihoskan kami di **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Mencari Dokumentasi Pengguna?"
    Jika anda adalah pengguna biasa yang ingin mempelajari cara menggunakan Ministry Mapper, lihat [Memulakan](getting-started.md) atau [Panduan Pengguna](user-guide.md) sebaliknya.

## Gambaran Keseluruhan

Bahagian belakang Ministry Mapper (ministry-mapper-be) dibina dengan Go dan PocketBase. Ia menyediakan:

- Penyimpanan dan pengurusan data (pangkalan data SQLite)
- Pengesahan dan kebenaran pengguna
- Penyegerakan data masa nyata
- Titik akhir API tersuai untuk pengurusan wilayah
- Kerja latar belakang untuk tugas automatik
- Pemberitahuan e-mel melalui SMTP

**Panduan ini mengandaikan anda mempunyai**:
- Pengalaman dengan pentadbiran pelayan
- Pemahaman tentang Docker dan kontenerisasi
- Kebiasaan dengan pembolehubah persekitaran
- Pengetahuan tentang amalan terbaik keselamatan

Untuk arahan pengehosan sendiri yang lengkap, lihat [Panduan Pengehosan Sendiri](self-hosting.md).

## Rujukan Pantas

Halaman ini menyediakan rujukan pantas untuk konfigurasi bahagian belakang. **Untuk arahan persediaan yang lengkap, lihat [Panduan Pengehosan Sendiri](self-hosting.md).**

### Timbunan Teknologi

- **Go**: Bahasa pengaturcaraan yang pantas dan cekap
- **PocketBase**: Bahagian-belakang-sebagai-perkhidmatan dengan panel pentadbir terbina dalam
- **SQLite**: Pangkalan data ringan dalam folder `pb_data`
- **Docker**: Kontena berasaskan Alpine Linux

### Ciri-Ciri Utama

- Penyimpanan dan pengurusan data
- Pengesahan pengguna
- Langganan masa nyata (Server-Sent Events)
- Titik akhir API tersuai
- Kerja latar belakang berjadual
- Pemberitahuan e-mel (SMTP)
- Penjejakan ralat (Sentry)
- Bendera ciri (LaunchDarkly)

---

!!! note "Arahan Persediaan Lengkap"
    Bahagian di bawah menyediakan rujukan teknikal untuk konfigurasi bahagian belakang. Untuk arahan persediaan langkah demi langkah dengan konteks dan penjelasan, sila lihat **[Panduan Pengehosan Sendiri](self-hosting.md)**.

---

## Pembolehubah Persekitaran Dijelaskan

### Kunci Integrasi Perkhidmatan (Pilihan)

```bash
# Kunci API MailerSend - untuk menghantar e-mel melalui perkhidmatan MailerSend
# Tinggalkan kosong jika menggunakan SMTP sebaliknya
MAILERSEND_API_KEY=

# Alamat e-mel untuk pengirim MailerSend
MAILERSEND_FROM_EMAIL=

# Kunci SDK LaunchDarkly - untuk bendera ciri bagi mengawal kerja latar belakang
# Kerja berjalan secara lalai jika tidak dikonfigurasi
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# Kunci API OpenAI - untuk ringkasan yang dijana AI dalam laporan bulanan dan digest mesej
# Pilihan: laporan dan digest berfungsi baik tanpa ini; bahagian AI hanya ditinggalkan
OPENAI_API_KEY=
```

#### E-mel (MailerSend)

| Pembolehubah | Diperlukan | Penerangan |
|--------------|------------|------------|
| `MAILERSEND_API_KEY` | Pilihan | Kunci API MailerSend untuk penghantaran e-mel transaksi (digunakan untuk laporan dan e-mel kitaran hayat). Jika tidak ditetapkan, menggunakan SMTP sebagai sandaran. |
| `MAILERSEND_FROM_EMAIL` | Pilihan | Alamat e-mel pengirim untuk e-mel MailerSend. |

#### Bendera Ciri (LaunchDarkly)

| Pembolehubah | Diperlukan | Penerangan |
|--------------|------------|------------|
| `LAUNCHDARKLY_SDK_KEY` | Pilihan | Kunci SDK sisi pelayan LaunchDarkly. Diperlukan jika menggunakan kerja latar belakang yang dikawal bendera ciri. |
| `LAUNCHDARKLY_CONTEXT_KEY` | Pilihan | Kunci konteks LaunchDarkly yang mengenal pasti persekitaran penempatan ini. |

#### Ringkasan AI (OpenAI)

| Pembolehubah | Diperlukan | Penerangan |
|--------------|------------|------------|
| `OPENAI_API_KEY` | Pilihan | Kunci API OpenAI. Diperlukan hanya jika ringkasan laporan yang dijana AI diaktifkan melalui bendera ciri. |

### Tetapan Aplikasi

```bash
# Nama ditunjukkan dalam e-mel dan panel pentadbir
PB_APP_NAME=Ministry Mapper

# URL di mana bahagian hadapan anda dihoskan
PB_APP_URL=http://localhost:3000
```

### Akaun Pentadbir Lalai

```bash
# Akaun pentadbir awal yang dibuat semasa persediaan pertama
# Tukar kata laluan segera selepas log masuk pertama!
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### Konfigurasi E-mel (SMTP)

```bash
# Alamat pelayan SMTP (cth., smtp.gmail.com, smtp.sendgrid.net)
PB_SMTP_HOST=

# Kata laluan SMTP (gunakan Kata Laluan Aplikasi untuk Gmail, bukan kata laluan biasa anda)
PB_SMTP_PASSWORD=

# Nama pengguna SMTP (biasanya alamat e-mel anda)
PB_SMTP_USERNAME=

# Port SMTP (587 untuk TLS, 465 untuk SSL)
PB_SMTP_PORT=587

# Alamat e-mel ditunjukkan dalam medan "Daripada"
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# Nama ditunjukkan dalam medan "Daripada"
PB_SMTP_SENDER_NAME=MM Support
```

### Keselamatan & Kawalan Akses

```bash
# Senarai asal yang dibenarkan untuk CORS, dipisahkan koma
# Gunakan * untuk pembangunan, domain tertentu untuk pengeluaran
# Contoh: https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# Sembunyikan UI pentadbir dari akses awam (true/false)
# Tetapkan ke true dalam pengeluaran untuk keselamatan
PB_HIDE_CONTROLS=false
```

### Tetapan Pengesahan

```bash
# Aktifkan log masuk kata laluan sekali guna (true/false)
PB_OTP_ENABLED=false

# Aktifkan pengesahan pelbagai faktor (true/false)
PB_MFA_ENABLED=false
```

### Had Kadar

```bash
# Aktifkan had kadar untuk mencegah penyalahgunaan API (true/false)
PB_ENABLE_RATE_LIMITING=false
```

### Penjejakan Ralat (Tidak dalam .env.sample - tambah secara manual jika perlu)

```bash
# DSN Sentry untuk pemantauan dan penjejakan ralat
SENTRY_DSN=

# Nama persekitaran untuk Sentry (development, staging, production)
SENTRY_ENV=production

# Hash komit git - ditetapkan secara automatik oleh platform pengehosan seperti Coolify
SOURCE_COMMIT=
```

## Bendera Ciri

Ministry Mapper menggunakan [LaunchDarkly](https://launchdarkly.com) untuk mengawal pelaksanaan kerja latar belakang. Setiap kerja boleh diaktifkan atau dinyahaktifkan secara bebas tanpa penggunaan semula. Bendera ciri memerlukan `LAUNCHDARKLY_SDK_KEY` dan `LAUNCHDARKLY_CONTEXT_KEY` dikonfigurasi.

| Kunci Bendera | Kerja Dikawal | Penerangan |
|---------------|---------------|------------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | Padam tugasan peta yang tamat tempoh (berjalan setiap 5 min) |
| `enable-territory-aggregations` | `updateTerritoryAggregates` | Kira semula statistik kemajuan wilayah (berjalan setiap 10 min) |
| `enable-message-processing` | `processMessages` | Proses mesej penerbit yang belum dibaca (berjalan setiap 30 min) |
| `enable-instruction-processing` | `processInstructions` | Proses arahan tugasan wilayah (berjalan setiap 30 min) |
| `enable-note-processing` | `processNotes` | Proses nota jemaah yang dikemas kini (berjalan setiap jam) |
| `enable-monthly-report` | `generateMonthlyReport` | Jana dan e-mel laporan Excel (berjalan 1hb bulan) |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | Amaran dan lumpuhkan pengguna tanpa peranan yang ditetapkan (berjalan harian) |
| `enable-inactive-user-processing` | `processInactiveUsers` | Amaran dan lumpuhkan akaun tidak aktif — NIST AC-2(3) (berjalan harian) |
| `enable-new-addresses-notification` | `processNewAddresses` | Hantar e-mel ringkasan harian untuk alamat yang dicipta dalam aplikasi (berjalan harian) |
| `enable-report-ai-summary` | Ringkasan AI dalam laporan | Sertakan ringkasan yang dijana OpenAI dalam laporan jemaah |

!!! note
    Jika LaunchDarkly tidak dikonfigurasi, semua kerja berjalan secara lalai (diaktifkan: true).

## Langkah-Langkah Pemasangan

### Pilihan 1: Pelaksanaan Docker (Disyorkan)

Docker menjadikan pelaksanaan mudah dan konsisten merentasi platform.

#### 1. Bina Imej Docker

```bash
docker build -t ministry-mapper-backend .
```

#### 2. Jalankan Kontena

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Nota Penting:**

- Kontena berjalan pada port **8080** (bukan 8090)
- `-p 8080:8080` memetakan port 8080 kontena ke port 8080 hos anda
- `-v /path/to/pb_data:/app/pb_data` menyimpan pangkalan data anda secara kekal (gantikan `/path/to/pb_data` dengan laluan sebenar pada sistem anda)
- `--env-file .env` memuatkan pembolehubah persekitaran dari fail `.env`
- `-d` menjalankan kontena di latar belakang (mod terputus)

### Pilihan 2: Pembangunan Tempatan

Untuk pembangunan dan ujian pada komputer anda:

#### 1. Pasang Dependensi Go

```bash
./scripts/install.sh
```

Skrip ini menjalankan `go get ./...` dan `go mod tidy` untuk memasang semua dependensi.

#### 2. Sediakan Pembolehubah Persekitaran

```bash
cp .env.sample .env
# Edit .env dengan tetapan anda
```

#### 3. Jalankan Aplikasi

```bash
./scripts/start.sh
```

Skrip ini mengeksport pembolehubah persekitaran dari `.env` dan menjalankan `go run main.go serve`.

Bahagian belakang akan bermula pada **http://localhost:8090** secara lalai (port lalai PocketBase).

## Mengakses Panel Pentadbir

Setelah bahagian belakang berjalan:

1. Navigasi ke `http://url-bahagian-belakang-anda/_/` (perhatikan garis bawah dan garis miring akhir)
2. Log masuk dengan kelayakan pentadbir anda dari `PB_ADMIN_EMAIL` dan `PB_ADMIN_PASSWORD`
3. Anda akan melihat papan pemuka pentadbir PocketBase

### Apa Yang Boleh Anda Lakukan di Panel Pentadbir

- **Koleksi**: Lihat dan edit semua jadual pangkalan data (pengguna, jemaah, wilayah, peta, alamat, dll.)
- **Log**: Lihat log aplikasi dan permintaan
- **Tetapan**: Konfigurasikan pengesahan, e-mel, sandaran, dan lain-lain
- **Fail**: Urus fail yang dimuat naik dan storan
- **Peraturan API**: Tetapkan kebenaran untuk setiap koleksi

**Nota Keselamatan**: Tetapkan `PB_HIDE_CONTROLS=true` dalam pengeluaran untuk menyembunyikan UI pentadbir dari akses awam.

## Pengurusan Pangkalan Data

### Menyandarkan Data Anda

Data anda berada dalam folder `pb_data`. Sandaran tetap adalah penting.

#### Sandaran Manual

```bash
# Buat sandaran folder pb_data keseluruhan
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### Sandaran Harian Automatik

Sediakan tugas cron untuk sandaran automatik:

```bash
# Tambah ke crontab (jalankan: crontab -e)
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

Ini berjalan pada pukul 2 pagi setiap hari dan mencipta sandaran bertanda masa.

#### Memulihkan dari Sandaran

```bash
# Hentikan aplikasi terlebih dahulu
# Kemudian ekstrak sandaran
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## Senarai Semak Keselamatan

Sebelum beralih ke pengeluaran:

- [ ] Tukar kata laluan pentadbir lalai segera selepas log masuk pertama
- [ ] Tetapkan `PB_ALLOW_ORIGINS` ke domain anda yang sebenar, bukan `*`
- [ ] Aktifkan HTTPS (jangan gunakan HTTP dalam pengeluaran)
- [ ] Tetapkan `PB_HIDE_CONTROLS=true` untuk menyembunyikan UI pentadbir dari akses tanpa kebenaran
- [ ] Pertimbangkan untuk mengaktifkan had kadar dengan `PB_ENABLE_RATE_LIMITING=true`
- [ ] Konfigurasikan Sentry (`SENTRY_DSN` dan `SENTRY_ENV`) untuk pemantauan ralat
- [ ] Sediakan sandaran harian automatik
- [ ] Gunakan kata laluan yang kuat dan unik untuk semua akaun
- [ ] Untuk Gmail SMTP, gunakan Kata Laluan Aplikasi dan bukan kata laluan biasa anda
- [ ] Pastikan dependensi Go dikemas kini secara tetap dengan `./scripts/update.sh`

## Mengemas Kini Bahagian Belakang

Untuk mengemas kini ke versi baru:

### Kemas Kini Docker

```bash
# Tarik kod terbaru
git pull origin main

# Bina semula imej Docker
docker build -t ministry-mapper-backend .

# Hentikan dan buang kontena lama
docker stop ministry-mapper
docker rm ministry-mapper

# Mulakan kontena baru (gunakan port 8080, bukan 8090)
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### Kemas Kini Pembangunan Tempatan

```bash
# Tarik kod terbaru
git pull origin main

# Kemas kini dependensi
./scripts/update.sh

# Mulakan semula aplikasi
./scripts/start.sh
```

**Nota**: Data anda dalam `pb_data` dipelihara semasa kemas kini.

## Migrasi Pangkalan Data

Bahagian belakang secara automatik menjalankan migrasi pangkalan data apabila bermula dalam mod pembangunan (apabila menggunakan `go run`). Anda akan melihat mesej dalam log seperti:

```
Running migrations...
Migration 1759244334_collections_snapshot completed
```

**Cara Ia Berfungsi:**

- Migrasi disimpan dalam folder `migrations/`
- PocketBase mengesan jika anda menjalankan melalui `go run` dan auto-migrasi
- Dalam pengeluaran (binari yang dikompil), anda perlu menjalankan migrasi secara manual menggunakan: `./main migrate`

**Penting:** Jangan padam atau ubah suai fail migrasi secara manual. Ini boleh merosakkan pangkalan data anda.

## Pemantauan dan Log

### Melihat Log

#### Log Docker

```bash
# Lihat log terkini
docker logs ministry-mapper

# Ikuti log dalam masa nyata (berguna untuk nyahpepijat)
docker logs -f ministry-mapper

# Lihat 100 baris terakhir
docker logs --tail 100 ministry-mapper
```

#### Log Aplikasi

PocketBase menyimpan log dalam folder `pb_data/logs/`. Ini termasuk:

- Log permintaan
- Log ralat
- Log aplikasi tersuai

### Apa Yang Perlu Dicari dalam Log

- **Mesej Permulaan**: Mengesahkan inisialisasi berjaya (cth., "Starting Ministry Mapper build...")
- **Mesej Migrasi**: Migrasi pangkalan data yang sedang diterapkan
- **Inisialisasi Sentry**: Persediaan penjejakan ralat
- **Kerja Cron**: Pelaksanaan tugas latar belakang (agregat wilayah, pemprosesan mesej, dll.)
- **Mesej Ralat**: Masalah dengan pangkalan data, e-mel, panggilan API, atau LaunchDarkly
- **Acara Pengesahan**: Percubaan log masuk dan aktiviti pengguna
- **Isu Prestasi**: Pertanyaan yang lambat atau tamat masa

## Penyelesaian Masalah

### Port Sudah Digunakan

**Masalah**: Ralat "Port already in use"

**Penyelesaian**:

```bash
# Untuk Docker (port 8080)
lsof -i :8080

# Untuk pembangunan tempatan (port 8090)
lsof -i :8090

# Matikan proses yang menggunakan port
kill -9 <process_id>

# Atau petakan ke port berbeza
docker run -p 8081:8080 ...  # Memetakan port hos 8081 ke port kontena 8080
```

### Pangkalan Data Dikunci

**Masalah**: Ralat "Database is locked"

**Penyelesaian**:
- Hentikan semua kontena/proses yang mengakses pangkalan data
- Semak jika proses sandaran sedang berjalan
- Pastikan hanya satu instance aplikasi berjalan
- SQLite tidak menyokong penulisan serentak dari berbilang proses
- Mulakan semula aplikasi

### E-mel Tidak Menghantar

**Masalah**: Pengguna tidak menerima e-mel

**Penyelesaian**:
1. **Sahkan Tetapan SMTP**: Semak semua pembolehubah `PB_SMTP_*` dalam `.env`
2. **Pengguna Gmail**: Gunakan [Kata Laluan Aplikasi](https://support.google.com/accounts/answer/185833), bukan kata laluan biasa anda
3. **Semak Folder Spam**: E-mel mungkin ditapis
4. **Uji Sambungan SMTP**: Gunakan alat seperti `telnet` atau klien e-mel untuk mengesahkan kelayakan SMTP
5. **Semak Log**: Cari mesej ralat dalam `pb_data/logs/` atau log Docker
6. **Isu Port**: Pastikan port 587 (TLS) atau 465 (SSL) tidak disekat oleh tembok api

### Kehabisan Memori

**Masalah**: Aplikasi ranap dengan ralat memori

**Penyelesaian**:
- Tingkatkan had memori Docker: `docker run -m 1g ...`
- Semak log untuk kebocoran memori
- Optimumkan pertanyaan pangkalan data yang besar
- Naik taraf peruntukan RAM pelan pengehosan anda
- Pertimbangkan pengoptimuman SQLite PRAGMA untuk set data yang besar

### Tidak Dapat Mengakses Panel Pentadbir

**Masalah**: Ralat 404 apabila mengakses URL pentadbir

**Penyelesaian**:
- Pastikan format URL: `http://url-anda/_/` (dengan garis bawah dan garis miring akhir)
- Semak jika `PB_HIDE_CONTROLS=true` (menghalang akses UI pentadbir)
- Sahkan aplikasi berjalan: `docker ps` atau semak proses
- Kosongkan cache dan kuki pelayar
- Semak log untuk ralat permulaan

## Petua Prestasi

### Jemaah Kecil (< 100 pengguna)
- Tetapan lalai berfungsi dengan baik
- 512MB RAM adalah mencukupi
- SQLite mengendalikan beban ini dengan mudah
- Tiada konfigurasi khas diperlukan

### Jemaah Sederhana (100-500 pengguna)
- Disyorkan: 1GB RAM
- Aktifkan had kadar: `PB_ENABLE_RATE_LIMITING=true`
- Sediakan sandaran harian automatik
- Pantau saiz pangkalan data dalam `pb_data/data.db`

### Jemaah Besar (> 500 pengguna)
- Disyorkan: 2GB+ RAM
- Aktifkan had kadar
- Pertimbangkan pengehosan khusus (bukan dikongsi)
- Pantau log untuk pertanyaan yang lambat
- Sediakan dasar pengekalan sandaran (simpan 30 hari terakhir)
- Pertimbangkan bendera ciri LaunchDarkly untuk mengawal kekerapan kerja latar belakang

## Titik Akhir API

Bahagian belakang menyediakan titik akhir API tersuai yang disahkan ini (semua memerlukan pengesahan pengguna):

### Pengurusan Peta
- `POST /map/codes` - Dapatkan semua kod peta untuk peta tertentu
- `POST /map/code/add` - Tambah kod alamat baru ke peta
- `POST /map/codes/update` - Kemas kini urutan/susunan kod peta
- `POST /map/code/delete` - Padam kod alamat dari peta
- `POST /map/add` - Cipta peta baru
- `POST /map/territory/update` - Kemas kini wilayah mana yang dimiliki peta
- `POST /map/reset` - Tetapkan semula peta (kosongkan semua tugasan dan data)

### Pengurusan Tingkat
- `POST /map/floor/add` - Tambah tingkat ke peta
- `POST /map/floor/remove` - Buang tingkat dari peta

### Pengurusan Wilayah
- `POST /territory/reset` - Tetapkan semula wilayah (kosongkan tugasan)
- `POST /territory/link` - Cipta pautan akses pantas untuk wilayah

### Konfigurasi
- `POST /options/update` - Kemas kini pilihan/tetapan jemaah

**Pengesahan**: Semua titik akhir memerlukan token pengesahan PocketBase yang sah dalam pengepala permintaan.

**API Terbina Dalam PocketBase**: Selain titik akhir tersuai, PocketBase menyediakan API REST automatik untuk semua koleksi (operasi CRUD) di `/api/collections/{collection}/records`.

## Kerja Latar Belakang (Penjadual Cron)

Bahagian belakang menjalankan tugas latar belakang automatik menggunakan penjadual cron. Kerja-kerja ini dikawal oleh bendera ciri LaunchDarkly:

### Kerja Berjadual

1. **Agregat Wilayah** (`*/10 * * * *` - setiap 10 minit)
   - Mengemas kini statistik wilayah dan data agregat
   - Bendera ciri: `enable-territory-aggregations`

2. **Pembersihan Tugasan** (`*/5 * * * *` - setiap 5 minit)
   - Membuang tugasan wilayah yang tamat tempoh atau tidak sah
   - Bendera ciri: `enable-assignments-cleanup`

3. **Pemprosesan Mesej** (`*/30 * * * *` - setiap 30 minit)
   - Memproses mesej yang belum diproses dalam baris gilir
   - Secara pilihan melampirkan gambaran keseluruhan yang dijana AI apabila `OPENAI_API_KEY` ditetapkan
   - Bendera ciri: `enable-message-processing`

4. **Pemprosesan Arahan** (`*/30 * * * *` - setiap 30 minit)
   - Mengendalikan arahan tugasan wilayah yang belum diproses
   - Bendera ciri: `enable-instruction-processing`

5. **Pemprosesan Nota** (`0 * * * *` - setiap jam)
   - Memproses nota yang dikemas kini untuk jemaah
   - Secara pilihan melampirkan digest yang dijana AI apabila `OPENAI_API_KEY` ditetapkan
   - Bendera ciri: `enable-note-processing`

6. **Laporan Bulanan** (`0 0 1 * *` - 1hb setiap bulan pada tengah malam)
   - Menjana laporan Excel data jemaah dan menghantarnya melalui e-mel kepada pentadbir
   - Bendera ciri: `enable-monthly-report`
   - Bendera ringkasan AI: `enable-report-ai-summary` (memerlukan `OPENAI_API_KEY`)

7. **Pemprosesan Pengguna Tidak Diperuntukkan** (`0 1 * * *` - harian pada 01:00 UTC)
   - Menguatkuasakan kitaran hayat pengguna untuk akaun tanpa tugasan peranan:
     hari ke-3 → e-mel amaran pertama, hari ke-6 → e-mel amaran terakhir, hari ke-7 → akaun dilumpuhkan, hari ke-37+ → pemadaman kekal
   - Bendera ciri: `enable-unprovisioned-user-processing`

8. **Pemprosesan Pengguna Tidak Aktif** (`30 1 * * *` - harian pada 01:30 UTC)
   - Memberi amaran dan akhirnya melumpuhkan akaun yang tidak aktif melebihi ambang yang dikonfigurasi
   - Bendera ciri: `enable-inactive-user-processing`

**Nota**: Jika LaunchDarkly tidak dikonfigurasi, semua kerja berjalan secara lalai (diaktifkan: true).

### Ringkasan Laporan AI

Apabila `OPENAI_API_KEY` dikonfigurasi **dan** bendera LaunchDarkly `enable-report-ai-summary` aktif, laporan Excel bulanan menyertakan ringkasan yang dijana AI untuk setiap helaian wilayah. AI menganalisis data aktiviti dan menghasilkan gambaran keseluruhan 2-3 ayat serta senarai pemerhatian utama.

Ciri ini adalah sepenuhnya pilihan — laporan berfungsi sepenuhnya tanpanya. Untuk mengaktifkannya:

1. Tetapkan `OPENAI_API_KEY` dalam fail `.env` anda.
2. Aktifkan bendera `enable-report-ai-summary` dalam projek LaunchDarkly anda (atau tinggalkan LaunchDarkly sepenuhnya dan bendera lalai kepada diaktifkan).

## Cangkuk Pangkalan Data

Aplikasi secara automatik mengendalikan acara tertentu:

- **Kemas Kini Alamat**: Apabila nota alamat berubah, cap masa dan maklumat pengguna direkodkan
- **Kemas Kini Alamat**: Mencetuskan pengiraan semula agregat peta
- **Pengesahan Pengguna**: Mengemas kini cap masa log masuk terakhir
- **Penciptaan Pengguna**: Menormalkan dan menurunkan huruf alamat e-mel

## Langkah Seterusnya

- Sediakan [Aplikasi Bahagian Hadapan](frontend-setup.md)
- Ketahui tentang [Pengurusan Pengguna](user-management.md)
- Konfigurasikan [Tetapan Keselamatan](security.md)
