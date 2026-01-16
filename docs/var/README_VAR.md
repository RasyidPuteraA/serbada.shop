# Laporan VAR (/var/www)

## Ringkasan
- `/var/www/html` berisi halaman default Nginx (`index.nginx-debian.html`).
- `/var/www/serbada.shop` berisi frontend statis (HTML/CSS/JS) untuk Serbada.
- Tidak ada indikasi build system di `/var/www` (tidak ditemukan `package.json`, `Dockerfile`, atau konfigurasi build lain di folder ini). Jika ada pipeline build, **Perlu verifikasi** di lokasi lain.

## Struktur & Fungsi Folder
### `/var/www/html`
- Fungsi: halaman default Nginx.
- Stack: HTML statis.

### `/var/www/serbada.shop`
- `public/`: root aset statis yang di-serve ke browser.
- `public/js/`: logic halaman (landing, checkout, login, brand, order detail).
- `public/account/`: halaman akun (profil, alamat, pesanan, notifikasi, password).
- `public/shared/`: helper auth/cart/guard dan CSS bersama.
- `app/`: kosong (placeholder). Fungsi **Perlu verifikasi**.
- `logs/`: kosong (placeholder). Penggunaan **Perlu verifikasi**.

## Flow Frontend/Static (berdasarkan indikator file)
### Halaman utama
- `public/index.html` memuat `public/js/script.js` untuk listing produk dan brand.
- `public/brand.html` memuat `public/js/brand.js` untuk detail brand + produk brand.

### Auth & akun
- `public/login.html` dan `public/js/login.js` mengakses API auth: `POST /auth/login`, `POST /auth/register`, `POST /auth/resend-verification`, dan `POST /admin/invites/accept`.
- `public/shared/js/auth.js` mendefinisikan base API default `https://api.serbada.shop` dan helper `GET /me` untuk validasi token.

### Keranjang & checkout
- `public/shared/js/cart.js` mengakses `/me/cart`, `/me/cart/items`, `/me/cart/sync`.
- `public/js/checkout.js` memuat alamat lewat `GET /me/addresses` lalu membuat order via `POST /orders`.

### Pembayaran (Midtrans)
- `public/account/index.html` memuat script Midtrans Snap dari `https://app.sandbox.midtrans.com/snap/snap.js`.
- `public/account/orders.js` memanggil `POST /payments/midtrans/snap` dan menjalankan `window.snap.pay`.
- Lingkungan produksi vs sandbox **Perlu verifikasi** (indikator file hanya menunjukkan sandbox).

### Fallback data
- `public/js/script.js` memiliki fallback ke `public/menu.json` saat API gagal (indikator komentar di file).

## Kaitan ke Backend Eksternal (indikator)
- Base API hardcoded/default mengarah ke `https://api.serbada.shop` di `public/shared/js/auth.js` dan JS lain.
- Endpoint yang digunakan dari frontend:
  - Auth: `/auth/login`, `/auth/register`, `/auth/resend-verification`, `/auth/forgot-password`, `/auth/reset-password`.
  - User: `/me`, `/me/addresses`, `/me/cart`, `/me/cart/items`, `/me/cart/sync`.
  - Catalog/Brand: `/products`, `/brands`, `/brands/:slug`, `/brands/:slug/products`.
  - Orders/Payment: `/orders`, `/payments/midtrans/snap`.
- Penempatan backend API (host, container, service) **Perlu verifikasi**.

## Catatan Keamanan
- Tidak ditemukan file `.env` di `/var/www`.
- Jika ada konfigurasi rahasia, lokasinya **Perlu verifikasi**.
