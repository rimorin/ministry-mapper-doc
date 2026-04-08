# Gambaran Keseluruhan Seni Bina

## Seni Bina Sistem

Ministry Mapper terdiri daripada dua komponen utama yang bekerjasama:

### Rajah Seni Bina Peringkat Tinggi

```
┌──────────────────────────────────────────────────────────────────┐
│                     Aplikasi Klien                                │
│         (UI Web, Aplikasi Mudah Alih, Integrasi Pihak Ketiga)    │
└──────────────────────────┬───────────────────────────────────────┘
                           │
                HTTP REST API (Port 8080)
                           │
┌──────────────────────────▼───────────────────────────────────────┐
│                    Aplikasi PocketBase                            │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Lapisan API (internal/handlers/)                            │ │
│  │ - Pengurusan Peta (9 titik akhir)                           │ │
│  │ - Pengurusan Wilayah (2 titik akhir)                        │ │
│  │ - Konfigurasi (1 titik akhir)                               │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Lapisan Middleware                                           │ │
│  │ - Pengesahan & Kebenaran                                    │ │
│  │ - Tangkap Ralat (Sentry)                                    │ │
│  │ - Pengelogan Permintaan                                     │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Kerja Latar Belakang (internal/jobs/)                       │ │
│  │ - Tugas berjadual (berasaskan cron)                         │ │
│  │ - Kawalan bendera ciri (LaunchDarkly)                       │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Perkhidmatan Terbina Dalam                                  │ │
│  │ - Pengesahan (E-mel/Kata Laluan, OAuth2, OTP, MFA)         │ │
│  │ - Acara Masa Nyata (SSE)                                    │ │
│  │ - Papan Pemuka Pentadbir                                    │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
└────────────┬──────────────────────┬────────────────┬──────────────┘
             │                      │                │
             │                      │                │
    ┌────────▼──────┐      ┌────────▼──────┐  ┌─────▼────────┐
    │   SQLite       │      │  Sentry       │  │MailerSend    │
    │   Pangkalan    │      │  (Penjejak    │  │(E-mel)       │
    │   Data         │      │   Ralat)      │  │              │
    │   (/app/pb_data)│      │               │  │              │
    └────────────────┘      └────────────────┘  └──────────────┘
```

## Timbunan Teknologi

### Timbunan Bahagian Belakang

| Lapisan | Teknologi | Versi | Tujuan |
|---------|-----------|-------|--------|
| **Bahasa** | Go | 1.25.0 | Bahagian belakang berprestasi tinggi |
| **Rangka Kerja Bahagian Belakang** | PocketBase | 0.36.7 | BaaS dengan SQLite |
| **Pangkalan Data** | SQLite | v1.40.2+ | Pangkalan data berdikari |
| **Rangka Kerja Web** | Echo | v5 | Penghalaan HTTP |
| **Kontena** | Docker | Terkini | Kontenerisasi |
| **Penjejakan Ralat** | Sentry | v0.43.0 | Pemantauan ralat |
| **Perkhidmatan E-mel** | MailerSend | v1.6.3 | API E-mel |
| **Bendera Ciri** | LaunchDarkly | v7.14.6 | Pengurusan ciri |
| **Penjanaan Laporan** | Excelize | v2.10.1 | Laporan Excel |
| **Ringkasan AI** | OpenAI GPT | v3.29.0 | Ringkasan laporan & mesej pintar |

### Timbunan Bahagian Hadapan

| Lapisan | Teknologi | Versi | Tujuan |
|---------|-----------|-------|--------|
| **Rangka Kerja** | React | 19.2.4 | Perpustakaan UI |
| **Bahasa** | TypeScript | 5.9.3 | Keselamatan jenis |
| **Alat Binaan** | Vite | 8.0.2 | Pembangunan & binaan pantas |
| **Penghalaan** | Wouter | 3.9.0 | Penghalaan ringan |
| **Pengurusan Keadaan** | Context API | - | Keadaan global |
| **Rangka Kerja UI** | Bootstrap | 5.3.8 | Rangka kerja CSS |
| **Pemetaan** | Leaflet | 1.9.4 | Peta interaktif |
| **Pengujian** | Vitest | 4.1.1 | Ujian unit |
| **Kualiti Kod** | ESLint + Prettier | Terkini | Pembaikan & pemformatan |

## Seni Bina Data

### Model Hubungan Entiti

Sistem mengatur data secara hierarki dengan pengasingan peringkat jemaah:

