# Laporan OPT (/opt)

## Ringkasan
- `/opt/serbada-api` adalah backend API (Node.js + Express + Prisma) untuk Serbada.
- `/opt/serbada-web` berisi frontend admin statis (HTML/JS) untuk pengelolaan produk dan jaringan admin.
- `/opt/n8n` ada tetapi kosong.
- File sensitif terdeteksi: `/opt/serbada-api/.env` (tidak dibuka).

## Struktur Project & Fungsi

### 1) `/opt/serbada-api` (Backend API)
**Entrypoint & server**
- Entrypoint: `src/index.ts`.
- Express server dengan `trust proxy` (indikasi berada di balik reverse proxy; detail infra **Perlu verifikasi**).
- Webhook Midtrans menggunakan `express.raw` khusus path `/payments/midtrans/webhook`.

**Routes (file → endpoint utama)**
- `src/routes/auth.routes.ts`: `/auth/register`, `/auth/login`, `/auth/forgot-password`, `/auth/reset-password`, `/auth/resend-verification`, `/auth/verify-email`.
- `src/routes/auth.verify.routes.ts`: `/auth/verify-email` (redirect success/fail).
- `src/routes/account.routes.ts`: `/me/profile`, `/me/change-password`, `/me/notification-preferences`, `/me/addresses*`.
- `src/routes/product.routes.ts`: `/products`, `/products/:slug` (public).
- `src/routes/cart.routes.ts`: `/me/cart*` (cart CRUD + sync).
- `src/routes/orders.ts`: `/orders`, `/orders/history`, `/orders/by-no/:orderNo`, `/orders/:id`.
- `src/routes/payments.midtrans.routes.ts`: `/payments/midtrans/snap`, `/payments/midtrans/webhook`.
- `src/routes/admin.routes.ts`: `/admin/products*`, `/admin/invites*`, `/admin/children*`, `/master/admins*`.
- `src/routes/brand.routes.ts`: `/brands`, `/brands/:slug`, `/brands/:slug/products` (public).

**Controllers**
- `src/controllers/adminProduct.controller.ts` ada tetapi kosong; logic admin product berada langsung di routes.

**Middleware & Auth**
- `src/middleware/auth.ts`: JWT auth, cek `tokenVersion`, cek soft delete, role USER/SUPERUSER/MASTER (+ legacy ADMIN).
- Middleware role lain: `requireAdminOrMaster`, `requireDirectParentOrMaster`, `requireMaster`.

**Services / Library**
- `src/lib/midtrans.ts`: konfigurasi Midtrans Snap (baca `MIDTRANS_*`).
- `src/lib/mailer.ts`: SMTP via Nodemailer + template HTML di `src/emails/`.
- Util: `src/lib/tokens.ts`, `src/lib/crypto.ts`, `src/lib/users.ts`, `src/lib/password.ts`.

**ORM & Migrations (Prisma)**
- Schema di `prisma/schema.prisma`.
- Migrations di `prisma/migrations/*` (init → auth → order → cart → admin network → ownership produk → email binding admin invite).

**Integrasi Midtrans & webhook**
- Snap token dibuat di `POST /payments/midtrans/snap`.
- Webhook di `POST /payments/midtrans/webhook` memvalidasi signature SHA-512 dan update status order.
- Email pembayaran sukses dikirim saat transisi pertama ke PAID.

**Indikator run/deploy (berbasis file)**
- `package.json` menyediakan `dev` (ts-node-dev), `build` (tsc), `start` (node `dist/index.js`).
- `docker-compose.yml` menyediakan PostgreSQL untuk local dev.
- `src/db.ts` memuat `.env` dari root project dan menggunakan `DATABASE_URL`.
- Proses manager dan konfigurasi server produksi **Perlu verifikasi**.

