# Ikhtisar Arsitektur

## Arsitektur Sistem

Ministry Mapper terdiri dari dua komponen utama yang bekerja bersama:

### Diagram Arsitektur Tingkat Tinggi

```
┌──────────────────────────────────────────────────────────────────┐
│                     Client Applications                           │
│         (Web UI, Mobile Apps, Third-party Integrations)          │
└──────────────────────────┬───────────────────────────────────────┘
                           │
                HTTP REST API (Port 8080)
                           │
┌──────────────────────────▼───────────────────────────────────────┐
│                    PocketBase Application                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ API Layer (internal/handlers/)                              │ │
│  │ - Map Management (9 endpoints)                              │ │
│  │ - Territory Management (2 endpoints)                        │ │
│  │ - Configuration (1 endpoint)                                │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Middleware Layer                                            │ │
│  │ - Authentication & Authorization                            │ │
│  │ - Error Capture (Sentry)                                    │ │
│  │ - Request Logging                                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Background Jobs (internal/jobs/)                            │ │
│  │ - Scheduled tasks (cron-based)                             │ │
│  │ - Feature flag control (LaunchDarkly)                      │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Built-in Services                                           │ │
│  │ - Authentication (Email/Password, OAuth2, OTP, MFA)        │ │
│  │ - Real-time Events (SSE)                                   │ │
│  │ - Admin Dashboard                                          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
└────────────┬──────────────────────┬────────────────┬──────────────┘
             │                      │                │
             │                      │                │
    ┌────────▼──────┐      ┌────────▼──────┐  ┌─────▼────────┐
    │   SQLite       │      │  Sentry       │  │MailerSend    │
    │   Database     │      │  (Error Track)│  │(Email)       │
    │   (/app/pb_data)│      │               │  │              │
    └────────────────┘      └────────────────┘  └──────────────┘
```

## Tumpukan Teknologi

### Tumpukan Backend

| Layer | Teknologi | Versi | Tujuan |
|-------|-----------|-------|--------|
| **Bahasa** | Go | 1.25.0 | Backend berkinerja tinggi |
| **Framework Backend** | PocketBase | 0.36.7 | BaaS dengan SQLite |
| **Database** | SQLite | v1.40.2+ | Database mandiri |
| **Framework Web** | Echo | v5 | HTTP routing |
| **Container** | Docker | Latest | Kontainerisasi |
| **Pelacakan Error** | Sentry | v0.43.0 | Pemantauan error |
| **Layanan Email** | MailerSend | v1.6.3 | Email API |
| **Feature Flags** | LaunchDarkly | v7.14.6 | Manajemen fitur |
| **Pembuatan Laporan** | Excelize | v2.10.1 | Laporan Excel |
| **Ringkasan AI** | OpenAI GPT | v3.29.0 | Ringkasan laporan & pesan cerdas |

### Tumpukan Frontend

| Layer | Teknologi | Versi | Tujuan |
|-------|-----------|-------|--------|
| **Framework** | React | 19.2.4 | Library UI |
| **Bahasa** | TypeScript | 5.9.3 | Type safety |
| **Build Tool** | Vite | 8.0.2 | Dev & build yang cepat |
| **Routing** | Wouter | 3.9.0 | Routing ringan |
| **Manajemen State** | Context API | - | State global |
| **Framework UI** | Bootstrap | 5.3.8 | Framework CSS |
| **Pemetaan** | Leaflet | 1.9.4 | Peta interaktif |
| **Pengujian** | Vitest | 4.1.1 | Unit testing |
| **Kualitas Kode** | ESLint + Prettier | Latest | Linting & formatting |

## Arsitektur Data

### Model Entity Relationship

Sistem mengorganisir data secara hierarkis dengan isolasi di tingkat jemaat:

```
Congregation (Batas organisasi)
    ├── Territory (Wilayah geografis)
    │   ├── Map (Bangunan/Lokasi)
    │   │   └── Address (Unit individual)
    │   └── Option (Klasifikasi tipe alamat)
    ├── User (Pengguna sistem)
    │   └── Role (Penugasan izin)
    ├── Assignment (Akses penerbit)
    └── Message (Notifikasi)
```

