# Architecture & System Decisions — Serbada.shop

Dokumen ini mencatat **keputusan-keputusan penting** yang diambil dalam pengembangan dan dokumentasi Serbada.shop.

Tujuannya adalah agar:
- Maintainer masa depan memahami *“kenapa sistem dibuat seperti ini”*
- Perubahan besar tidak mengulang diskusi yang sama
- AI atau developer baru punya konteks historis

---

## ADR-001 — Pemisahan Direktori VAR dan OPT

**Status:** Accepted  
**Tanggal:** 2026-01

### Keputusan
Sistem dipahami dan didokumentasikan dengan pemisahan:
- **`/var/www`** → frontend statis / web root / hasil build
- **`/opt`** → source code backend, service, automation

### Alasan
- `/var` bersifat runtime & deployment-oriented
- `/opt` bersifat source & logic-oriented
- Memudahkan analisis, debugging, dan onboarding

### Konsekuensi
- Dokumentasi dipisah ke `docs/var` dan `docs/opt`
- Korelasi dijelaskan di `docs/correlation`

---

## ADR-002 — Repository Dokumentasi Terpisah dari Source Code

**Status:** Accepted  
**Tanggal:** 2026-01

### Keputusan
Repository `serbada.shop` digunakan sebagai **docs-only repository**.

### Alasan
- Menghindari kebocoran credential
- Dokumentasi bisa dibuka publik tanpa risiko
- AI & developer dapat membaca tanpa akses server

### Konsekuensi
- Source code tetap berada di server / repo privat
- Repo ini fokus pada struktur, flow, dan konsep

---

## ADR-003 — Penulisan Topologi Bisnis (Master / Admin / Brand)

**Status:** Accepted  
**Tanggal:** 2026-01

### Keputusan
Topologi bisnis dan role hierarchy dijelaskan eksplisit dalam dokumentasi.

### Alasan
- Sistem marketplace memiliki relasi non-trivial (master tree)
- Tanpa dokumentasi, logika bisnis sulit dipahami
- Banyak project gagal dipelihara karena topologi bisnis tidak jelas

### Konsekuensi
- Dokumentasi mencakup:
  - Role & kewenangan
  - Alur onboarding admin/brand
  - Relasi data (tanpa PII)

---

## ADR-004 — Tidak Menyertakan Data User & Credential

**Status:** Accepted  
**Tanggal:** 2026-01

### Keputusan
Dokumentasi **tidak pernah** memuat:
- Data user
- Credential
- Token
- Informasi autentikasi

### Alasan
- Keamanan
- Kepatuhan privasi
- Dokumentasi bersifat publik & jangka panjang

### Konsekuensi
- Contoh menggunakan data dummy
- Data nyata hanya dijelaskan dalam bentuk struktur

---

## ADR-005 — Dokumentasi AI-Friendly

**Status:** Accepted  
**Tanggal:** 2026-01

### Keputusan
Dokumentasi ditulis agar:
- Mudah dipahami manusia
- Mudah dianalisis AI (struktur jelas, heading konsisten)

### Alasan
- AI akan menjadi bagian dari maintenance workflow
- Dokumentasi yang ambigu menyulitkan otomatisasi

### Konsekuensi
- Bahasa jelas & eksplisit
- Struktur markdown konsisten
- Minim jargon tanpa definisi

---

Dokumen ini akan diperbarui jika ada keputusan arsitektur besar di masa depan.
