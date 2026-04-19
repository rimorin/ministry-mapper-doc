# Panduan Pengaturan Backend

!!! warning "Self-Hosting Tidak Disarankan"
    Panduan ini untuk pengguna tingkat lanjut yang ingin melakukan self-hosting Ministry Mapper. Kami tidak mendorong self-hosting karena kompleksitas teknis, beban pemeliharaan, dan tanggung jawab keamanan yang terlibat.
    
    **Sebagian besar pengguna harus**: Gunakan layanan hosted kami di **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "Mencari Dokumentasi Pengguna?"
    Jika Anda adalah pengguna biasa yang mencoba mempelajari cara menggunakan Ministry Mapper, lihat [Memulai](getting-started.md) atau [Panduan Pengguna](user-guide.md) sebagai gantinya.

## Ikhtisar

Backend Ministry Mapper (ministry-mapper-be) dibangun dengan Go dan PocketBase. Backend ini menyediakan:

- Penyimpanan dan pengelolaan data (database SQLite)
- Autentikasi dan otorisasi pengguna
- Sinkronisasi data real-time
- Endpoint API kustom untuk manajemen wilayah
- Background jobs untuk tugas otomatis
- Notifikasi email melalui SMTP

**Panduan ini mengasumsikan Anda memiliki**:
- Pengalaman dengan administrasi server
- Pemahaman tentang Docker dan kontainerisasi
- Keakraban dengan variabel lingkungan
- Pengetahuan tentang praktik keamanan terbaik

Untuk instruksi self-hosting lengkap, lihat [Panduan Self-Hosting](self-hosting.md).

## Referensi Singkat

Halaman ini menyediakan referensi singkat untuk konfigurasi backend. **Untuk instruksi pengaturan lengkap, lihat [Panduan Self-Hosting](self-hosting.md).**

### Tumpukan Teknologi

- **Go**: Bahasa pemrograman yang cepat dan efisien
- **PocketBase**: Backend-as-a-service dengan panel admin bawaan
- **SQLite**: Database ringan di folder `pb_data`
- **Docker**: Container berbasis Alpine Linux

### Fitur Utama

- Penyimpanan dan pengelolaan data
- Autentikasi pengguna
- Langganan real-time (Server-Sent Events)
- Endpoint API kustom
- Background jobs terjadwal
- Notifikasi email (SMTP)
- Pelacakan error (Sentry)
- Feature flags (LaunchDarkly)

---

!!! note "Instruksi Pengaturan Lengkap"
    Bagian di bawah ini menyediakan referensi teknis untuk konfigurasi backend. Untuk instruksi pengaturan langkah demi langkah dengan konteks dan penjelasan, silakan lihat **[Panduan Self-Hosting](self-hosting.md)**.

---

## Penjelasan Variabel Lingkungan

### Kunci Integrasi Layanan (Opsional)

```bash
# Kunci API MailerSend - untuk mengirim email melalui layanan MailerSend
# Biarkan kosong jika menggunakan SMTP sebagai gantinya
MAILERSEND_API_KEY=

# Alamat email untuk pengirim MailerSend
MAILERSEND_FROM_EMAIL=

# Kunci SDK LaunchDarkly - untuk feature flags mengontrol background jobs
# Job berjalan secara default jika tidak dikonfigurasi
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# Kunci API OpenAI - untuk ringkasan bertenaga AI dalam laporan bulanan dan digest pesan
# Opsional: laporan dan digest berfungsi baik tanpa ini; bagian AI cukup dihilangkan
OPENAI_API_KEY=
```

#### Email (MailerSend)

| Variabel | Wajib | Deskripsi |
|----------|-------|-----------|
| `MAILERSEND_API_KEY` | Opsional | Kunci API MailerSend untuk pengiriman email transaksional (digunakan untuk laporan dan email siklus hidup). Jika tidak diatur, akan menggunakan SMTP sebagai fallback. |
| `MAILERSEND_FROM_EMAIL` | Opsional | Alamat email pengirim untuk email MailerSend. |

#### Feature Flags (LaunchDarkly)

