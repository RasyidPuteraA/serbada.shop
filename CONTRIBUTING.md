# Contributing Guide — Serbada.shop

Terima kasih telah tertarik untuk berkontribusi pada project **Serbada.shop**.

Repository ini berfokus pada **dokumentasi sistem** agar pengembangan dan pemeliharaan project dapat dilakukan secara berkelanjutan oleh siapa pun di masa depan.

---

## 🎯 Tujuan Repository Ini

- Menjadi **sumber kebenaran (source of truth)** dokumentasi sistem Serbada.shop
- Menjelaskan:
  - Struktur frontend (`/var/www`)
  - Struktur backend & service (`/opt`)
  - Korelasi sistem dan topologi bisnis
- Memudahkan onboarding developer, AI assistant, maupun maintainer baru

⚠️ Repository ini **BUKAN** tempat menyimpan source code aplikasi.

---

## 📁 Struktur Dokumentasi
.
├── README.md # Ringkasan utama project
├── docs/
│ ├── var/ # Dokumentasi frontend / web root
│ ├── opt/ # Dokumentasi backend & service
│ └── correlation/ # Korelasi sistem & alur end-to-end
├── ARCHITECTURE.md # Ringkasan arsitektur tingkat tinggi
├── DEPLOYMENT.md # Ringkasan deployment
├── GLOSSARY.md # Istilah & terminologi
├── CONTRIBUTING.md # Panduan kontribusi
└── DECISIONS.md # Catatan keputusan arsitektur


---

## 🧭 Aturan Umum Kontribusi

### ✅ Yang BOLEH dilakukan
- Menambahkan atau memperbaiki dokumentasi (`.md`)
- Merapikan penjelasan agar lebih mudah dipahami
- Menambahkan diagram teks / ASCII / flow
- Menambahkan referensi ke dokumen lain dalam repo
- Menambahkan klarifikasi hasil pembacaan struktur sistem

### ❌ Yang TIDAK BOLEH dilakukan
- Menambahkan source code aplikasi
- Menambahkan file `.env`, token, API key, password, credential
- Menyertakan data user pribadi (email, nomor HP, alamat, dsb)
- Menyertakan data sensitif pembayaran atau autentikasi

---

## 🧠 Prinsip Dokumentasi

Saat menulis atau mengubah dokumentasi, ikuti prinsip berikut:

1. **Berdasarkan fakta struktur sistem**
   - Jelaskan apa yang terlihat dari folder, flow, dan konfigurasi non-rahasia
   - Jika belum pasti, tulis: *“Perlu verifikasi”*

2. **Jangan berasumsi berlebihan**
   - Hindari klaim tanpa indikator teknis

3. **Pisahkan level penjelasan**
   - Ringkasan → README utama
   - Detail → `docs/var`, `docs/opt`, `docs/correlation`

4. **Utamakan keberlanjutan**
   - Tulis seolah pembaca tidak mengenal project sama sekali

---

## 🔀 Workflow Kontribusi

1. Fork / clone repository
2. Buat perubahan pada file dokumentasi
3. Pastikan:
   - Tidak ada informasi sensitif
   - Struktur tetap konsisten
4. Commit dengan pesan jelas, contoh:
    docs: clarify admin onboarding flow
    docs(opt): update backend data model explanation
5. Buat Pull Request (jika kolaborasi)

---

## 🤖 Kontribusi oleh AI / Assistant

Repository ini **AI-friendly**.

Jika menggunakan AI (Codex, ChatGPT, dsb):
- Gunakan AI untuk **membaca dan merangkum**, bukan mengarang
- Selalu lakukan **review manual** sebelum commit
- Pastikan bahasa tetap profesional dan netral

---

Terima kasih telah membantu menjaga dokumentasi Serbada.shop tetap rapi dan berkelanjutan.

