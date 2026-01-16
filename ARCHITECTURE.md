# Arsitektur Sistem Serbada.shop

Dokumen ini menjelaskan arsitektur yang terindikasi dari struktur `/var/www` dan file non-sensitif yang terlihat di frontend statis.

## Gambaran Umum
- **Frontend statis**: `/var/www/serbada.shop/public` berisi halaman HTML/CSS/JS.
- **Backend API eksternal**: dipanggil dari browser melalui base URL `https://api.serbada.shop`.
- **Pembayaran**: integrasi Midtrans Snap di sisi frontend; proses finalisasi pembayaran diproses oleh backend API.

## Komponen & Relasi
1. **Browser (client)**
   - Memuat HTML/CSS/JS statis.
   - Menyimpan token sesi di `localStorage` (indikasi dari `shared/js/auth.js`).
   - Melakukan request API ke backend eksternal.

2. **Frontend statis (Nginx/HTTP server)**
   - Menyajikan file statis dari `public/`.
   - Tidak melakukan render server-side.

3. **Backend API eksternal**
   - Endpoint utama untuk auth, katalog, cart, orders, dan profil.
   - Mengelola database dan integrasi pembayaran (tidak ada file backend di `/var/www`).

4. **Payment Gateway (Midtrans)**
   - Frontend memanggil endpoint backend untuk mendapatkan Snap token/URL.
   - Proses webhook dan update status transaksi di backend (tidak terlihat di repo statis).

## Alur Data Utama
- **Auth & Session**: browser → `/auth/*` → backend; token disimpan lokal; `GET /me` untuk validasi.
- **Katalog & Brand**: browser → `/products` dan `/brands/:slug*` → backend.
- **Cart**: browser → `/me/cart*` untuk sinkronisasi dan CRUD item.
- **Checkout**: browser → `/orders` (buat order) → `/payments/midtrans/snap` (inisiasi pembayaran).

## Batasan
- Tidak ada konfigurasi database, worker, queue, atau webhook di `/var/www`.
- Semua proses backend berada di layanan eksternal.
