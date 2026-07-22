# Setup Risk Calculator (versi Firebase)

## 1. Isi konfigurasi Firebase
Buka `index.html`, cari bagian ini di dekat atas `<script>` paling bawah:

```js
const firebaseConfig = {
  apiKey: "GANTI_DENGAN_API_KEY",
  authDomain: "GANTI.firebaseapp.com",
  projectId: "GANTI_PROJECT_ID",
  storageBucket: "GANTI.appspot.com",
  messagingSenderId: "GANTI",
  appId: "GANTI"
};
```

Ganti dengan config dari project Firebase kamu (Project settings > General > Your apps > SDK setup and config).

**Bisa pakai project Firebase yang sama dengan Monthly-budget.** Datanya otomatis terpisah karena disimpan di collection `users/{uid}/risk_trades`, tidak akan tercampur dengan data budget tracker kamu. Tidak perlu bikin project baru.

## 2. Aktifkan Email/Password Authentication
Di Firebase Console: **Authentication > Sign-in method > Email/Password > Enable**.
(Kalau sudah aktif untuk Monthly-budget, berarti sudah aktif juga di sini karena satu project.)

## 3. Set Firestore Security Rules
Ini bagian penting — supaya data kamu tidak bisa diakses/diubah orang lain. Buka **Firestore Database > Rules**, pastikan ada rule seperti ini:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/risk_trades/{tradeId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Kalau kamu sudah punya rules untuk collection lain (misal budget tracker), tinggal tambahkan blok `match /users/{userId}/risk_trades/{tradeId}` ini di dalam blok `match /databases/{database}/documents { ... }` yang sudah ada. Jangan menimpa rules yang lama.

Soal API key yang kena Secret Scanning alert kemarin: itu wajar untuk aplikasi web client-side seperti ini — Firebase API key memang didesain untuk dikirim ke browser, bukan rahasia seperti password. Yang benar-benar menjaga keamanan data adalah **Security Rules** di atas, bukan menyembunyikan API key. Selama rules-nya sudah membatasi akses per-user seperti contoh di atas, aman untuk di-commit ke GitHub.

## 4. Deploy ke GitHub Pages
Sama seperti Monthly-budget: upload `index.html` ke repo, aktifkan GitHub Pages dari branch tersebut.

## 5. Pertama kali pakai
Buka aplikasinya di browser (PC atau HP) → klik "Daftar di sini" → isi email + password → otomatis login. Login yang sama dipakai baik dari PC maupun HP, datanya otomatis sinkron real-time karena disimpan di Firestore.