```
Jemaah (Sempadan organisasi)
    ├── Wilayah (Kawasan geografi)
    │   ├── Peta (Bangunan/Lokasi)
    │   │   └── Alamat (Unit individu)
    │   └── Pilihan (Pengelasan jenis alamat)
    ├── Pengguna (Pengguna sistem)
    │   └── Peranan (Tugasan kebenaran)
    ├── Tugasan (Akses penerbit)
    └── Mesej (Pemberitahuan)
```

### Koleksi Teras

**1. Jemaah** - Tetapan dan sempadan organisasi
- Mengawal pengasingan berbilang penyewa
- Menentukan tempoh tugasan dan had percubaan semula
- Tetapan serantau (zon waktu, negara)

**2. Wilayah** - Bahagian geografi
- Mengandungi beberapa peta
- Menjejak peratusan penyelesaian agregat
- Mengatur kawasan pelayanan lapangan

**3. Peta** - Lokasi khusus (bangunan)
- Struktur satu atau berbilang tingkat
- Koordinat geografi
- Penjejakan kemajuan dan statistik

**4. Alamat** - Unit/isi rumah individu
- Penjejakan status (belum selesai, selesai, tiada di rumah, TGG)
- Nota lawatan dan sejarah
- Maklumat tingkat dan unit

**5. Pengguna** - Akaun sistem
- Pengesahan e-mel/kata laluan atau OAuth2
- Sokongan pengesahan pelbagai faktor
- Peranan khusus jemaah

**6. Peranan** - Tugasan kebenaran
- Tiga tahap: read_only, conductor, administrator
- Kebenaran khusus jemaah
- Kawalan akses berasaskan peranan

**7. Tugasan** - Token akses penerbit
- Pautan akses terhad masa
- Akses pekerja lapangan tanpa nama
- Tamat tempoh automatik

**8. Pilihan** - Pengelasan alamat
- Boleh disesuaikan per jemaah
- Jenis yang boleh dikira berbanding tidak boleh dikira
- Mempengaruhi pengiraan kemajuan

**9. Mesej** - Komunikasi
- Maklum balas penerbit
- Arahan pentadbir
- Pemberitahuan e-mel

## Seni Bina Bahagian Hadapan

### Corak Organisasi Komponen

Ministry Mapper menggunakan **organisasi komponen berasaskan ciri**:

```
Halaman (Laluan)
  ├── Papan Pemuka Pentadbir (/)
  ├── Peta Penerbit (/map/:id)
  ├── Pengurusan Pengguna (/usermgmt)
  └── Halaman Depan (/)

Komponen (UI Boleh Guna Semula)
  ├── Komponen Borang (14 komponen)
  ├── Komponen Modal (24+ modal)
  ├── Komponen Navigasi (28 komponen)
  ├── Komponen Jadual (9 komponen)
  ├── Komponen Peta (7 komponen)
  ├── Middleware/Pembekal (5 komponen)
  └── Halaman Statik (8 komponen)

Logik Perniagaan (Cangkuk Tersuai)
  ├── useTerritoryManagement
  ├── useMapManagement
  ├── useCongManagement
  ├── useModalManagement
  ├── useRealtimeSubscription
  └── [4 lagi...]

Utiliti & Jenis
  ├── Antara Muka TypeScript
  ├── Pemalar
  ├── Fungsi Pembantu (30+)
  ├── Integrasi PocketBase
  ├── Dasar Kebenaran
  └── Konfigurasi i18n
```

### Strategi Pengurusan Keadaan

**Pendekatan Empat Lapisan:**

1. **Konteks Global** - Tema, bahasa, mod aplikasi
2. **Cangkuk Tersuai** - Logik dan data khusus domain
3. **Keadaan Komponen Tempatan** - Togol UI dan borang
4. **Keadaan Berterusan** - LocalStorage untuk keutamaan

**Manfaat:**
- Tiada overhead Redux
- Aliran data lebih mudah
- Pemisahan kod yang lebih baik
- Pengujian lebih mudah

### Corak Aliran Data

```
Tindakan Pengguna
    ↓
Pengendali Acara Komponen
    ↓
Cangkuk Tersuai (pengesahan, pengurusan keadaan)
    ↓
Utiliti PocketBase (operasi CRUD)
    ↓
Bahagian Belakang PocketBase (kebenaran, pangkalan data)
    ↓
Langganan Masa Nyata (SSE)
    ↓
Kemas Kini UI Automatik
```

## Corak Reka Bentuk Utama

