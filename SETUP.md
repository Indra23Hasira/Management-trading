# Setup & Keamanan — Management Trading

Aplikasi satu file (`index.html`) di GitHub Pages, dengan Firebase Authentication (email/password) dan Firestore untuk menyimpan data per pengguna.

## Coba tanpa akun

Buka aplikasi dengan `?demo` di akhir alamat, atau klik **Coba versi demo** di halaman login.
Mode demo memakai database tiruan di memori browser dengan data contoh. SDK Firebase tidak dimuat sama sekali, tidak ada data yang dikirim ke server, dan sesi login yang ada di browser tidak dibaca.

## Struktur data

Semua data pengguna tersimpan di bawah `users/{uid}/`:

| Koleksi | Dipakai di tab |
|---|---|
| `entry_plans` | Rencana Entry |
| `risk_trades` | Jurnal Trading |
| `dividend_sims` | Simulasi Dividen |
| `dca_analysis` | Timing DCA |
| `fund_history`, `fund_manual`, `fund_positions` | Cek Fundamental |
| `meta/sizing`, `meta/aturanKeuangan` | Batas Posisi, Aturan Keuangan |

## Keamanan

- **Firestore rules** ada di [`firestore.rules`](firestore.rules). Setiap pengguna hanya bisa membaca dan menulis `users/{uid}` miliknya sendiri (dicek lewat UID login). Koleksi yang tidak terdaftar dan akses tanpa login otomatis ditolak.
  Rules di repo tidak aktif sendiri. Setelah diubah, salin isinya ke Firebase Console → Firestore Database → Rules → **Publish**.
- **Konfigurasi Firebase di `index.html` bukan rahasia.** Konfigurasi web Firebase memang dikirim ke browser. Kuncinya dibatasi di Google Cloud Console supaya hanya diterima dari `https://indra23hasira.github.io/*`. Keamanan data dijaga oleh rules di atas.
- **Data harga Yahoo Finance** diambil lewat Cloudflare Worker milik sendiri (`yahoo-proxy`), yang hanya meneruskan permintaan GET ke `query1`/`query2.finance.yahoo.com` dan hanya melayani origin GitHub Pages ini. Proxy publik hanya dipakai sebagai cadangan kalau Worker gagal.
- **Teks dari pengguna** (catatan, alasan, ticker) di-escape sebelum ditampilkan, dan ticker dibatasi ke huruf dan angka.
- **Koneksi ke Radar IDX** (opsional, untuk bantu isi SL/TP) hanya membaca data dari project Firebase `radar-idx-hasira` dengan login akun Radar. Fitur ini tidak tersedia di mode demo.

## Menjalankan sendiri

1. Buat project Firebase, aktifkan **Authentication → Email/Password** dan **Firestore Database**.
2. Ganti `firebaseConfig` di `index.html` dengan konfigurasi web project kamu.
3. Publish isi `firestore.rules` di Firebase Console.
4. Batasi API key ke domain hosting kamu (Google Cloud Console → APIs & Services → Credentials).
5. Hosting `index.html`, `manifest.json`, dan ikon di GitHub Pages atau hosting statis lain.
