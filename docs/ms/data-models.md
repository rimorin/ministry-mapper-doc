# Model Data & Skema

## Gambaran Keseluruhan

Ministry Mapper menggunakan SQLite melalui PocketBase dengan model data hierarki yang direka untuk pengurusan jemaah berbilang penyewa. Dokumen ini menyediakan dokumentasi komprehensif semua model data, hubungan, dan peraturan perniagaan.

## Rajah Hubungan Entiti

```
Jemaah (Entiti akar - pengasingan berbilang penyewa)
    │
    ├─── Wilayah (1:M)
    │      │
    │      └─── Peta (1:M)
    │             │
    │             ├─── Alamat (1:M)
    │             ├─── Tugasan (1:M)
    │             └─── Mesej (1:M)
    │
    ├─── Pilihan (1:M)
    │      └─── Alamat (M:M melalui tatasusunan JSON)
    │
    ├─── Pengguna (1:M melalui Peranan)
    │      │
    │      ├─── Peranan (1:M)
    │      └─── Tugasan (1:M)
    │
    └─── Mesej (1:M)
```

## Koleksi Teras

### 1. Jemaah

**Tujuan:** Entiti pusat untuk pengasingan berbilang penyewa dan tetapan organisasi

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `code` | text | Ya | Kod organisasi |
| `name` | text | Ya | Nama organisasi |
| `expiry_hours` | number | Ya | Tempoh pautan tugasan (jam) |
| `max_tries` | number | Ya | Maksimum percubaan "tiada di rumah" sebelum menandakan selesai |
| `origin` | select | Ya | Negara/wilayah (sg, my) |
| `timezone` | select | Ya | Zon waktu untuk pengiraan tarikh |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Peraturan Akses:**
- **Senarai:** Pengguna mesti mempunyai peranan dalam jemaah
- **Lihat:** Pengguna dengan peranan ATAU akses berasaskan pautan
- **Kemas kini:** Pentadbir sahaja
- **Padam:** Pentadbir sahaja

**Peraturan Perniagaan:**
- Mengawal tempoh tugasan lalai untuk semua peta
- `max_tries` menentukan bilakah alamat "tiada di rumah" dikira sebagai selesai
- Zon waktu mempengaruhi pengiraan berasaskan tarikh dan laporan

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

### 2. Wilayah

**Tujuan:** Bahagian geografi dalam jemaah untuk mengatur pelayanan lapangan

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `code` | text | Ya | Pengenal wilayah (auto: "0") |
| `description` | text | Tidak | Penerangan mesra manusia |
| `progress` | number | Auto | Peratusan penyelesaian (0-100) |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Indeks:**
- `(congregation, code)` - Carian pantas mengikut jemaah
- `(congregation)` - Penapis jemaah

**Hubungan:**
- 1:M dengan Peta
- 1:M dengan Alamat (denormalkan untuk prestasi)

**Peraturan Akses:**
- **Senarai:** Pengguna dengan peranan dalam jemaah
- **Lihat:** Pengguna dengan peranan ATAU akses pautan
- **Cipta/Kemas kini/Padam:** Konduktor atau Pentadbir

**Pengiraan Kemajuan:**
Dikira secara automatik dari agregat semua peta dalam wilayah

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

### 3. Peta

**Tujuan:** Mewakili lokasi atau bangunan tertentu yang mengandungi alamat

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `territory` | relation | Ya | FK ke Wilayah (padam berkaskad) |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `sequence` | number | Auto | Urutan dalam wilayah (auto-kenaikan) |
| `code` | text | Ya | Pengenal/nombor peta (auto: "0") |
| `description` | text | Tidak | Penerangan lokasi |
| `type` | select | Ya | `single` atau `multi` (jenis tingkat) |
| `coordinates` | JSON | Tidak | Lokasi geografi `{lat, lng}` |
| `aggregates` | JSON | Auto | Statistik dalam cache (maks 2MB) |
| `progress` | number | Auto | Peratusan penyelesaian |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Indeks:**
- `(territory, code)` - Carian utama
- `(territory, sequence)` - Pengurutan
- `(territory)` - Penapis wilayah

**Padaman Berkaskad:**
- Alamat
- Tugasan
- Mesej