| Corak | Penggunaan | Tujuan |
|-------|-----------|--------|
| **Pembalut Middleware** | Integrasi Sentry | Penjejakan ralat terpusat |
| **Cangkuk Acara** | Kitaran hayat PocketBase | Kemas kini automatik |
| **Penjadual Kerja** | Cron dengan bendera ciri | Operasi berjadual |
| **Corak Transaksi** | Operasi pangkalan data | Konsistensi data |
| **Akses Berasaskan Pautan** | Token tugasan | Akses tanpa nama |
| **Caching Agregat** | Medan JSON | Pengiraan pantas |
| **Cangkuk Tersuai** | Logik perniagaan | Logik boleh guna semula |
| **Corak Pembekal** | Pembekal konteks | Keadaan global |
| **Pemuatan Malas** | Pemisahan laluan | Prestasi |
| **Corak Dasar** | Kebenaran | Peraturan keselamatan |

## Seni Bina Keselamatan

### Kaedah Pengesahan

1. **E-mel/Kata Laluan** - Pengesahan standard dengan pencincangan bcrypt
2. **OAuth2 (Google)** - Log masuk sosial dengan aliran PKCE
3. **Kata Laluan Sekali Guna (OTP)** - Lapisan keselamatan tambahan
4. **Pengesahan Pelbagai Faktor (MFA)** - Keselamatan ditingkatkan untuk pentadbir

### Model Kebenaran

**Kawalan Akses Berasaskan Peranan (RBAC):**

| Peranan | Kebenaran | Kes Penggunaan |
|---------|-----------|----------------|
| **read_only** | Lihat data sahaja | Pemerhati, juruaudit |
| **conductor** | Cipta tugasan, urus peta | Penyelaras lapangan |
| **administrator** | Akses penuh | Pentadbir sistem |

### Corak Kawalan Akses

**Corak 1: Pengguna Disahkan dengan Peranan**
```
Pengguna mesti log masuk
+ Pengguna mempunyai peranan dalam koleksi peranan
+ Peranan adalah untuk jemaah tertentu
```

**Corak 2: Akses Berasaskan Pautan**
```
ID tugasan dihantar dalam pengepala
+ Tugasan wujud dan belum tamat tempoh
+ Tugasan milik peta yang diminta
```

**Corak 3: Pengasingan Jemaah**
```
Setiap entiti mempunyai kunci asing jemaah
+ Peraturan akses menapis mengikut jemaah
+ Mencegah akses data merentas jemaah
```

## Penyegerakan Masa Nyata

### Seni Bina SSE

- **API Masa Nyata PocketBase**: Server-Sent Events (SSE)
- **Langganan Automatik**: Komponen melanggan koleksi
- **Kemas Kini Langsung**: Perubahan disiarkan kepada semua klien yang disambungkan
- **Penyelesaian Konflik**: Penyelesaian berasaskan cap masa
- **Pengesanan Keterlihatan**: Muat semula auto apabila tab menjadi aktif

### Corak Langganan

```typescript
// Komponen melanggan perubahan data
useRealtimeSubscription('addresses', (data) => {
  // UI dikemas kini secara automatik
});
```

## Kerja Latar Belakang

### Tugas Berjadual

| Kerja | Jadual | Tujuan |
|-------|--------|--------|
| **Pembersihan Tugasan** | Setiap 5 min | Padam tugasan yang tamat tempoh |
| **Agregat Wilayah** | Setiap 10 min | Kemas kini statistik kemajuan |
| **Pemprosesan Mesej** | Setiap 30 min | Hantar pemberitahuan mesej |
| **Arahan** | Setiap 30 min | Hantar mesej pentadbir |
| **Kemas Kini Nota** | Setiap 1 jam | Beritahu perubahan nota |
| **Laporan Bulanan** | 1hb setiap bulan | Janakan laporan Excel (dengan ringkasan AI pilihan) |
| **Pengguna Tidak Diperuntukkan** | Harian 01:00 UTC | Kuatkuasakan kitaran hayat pengguna (amaran → lumpuhkan → padam) |
| **Pengguna Tidak Aktif** | Harian 01:30 UTC | Amaran dan lumpuhkan akaun tidak aktif |

### Kawalan Bendera Ciri

Semua kerja latar belakang dikawal oleh bendera ciri LaunchDarkly:
- Aktif/nyahaktif kerja tanpa pelaksanaan semula
- Keupayaan pelancaran beransur-ansur
- Suis kecemasan matikan

### Integrasi AI/LLM

Ministry Mapper diintegrasikan dengan model GPT OpenAI untuk ringkasan teks pintar. Ini adalah ciri **pilihan** yang dikawal oleh bendera LaunchDarkly `enable-report-ai-summary`.

**Model**: GPT (terkini tersedia melalui `openai-go` SDK v3.29.0)  
**Suhu**: 0.3 (output fakta dan deterministik)  
**Format Respons**: Mod JSON untuk data berstruktur

