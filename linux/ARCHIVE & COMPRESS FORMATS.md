# 📦 ARCHIVE & COMPRESS FORMATS

---

# 🎯 Tujuan Pembelajaran

Setelah memahami materi ini, mahasiswa mampu:

1. Memahami konsep archive dan compression di Linux
2. Menjelaskan fungsi setiap format file:

   * tar
   * gzip
   * tar.gz
   * zip
   * rar
3. Membedakan karakteristik masing-masing format
4. Menentukan kapan menggunakan format tertentu dalam dunia administrasi sistem

---

# 📌 1. Konsep Dasar

Dalam Linux, ada 2 konsep utama:

## 🔹 1. Archive

Menggabungkan banyak file/folder menjadi satu file.

➡️ Tidak mengecilkan ukuran
➡️ Hanya “mengemas”

Contoh tool:

* tar

---

## 🔹 2. Compression

Mengurangi ukuran file dengan menghapus redundansi data.

Contoh tool:

* gzip
* zip
* rar

---

## 🔥 Analogi sederhana:

| Proses                | Analogi                  |
| --------------------- | ------------------------ |
| Archive               | Masukin barang ke koper  |
| Compression           | Vacuum packing koper     |
| Archive + Compression | Koper + vacuum sekaligus |

---

# 📦 2. TAR (Tape Archive)

## 🔹 Pengertian

`tar` adalah tool Linux untuk:

> Menggabungkan banyak file dan folder menjadi satu file archive

---

## 🔧 Karakteristik TAR:

* Tidak mengompres
* Hanya menggabungkan file
* Format paling dasar di Linux
* Output: `.tar`

---

## 📌 Fungsi utama:

* Backup folder
* Packaging file sistem Linux
* Transfer struktur folder

---

## 💡 Kelebihan:

* Cepat
* Stabil
* Default di hampir semua Linux
* Menjaga struktur folder

---

## ❌ Kekurangan:

* Tidak mengecilkan ukuran file
* Tidak hemat storage

---

# 📦 3. GZIP (gzip)

## 🔹 Pengertian

`gzip` adalah tool untuk:

> Mengompres file agar ukurannya lebih kecil

---

## 📌 Karakteristik:

* Hanya compress, tidak archive
* Biasanya menghasilkan file `.gz`
* Bekerja per file (bukan folder)

---

## 💡 Kelebihan:

* Kompresi cepat
* Efisien untuk file teks
* Ringan untuk server

---

## ❌ Kekurangan:

* Tidak bisa meng-handle folder langsung
* File asli biasanya diganti hasil `.gz`

---

## 🔥 Catatan penting:

`gzip` hampir selalu dipakai bersama `tar`

---

# 📦 4. TAR.GZ (tar + gzip)

## 🔹 Pengertian

`tar.gz` adalah gabungan:

> TAR (archive) + GZIP (compression)

---

## 📌 Prosesnya:

1. Folder → di-archive jadi `.tar`
2. `.tar` → dikompres jadi `.gz`

Hasil akhir:

```bash
file.tar.gz
```

---

## 💡 Karakteristik:

* Format paling umum di Linux server
* Hemat storage
* Cocok untuk backup sistem

---

## 🔥 Kelebihan:

* Archive + compression dalam satu file
* Sangat efisien
* Standar industri Linux

---

## ❌ Kekurangan:

* Tidak native di Windows tanpa tools tambahan

---

# 📦 5. ZIP

## 🔹 Pengertian

`zip` adalah format:

> Archive + Compression dalam satu proses

---

## 📌 Karakteristik:

* Bisa compress folder + file
* Bisa dibuka di Windows, Linux, Mac
* Sangat universal

---

## 💡 Kelebihan:

* Cross-platform
* Mudah digunakan user biasa
* Banyak software support

---

## ❌ Kekurangan:

* Kompresi biasanya kalah dibanding gzip/rar
* Kurang optimal untuk server Linux besar

---

## 🔥 Penggunaan umum:

* Kirim file ke user Windows
* Sharing file dokumen
* Distribusi file ringan

---

# 📦 6. RAR

## 🔹 Pengertian

`rar` adalah format:

> Archive + Compression + Advanced Features

---

## 📌 Karakteristik:

* Kompresi sangat efisien
* Bisa password
* Bisa recovery file
* Bisa split file besar

---

## 💡 Kelebihan:

* Ukuran file sering paling kecil
* Bisa recovery jika file rusak
* Bisa split file (multi-part)
* Cocok untuk file besar

---

## ❌ Kekurangan:

* Tidak open-source penuh
* Tidak default di Linux
* Perlu install tambahan (`rar`, `unrar`)

---

## 🔥 Fitur unggulan RAR:

### 1. Password protection

File bisa dikunci

### 2. Split file

File besar dibagi:

```
file.part1.rar
file.part2.rar
```

### 3. Recovery record

Bisa memperbaiki file corrupt

---

# 📊 7. Perbandingan Semua Format

| Format | Fungsi           | Compression | Archive | Platform | Kelebihan      |
| ------ | ---------------- | ----------- | ------- | -------- | -------------- |
| tar    | archive          | ❌           | ✔       | Linux    | standar Linux  |
| gzip   | compress         | ✔           | ❌       | Linux    | ringan & cepat |
| tar.gz | archive+compress | ✔           | ✔       | Linux    | standar server |
| zip    | archive+compress | ✔           | ✔       | semua OS | universal      |
| rar    | archive+compress | ✔✔          | ✔       | semua OS | fitur lengkap  |

---

# 🧠 8. Cara Memilih Format

## 🔹 Gunakan TAR.GZ jika:

* di Linux server
* backup sistem
* transfer antar server

---

## 🔹 Gunakan ZIP jika:

* kirim ke Windows user
* sharing file dokumen
* kompatibilitas penting

---

## 🔹 Gunakan RAR jika:

* file besar
* butuh password
* butuh split file
* butuh recovery

---

## 🔹 Gunakan TAR jika:

* hanya butuh bundling cepat
* sebelum compress

---

## 🔹 Gunakan GZIP jika:

* kompres file tunggal
* log file server
* file teks besar

---

# 🔥 9. Intuisi Sederhana (biar mahasiswa gampang ingat)

* `tar` = bungkus
* `gzip` = kecilkan
* `tar.gz` = bungkus + kecilkan
* `zip` = universal bungkus + kecilkan
* `rar` = versi “pro + fitur lengkap”

---

# Kesimpulan

Dalam sistem Linux, pemahaman archive dan compression sangat penting karena:

* membantu backup data
* menghemat storage
* mempercepat transfer file
* menjaga struktur file sistem