**Struktur Agregat:**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**Jenis Peta:**
- **single**: Alamat swasta (rumah)
- **multi**: Alamat awam (bangunan berbilang tingkat)

**Peraturan Akses:**
- **Senarai:** Pengguna dengan peranan ATAU akses pautan
- **Lihat:** Pengguna dengan peranan ATAU akses pautan
- **Cipta/Kemas kini:** Konduktor atau Pentadbir
- **Padam:** Pentadbir sahaja

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

### 4. Alamat

**Tujuan:** Unit atau isi rumah individu dalam peta

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `map` | relation | Ya | FK ke Peta (padam berkaskad) |
| `congregation` | relation | Ya | FK ke Jemaah (denormalkan) |
| `territory` | relation | Ya | FK ke Wilayah (denormalkan) |
| `floor` | number | Ya | Nombor tingkat (berasaskan 0) |
| `code` | text | Ya | Kod unit/pintu (cth., "101", "A") |
| `sequence` | number | Ya | Urutan dalam peta |
| `status` | select | Ya | Status lawatan alamat |
| `not_home_tries` | number | Auto | Kaunter untuk percubaan "tiada di rumah" |
| `type` | relation | Tidak | M:M ke Pilihan (tatasusunan JSON) |
| `notes` | text | Tidak | Nota pekerja lapangan |
| `last_notes_updated` | date | Auto | Bila nota terakhir diubah (melalui cangkuk) |
| `last_notes_updated_by` | text | Auto | Nama pengguna pengemas kini terakhir (melalui cangkuk) |
| `dnc_time` | date | Tidak | Bila ditandai "jangan hubungi" |
| `coordinates` | JSON | Tidak | GPS terperinci `{lat, lng}` |
| `updated_by` | text | Tidak | Jejak siapa yang membuat kemas kini |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Nilai Status:**
- `not_done` - Belum dilawati (keadaan awal)
- `done` - Berjaya diselesaikan
- `not_home` - Tiada orang di rumah (boleh cuba semula)
- `do_not_call` - Dikecualikan dari kerja
- `invalid` - Alamat tidak wujud

**Indeks (7 jumlah):**
- `(map)` - Penapis peta
- `(map, status)` - Pertanyaan taburan status
- `(map, code)` - Carian kod
- `(map, floor)` - Organisasi tingkat
- `(type, congregation)` - Taburan pilihan
- `(congregation, last_notes_updated_by)` - Nota terkini mengikut pengguna
- `(territory, status)` - Status seluruh wilayah

**Cangkuk Auto:**
- Kemas kini mencetuskan medan `last_notes_updated` dan `last_notes_updated_by`
- Mencetuskan pengiraan semula agregat pada peta
- Mengemas kini kemajuan wilayah

**Peraturan Akses:**
- **Senarai:** Pengguna dengan peranan ATAU akses pautan
- **Lihat:** Pengguna dengan peranan ATAU akses pautan
- **Cipta:** Konduktor atau Pentadbir
- **Kemas kini:** Pengguna dengan peranan ATAU akses pautan (penerbit boleh mengemas kini)
- **Padam:** Pentadbir sahaja

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
  "notes": "Try after 6pm",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. Pengguna

**Tujuan:** Akaun pengguna sistem dengan pengesahan

**Jenis:** Koleksi auth (jenis-auth-pengguna PocketBase)

**Medan Sistem:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `email` | email | Ya | Identiti log masuk (unik) |
| `emailVisibility` | bool | Ya | Sama ada e-mel adalah awam |
| `verified` | bool | Auto | Status pengesahan e-mel |
| `password` | password | Ya | Bcrypt dicincang (kos: 10, min 6 aksara) |
| `tokenKey` | text | Tersembunyi | Kunci penjanaan token (30-60 aksara) |

**Medan Tersuai:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `name` | text | Ya | Nama paparan (2-50 aksara) |
| `disabled` | bool | Tidak | Bendera akaun dilumpuhkan |
| `last_login` | date | Auto | Cap masa pengesahan terakhir (melalui cangkuk) |

