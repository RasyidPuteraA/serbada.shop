# Topologi Bisnis (Master Tree, Admin, Brand)

## Sumber Indikator
- Skema data: `prisma/schema.prisma`.
- Endpoint admin: `src/routes/admin.routes.ts`.
- Middleware otorisasi: `src/middleware/auth.ts` dan guard lain.

## Definisi Role (indikator skema)
- **USER**: pengguna umum (pembeli) untuk login, cart, order.
- **SUPERUSER**: admin/merchant yang memiliki brand profile.
- **MASTER**: admin tertinggi dengan akses lintas admin dan reparent tree.
- **ADMIN**: role legacy (masih dikenali pada middleware). Status dan pemakaiannya **Perlu verifikasi**.

> Istilah **Brand/Merchant** secara teknis direpresentasikan oleh **User ber-role SUPERUSER** yang memiliki **BrandProfile**.

## Struktur Tree/Downline
- **Model utama**: `User.parentAdminId`.
- **Relasi**: satu user dapat punya banyak child (`childrenAdmins`).
- **Pola**: ini membentuk pohon (tree) downline admin.
- **Aturan integritas** (indikator endpoint):
  - Anti-cycle saat reparent (mencegah parent menjadi turunannya sendiri).
  - Depth guard melalui `MAX_ADMIN_DEPTH` (batas kedalaman tree).
  - Soft delete pada admin (`deletedAt`) dan disaring di beberapa query.

## Alur Bergabung Admin/Brand (berdasarkan endpoint)
1. **Master/Admin membuat undangan**
   - Endpoint: `POST /admin/invites`.
   - Hasil: token invite yang diikat ke email (email-binding).
2. **Calon admin menerima undangan**
   - Endpoint: `POST /admin/invites/accept`.
   - Membuat user ber-role **SUPERUSER** + **BrandProfile**.
   - Mengirim email verifikasi.
3. **Verifikasi email**
   - Endpoint: `GET /auth/verify-email` (redirect success/fail).
4. **Pengelolaan brand**
   - Parent atau Master dapat update profil brand child: `PATCH /admin/children/:id/brand`.

> Tidak ada indikasi proses approval manual terpisah di backend. Jika ada proses approval di luar API, **Perlu verifikasi**.

## Kewenangan Ringkas
- **MASTER**
  - Akses penuh terhadap daftar admin (`GET /master/admins`).
  - Bisa reparent admin (`PATCH /master/admins/:id/reparent`).
- **SUPERUSER**
  - Mengelola produk miliknya sendiri (`/admin/products*` dengan scope owner).
  - Mengundang admin child (invite email-bound).
- **USER**
  - Akses fitur pembelian: cart, order, alamat, profil.

## Relasi Inti (teks ERD ringkas)
```text
User (role)
  ├─ parentAdminId -> User (tree downline)
  ├─ BrandProfile (0..1) [untuk SUPERUSER]
  ├─ Orders (0..n)
  ├─ CartItems (0..n)
  └─ Addresses (0..n)

AdminInvite
  └─ inviterId -> User

AdminWarning
  ├─ createdByAdminId -> User
  └─ targetAdminId -> User
```

## Referensi Detail
- Model data lengkap: `docs/opt/DATA_MODEL.md`.
- Korelasi sistem: `docs/correlation/OVERVIEW_SYSTEM.md`.