### Koleksi Inti

**1. Congregations** - Pengaturan dan batas organisasi
- Mengontrol isolasi multi-tenant
- Mendefinisikan durasi penugasan dan batas percobaan ulang
- Pengaturan regional (zona waktu, negara)

**2. Territories** - Pembagian geografis
- Berisi banyak peta
- Melacak persentase penyelesaian agregat
- Mengorganisir area pelayanan lapangan

**3. Maps** - Lokasi tertentu (bangunan)
- Struktur lantai tunggal atau multi-lantai
- Koordinat geografis
- Pelacakan kemajuan dan statistik

**4. Addresses** - Unit/rumah tangga individual
- Pelacakan status (belum selesai, selesai, tidak ada di rumah, DNC)
- Catatan kunjungan dan riwayat
- Informasi lantai dan unit

**5. Users** - Akun sistem
- Autentikasi email/kata sandi atau OAuth2
- Dukungan autentikasi multi-faktor
- Peran khusus jemaat

**6. Roles** - Penugasan izin
- Tiga level: read_only, conductor, administrator
- Izin khusus jemaat
- Kontrol akses berbasis peran

**7. Assignments** - Token akses penerbit
- Tautan akses terbatas waktu
- Akses pekerja lapangan anonim
- Kedaluwarsa otomatis

**8. Options** - Klasifikasi alamat
- Dapat dikustomisasi per jemaat
- Tipe yang dapat dihitung vs tidak dapat dihitung
- Mempengaruhi kalkulasi kemajuan

**9. Messages** - Komunikasi
- Umpan balik penerbit
- Instruksi admin
- Notifikasi email

## Arsitektur Frontend

### Pola Organisasi Komponen

Ministry Mapper menggunakan **organisasi komponen berbasis fitur**:

```
Pages (Routes)
  ├── Admin Dashboard (/)
  ├── Publisher Map (/map/:id)
  ├── User Management (/usermgmt)
  └── Front Page (/)

Components (UI yang dapat digunakan ulang)
  ├── Form Components (14 komponen)
  ├── Modal Components (24+ modal)
  ├── Navigation Components (28 komponen)
  ├── Table Components (9 komponen)
  ├── Map Components (7 komponen)
  ├── Middleware/Providers (5 komponen)
  └── Static Pages (8 komponen)

Business Logic (Custom Hooks)
  ├── useTerritoryManagement
  ├── useMapManagement
  ├── useCongManagement
  ├── useModalManagement
  ├── useRealtimeSubscription
  └── [4 lainnya...]

Utilities & Types
  ├── TypeScript Interfaces
  ├── Constants
  ├── Helper Functions (30+)
  ├── PocketBase Integration
  ├── Authorization Policies
  └── i18n Configuration
```

### Strategi Manajemen State

**Pendekatan Empat Layer:**

1. **Global Context** - Tema, bahasa, mode aplikasi
2. **Custom Hooks** - Logika domain-spesifik dan data
3. **Local Component State** - Toggle UI dan form
4. **Persistent State** - LocalStorage untuk preferensi

**Keuntungan:**
- Tidak ada overhead Redux
- Alur data yang lebih sederhana
- Code splitting yang lebih baik
- Pengujian yang lebih mudah

### Pola Alur Data

```
Aksi Pengguna
    ↓
Component Event Handler
    ↓
Custom Hook (validasi, manajemen state)
    ↓
PocketBase Utils (operasi CRUD)
    ↓
PocketBase Backend (izin, database)
    ↓
Real-time Subscription (SSE)
    ↓
UI Auto-updates
```

## Pola Desain Kunci

