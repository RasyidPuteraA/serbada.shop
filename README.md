# Serbada.shop — Dokumentasi /var/www

Ringkasan singkat: direktori `/var/www` berisi satu halaman default Nginx dan satu frontend statis untuk marketplace “Serba Ada Jakarta” yang terhubung ke backend API eksternal (`api.serbada.shop`) serta integrasi pembayaran Midtrans.

## Ringkasan Temuan
- `/var/www/html` hanya memuat halaman default Nginx (`index.nginx-debian.html`).
- `/var/www/serbada.shop` adalah frontend statis (HTML/CSS/JS) dengan halaman akun, checkout, brand, dan utilitas auth/cart.
- Tidak ditemukan berkas build system (`package.json`, `Dockerfile`, `composer.json`) di `/var/www`.
- Integrasi terindikasi: backend API eksternal (`https://api.serbada.shop`), Midtrans Snap untuk pembayaran, dan Cloudinary untuk aset gambar.

## Peta Direktori /var/www
```
/var/www
├── html
│   └── index.nginx-debian.html
└── serbada.shop
    ├── app
    ├── logs
    └── public
        ├── account
        │   ├── account.js
        │   ├── address.html
        │   ├── index.html
        │   ├── notifications.html
        │   ├── orders.html
        │   ├── orders.js
        │   ├── password.html
        │   └── profile.html
        ├── js
        │   ├── brand.js
        │   ├── cart-ui.js
        │   ├── cart.js
        │   ├── checkout.js
        │   ├── login.js
        │   ├── order-detail.js
        │   └── script.js
        ├── shared
        │   ├── css
        │   ├── footer.html
        │   ├── footer.js
        │   └── js
        │       ├── auth.js
        │       ├── cart.js
        │       └── guards.js
        ├── account.html
        ├── brand.html
        ├── checkout.html
        ├── forgot-password.html
        ├── index.html
        ├── login.html
        ├── menu.json
        ├── privacy.html
        ├── reset-password.html
        ├── style.css
        ├── terms.html
        ├── verified.html
        └── verify-fail.html
```

## Daftar Komponen/Project di /var/www
### 1) /var/www/html
- Tujuan/fungsi: halaman default Nginx.
- Stack/teknologi: HTML statis.
- Cara run/build: disajikan langsung oleh Nginx; tidak ada proses build.

### 2) /var/www/serbada.shop
- Tujuan/fungsi: frontend statis marketplace “Serba Ada Jakarta”.
- Stack/teknologi: HTML, CSS, JavaScript vanilla; integrasi API eksternal.
- Cara run/build: static hosting (Nginx/HTTP server); tidak ada bundler atau build pipeline di `/var/www`.

## Flow Sistem Menyeluruh
1. Pengguna mengakses halaman frontend statis dari `/var/www/serbada.shop/public`.
2. Frontend memanggil API eksternal `https://api.serbada.shop` untuk autentikasi, katalog produk, profil, keranjang, alamat, dan pesanan.
3. Saat checkout, frontend men-trigger proses pembuatan order ke API dan memulai pembayaran melalui Midtrans Snap (indikasi endpoint `/payments/midtrans/snap`).
4. Midtrans berinteraksi dengan backend API (webhook tidak terlihat di `/var/www`, diasumsikan dikelola di backend API eksternal).
5. Admin/role khusus diindikasikan oleh endpoint `/admin/invites/accept` dan peran di token; panel admin tidak terlihat di `/var/www`.

## Flow per Folder dan File Penting
### Project: /var/www/html
- `index.nginx-debian.html`: halaman default Nginx; tidak ada flow aplikasi.

### Project: /var/www/serbada.shop
**Folder utama**
- `public/`: root semua aset statis yang di-serve ke browser.
- `app/`: kosong (kemungkinan placeholder untuk backend/SSR di masa lalu atau rencana ke depan).
- `logs/`: kosong (placeholder log; jangan isi dengan data sensitif tanpa kebijakan retensi).

