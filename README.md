# Serbada.shop — Dokumentasi Sistem

Dokumentasi ini memetakan artefak di **VAR (/var/www)** dan **OPT (/opt)** beserta korelasinya.

## Navigasi
- VAR
  - [Laporan VAR](docs/var/README_VAR.md)
  - [Tree VAR](docs/var/TREE_VAR.md)
- OPT
  - [Laporan OPT](docs/opt/README_OPT.md)
  - [Tree OPT](docs/opt/TREE_OPT.md)
  - [Model Data (Ringkas)](docs/opt/DATA_MODEL.md)
- CORRELATION
  - [Overview Sistem](docs/correlation/OVERVIEW_SYSTEM.md)
  - [Data Flow End-to-End](docs/correlation/DATA_FLOW.md)
  - [Topologi Bisnis](docs/correlation/BUSINESS_TOPOLOGY.md)

## Catatan
- Semua klaim dibuat berdasarkan indikator file; jika belum pasti ditandai **Perlu verifikasi**.
- Tidak ada isi `.env` atau secret yang disalin ke dokumentasi.

## Arsitektur Sistem (Frontend → Backend)
**Komponen utama (berdasarkan indikator file):**
- **/var/www/serbada.shop**: frontend publik statis (HTML/CSS/JS).
- **/opt/serbada-api**: backend API (Express + Prisma).
- **/opt/serbada-web**: admin panel statis (HTML/JS) untuk pengelolaan produk & admin.
- **/opt/n8n**: folder kosong; fungsi **Perlu verifikasi**.

**Topologi (ringkas):**
```text
Browser (frontend publik)
  -> /var/www/serbada.shop/public (HTML/CSS/JS)
  -> https://api.serbada.shop (backend API)
       -> Prisma ORM -> PostgreSQL (indikator: prisma/schema.prisma, docker-compose.yml)

Browser (admin panel)
  -> /opt/serbada-web/admin (hosting lokasi **Perlu verifikasi**)
  -> https://api.serbada.shop (endpoint /admin/*, /master/*)

Midtrans
  -> webhook POST /payments/midtrans/webhook (backend API)
```

## Topologi Bisnis & Role
**Definisi role (indikator skema):**
- **MASTER**: admin tertinggi, dapat reparent tree admin.
- **SUPERUSER**: admin/brand (merchant) yang memiliki BrandProfile.
- **USER**: pengguna umum (pembeli).
- **ADMIN**: legacy role yang masih dikenali middleware; penggunaan **Perlu verifikasi**.

**Hubungan & kewenangan ringkas:**
- Tree/downline memakai `parentAdminId` pada User (membentuk hierarchy admin).
- Master dapat melihat semua admin dan melakukan reparent.
- SUPERUSER mengelola produk miliknya (scoped owner) dan dapat membuat invite child admin.
- Brand/Merchant direpresentasikan oleh SUPERUSER + BrandProfile.

Detail lebih lengkap: [Topologi Bisnis](docs/correlation/BUSINESS_TOPOLOGY.md).

## Cara Bergabung sebagai Admin/Brand
**Alur ringkas (indikator endpoint):**
1) Admin/Master membuat undangan: `POST /admin/invites`.
2) Calon admin menerima undangan: `POST /admin/invites/accept`.
3) Sistem membuat user SUPERUSER + BrandProfile dan mengirim verifikasi email.
4) Verifikasi email via `GET /auth/verify-email` (redirect success/fail).

Catatan: tidak ada indikasi approval manual terpisah di backend; **Perlu verifikasi** bila ada proses di luar API.

## Model Data (Ringkas)
Entitas penting: User, BrandProfile, Product, Order, OrderItem, CartItem, Address, AdminInvite, AdminWarning, EmailVerificationToken, PasswordResetToken.

Referensi detail: [Model Data](docs/opt/DATA_MODEL.md) dan [Laporan OPT](docs/opt/README_OPT.md).