| Pola | Penggunaan | Tujuan |
|------|-----------|--------|
| **Middleware Wrapper** | Integrasi Sentry | Pelacakan error terpusat |
| **Event Hooks** | Siklus hidup PocketBase | Pembaruan otomatis |
| **Job Scheduler** | Cron dengan feature flags | Operasi terjadwal |
| **Transaction Pattern** | Operasi database | Konsistensi data |
| **Link-Based Access** | Token penugasan | Akses anonim |
| **Aggregate Caching** | Field JSON | Kalkulasi cepat |
| **Custom Hooks** | Logika bisnis | Logika yang dapat digunakan ulang |
| **Provider Pattern** | Context providers | State global |
| **Lazy Loading** | Route splitting | Kinerja |
| **Policy Pattern** | Otorisasi | Aturan keamanan |

## Arsitektur Keamanan

### Metode Autentikasi

1. **Email/Password** - Autentikasi standar dengan hashing bcrypt
2. **OAuth2 (Google)** - Login sosial dengan alur PKCE
3. **One-Time Password (OTP)** - Layer keamanan tambahan
4. **Multi-Factor Authentication (MFA)** - Keamanan yang ditingkatkan untuk admin

### Model Otorisasi

**Role-Based Access Control (RBAC):**

| Peran | Izin | Kasus Penggunaan |
|-------|------|-----------------|
| **read_only** | Lihat data saja | Observer, auditor |
| **conductor** | Buat penugasan, kelola peta | Koordinator lapangan |
| **administrator** | Akses penuh | Administrator sistem |

### Pola Kontrol Akses

**Pola 1: Pengguna Terautentikasi dengan Peran**
```
Pengguna harus login
+ Pengguna memiliki peran dalam koleksi roles
+ Peran adalah untuk jemaat tertentu
```

**Pola 2: Akses Berbasis Tautan**
```
Assignment ID diteruskan di header
+ Penugasan ada dan belum kedaluwarsa
+ Penugasan milik peta yang diminta
```

**Pola 3: Isolasi Jemaat**
```
Setiap entitas memiliki foreign key jemaat
+ Aturan akses memfilter berdasarkan jemaat
+ Mencegah akses data lintas jemaat
```

## Sinkronisasi Real-Time

### Arsitektur SSE

- **PocketBase Real-time API**: Server-Sent Events (SSE)
- **Langganan Otomatis**: Komponen berlangganan ke koleksi
- **Pembaruan Langsung**: Perubahan disiarkan ke semua klien yang terhubung
- **Resolusi Konflik**: Resolusi berbasis timestamp
- **Deteksi Visibilitas**: Auto-refresh saat tab menjadi aktif

### Pola Langganan

```typescript
// Komponen berlangganan ke perubahan data
useRealtimeSubscription('addresses', (data) => {
  // UI diperbarui secara otomatis
});
```

## Background Jobs

### Tugas Terjadwal

| Job | Jadwal | Tujuan |
|-----|--------|--------|
| **Assignment Cleanup** | Setiap 5 menit | Hapus penugasan yang kedaluwarsa |
| **Territory Aggregates** | Setiap 10 menit | Perbarui statistik kemajuan |
| **Message Processing** | Setiap 30 menit | Kirim notifikasi pesan |
| **Instructions** | Setiap 30 menit | Kirim pesan admin |
| **Note Updates** | Setiap 1 jam | Beri tahu perubahan catatan |
| **Monthly Reports** | Tanggal 1 setiap bulan | Buat laporan Excel (dengan ringkasan AI opsional) |
| **Unprovisioned Users** | Harian 01:00 UTC | Terapkan siklus hidup pengguna (peringatan → nonaktif → hapus) |
| **Inactive Users** | Harian 01:30 UTC | Peringatkan dan nonaktifkan akun yang tidak aktif |

### Kontrol Feature Flag

Semua background job dikontrol oleh feature flags LaunchDarkly:
- Aktifkan/nonaktifkan job tanpa deployment
- Kemampuan rollout bertahap
- Tombol darurat

### Integrasi AI/LLM

Ministry Mapper berintegrasi dengan model GPT OpenAI untuk ringkasan teks yang cerdas. Ini adalah fitur **opsional** yang dikontrol oleh flag LaunchDarkly `enable-report-ai-summary`.

**Model**: GPT (terbaru tersedia melalui `openai-go` SDK v3.29.0)  
**Temperature**: 0.3 (output faktual, deterministik)  
**Format Respons**: Mode JSON untuk data terstruktur