**Entrypoint halaman**
- `public/index.html`: beranda marketplace; memuat `style.css`, `shared/js/auth.js`, `shared/js/cart.js`, dan `js/script.js`.
- `public/checkout.html`: halaman checkout; memuat `shared/js/auth.js`, `shared/js/cart.js`, `js/checkout.js`.
- `public/login.html`, `public/forgot-password.html`, `public/reset-password.html`: flow auth dan recovery.
- `public/account.html` serta `public/account/*.html`: area akun (profil, alamat, pesanan, notifikasi, sandi).
- `public/brand.html`: halaman brand/toko (indikasi pemanggilan data brand dan produk).
- `public/terms.html`, `public/privacy.html`: legal pages.

**Routing & data flow (berbasis JS)**
- `public/shared/js/auth.js`: helper autentikasi (token, role, `GET /me`) dan penyesuaian UI login/logout.
- `public/shared/js/cart.js`: helper keranjang; sinkronisasi cart lokal vs server (`/me/cart`, `/me/cart/sync`, `/me/cart/items`).
- `public/shared/js/guards.js`: guard halaman (depend pada `auth.js`).
- `public/js/script.js`: logic beranda (render produk, wishlist/cart, modal detail, fetch `/products` + `/health`).
- `public/js/checkout.js`: proses checkout (fetch alamat, create order, lalu pembayaran).
- `public/js/login.js`: login/register/verification; indikasi endpoint auth dan admin invite.
- `public/account/account.js`: helper `apiFetch` dan mapping menu akun.
- `public/account/orders.js`: riwayat pesanan dan trigger pembayaran Midtrans Snap.
- `public/js/brand.js`: detail brand dan produk brand.

**Integrasi pembayaran**
- Indikasi penggunaan Midtrans Snap melalui endpoint `/payments/midtrans/snap`.

**Database & backend**
- Tidak ada file konfigurasi database di `/var/www`.
- Semua akses data melalui API eksternal; integrasi DB ada di sisi backend API (tidak terlihat di sini).

**Deployment/infra**
- Tidak ada `Dockerfile`, `docker-compose.yml`, `pm2` ecosystem, atau systemd service di `/var/www`.
- Hosting kemungkinan: Nginx menyajikan `/var/www/serbada.shop/public` sebagai static root.

## Cara Menjalankan (tanpa secrets)
**Produksi (static hosting)**
- Konfigurasikan Nginx/HTTP server untuk mengarahkan document root ke ` /var/www/serbada.shop/public`.
- Pastikan file statis dapat diakses (`index.html`, `style.css`, JS, dan halaman akun/checkout).
- Backend API harus tersedia di `https://api.serbada.shop`.

**Pengembangan lokal (opsional, tanpa build)**
- Gunakan static file server sederhana (mis. `python -m http.server`) di direktori `public/`.
- Pastikan API base mengarah ke backend staging/dev bila diperlukan (indikasi hardcoded ke `https://api.serbada.shop`).

## Catatan Keamanan
- File sensitif terdeteksi: tidak ada `.env` atau key/credential di `/var/www` berdasarkan pencarian pola umum.
- Rekomendasi:
  - Gunakan `.gitignore` untuk `.env*`, `*.key`, `*.pem`, `node_modules/`, `logs/`, `dist/`, `build/`, `uploads/`.
  - Simpan secret hanya di environment/secret manager, bukan di repo atau file statis.
  - Hindari menyimpan token di client-side JS; gunakan backend untuk operasi privileged.

## Troubleshooting
- **Halaman kosong / asset 404**: periksa `root` Nginx mengarah ke ` /var/www/serbada.shop/public`.
- **Login gagal**: backend `https://api.serbada.shop` tidak reachable atau CORS bermasalah.
- **Checkout gagal**: API `/orders` atau `/payments/midtrans/snap` tidak merespons.
- **Cart tidak sinkron**: periksa endpoint `/me/cart` dan token di localStorage.
- **Reset password gagal**: endpoint `/auth/forgot-password` atau `/auth/reset-password` bermasalah.
- **Produksi perlu domain**: pastikan reverse proxy dan TLS sudah benar untuk domain frontend.