**Kes Penggunaan:**

| Konteks | Kerja | Output |
|---------|-------|--------|
| Laporan Excel bulanan | `generateMonthlyReport` | `summary` dan `key_points` per wilayah |
| Ringkasan mesej penerbit | `processMessages` | `overview` dan `key_themes` |
| Ringkasan nota jemaah | `processNotes` | `overview` dan `key_themes` |

**Degradasi Anggun**: Jika `OPENAI_API_KEY` tidak ditetapkan atau bendera `enable-report-ai-summary` tidak aktif, sistem kembali ke laporan dan e-mel tanpa kandungan yang dijana AI — tiada ralat dibangkitkan.

### Pengoptimuman Bahagian Hadapan

- **Pemisahan Kod**: Laluan yang dimuatkan secara malas
- **Pengoptimuman Bundle**: Penggoncangan pokok, pemadatan
- **Pengkompil React 19**: Memoization automatik
- **Caching PWA**: Pekerja perkhidmatan untuk aset statik
- **Senarai Dipervirtualkan**: React-window untuk set data besar

### Pengoptimuman Bahagian Belakang

- **Indeks Pangkalan Data**: 25+ indeks untuk pertanyaan pantas
- **Caching Agregat**: Statistik pra-dikira dalam JSON
- **Pengumpulan Sambungan**: Sambungan pangkalan data yang cekap
- **Pengelompokan Transaksi**: Kurangkan perjalanan pusingan pangkalan data

## Seni Bina Pelaksanaan

### Kontenerisasi Docker

**Bahagian Belakang:**
- Binaan Docker berbilang peringkat
- Asas Alpine untuk jejak kecil
- Pemasangan volum untuk kegigihan data
- Pemeriksaan kesihatan untuk kebolehpercayaan

**Bahagian Hadapan:**
- Binaan statik dihidangkan oleh CDN
- Caching tepi untuk penghantaran pantas
- Keupayaan Aplikasi Web Progresif

### Pilihan Infrastruktur

1. **Perkhidmatan yang Dihoskan** (Disyorkan)
   - ministry-mapper.com
   - Infrastruktur terurus
   - Kemas kini automatik

2. **Dihoskan Sendiri**
   - Railway, Render, DigitalOcean
   - Memerlukan kepakaran teknikal
   - Kawalan penuh

## Pemantauan & Kebolehperhatian

### Penjejakan Ralat

- **Integrasi Sentry**: Bahagian hadapan dan belakang
- **Pengkategorian Ralat**: Mengikut keparahan dan jenis
- **Peta Sumber**: Nyahpepijat kod yang dimampatkan
- **Penjejakan Keluaran**: Ralat khusus versi

### Pengelogan

- **Bahagian Belakang**: Log JSON berstruktur
- **Bahagian Hadapan**: Pengelogan konsol dalam pembangunan
- **Pengeluaran**: Ralat dihantar ke Sentry
- **Jejak Audit**: Tindakan pengguna dijejak

## Pertimbangan Skalabiliti

### Batasan Semasa

- Had penulis tunggal SQLite
- Sesuai untuk jemaah kecil hingga sederhana (< 1000 pengguna serentak)
- Penskalaan menegak (pelayan lebih berkuasa)

### Skalabiliti Masa Hadapan

- PocketBase menyokong replika baca
- Boleh berhijrah ke PostgreSQL jika diperlukan
- Caching CDN untuk aset statik
- Fungsi tepi untuk pengoptimuman API

## Keputusan Teknologi

### Mengapa Teknologi-Teknologi Ini?

**React 19**: Ciri terkini, pengoptimuman automatik, bundle lebih kecil

**TypeScript**: Keselamatan jenis, sokongan IDE yang lebih baik, lebih sedikit ralat masa jalan

**Vite**: Pelayan pembangunan pantas, HMR segera, binaan dioptimumkan

**PocketBase**: Dihoskan sendiri, tiada kunci vendor, mudah diselenggara

**Wouter**: Ringan (3KB berbanding 40KB untuk React Router)

**Bootstrap**: Diuji pertempuran, komponen komprehensif, kebolehcapaian yang baik

**Leaflet**: Peta percuma, tiada had API, ringan

**SQLite**: Berdikari, tiada pelayan pangkalan data berasingan, sempurna untuk pengehosan sendiri

## Langkah Seterusnya

- [Persediaan Bahagian Belakang](backend-setup.md) - Konfigurasikan bahagian belakang
- [Persediaan Bahagian Hadapan](frontend-setup.md) - Laksanakan bahagian hadapan
- [Panduan Keselamatan](security.md) - Amalan terbaik keselamatan