**Konfigurasi Auth:**
- **Auth Kata Laluan:** Diaktifkan (min 6 aksara)
- **OAuth2:** Aliran PKCE Google
- **MFA:** Diaktifkan (tempoh: 1800s) - Dilumpuhkan untuk OAuth2
- **OTP:** Diaktifkan (tempoh: 180s, panjang: 4 digit)
- **Sahkan E-mel:** Tempoh 604800s (7 hari)
- **Tetapan Semula Kata Laluan:** Tempoh 1800s (30 min)

**Peraturan Akses:**
- **Senarai:** Pentadbir sahaja
- **Lihat:** Pentadbir atau diri sendiri
- **Cipta:** Awam (OAuth atau pendaftaran eksplisit)
- **Kemas kini:** Diri sendiri sahaja
- **Padam:** Pentadbir sahaja

---

### 6. Peranan

**Tujuan:** Tugasan kebenaran yang menghubungkan pengguna ke jemaah

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `user` | relation | Ya | FK ke Pengguna (padam berkaskad) |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `role` | select | Ya | Tahap kebenaran |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Nilai Peranan:**
- `read_only` - Akses lihat sahaja
- `conductor` - Boleh urus tugasan dan peta
- `administrator` - Akses penuh

**Peraturan Perniagaan:**
- Satu peranan per pengguna per jemaah
- Peranan menentukan kebenaran untuk semua tindakan

---

### 7. Tugasan

**Tujuan:** Token akses penerbit untuk akses peta terhad masa

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(20) | Ya | **Berfungsi sebagai token akses tanpa nama** |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `map` | relation | Ya | FK ke Peta (padam berkaskad) |
| `user` | relation | Tidak | Pengguna penerbit (pilihan) |
| `type` | select | Ya | Klasifikasi tugasan |
| `expiry_date` | date | Ya | Cap masa tamat tempoh automatik |
| `publisher` | text | Tidak | Nama/pengenal penerbit |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Jenis Tugasan:**
- `normal` - Kerja wilayah biasa
- `personal` - Wilayah pelayanan peribadi

**Aliran Kerja:**
1. Konduktor mencipta melalui titik akhir `/territory/link`
2. Penerbit menerima `link_id` 20-aksara (ID tugasan ini)
3. Penerbit menghantar `link_id` melalui pengepala HTTP `@request.headers.link_id`
4. PocketBase mengesahkan: link_id sepadan dengan assignment.id DAN expiry_date > sekarang
5. Kerja latar belakang memadamkan tugasan yang tamat tempoh setiap 5 minit

---

### 8. Pilihan

**Tujuan:** Klasifikasi jenis alamat (boleh disesuaikan per jemaah)

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `code` | text | Ya | Pengenal pilihan (cth., "RES", "BUS") |
| `description` | text | Ya | Label mesra manusia |
| `is_countable` | bool | Ya | Sertakan dalam pengiraan kemajuan |
| `is_default` | bool | Ya | Pilihan lalai untuk alamat baru |
| `sequence` | number | Ya | Urutan paparan/isihan |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Kesan terhadap Kemajuan:**
- Pilihan yang boleh dikira (`is_countable=true`) disertakan dalam pengiraan peratusan kemajuan
- Pilihan yang tidak boleh dikira dijejak secara berasingan tetapi dikecualikan dari kemajuan
- Apabila pilihan dipadamkan, alamat mendapat pilihan lalai ditambah secara automatik

---

### 9. Mesej

**Tujuan:** Komunikasi dan pemberitahuan antara pengguna

**Jenis:** Koleksi asas

**Medan:**

| Medan | Jenis | Diperlukan | Penerangan |
|-------|-------|------------|------------|
| `id` | text(15) | Ya | ID unik yang dijana secara auto |
| `map` | relation | Ya | FK ke Peta (padam berkaskad) |
| `congregation` | relation | Ya | FK ke Jemaah (padam berkaskad) |
| `message` | text | Ya | Kandungan mesej |
| `created_by` | text | Ya | Nama pengguna pencipta |
| `read` | bool | Ya | Sama ada telah dibaca oleh pentadbir |
| `pinned` | bool | Ya | Bendera arahan yang disematkan oleh pentadbir |
| `type` | select | Ya | Jenis penerima yang dimaksudkan |
| `created` | date | Auto | Cap masa penciptaan |
| `updated` | date | Auto | Cap masa kemas kini terakhir |

**Jenis Mesej:**
- `publisher` - Dari penerbit kepada pentadbir
- `conductor` - Dari konduktor
- `administrator` - Arahan pentadbir (boleh disematkan)

