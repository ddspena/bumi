# 🌍 Peta Posisi Matahari, Bulan & Waktu Lokal Dunia

Aplikasi web interaktif satu file HTML yang menampilkan **posisi Matahari dan Bulan secara real-time** di atas peta **OpenStreetMap**, dilengkapi dengan **area malam**, **terminator siang/malam**, **nama negara + zona waktu**, **hitungan mundur gerhana**, dan **klik peta untuk melihat waktu lokal, hari, dan tanggal** di lokasi mana pun di dunia.

Cocok untuk desktop maupun **mobile** — panel dapat dilipat agar peta tetap lega di layar kecil.

---

## ✨ Fitur Utama

| Fitur | Keterangan |
|---|---|
| ☀️ **Subsolar Point** | Titik di Bumi yang saat ini tepat berada di bawah Matahari |
| 🌙 **Sublunar Point** | Titik di Bumi yang saat ini tepat berada di bawah Bulan |
| 🌓 **Area malam (night side)** | Belahan Bumi yang sedang gelap diarsir biru gelap transparan |
| 🟡 **Garis terminator** | Garis kuning pemisah siang dan malam |
| 🏳️ **Nama negara + bendera** | Otomatis lewat reverse geocoding, muncul di label peta & panel |
| 🕒 **Zona waktu & waktu lokal** | Untuk titik Matahari dan Bulan |
| 👆 **Klik peta → waktu lokal** | Ketuk/klik di mana saja → popup menampilkan **jam (berdetak tiap detik), hari, dan tanggal** di lokasi tersebut |
| ⏳ **Hitungan mundur gerhana** | Info gerhana terakhir & berikutnya + hitungan mundur real-time |
| 📱 **Mobile-friendly** | Panel *bottom sheet* yang bisa dibuka/tutup dengan tap |
| 🔄 **Auto-refresh** | Posisi diperbarui tiap **30 detik**, jam popup tiap **1 detik** |
| ⏱️ **Timeout API** | Setiap `fetch` punya timeout 10 detik agar UI tidak macet |
| 🧪 **Diagnostik** | Panel kecil menampilkan status library & metode perhitungan yang berhasil |

---
## 📁 Struktur Repositori

📁 repo-anda/
├── 📄 index.html ← aplikasi utama (HTML + CSS + JS satu file)
├── 📄 astronomy.browser.min.js ← library Astronomy Engine (dimuat lokal)
└── 📄 README.md ← file ini

Cara Menggunakan
Di Desktop
Panel kanan menampilkan semua info secara lengkap (Matahari, Bulan, gerhana, legenda).

Klik di peta → popup muncul dengan:

📍 Nama lokasi (bendera + kota + negara)

🕐 Waktu lokal (berdetak tiap detik)

📅 Hari & tanggal dalam bahasa Indonesia

🌐 Nama zona waktu IANA + singkatan

Klik marker ☀️ atau 🌙 → popup menampilkan detail subsolar/sublunar point.

Di Mobile
Panel muncul sebagai strip 60 px di bawah layar dengan judul "🌍 Posisi Langit" dan badge hitungan mundur gerhana.

Tap strip → panel meluncur naik menampilkan seluruh detail.

Tap header lagi → panel turun, peta kembali penuh.

Ketuk peta → popup waktu lokal muncul di titik yang diketuk.

Ketuk di tempat lain → popup berpindah, jam ikut berganti zona waktu.

🧭 Cara Kerja Singkat
1. Menentukan Subsolar / Sublunar Point
Dari library Astronomy Engine diperoleh:

Right Ascension (RA) — posisi benda langit di bola langit

Declination (Dec) — kemiringan terhadap ekuator langit

Dengan Greenwich Sidereal Time (GST) pada saat itu:

text
lng = (RA − GST) × 15°
lat = Declination
Bujur dinormalisasi ke rentang −180°..+180°.

2. Menggambar Area Malam
Untuk setiap bujur λ, latitude terminator:

text
tan(lat) = −cos(λ − λ_sun) / tan(lat_sun)
Kurva terminator dibuat dari 361 titik (setiap 1° bujur), lalu ditutup melalui kutub yang sedang malam:

lat_sun > 0 → kutub selatan sedang malam

lat_sun < 0 → kutub utara sedang malam

Hasilnya adalah poligon besar yang menutupi setengah Bumi bagian gelap.

3. Klik Peta → Waktu Lokal
Saat pengguna klik peta:

Ambil koordinat (lat, lng).

Panggil BigDataCloud untuk nama negara, kota, dan kode negara.

Panggil Open-Meteo untuk nama zona waktu IANA.

Format waktu lokal, hari, dan tanggal memakai Intl.DateTimeFormat dengan timeZone: zonaIANA.

Popup diperbarui setiap 1 detik agar jam terus berdetak.

4. Reverse Geocoding & Zona Waktu
BigDataCloud → nama & kode negara (untuk bendera emoji), nama kota

Open-Meteo → nama zona waktu IANA + singkatan

Info untuk Matahari dan Bulan di-cache selama 10 menit, sehingga hanya ±288 request/hari per API — jauh di bawah batas gratis.

🔌 API yang Digunakan
API	Fungsi	Batas gratis
BigDataCloud Reverse Geocode	Nama negara, kode negara, nama kota	Tanpa API key, rate limit ringan
Open-Meteo	Nama zona waktu IANA & singkatan	10.000 request/hari
OpenStreetMap Tiles	Ubin peta	Penggunaan wajar
Dengan cache 10 menit dan popup hanya saat diklik, aplikasi memakai < 1.000 request/hari secara tipikal.

🛠️ Teknologi
Leaflet 1.9.4 — peta interaktif

Astronomy Engine 2.1.x — perhitungan astronomi akurat

Vanilla JavaScript (ES2017+) — tanpa framework

Intl.DateTimeFormat — format waktu lokal per zona waktu

AbortController — timeout fetch 10 detik

🌑 Data Gerhana
Aplikasi menampilkan dua gerhana terdekat:

Gerhana	Tanggal	Puncak (UTC)	Wilayah Utama
Gerhana Bulan Sebagian (96,3%) — terakhir	28 Agustus 2026	04:14	Amerika, Eropa, Afrika, Asia Barat/Tengah ❌ tidak terlihat dari Indonesia
Gerhana Matahari Cincin 🌋 — berikutnya	6 Februari 2027	16:00	Chili, Argentina, Uruguay, Brasil, Pantai Gading, Ghana, Togo, Benin, Nigeria
Hitungan mundur berjalan setiap detik dan muncul di dua tempat:

Body panel — format lengkap (⏳ X hari Y jam Z menit W detik lagi)

Badge header — format ringkas (⏳ X hari atau ⏳ Xj Ym Zs saat < 1 hari)
