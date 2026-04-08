# Model Data & Skema

## Ikhtisar

Ministry Mapper menggunakan SQLite melalui PocketBase dengan model data hierarkis yang dirancang untuk manajemen jemaat multi-tenant. Dokumen ini menyediakan dokumentasi komprehensif dari semua model data, hubungan, dan aturan bisnis.

## Diagram Entity Relationship

```
Congregation (Entitas root - isolasi multi-tenant)
    │
    ├─── Territory (1:M)
    │      │
    │      └─── Map (1:M)
    │             │
    │             ├─── Address (1:M)
    │             ├─── Assignment (1:M)
    │             └─── Message (1:M)
    │
    ├─── Option (1:M)
    │      └─── Address (M:M via JSON array)
    │
    ├─── User (1:M via Role)
    │      │
    │      ├─── Role (1:M)
    │      └─── Assignment (1:M)
    │
    └─── Message (1:M)
```

## Koleksi Inti

### 1. Congregations

**Tujuan:** Entitas sentral untuk isolasi multi-tenant dan pengaturan organisasi

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `code` | text | Ya | Kode organisasi |
| `name` | text | Ya | Nama organisasi |
| `expiry_hours` | number | Ya | Durasi tautan penugasan (jam) |
| `max_tries` | number | Ya | Maksimal percobaan "tidak ada di rumah" sebelum ditandai selesai |
| `origin` | select | Ya | Negara/wilayah (sg, my) |
| `timezone` | select | Ya | Zona waktu untuk kalkulasi tanggal |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Aturan Akses:**
- **Daftar:** Pengguna harus memiliki peran di jemaat
- **Lihat:** Pengguna dengan peran ATAU akses berbasis tautan
- **Perbarui:** Hanya Administrator
- **Hapus:** Hanya Administrator

**Aturan Bisnis:**
- Mengontrol durasi penugasan default untuk semua peta
- `max_tries` menentukan kapan alamat "tidak ada di rumah" dihitung sebagai selesai
- Zona waktu mempengaruhi kalkulasi berbasis tanggal dan laporan

**Contoh:**
```json
{
  "id": "abc123",
  "code": "CONG001",
  "name": "North Congregation",
  "expiry_hours": 24,
  "max_tries": 3,
  "origin": "sg",
  "timezone": "Asia/Singapore"
}
```

---

### 2. Territories

**Tujuan:** Pembagian geografis dalam jemaat untuk mengorganisir pelayanan lapangan

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `code` | text | Ya | Pengenal wilayah (auto: "0") |
| `description` | text | Tidak | Deskripsi yang dapat dibaca manusia |
| `progress` | number | Auto | Persentase penyelesaian (0-100) |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Indeks:**
- `(congregation, code)` - Pencarian cepat berdasarkan jemaat
- `(congregation)` - Filter jemaat

**Hubungan:**
- 1:M dengan Maps
- 1:M dengan Addresses (denormalized untuk kinerja)

**Aturan Akses:**
- **Daftar:** Pengguna dengan peran di jemaat
- **Lihat:** Pengguna dengan peran ATAU akses tautan
- **Buat/Perbarui/Hapus:** Konduktor atau Administrator

**Kalkulasi Kemajuan:**
Secara otomatis dikalkulasikan dari agregat semua peta dalam wilayah