**Pemprosesan Latar Belakang:**
- Setiap 30 min: Mesej yang belum dibaca dihantar ke pentadbir melalui e-mel
- Setiap 30 min: Mesej pentadbir yang disematkan dihantar ke penerbit melalui e-mel
- Setiap 1 jam: Kemas kini nota dihantar ke pentadbir

---

## Teknologi Pangkalan Data

**Enjin:** SQLite melalui PocketBase (modernc.org/sqlite v1.40.2+)

**Ciri-ciri:**
- Pelaksanaan Go tulen (bebas CGo) - membolehkan kompilasi merentasi platform
- Berdasarkan C SQLite yang ditranspil ke Go menggunakan ccgo/v4
- Diselenggara secara aktif dengan kemas kini keselamatan tetap (sejajar SQLite 3.50+)

**Ciri SQL yang Digunakan:**
- Medan JSON untuk data fleksibel (koordinat, agregat)
- Indeks komposit untuk pengoptimuman pertanyaan
- Transaksi untuk pematuhan ACID
- CTE (Common Table Expressions) untuk pengagregatan kompleks

**Kelebihan:**
- Pangkalan data berasaskan fail yang berdikari
- Tiada perkhidmatan pangkalan data berasingan diperlukan
- Sempurna untuk pelaksanaan yang dihoskan sendiri
- Tiada pergantungan CGO membolehkan binaan binari statik

---

## Hubungan Utama

| Hubungan | Kardinaliti | Tingkah Laku Kaskad |
|----------|-------------|---------------------|
| Jemaah → Wilayah | 1:M | Padam berkaskad |
| Wilayah → Peta | 1:M | Padam berkaskad |
| Peta → Alamat | 1:M | Padam berkaskad |
| Peta → Tugasan | 1:M | Padam berkaskad |
| Peta → Mesej | 1:M | Padam berkaskad |
| Alamat → Pilihan | M:M | Tatasusunan JSON |
| Pengguna → Peranan | M:M | Melalui jadual peranan |
| Pengguna → Tugasan | 1:M | Simpan tugasan |
| Jemaah → Pilihan | 1:M | Padam berkaskad |
| Jemaah → Peranan | M:M | Melalui jadual peranan |

---

## Peraturan Perniagaan

### Pengiraan Kemajuan

**Formula:**
```
selesai = (done + not_home_max_tries) / total_countable * 100
```

**Alamat yang Boleh Dikira:**
- Hanya alamat dengan pilihan `is_countable=true`
- Mengecualikan alamat TGG dan tidak sah

**Kriteria Penyelesaian:**
- Status = `done`, ATAU
- Status = `not_home` DAN `not_home_tries >= congregation.max_tries`

### Kitaran Hayat Alamat

```
not_done → not_home (percubaan semula) → done
         → do_not_call (dikecualikan)
         → invalid (dikecualikan)
```

### Pencetus Kemas Kini Auto

1. **Kemas Kini Alamat:**
   - Mengemas kini `last_notes_updated` jika nota berubah
   - Menetapkan `last_notes_updated_by` ke pengguna semasa
   - Mencetuskan pengiraan semula agregat
   - Mengemas kini kemajuan peta
   - Mengemas kini kemajuan wilayah

2. **Operasi Peta:**
   - Mengira semula agregat apabila alamat berubah
   - Mengemas kini kemajuan wilayah

3. **Penciptaan Tugasan:**
   - Menetapkan tamat tempoh berdasarkan tetapan jemaah
   - Menjana ID 20-aksara unik sebagai token akses

---

## Migrasi & Kemas Kini Skema

**Fail Skema:** `migrations/1764846700_collections_snapshot.go`

**Saiz:** 55,459 baris (dijana oleh PocketBase)

**Proses Migrasi:**
1. PocketBase secara automatik menerapkan migrasi semasa permulaan
2. Migrasi dijejak dalam koleksi sistem `_migrations`
3. Perubahan skema memerlukan fail migrasi baru

---

## Langkah Seterusnya

- [Seni Bina](architecture.md) - Seni bina sistem
- [Persediaan Bahagian Belakang](backend-setup.md) - Konfigurasikan pangkalan data
