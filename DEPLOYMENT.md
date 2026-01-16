# Deployment Serbada.shop (Docs-only)

Dokumen ini merangkum langkah deploy berdasarkan indikasi struktur file di `/var/www`.

## 1) Persiapan
- Pastikan folder ` /var/www/serbada.shop/public` berisi seluruh file statis.
- Pastikan backend API tersedia dan dapat diakses dari browser.

## 2) Nginx / Web Server
Contoh arahkan document root ke frontend statis:
- Root: ` /var/www/serbada.shop/public`
- Index: `index.html`

Catatan:
- Pastikan file seperti `style.css`, `shared/js/*`, dan halaman akun/checkout dapat diakses langsung.
- Jika menggunakan reverse proxy untuk API, pastikan path API tidak bentrok dengan file statis.

## 3) Domain & TLS
- Konfigurasikan TLS (mis. Let's Encrypt) untuk domain frontend.
- Pastikan CORS backend mengizinkan domain frontend.

## 4) Pembayaran (Midtrans)
- Frontend memicu inisiasi pembayaran melalui endpoint backend `/payments/midtrans/snap`.
- Webhook Midtrans dan penyimpanan status transaksi dikelola di backend (bukan di `/var/www`).

## 5) Monitoring & Logs
- Direktori ` /var/www/serbada.shop/logs` ada tetapi kosong; gunakan log server-side (Nginx) terpisah.
- Hindari menulis log sensitif ke filesystem tanpa kebijakan retensi dan masking.

## 6) Rollback
- Karena frontend statis, rollback biasanya dilakukan dengan mengganti folder `public/` ke versi sebelumnya.

## 7) Checklist Sebelum Go-Live
- Semua file statis terbaru sudah diunggah.
- Backend API reachable dari browser.
- Payment flow berjalan end-to-end.
- HTTPS aktif dan redirect HTTP → HTTPS bila perlu.
