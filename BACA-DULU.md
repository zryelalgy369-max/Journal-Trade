# Jurnal Trading — proyek aplikasi Android

Aplikasi ini adalah "bingkai" Android sederhana (Kotlin) yang membuka jurnal HTML kamu layar penuh,
tanpa bilah Claude, tanpa hosting, dan bisa jalan offline. Semua isi jurnal tetap HTML/CSS/JavaScript.

PENTING: proyek ini belum pernah dibangun atau dijalankan di perangkat nyata (tidak bisa dilakukan
di tempat saya membuatnya). Kalau ada error saat build, salin pesannya dan tanyakan, itu bagian normal
dari belajar. Hanya jembatan unduhan (JavaScript) yang sudah saya uji di browser.

## 1. Yang perlu disiapkan (semua gratis)
- Komputer Windows, Mac, atau Linux dengan RAM minimal 8 GB dan ruang kosong sekitar 10 GB.
- Android Studio (unduh di developer.android.com/studio). Sudah termasuk JDK dan Gradle.
- HP Android 10 atau lebih baru (proyek ini diatur minSdk 29).
- Kabel USB (opsional) atau cukup kirim file APK ke HP lewat WhatsApp/Drive/kabel.

## 2. Membangun APK (langkah demi langkah)
1. Ekstrak zip ini ke sebuah folder, misalnya `Documents/JurnalTrading`.
2. Buka Android Studio, pilih **Open**, lalu pilih folder `JurnalTrading` (yang berisi file `settings.gradle.kts`).
3. Tunggu "Gradle sync" selesai (pertama kali bisa 5 sampai 15 menit karena mengunduh banyak file).
   Kalau Android Studio bertanya soal versi Gradle atau JDK, terima saran bawaannya.
4. Menu **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
5. Setelah selesai, klik **locate** di notifikasi kanan bawah. File-nya `app/build/outputs/apk/debug/app-debug.apk`.
6. Kirim `app-debug.apk` ke HP, buka, dan izinkan "pasang dari sumber tidak dikenal" kalau diminta.
   Kalau muncul peringatan Play Protect, pilih "Tetap pasang". Itu normal untuk aplikasi buatan sendiri.

Mau mencoba langsung dari Android Studio: colokkan HP (aktifkan Opsi Pengembang dan USB debugging), lalu tekan tombol Run (segitiga hijau).

## 3. Alternatif tanpa komputer kuat: GitHub Actions (belum diuji)
Folder `.github/workflows/build.yml` berisi resep untuk membangun APK di server GitHub (gratis untuk repositori publik).
Unggah seluruh isi folder proyek ke repositori GitHub, buka tab **Actions**, jalankan "Build APK", lalu
unduh hasilnya di bagian Artifacts. Cara ini bagus untuk belajar, tapi lebih sulit dilacak kalau gagal.

## 3b. Mengunggah ke GitHub (langkah rinci)
1. Ekstrak zip. Buka folder `JurnalTrading` sampai terlihat `app`, `gradle`, `settings.gradle.kts`, dan seterusnya.
2. Di repositori GitHub baru (Public), pilih **uploading an existing file**.
3. Seret SEMUA isi folder `JurnalTrading` (bukan folder `JurnalTrading` itu sendiri) ke halaman unggah. Struktur folder akan ikut terbawa.
4. Folder `.github` bernama diawali titik dan sering tidak ikut terseret. Kalau di daftar unggahan tidak ada `.github/workflows/build.yml`, buat manual:
   **Add file > Create new file**, ketik nama `.github/workflows/build.yml` (garis miring otomatis membuat folder), lalu tempel isi file `build.yml` yang dikirim terpisah.
5. Klik **Commit changes**. Buka tab **Actions**, pilih "Build APK", klik **Run workflow**.
6. Setelah hijau, unduh `jurnal-trading-apk` di bagian Artifacts di bawah halaman run.
Cek cepat sebelum Commit: file `app/src/main/assets/index.html` dan `app/src/main/java/id/jurnal/trading/MainActivity.kt` harus ada di daftar.