| Variabel | Wajib | Deskripsi |
|----------|-------|-----------|
| `LAUNCHDARKLY_SDK_KEY` | Opsional | Kunci SDK sisi server LaunchDarkly. Diperlukan jika menggunakan background job yang dikontrol feature flag. |
| `LAUNCHDARKLY_CONTEXT_KEY` | Opsional | Kunci konteks LaunchDarkly yang mengidentifikasi lingkungan deployment ini. |

#### Ringkasan AI (OpenAI)

| Variabel | Wajib | Deskripsi |
|----------|-------|-----------|
| `OPENAI_API_KEY` | Opsional | Kunci API OpenAI. Diperlukan hanya jika ringkasan laporan yang dihasilkan AI diaktifkan melalui feature flag. |

### Pengaturan Aplikasi

```bash
# Nama yang ditampilkan di email dan panel admin
PB_APP_NAME=Ministry Mapper

# URL tempat frontend Anda di-hosting
PB_APP_URL=http://localhost:3000
```

### Akun Admin Default

```bash
# Akun admin awal yang dibuat selama pengaturan pertama
# Ganti kata sandi segera setelah login pertama!
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### Konfigurasi Email (SMTP)

```bash
# Alamat server SMTP (mis., smtp.gmail.com, smtp.sendgrid.net)
PB_SMTP_HOST=

# Kata sandi SMTP (gunakan App Password untuk Gmail, bukan kata sandi biasa Anda)
PB_SMTP_PASSWORD=

# Username SMTP (biasanya alamat email Anda)
PB_SMTP_USERNAME=

# Port SMTP (587 untuk TLS, 465 untuk SSL)
PB_SMTP_PORT=587

# Alamat email yang ditampilkan di field "From"
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# Nama yang ditampilkan di field "From"
PB_SMTP_SENDER_NAME=MM Support
```

### Keamanan & Kontrol Akses

```bash
# Daftar origin yang diizinkan untuk CORS, dipisahkan koma
# Gunakan * untuk pengembangan, domain tertentu untuk produksi
# Contoh: https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# Sembunyikan UI admin dari akses publik (true/false)
# Atur ke true di produksi untuk keamanan
PB_HIDE_CONTROLS=false
```

### Pengaturan Autentikasi

```bash
# Aktifkan login one-time password (true/false)
PB_OTP_ENABLED=false

# Aktifkan autentikasi multi-faktor (true/false)
PB_MFA_ENABLED=false
```

### Pembatasan Rate

```bash
# Aktifkan pembatasan rate untuk mencegah penyalahgunaan API (true/false)
PB_ENABLE_RATE_LIMITING=false
```

### Pelacakan Error (Tidak ada di .env.sample - tambahkan secara manual jika diperlukan)

```bash
# Sentry DSN untuk pemantauan dan pelacakan error
SENTRY_DSN=

# Nama environment untuk Sentry (development, staging, production)
SENTRY_ENV=production