### 2) `/opt/serbada-web` (Admin frontend statis)
- Struktur utama: `/opt/serbada-web/admin/*` (HTML + JS statis).
- Entry halaman: `login.html`, `dashboard.html`, `product.html`, `downline.html`, `master-tree.html`, `accept-invite.html`.
- `admin/shared/js/auth.js` memakai base API default `https://api.serbada.shop`.
- Tidak ada bundler/build tool yang terindikasi di folder ini.
- Penempatan/hosting admin UI **Perlu verifikasi** (tidak terlihat di `/var/www`).

### 3) `/opt/n8n`
- Folder kosong; penggunaan **Perlu verifikasi**.

## Environment Variables (tanpa nilai)
- Database: `DATABASE_URL`
- Server: `PORT`, `JWT_SECRET`
- Base URL: `APP_BASE_URL`, `API_BASE_URL`
- Email (SMTP): `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE`, `SMTP_USER`, `SMTP_PASS`, `MAIL_FROM`
- Email verification: `EMAIL_VERIFY_TOKEN_TTL_MIN`, `EMAIL_VERIFY_COOLDOWN_MIN`
- Password reset: `PASSWORD_RESET_TOKEN_TTL_MIN`, `PASSWORD_RESET_COOLDOWN_MIN`
- Midtrans: `MIDTRANS_SERVER_KEY`, `MIDTRANS_CLIENT_KEY`, `MIDTRANS_IS_PRODUCTION`
- Admin tree: `MAX_ADMIN_DEPTH`

## Ringkasan Flow Auth & Email (indikator file)
- Register user: `/auth/register` membuat user + token verifikasi email, kirim template `verify-email.html`.
- Verifikasi email: `/auth/verify-email` validasi token hash dan redirect success/fail.
- Lupa password: `/auth/forgot-password` membuat token reset, kirim `reset-password.html`.
- Reset password: `/auth/reset-password` mengubah password dan menaikkan `tokenVersion`.
- Perubahan password: `/me/change-password` mengirim `password-changed.html`.
- Order dibuat: `POST /orders` mengirim email `order-created.html`.
- Pembayaran sukses: webhook Midtrans mengirim `payments-success.html` saat pertama kali PAID.
- Admin invite: `/admin/invites` → `/admin/invites/accept` membuat SUPERUSER + brand profile dan kirim verifikasi.

## Diagram Flow (ringkas)
```text
User/Register
  -> POST /auth/register
  -> Email verify (verify-email.html)
  -> GET /auth/verify-email -> redirect verified/failed

Login
  -> POST /auth/login -> JWT (tokenVersion)
  -> GET /me (validate token + role)

Forgot/Reset Password
  -> POST /auth/forgot-password -> Email reset (reset-password.html)
  -> POST /auth/reset-password -> tokenVersion++ (invalidate old)
  -> Email password changed (password-changed.html)

Order & Payment
  -> POST /orders -> Email order-created.html
  -> POST /payments/midtrans/webhook
     -> Update order status (PENDING/PAID/CANCELLED)
     -> Email payments-success.html (first PAID only)

Admin Invite
  -> POST /admin/invites -> token
  -> POST /admin/invites/accept -> create SUPERUSER + BrandProfile
  -> Email verify (verify-email.html)
```

## Tabel Endpoint Ringkas (method, auth, dampak)

Auth
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| POST | /auth/register | Public | Create user + token verifikasi email + kirim email. |
| POST | /auth/login | Public | Issue JWT (cek verifikasi email & password). |
| POST | /auth/forgot-password | Public | Create reset token + kirim email reset (anti-enumeration). |
| POST | /auth/reset-password | Public | Reset password + tokenVersion++. |
| POST | /auth/resend-verification | Public | Kirim ulang email verifikasi. |
| GET | /auth/verify-email | Public | Validasi token + redirect success/fail. |