**Kasus Penggunaan:**

| Konteks | Job | Output |
|---------|-----|--------|
| Laporan Excel bulanan | `generateMonthlyReport` | `summary` dan `key_points` per wilayah |
| Digest pesan penerbit | `processMessages` | `overview` dan `key_themes` |
| Digest catatan jemaat | `processNotes` | `overview` dan `key_themes` |

**Graceful Degradation**: Jika `OPENAI_API_KEY` tidak diatur atau flag `enable-report-ai-summary` dinonaktifkan, sistem akan kembali ke laporan dan email tanpa konten yang dihasilkan AI — tidak ada error yang dimunculkan.

### Optimasi Frontend

- **Code Splitting**: Route lazy-loaded
- **Bundle Optimization**: Tree shaking, minifikasi
- **React 19 Compiler**: Memoization otomatis
- **PWA Caching**: Service worker untuk aset statis
- **Virtualized Lists**: React-window untuk dataset besar

### Optimasi Backend

- **Database Indexes**: 25+ indeks untuk kueri cepat
- **Aggregate Caching**: Statistik yang telah dikalkulasikan di JSON
- **Connection Pooling**: Koneksi database yang efisien
- **Transaction Batching**: Kurangi round-trip database

## Arsitektur Deployment

### Kontainerisasi Docker

**Backend:**
- Multi-stage Docker build
- Alpine base untuk footprint kecil
- Volume mounts untuk persistensi data
- Health checks untuk keandalan

**Frontend:**
- Static build yang disajikan oleh CDN
- Edge caching untuk pengiriman cepat
- Kemampuan Progressive Web App

### Opsi Infrastruktur

1. **Layanan Hosted** (Disarankan)
   - ministry-mapper.com
   - Infrastruktur yang dikelola
   - Pembaruan otomatis

2. **Self-Hosted**
   - Railway, Render, DigitalOcean
   - Memerlukan keahlian teknis
   - Kontrol penuh

## Pemantauan & Observabilitas

### Pelacakan Error

- **Integrasi Sentry**: Frontend dan backend
- **Kategorisasi Error**: Berdasarkan keparahan dan tipe
- **Source Maps**: Debugging kode yang di-minifikasi
- **Release Tracking**: Error khusus versi

### Logging

- **Backend**: Log JSON terstruktur
- **Frontend**: Console logging dalam pengembangan
- **Produksi**: Error dikirim ke Sentry
- **Audit Trail**: Aksi pengguna dilacak

## Pertimbangan Skalabilitas

### Keterbatasan Saat Ini

- Keterbatasan single-writer SQLite
- Cocok untuk jemaat kecil hingga menengah (< 1000 pengguna bersamaan)
- Vertical scaling (server yang lebih powerful)

### Skalabilitas Masa Depan

- PocketBase mendukung read replicas
- Dapat bermigrasi ke PostgreSQL jika diperlukan
- CDN caching untuk aset statis
- Edge functions untuk optimasi API

## Keputusan Teknologi

### Mengapa Teknologi Ini?

**React 19**: Fitur terbaru, optimasi otomatis, bundle yang lebih kecil

**TypeScript**: Type safety, dukungan IDE yang lebih baik, lebih sedikit error runtime

**Vite**: Dev server yang cepat, HMR instan, build yang dioptimalkan

**PocketBase**: Self-hosted, tanpa vendor lock-in, mudah dikelola

**Wouter**: Ringan (3KB vs 40KB untuk React Router)

**Bootstrap**: Battle-tested, komponen yang komprehensif, aksesibilitas yang baik

**Leaflet**: Peta gratis, tanpa batas API, ringan

**SQLite**: Mandiri, tanpa server database terpisah, sempurna untuk self-hosting

## Langkah Selanjutnya

- [Pengaturan Backend](backend-setup.md) - Konfigurasi backend
- [Pengaturan Frontend](frontend-setup.md) - Deploy frontend
- [Panduan Keamanan](security.md) - Praktik terbaik keamanan