# Hash commit git - secara otomatis diatur oleh platform hosting seperti Coolify
SOURCE_COMMIT=
```

## Feature Flags

Ministry Mapper menggunakan [LaunchDarkly](https://launchdarkly.com) untuk mengontrol eksekusi background job. Setiap job dapat diaktifkan atau dinonaktifkan secara independen tanpa perlu deployment ulang. Feature flags memerlukan `LAUNCHDARKLY_SDK_KEY` dan `LAUNCHDARKLY_CONTEXT_KEY` yang dikonfigurasi.

| Kunci Flag | Job yang Dikontrol | Deskripsi |
|------------|-------------------|-----------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | Hapus penugasan peta yang kedaluwarsa (berjalan setiap 5 menit) |
| `enable-territory-aggregations` | `updateTerritoryAggregates` | Kalkulasi ulang statistik kemajuan wilayah (berjalan setiap 10 menit) |
| `enable-message-processing` | `processMessages` | Proses pesan penerbit yang tertunda (berjalan setiap 30 menit) |
| `enable-instruction-processing` | `processInstructions` | Proses instruksi penugasan wilayah (berjalan setiap 30 menit) |
| `enable-note-processing` | `processNotes` | Proses catatan jemaat yang diperbarui (berjalan setiap jam) |
| `enable-monthly-report` | `generateMonthlyReport` | Buat dan kirim laporan Excel via email (berjalan tanggal 1 setiap bulan) |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | Peringatkan dan nonaktifkan pengguna tanpa peran yang ditetapkan (berjalan harian) |
| `enable-inactive-user-processing` | `processInactiveUsers` | Peringatkan dan nonaktifkan akun tidak aktif — NIST AC-2(3) (berjalan harian) |
| `enable-new-addresses-notification` | `processNewAddresses` | Kirim email digest harian untuk alamat yang dibuat dari aplikasi (berjalan harian) |
| `enable-report-ai-summary` | Ringkasan AI dalam laporan | Sertakan ringkasan yang dihasilkan OpenAI dalam laporan jemaat |

!!! note
    Jika LaunchDarkly tidak dikonfigurasi, semua job berjalan secara default (enabled: true).

## Langkah Instalasi

### Opsi 1: Deployment Docker (Disarankan)

Docker membuat deployment sederhana dan konsisten di berbagai platform.

#### 1. Build Docker Image

```bash
docker build -t ministry-mapper-backend .
```

#### 2. Jalankan Container

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Catatan Penting:**

- Container berjalan di port **8080** (bukan 8090)
- `-p 8080:8080` memetakan port 8080 container ke port host 8080
- `-v /path/to/pb_data:/app/pb_data` menyimpan database Anda secara permanen (ganti `/path/to/pb_data` dengan path nyata di sistem Anda)
- `--env-file .env` memuat variabel lingkungan dari file `.env`
- `-d` menjalankan container di latar belakang (detached mode)

### Opsi 2: Pengembangan Lokal

Untuk pengembangan dan pengujian di komputer Anda:

#### 1. Install Dependensi Go

```bash
./scripts/install.sh
```

Script ini menjalankan `go get ./...` dan `go mod tidy` untuk menginstal semua dependensi.

#### 2. Siapkan Variabel Lingkungan

```bash
cp .env.sample .env
# Edit .env dengan pengaturan Anda
```

#### 3. Jalankan Aplikasi

```bash
./scripts/start.sh
```

Script ini mengekspor variabel lingkungan dari `.env` dan menjalankan `go run main.go serve`.

Backend akan mulai di **http://localhost:8090** secara default (port default PocketBase).

## Mengakses Panel Admin

Setelah backend Anda berjalan:

1. Navigasi ke `http://your-backend-url/_/` (perhatikan garis bawah dan trailing slash)
2. Login dengan kredensial admin Anda dari `PB_ADMIN_EMAIL` dan `PB_ADMIN_PASSWORD`
3. Anda akan melihat dashboard admin PocketBase

### Yang Dapat Anda Lakukan di Panel Admin

- **Collections**: Lihat dan edit semua tabel database (users, congregations, territories, maps, addresses, dll.)
- **Logs**: Lihat log aplikasi dan permintaan
- **Settings**: Konfigurasi autentikasi, email, pencadangan, dan lainnya
- **Files**: Kelola file yang diunggah dan penyimpanan
- **API Rules**: Atur izin untuk setiap koleksi

**Catatan Keamanan**: Atur `PB_HIDE_CONTROLS=true` di produksi untuk menyembunyikan UI admin dari akses publik.

## Manajemen Database

### Mencadangkan Data Anda

Data Anda berada di folder `pb_data`. Pencadangan rutin sangat penting.

#### Backup Manual

```bash
# Buat backup dari seluruh folder pb_data
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### Backup Otomatis Harian

Siapkan cron job untuk mencadangkan secara otomatis:

```bash
# Tambahkan ke crontab (jalankan: crontab -e)
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

Ini berjalan setiap hari pukul 2 pagi dan membuat backup dengan timestamp.

#### Memulihkan dari Backup