Account
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /me | User | Ambil profil user (token required). |
| PUT | /me/profile | User | Update profil; jika email berubah -> reverify. |
| POST | /me/change-password | User | Update password + invalidate token lama. |
| GET | /me/notification-preferences | User | Get/ensure preference record. |
| PUT | /me/notification-preferences | User | Update preference email. |
| GET | /me/addresses | User | List alamat. |
| POST | /me/addresses | User | Create alamat (optional default). |
| PUT | /me/addresses/:id | User | Update alamat. |
| DELETE | /me/addresses/:id | User | Hapus alamat. |
| POST | /me/addresses/:id/default | User | Set alamat default. |

Catalog/Brand
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /products | Public | List produk aktif. |
| GET | /products/:slug | Public | Detail produk aktif. |
| GET | /brands | Public | List brand aktif. |
| GET | /brands/:slug | Public | Detail brand + jumlah produk aktif. |
| GET | /brands/:slug/products | Public | List produk aktif per brand. |

Cart
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /me/cart | User | List cart item. |
| POST | /me/cart/items | User | Add/increment item. |
| PATCH | /me/cart/items/:productId | User | Set qty / remove jika <=0. |
| DELETE | /me/cart/items/:productId | User | Remove item. |
| DELETE | /me/cart | User | Clear cart. |
| POST | /me/cart/sync | User | Replace/merge cart server. |

Orders
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /orders/history | User | Riwayat pesanan user. |
| GET | /orders/by-no/:orderNo | User | Detail order by nomor. |
| POST | /orders | User | Create order + reserve stock + email. |
| GET | /orders/:id | User | Detail order by id. |

Payments (Midtrans)
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| POST | /payments/midtrans/snap | User | Create Snap token untuk pembayaran. |
| GET | /payments/midtrans/webhook | Public | Health endpoint. |
| POST | /payments/midtrans/webhook | Public | Verifikasi signature + update status + email receipt. |

Admin
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /admin/products | Admin/Master | List produk (scoped owner). |
| POST | /admin/products | Admin/Master | Create produk (owner scoped). |
| GET | /admin/products/:id | Admin/Master | Detail produk (scoped). |
| PATCH | /admin/products/:id | Admin/Master | Update produk (scoped). |
| PATCH | /admin/products/:id/price | Admin/Master | Update harga. |
| PATCH | /admin/products/:id/stock | Admin/Master | Update stok. |
| PATCH | /admin/products/:id/enable | Admin/Master | Aktifkan produk. |
| PATCH | /admin/products/:id/disable | Admin/Master | Nonaktifkan produk. |
| POST | /admin/invites | Admin/Master | Buat token invite admin (email-bound). |
| POST | /admin/invites/accept | Public | Accept invite -> create SUPERUSER + brand profile. |
| GET | /admin/children | Admin/Master | List downline (master = all). |
| PATCH | /admin/children/:id/brand | Admin/Master (direct parent ok) | Update brand profile child. |
| POST | /admin/children/:id/warnings | Admin/Master (direct parent ok) | Buat warning ke child. |
| GET | /admin/children/:id/warnings | Admin/Master (direct parent ok) | List warning child. |
| DELETE | /admin/children/:id | Admin/Master (direct parent ok) | Soft delete child (blocked if has downline). |

Master
| Method | Path | Auth | Dampak utama |
| --- | --- | --- | --- |
| GET | /master/admins | Master | List admin tree. |
| PATCH | /master/admins/:id/reparent | Master | Re-parent admin (anti-cycle + depth guard). |

## Catatan Sensitif (tidak dibuka)
- `/opt/serbada-api/.env`
- `/opt/serbada-api/docker-compose.yml` (berisi kredensial DB)

## Observasi tambahan
- Ada dua definisi route `GET /auth/verify-email` di `src/routes/auth.routes.ts` dan `src/routes/auth.verify.routes.ts`. Keduanya ter-mount dan menghasilkan redirect verifikasi yang sama; sebaiknya dikonsolidasikan agar tidak membingungkan.
