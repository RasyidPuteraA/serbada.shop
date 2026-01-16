# Data Flow End-to-End

## Alur Utama (indikator file)
1. **User membuka frontend publik**
   - Aset statis disajikan dari `/var/www/serbada.shop/public` (VAR).
2. **Frontend memanggil backend API**
   - JS memakai base `https://api.serbada.shop` (indikator di `public/shared/js/auth.js`).
   - Contoh endpoint: `/products`, `/brands`, `/me`, `/orders`.
3. **Order dibuat di backend**
   - Frontend memanggil `POST /orders` (indikator di `public/js/checkout.js`).
   - Backend membuat order + item + pengurangan stok (indikator di `src/routes/orders.ts`).
4. **Pembayaran Midtrans (Snap)**
   - Frontend akun memanggil `POST /payments/midtrans/snap` (indikator di `public/account/orders.js`).
   - Midtrans Snap di-load dari `https://app.sandbox.midtrans.com/snap/snap.js` (indikator di `public/account/index.html`).
5. **Webhook dari Midtrans ke backend**
   - Midtrans memanggil `POST /payments/midtrans/webhook` (indikator di `src/routes/payments.midtrans.routes.ts`).
   - Backend memvalidasi signature lalu update status order dan kirim email receipt.
6. **User melihat status order**
   - Frontend mengambil riwayat via `GET /orders/history` dan detail via `GET /orders/:id` (indikator di backend routes; penggunaan frontend **Perlu verifikasi** jika tidak terlihat langsung di JS).

## Catatan
- Mapping domain aktual (frontend ↔ backend ↔ webhook) **Perlu verifikasi** di konfigurasi server/reverse proxy.
- Email notifikasi dikirim via SMTP (`src/lib/mailer.ts`), namun konfigurasi server email **Perlu verifikasi**.