```bash
# Hentikan aplikasi terlebih dahulu
# Kemudian ekstrak backup
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## Daftar Periksa Keamanan

Sebelum masuk ke produksi:

- [ ] Ganti kata sandi admin default segera setelah login pertama
- [ ] Atur `PB_ALLOW_ORIGINS` ke domain Anda yang sebenarnya, bukan `*`
- [ ] Aktifkan HTTPS (jangan pernah gunakan HTTP di produksi)
- [ ] Atur `PB_HIDE_CONTROLS=true` untuk menyembunyikan UI admin dari akses tidak sah
- [ ] Pertimbangkan mengaktifkan pembatasan rate dengan `PB_ENABLE_RATE_LIMITING=true`
- [ ] Konfigurasi Sentry (`SENTRY_DSN` dan `SENTRY_ENV`) untuk pemantauan error
- [ ] Siapkan backup harian otomatis
- [ ] Gunakan kata sandi yang kuat dan unik untuk semua akun
- [ ] Untuk Gmail SMTP, gunakan App Passwords alih-alih kata sandi biasa Anda
- [ ] Perbarui dependensi Go secara rutin dengan `./scripts/update.sh`

## Memperbarui Backend

Untuk memperbarui ke versi baru:

### Pembaruan Docker

```bash
# Tarik kode terbaru
git pull origin main

# Rebuild Docker image
docker build -t ministry-mapper-backend .

# Hentikan dan hapus container lama
docker stop ministry-mapper
docker rm ministry-mapper

# Mulai container baru (gunakan port 8080, bukan 8090)
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### Pembaruan Pengembangan Lokal

```bash
# Tarik kode terbaru
git pull origin main

# Perbarui dependensi
./scripts/update.sh

# Restart aplikasi
./scripts/start.sh
```

**Catatan**: Data Anda di `pb_data` dipertahankan selama pembaruan.

## Migrasi Database

Backend secara otomatis menjalankan migrasi database saat memulai dalam mode pengembangan (saat menggunakan `go run`). Anda akan melihat pesan di log seperti:

```
Running migrations...
Migration 1759244334_collections_snapshot completed
```

**Cara Kerjanya:**

- Migrasi disimpan di folder `migrations/`
- PocketBase mendeteksi jika Anda menjalankan melalui `go run` dan auto-migrasi
- Dalam produksi (binary yang dikompilasi), Anda perlu menjalankan migrasi secara manual menggunakan: `./main migrate`

**Penting:** Jangan pernah menghapus atau memodifikasi file migrasi secara manual. Ini dapat merusak database Anda.

## Pemantauan dan Log

### Melihat Log

#### Log Docker

```bash
# Lihat log terbaru
docker logs ministry-mapper

# Ikuti log secara real-time (berguna untuk debugging)
docker logs -f ministry-mapper

# Lihat 100 baris terakhir
docker logs --tail 100 ministry-mapper
```

#### Log Aplikasi

PocketBase menyimpan log di folder `pb_data/logs/`. Ini mencakup:

- Log permintaan
- Log error
- Log aplikasi kustom

### Yang Perlu Diperhatikan dalam Log

- **Pesan Startup**: Mengonfirmasi inisialisasi yang berhasil (mis., "Starting Ministry Mapper build...")
- **Pesan Migrasi**: Migrasi database yang sedang diterapkan
- **Inisialisasi Sentry**: Pengaturan pelacakan error
- **Cron Jobs**: Eksekusi tugas latar belakang (agregat wilayah, pemrosesan pesan, dll.)
- **Pesan Error**: Masalah dengan database, email, panggilan API, atau LaunchDarkly
- **Event Autentikasi**: Percobaan login dan aktivitas pengguna
- **Masalah Kinerja**: Kueri lambat atau timeout

## Pemecahan Masalah

### Port Sudah Digunakan

**Masalah**: Error "Port already in use"

**Solusi**:

```bash
# Untuk Docker (port 8080)
lsof -i :8080

# Untuk pengembangan lokal (port 8090)
lsof -i :8090

# Matikan proses yang menggunakan port
kill -9 <process_id>

# Atau peta ke port yang berbeda
docker run -p 8081:8080 ...  # Memetakan port host 8081 ke port container 8080
```