## 3c. Memperbarui jurnal lewat GitHub
Ganti isi `app/src/main/assets/index.html` (ikon pensil di GitHub), Commit, tunggu build, unduh APK baru, pasang di atas yang lama. Export JSON dulu sebagai cadangan.

## 4. Peta proyek: apa fungsi tiap file
| File | Fungsi |
|---|---|
| `app/src/main/assets/index.html` | Jurnalmu sendiri. Ganti file ini untuk memperbarui isi aplikasi. |
| `app/src/main/java/id/jurnal/trading/MainActivity.kt` | Satu-satunya kode Kotlin: membuat WebView, memuat jurnal, dan menyimpan file unduhan. |
| `app/src/main/AndroidManifest.xml` | Kartu identitas aplikasi: nama, ikon, izin (hanya INTERNET), layar utama. |
| `app/src/main/res/mipmap-*/ic_launcher.png` | Ikon aplikasi dalam beberapa ukuran. |
| `app/src/main/res/values/*.xml` | Nama aplikasi, warna latar, tema. |
| `app/build.gradle.kts` | Pengaturan build: nama paket, versi, minSdk, pustaka yang dipakai. |
| `settings.gradle.kts`, `build.gradle.kts` | Pengaturan proyek tingkat atas dan versi plugin. |

## 5. Cara kerja singkat (bahan belajar)
- **WebView** = browser mini di dalam aplikasimu. Jurnal berjalan di dalamnya persis seperti di Chrome.
- **WebViewAssetLoader** melayani file `assets/` lewat alamat `https://appassets.androidplatform.net/`.
  Dengan begitu jurnal punya "asal" tetap dan `localStorage` (tempat data jurnal) aman.
- **Jembatan unduhan**: JavaScript di `index.html` memanggil `AndroidBridge.saveFile(...)`, lalu Kotlin menyimpannya ke folder Download lewat MediaStore.
- **onShowFileChooser** membuka pemilih file saat jurnal meminta Import JSON.
- Data jurnal tersimpan di penyimpanan internal aplikasi. **Menghapus aplikasi menghapus datanya**, jadi rajin Export JSON.

## 6. Cara memperbarui jurnal
1. Minta versi terbaru jurnal (file HTML) dari Claude.
2. Ganti isi `app/src/main/assets/index.html`. Pastikan potongan script "Versi aplikasi Android" (shim unduhan) di bagian atas file tetap ada.
3. Naikkan `versionCode` di `app/build.gradle.kts` (mis. 1 menjadi 2), bangun ulang APK, lalu pasang di atas yang lama. Data tetap ada selama nama paket dan tanda tangan sama.
   Peringatan: APK debug dari komputer berbeda memakai kunci debug berbeda, jadi memasang di atas versi lama bisa gagal. Bangun selalu dari komputer yang sama.

## 7. Batasan yang perlu kamu tahu
- Hanya Android. iPhone tidak bisa memasang APK.
- Sinkronisasi akun dan foto chart (fitur yang hanya ada di versi Claude) tidak aktif di aplikasi ini. Data hanya di HP itu.
- Font Google dimuat dari internet. Tanpa internet jurnal tetap jalan dengan font cadangan.
- APK "debug" cocok untuk dibagikan ke orang terdekat. Menerbitkan di Play Store butuh APK/AAB rilis bertanda tangan dan akun developer berbayar (tidak perlu untuk pemakaian pribadi).
- Fitur gelembung/overlay di atas aplikasi lain belum ada. Itu bisa jadi langkah belajar berikutnya (butuh izin overlay dan Service).

## 8. Error yang sering muncul
- "SDK location not found": di Android Studio, buka **File > Project Structure** atau biarkan ia mengunduh SDK saat diminta.
- "Unsupported class file major version": JDK tidak cocok. Di **Settings > Build Tools > Gradle**, pilih Gradle JDK versi 17 atau lebih (JetBrains Runtime bawaan sudah benar).
- Layar putih saat dibuka: cek tab Logcat dengan filter `chromium` untuk pesan error dari jurnal.