**Contoh:**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "Downtown District",
  "progress": 65.5
}
```

---

### 3. Maps

**Tujuan:** Merepresentasikan lokasi atau bangunan tertentu yang berisi alamat

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `territory` | relation | Ya | FK ke Territories (cascade delete) |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `sequence` | number | Auto | Urutan dalam wilayah (auto-increment) |
| `code` | text | Ya | Pengenal/nomor peta (auto: "0") |
| `description` | text | Tidak | Deskripsi lokasi |
| `type` | select | Ya | `single` atau `multi` (tipe lantai) |
| `coordinates` | JSON | Tidak | Lokasi geografis `{lat, lng}` |
| `aggregates` | JSON | Auto | Statistik cache (maks 2MB) |
| `progress` | number | Auto | Persentase penyelesaian |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Indeks:**
- `(territory, code)` - Pencarian utama
- `(territory, sequence)` - Pengurutan
- `(territory)` - Filter wilayah

**Cascade Deletes:**
- Addresses
- Assignments
- Messages

**Struktur Aggregates:**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**Tipe Peta:**
- **single**: Alamat pribadi (rumah)
- **multi**: Alamat publik (bangunan multi-lantai)

**Aturan Akses:**
- **Daftar:** Pengguna dengan peran ATAU akses tautan
- **Lihat:** Pengguna dengan peran ATAU akses tautan
- **Buat/Perbarui:** Konduktor atau Administrator
- **Hapus:** Hanya Administrator

**Contoh:**
```json
{
  "id": "map_xyz",
  "territory": "terr_abc",
  "congregation": "cong_123",
  "sequence": 5,
  "code": "12",
  "description": "Main Street Apartments",
  "type": "multi",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "aggregates": {
    "done": 20,
    "not_done": 10,
    "dnc": 2,
    "invalid": 1,
    "not_home_max_tries": 3
  },
  "progress": 66.7
}
```

---

### 4. Addresses

**Tujuan:** Unit atau rumah tangga individual dalam peta

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `map` | relation | Ya | FK ke Maps (cascade delete) |
| `congregation` | relation | Ya | FK ke Congregations (denormalized) |
| `territory` | relation | Ya | FK ke Territories (denormalized) |
| `floor` | number | Ya | Nomor lantai (berbasis 0) |
| `code` | text | Ya | Kode unit/pintu (mis., "101", "A") |
| `sequence` | number | Ya | Urutan dalam peta |
| `status` | select | Ya | Status kunjungan alamat |
| `not_home_tries` | number | Auto | Counter untuk percobaan "tidak ada di rumah" |
| `type` | relation | Tidak | M:M ke Options (JSON array) |
| `notes` | text | Tidak | Catatan pekerja lapangan |
| `last_notes_updated` | date | Auto | Saat catatan terakhir diubah (via hook) |
| `last_notes_updated_by` | text | Auto | Username yang terakhir memperbarui (via hook) |
| `dnc_time` | date | Tidak | Saat ditandai "jangan hubungi" |
| `coordinates` | JSON | Tidak | GPS `{lat, lng}` yang detail |
| `updated_by` | text | Tidak | Lacak siapa yang melakukan pembaruan |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Nilai Status:**
- `not_done` - Belum dikunjungi (status awal)
- `done` - Berhasil diselesaikan
- `not_home` - Tidak ada orang di rumah (bisa dicoba ulang)
- `do_not_call` - Dikecualikan dari pekerjaan
- `invalid` - Alamat tidak ada

**Indeks (7 total):**
- `(map)` - Filter peta
- `(map, status)` - Kueri distribusi status
- `(map, code)` - Pencarian kode
- `(map, floor)` - Organisasi lantai
- `(type, congregation)` - Distribusi opsi
- `(congregation, last_notes_updated_by)` - Catatan terbaru oleh pengguna
- `(territory, status)` - Status seluruh wilayah

**Auto Hooks:**
- Pembaruan memicu field `last_notes_updated` dan `last_notes_updated_by`
- Memicu kalkulasi ulang agregat pada peta
- Memperbarui kemajuan wilayah

**Aturan Akses:**
- **Daftar:** Pengguna dengan peran ATAU akses tautan
- **Lihat:** Pengguna dengan peran ATAU akses tautan
- **Buat:** Konduktor atau Administrator
- **Perbarui:** Pengguna dengan peran ATAU akses tautan (penerbit dapat memperbarui)
- **Hapus:** Hanya Administrator

**Contoh:**
```json
{
  "id": "addr_123",
  "map": "map_xyz",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "floor": 2,
  "code": "201",
  "sequence": 5,
  "status": "not_home",
  "not_home_tries": 2,
  "type": ["opt_residential"],
  "notes": "Coba setelah pukul 18:00",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. Users

**Tujuan:** Akun pengguna sistem dengan autentikasi

**Tipe:** Koleksi autentikasi (tipe user-auth PocketBase)

**Field Sistem:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `email` | email | Ya | Identitas login (unik) |
| `emailVisibility` | bool | Ya | Apakah email bersifat publik |
| `verified` | bool | Auto | Status verifikasi email |
| `password` | password | Ya | Hash Bcrypt (cost: 10, min 6 karakter) |
| `tokenKey` | text | Tersembunyi | Kunci pembuatan token (30-60 karakter) |

**Field Kustom:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `name` | text | Ya | Nama tampilan (2-50 karakter) |
| `disabled` | bool | Tidak | Flag akun dinonaktifkan |
| `last_login` | date | Auto | Timestamp autentikasi terakhir (via hook) |

**Pola Validasi Nama:**
`^[A-Za-z][\w\s\.\-']*$` (dimulai dengan huruf, memungkinkan karakter kata, spasi, titik, tanda hubung, apostrof)

**Pola Autogen:**
`user[0-9]{5}[A-Za-z]` (mis., user12345A)

**Indeks:**
- `email` (unik) - Pencarian autentikasi yang cepat
- `tokenKey` (unik) - Validasi token

**Konfigurasi Autentikasi:**
- **Auth Kata Sandi:** Diaktifkan (min 6 karakter)
- **OAuth2:** Alur PKCE Google
  - Memetakan `name` dari penyedia OAuth
  - Auto-membuat pengguna pada login pertama
- **MFA:** Diaktifkan (durasi: 1800 detik) - Dinonaktifkan untuk OAuth2
- **OTP:** Diaktifkan (durasi: 180 detik, panjang: 4 digit)
- **Verifikasi Email:** Durasi 604800 detik (7 hari)
- **Reset Kata Sandi:** Durasi 1800 detik (30 menit)
- **Alert Autentikasi:** Diaktifkan (memberi tahu saat login dari lokasi baru)

**Aturan Akses:**
- **Daftar:** Hanya Administrator
- **Lihat:** Administrator atau diri sendiri
- **Buat:** Publik (OAuth atau registrasi eksplisit)
- **Perbarui:** Hanya diri sendiri
- **Hapus:** Hanya Administrator

**Token File:** Durasi 120 detik

**Hubungan:**
- 1:M dengan Roles
- 1:M dengan Assignments

**Contoh:**
```json
{
  "id": "user_abc",
  "email": "john.doe@example.com",
  "name": "John Doe",
  "verified": true,
  "disabled": false,
  "last_login": "2025-12-19T14:30:00Z"
}
```

---

### 6. Roles

**Tujuan:** Penugasan izin yang menghubungkan pengguna ke jemaat

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `user` | relation | Ya | FK ke Users (cascade delete) |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `role` | select | Ya | Level izin |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Nilai Peran:**
- `read_only` - Hanya akses lihat
- `conductor` - Dapat mengelola penugasan dan peta
- `administrator` - Akses penuh

**Indeks:**
- `(user, congregation)` - Kunci komposit (satu peran per pengguna per jemaat)
- `(user)` - Peran pengguna
- `(congregation, role)` - Distribusi peran
- `(user, congregation, role)` - Pencarian komposit penuh

**Aturan Akses:**
- **Buat/Perbarui/Hapus:** Hanya Administrator
- **Daftar:** Peran pengguna sendiri
- **Lihat:** Peran pengguna sendiri

**Hubungan:**
- M:N antara Users dan Congregations

**Aturan Bisnis:**
- Satu peran per pengguna per jemaat
- Peran menentukan izin untuk semua aksi

**Contoh:**
```json
{
  "id": "role_xyz",
  "user": "user_abc",
  "congregation": "cong_123",
  "role": "conductor"
}
```

---

### 7. Assignments

**Tujuan:** Token akses penerbit untuk akses peta terbatas waktu

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(20) | Ya | **Berfungsi sebagai token akses anonim** |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `map` | relation | Ya | FK ke Maps (cascade delete) |
| `user` | relation | Tidak | Pengguna penerbit (opsional) |
| `type` | select | Ya | Klasifikasi penugasan |
| `expiry_date` | date | Ya | Timestamp kedaluwarsa otomatis |
| `publisher` | text | Tidak | Nama/pengenal penerbit |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Tipe Penugasan:**
- `normal` - Pekerjaan wilayah biasa
- `personal` - Wilayah pelayanan pribadi

**Indeks:**
- `(map)` - Penugasan aktif per peta
- `(expiry_date)` - Untuk job cleanup latar belakang
- `(user, created)` - Riwayat penugasan pengguna
- `(map, expiry_date)` - Pemeriksaan kedaluwarsa per peta

**Aturan Akses:**
- **Buat/Hapus:** Konduktor atau Administrator
- **Lihat:** Pengguna dengan peran ATAU link_id yang cocok dengan ID ini + belum kedaluwarsa
- **Daftar:** Pengguna dengan peran

**Alur Kerja:**
1. Konduktor membuat melalui endpoint `/territory/link`
2. Penerbit menerima `link_id` 20 karakter (ID penugasan ini)
3. Penerbit meneruskan `link_id` melalui HTTP header `@request.headers.link_id`
4. PocketBase memvalidasi: link_id cocok dengan assignment.id DAN expiry_date > sekarang
5. Background job menghapus penugasan yang kedaluwarsa setiap 5 menit

**Contoh:**
```json
{
  "id": "asgn_1234567890abcdef",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2025-12-20T14:30:00Z",
  "publisher": "John Doe"
}
```

---

### 8. Options

**Tujuan:** Klasifikasi tipe alamat (dapat dikustomisasi per jemaat)

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `code` | text | Ya | Pengenal opsi (mis., "RES", "BUS") |
| `description` | text | Ya | Label yang dapat dibaca manusia |
| `is_countable` | bool | Ya | Sertakan dalam kalkulasi kemajuan |
| `is_default` | bool | Ya | Opsi default untuk alamat baru |
| `sequence` | number | Ya | Urutan tampilan/sortir |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Indeks:**
- `(congregation, sequence)` - Pengurutan dalam jemaat
- `(congregation, is_default)` - Temukan opsi default

**Batasan:**
- Tepat satu `is_default = true` per jemaat (diterapkan di layer aplikasi)
- Kode unik per jemaat
- Urutan unik per jemaat

**Dampak pada Kemajuan:**
- Opsi yang dapat dihitung (`is_countable=true`) disertakan dalam kalkulasi % kemajuan
- Opsi yang tidak dapat dihitung dilacak secara terpisah tetapi dikecualikan dari kemajuan
- Ketika opsi dihapus, alamat secara otomatis mendapatkan opsi default ditambahkan

**Aturan Akses:**
- **Daftar/Lihat:** Pengguna dengan peran ATAU akses tautan
- **Buat/Perbarui/Hapus:** Gunakan endpoint `/options/update` (Konduktor atau Administrator)

**Hubungan:**
- M:M dengan Addresses (disimpan sebagai array JSON di field address.type)

**Contoh:**
```json
{
  "id": "opt_res",
  "congregation": "cong_123",
  "code": "RES",
  "description": "Residential",
  "is_countable": true,
  "is_default": true,
  "sequence": 1
}
```

---

### 9. Messages

**Tujuan:** Komunikasi dan notifikasi antar pengguna

**Tipe:** Koleksi dasar

**Field:**

| Field | Tipe | Wajib | Deskripsi |
|-------|------|-------|-----------|
| `id` | text(15) | Ya | ID unik yang dihasilkan otomatis |
| `map` | relation | Ya | FK ke Maps (cascade delete) |
| `congregation` | relation | Ya | FK ke Congregations (cascade delete) |
| `message` | text | Ya | Konten pesan |
| `created_by` | text | Ya | Username pembuat |
| `read` | bool | Ya | Apakah dibaca oleh administrator |
| `pinned` | bool | Ya | Flag instruksi yang di-pin oleh admin |
| `type` | select | Ya | Tipe penerima yang dituju |
| `created` | date | Auto | Timestamp pembuatan |
| `updated` | date | Auto | Timestamp pembaruan terakhir |

**Tipe Pesan:**
- `publisher` - Dari penerbit ke admin
- `conductor` - Dari konduktor
- `administrator` - Instruksi admin (bisa di-pin)

**Indeks:**
- `(map, pinned, created)` - Pesan yang di-pin terbaru
- `(map, type, pinned)` - Filter berdasarkan tipe penerima dan status pin
- `(map, type, read)` - Jumlah yang belum dibaca per tipe

**Aturan Akses:**
- **Buat:** Pengguna dengan peran ATAU akses tautan
- **Perbarui:** Hanya Administrator
- **Daftar:** Pengguna dengan peran ATAU akses tautan

**Pemrosesan Latar Belakang:**
- Setiap 30 menit: Pesan yang belum dibaca dikirim ke administrator melalui email
- Setiap 30 menit: Pesan admin yang di-pin dikirim ke penerbit melalui email
- Setiap 1 jam: Pembaruan catatan dikirim ke administrator

**Contoh:**
```json
{
  "id": "msg_abc",
  "map": "map_xyz",
  "congregation": "cong_123",
  "message": "Mohon kunjungi kembali unit 205",
  "created_by": "john.doe",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

## Teknologi Database

**Engine:** SQLite melalui PocketBase (modernc.org/sqlite v1.40.2+)

**Fitur:**
- Implementasi Go murni (bebas CGo) - memungkinkan kompilasi lintas platform
- Berbasis C SQLite yang ditranspilasi ke Go menggunakan ccgo/v4
- Dikelola secara aktif dengan pembaruan keamanan rutin (selaras dengan SQLite 3.50+)

**Fitur SQL yang Digunakan:**
- Field JSON untuk data yang fleksibel (koordinat, agregat)
- Indeks komposit untuk optimasi kueri
- Transaksi untuk kepatuhan ACID
- CTE (Common Table Expressions) untuk agregasi yang kompleks

**Keuntungan:**
- Database berbasis file mandiri
- Tidak diperlukan layanan database terpisah
- Sempurna untuk deployment self-hosted
- Tidak ada ketergantungan CGO memungkinkan build binary statis
- Stabilitas yang terbukti dengan patch keamanan

---

## Hubungan Kunci

| Hubungan | Kardinalitas | Perilaku Cascade |
|----------|-------------|-----------------|
| Congregation → Territory | 1:M | Delete cascade |
| Territory → Map | 1:M | Delete cascade |
| Map → Address | 1:M | Delete cascade |
| Map → Assignment | 1:M | Delete cascade |
| Map → Message | 1:M | Delete cascade |
| Address → Options | M:M | Array JSON |
| User → Role | M:M | Melalui tabel roles |
| User → Assignment | 1:M | Pertahankan penugasan |
| Congregation → Option | 1:M | Delete cascade |
| Congregation → Role | M:M | Melalui tabel roles |

---

## Aturan Bisnis

### Kalkulasi Kemajuan

**Formula:**
```
completed = (done + not_home_max_tries) / total_countable * 100
```

**Alamat yang Dapat Dihitung:**
- Hanya alamat dengan opsi `is_countable=true`
- Mengecualikan alamat DNC dan tidak valid

**Kriteria Penyelesaian:**
- Status = `done`, ATAU
- Status = `not_home` DAN `not_home_tries >= congregation.max_tries`

### Siklus Hidup Alamat

```
not_done → not_home (percobaan ulang) → done
        → do_not_call (dikecualikan)
        → invalid (dikecualikan)
```

### Trigger Auto-Update

1. **Pembaruan Alamat:**
   - Memperbarui `last_notes_updated` jika catatan berubah
   - Menetapkan `last_notes_updated_by` ke pengguna saat ini
   - Memicu kalkulasi ulang agregat
   - Memperbarui kemajuan peta
   - Memperbarui kemajuan wilayah

2. **Operasi Peta:**
   - Menghitung ulang agregat saat alamat berubah
   - Memperbarui kemajuan wilayah

3. **Pembuatan Penugasan:**
   - Menetapkan kedaluwarsa berdasarkan pengaturan jemaat
   - Menghasilkan ID 20 karakter unik sebagai token akses

---

## Migrasi & Pembaruan Skema

**File Skema:** `migrations/1764846700_collections_snapshot.go`

**Ukuran:** 55.459 baris (dihasilkan oleh PocketBase)

**Proses Migrasi:**
1. PocketBase secara otomatis menerapkan migrasi saat startup
2. Migrasi dilacak di koleksi sistem `_migrations`
3. Perubahan skema memerlukan file migrasi baru

---

## Langkah Selanjutnya

- [Arsitektur](architecture.md) - Arsitektur sistem
- [Pengaturan Backend](backend-setup.md) - Konfigurasi database