```

### Database Terkunci

**Masalah**: Error "Database is locked"

**Solusi**:
- Hentikan semua container/proses yang mengakses database
- Periksa apakah proses backup sedang berjalan
- Pastikan hanya satu instance aplikasi yang berjalan
- SQLite tidak mendukung penulisan bersamaan dari beberapa proses
- Restart aplikasi

### Email Tidak Terkirim

**Masalah**: Pengguna tidak menerima email

**Solusi**:
1. **Verifikasi Pengaturan SMTP**: Periksa semua variabel `PB_SMTP_*` di `.env`
2. **Pengguna Gmail**: Gunakan [App Password](https://support.google.com/accounts/answer/185833), bukan kata sandi biasa Anda
3. **Periksa Folder Spam**: Email mungkin difilter
4. **Uji Koneksi SMTP**: Gunakan alat seperti `telnet` atau klien email untuk memverifikasi kredensial SMTP
5. **Tinjau Log**: Cari pesan error di `pb_data/logs/` atau log Docker
6. **Masalah Port**: Pastikan port 587 (TLS) atau 465 (SSL) tidak diblokir oleh firewall

### Kehabisan Memori

**Masalah**: Aplikasi crash dengan error memori

**Solusi**:
- Tingkatkan batas memori Docker: `docker run -m 1g ...`
- Periksa log untuk kebocoran memori
- Optimalkan kueri database yang besar
- Upgrade alokasi RAM paket hosting Anda
- Pertimbangkan menggunakan optimasi SQLite PRAGMA untuk dataset besar

### Tidak Dapat Mengakses Panel Admin

**Masalah**: Error 404 saat mengakses URL admin

**Solusi**:
- Pastikan format URL: `http://your-url/_/` (dengan garis bawah dan trailing slash)
- Periksa apakah `PB_HIDE_CONTROLS=true` (mencegah akses UI admin)
- Verifikasi aplikasi berjalan: `docker ps` atau periksa proses
- Bersihkan cache dan cookie browser
- Periksa log untuk error startup

## Tips Kinerja

### Jemaat Kecil (< 100 pengguna)
- Pengaturan default bekerja dengan baik
- 512MB RAM sudah cukup
- SQLite menangani beban ini dengan mudah
- Tidak diperlukan konfigurasi khusus

### Jemaat Menengah (100-500 pengguna)
- Disarankan: 1GB RAM
- Aktifkan pembatasan rate: `PB_ENABLE_RATE_LIMITING=true`
- Siapkan backup otomatis harian
- Monitor ukuran database di `pb_data/data.db`

### Jemaat Besar (> 500 pengguna)
- Disarankan: 2GB+ RAM
- Aktifkan pembatasan rate
- Pertimbangkan hosting khusus (bukan berbagi)
- Monitor log untuk kueri lambat
- Siapkan kebijakan retensi backup (simpan 30 hari terakhir)
- Pertimbangkan feature flags LaunchDarkly untuk mengontrol frekuensi background job

## Endpoint API

Backend menyediakan endpoint API yang diautentikasi kustom ini (semua memerlukan autentikasi pengguna):

### Manajemen Peta
- `POST /map/codes` - Dapatkan semua kode peta untuk peta tertentu
- `POST /map/code/add` - Tambahkan kode alamat baru ke peta
- `POST /map/codes/update` - Perbarui urutan/urutan kode peta
- `POST /map/code/delete` - Hapus kode alamat dari peta
- `POST /map/add` - Buat peta baru
- `POST /map/territory/update` - Perbarui wilayah mana yang menjadi milik peta
- `POST /map/reset` - Reset peta (bersihkan semua penugasan dan data)

### Manajemen Lantai
- `POST /map/floor/add` - Tambahkan level lantai ke peta
- `POST /map/floor/remove` - Hapus lantai dari peta

### Manajemen Wilayah
- `POST /territory/reset` - Reset wilayah (bersihkan penugasan)
- `POST /territory/link` - Buat tautan akses cepat untuk wilayah

### Konfigurasi
- `POST /options/update` - Perbarui opsi/pengaturan jemaat

**Autentikasi**: Semua endpoint memerlukan token autentikasi PocketBase yang valid di header permintaan.

**API Bawaan PocketBase**: Selain endpoint kustom, PocketBase menyediakan API REST otomatis untuk semua koleksi (operasi CRUD) di `/api/collections/{collection}/records`.

