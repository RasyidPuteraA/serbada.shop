# Overview Sistem (VAR ↔ OPT)

## Pemisahan Lokasi
- **VAR (/var/www)**: frontend statis untuk domain utama Serbada (`/var/www/serbada.shop/public`).
- **OPT (/opt)**:
  - `serbada-api`: backend API (Express + Prisma) untuk autentikasi, katalog, order, dan pembayaran.
  - `serbada-web`: frontend admin statis (panel admin).
  - `n8n`: folder kosong (fungsi **Perlu verifikasi**).

## Korelasi Peran
- **Frontend publik (VAR)** memanggil API di `https://api.serbada.shop` (indikator di JS: `shared/js/auth.js`, `js/*`).
- **Backend (OPT/serbada-api)** menyediakan endpoint yang dipakai frontend publik dan admin.
- **Frontend admin (OPT/serbada-web)** menggunakan endpoint admin di API (`/admin/*`, `/master/*`). Lokasi hosting admin UI **Perlu verifikasi** karena tidak terlihat di `/var/www`.
- **Pembayaran**: frontend publik memulai pembayaran melalui `POST /payments/midtrans/snap`, backend menangani webhook `POST /payments/midtrans/webhook`.

## Catatan Integrasi Eksternal
- Midtrans Snap di-load dari `https://app.sandbox.midtrans.com/snap/snap.js` pada halaman akun (indikator file di VAR). Lingkungan produksi **Perlu verifikasi**.