## Background Jobs (Cron Scheduler)

Backend menjalankan tugas latar belakang otomatis menggunakan cron scheduler. Job-job ini dikontrol oleh feature flags LaunchDarkly:

### Job Terjadwal

1. **Territory Aggregates** (`*/10 * * * *` - setiap 10 menit)
   - Memperbarui statistik wilayah dan data agregat
   - Feature flag: `enable-territory-aggregations`

2. **Assignment Cleanup** (`*/5 * * * *` - setiap 5 menit)
   - Menghapus penugasan wilayah yang kedaluwarsa atau tidak valid
   - Feature flag: `enable-assignments-cleanup`

3. **Message Processing** (`*/30 * * * *` - setiap 30 menit)
   - Memproses pesan yang tertunda dalam antrian
   - Opsional melampirkan ikhtisar yang dihasilkan AI saat `OPENAI_API_KEY` diatur
   - Feature flag: `enable-message-processing`

4. **Instruction Processing** (`*/30 * * * *` - setiap 30 menit)
   - Menangani instruksi penugasan wilayah yang tertunda
   - Feature flag: `enable-instruction-processing`

5. **Note Processing** (`0 * * * *` - setiap jam)
   - Memproses catatan yang diperbarui untuk jemaat
   - Opsional melampirkan digest yang dihasilkan AI saat `OPENAI_API_KEY` diatur
   - Feature flag: `enable-note-processing`

6. **Monthly Report** (`0 0 1 * *` - tanggal 1 setiap bulan pukul 00:00)
   - Menghasilkan laporan Excel data jemaat dan mengirimkannya ke administrator melalui email
   - Feature flag: `enable-monthly-report`
   - Flag ringkasan AI: `enable-report-ai-summary` (memerlukan `OPENAI_API_KEY`)

7. **Unprovisioned User Processing** (`0 1 * * *` - harian pukul 01:00 UTC)
   - Menerapkan siklus hidup pengguna untuk akun tanpa penugasan peran:
     hari ke-3 → email peringatan pertama, hari ke-6 → email peringatan terakhir, hari ke-7 → akun dinonaktifkan, hari ke-37+ → penghapusan permanen
   - Feature flag: `enable-unprovisioned-user-processing`

8. **Inactive User Processing** (`30 1 * * *` - harian pukul 01:30 UTC)
   - Memperingatkan dan akhirnya menonaktifkan akun yang tidak aktif melampaui ambang yang dikonfigurasi
   - Feature flag: `enable-inactive-user-processing`

**Catatan**: Jika LaunchDarkly tidak dikonfigurasi, semua job berjalan secara default (enabled: true).

### Ringkasan Laporan AI

Ketika `OPENAI_API_KEY` dikonfigurasi **dan** flag LaunchDarkly `enable-report-ai-summary` aktif, laporan Excel bulanan mencakup ringkasan yang dihasilkan AI untuk setiap sheet wilayah. AI menganalisis data aktivitas dan menghasilkan ikhtisar 2-3 kalimat beserta daftar pengamatan utama.

Fitur ini sepenuhnya opsional — laporan berfungsi penuh tanpanya. Untuk mengaktifkannya:

1. Atur `OPENAI_API_KEY` di file `.env` Anda.
2. Aktifkan flag `enable-report-ai-summary` di proyek LaunchDarkly Anda (atau lewati LaunchDarkly sepenuhnya dan flag akan default ke diaktifkan).

## Hooks Database

Aplikasi secara otomatis menangani event tertentu:

- **Pembaruan Alamat**: Ketika catatan alamat berubah, timestamp dan info pengguna dicatat
- **Pembaruan Alamat**: Memicu kalkulasi ulang agregat peta
- **Autentikasi Pengguna**: Memperbarui timestamp login terakhir
- **Pembuatan Pengguna**: Menormalkan dan mengecilkan huruf alamat email

## Langkah Selanjutnya

- Siapkan [Aplikasi Frontend](frontend-setup.md)
- Pelajari tentang [Manajemen Pengguna](user-management.md)
- Konfigurasi [Pengaturan Keamanan](security.md)
