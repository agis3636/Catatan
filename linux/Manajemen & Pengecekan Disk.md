# Modul Pembelajaran Linux: Manajemen & Pengecekan Disk

## 1. Pendahuluan

Dalam sistem operasi Linux, mengelola dan memantau ruang penyimpanan (*disk space*) adalah salah satu tugas paling krusial, baik bagi pengguna sehari-hari maupun Administrator Sistem (Sysadmin). Kehabisan ruang disk dapat menyebabkan sistem menjadi lambat, aplikasi error, bahkan kegagalan sistem untuk melakukan *booting*.

Modul ini akan membahas berbagai perkakas (*tools*) di terminal Linux untuk memeriksa penggunaan disk, mulai dari pemantauan global, pelacakan file raksasa, hingga melihat struktur fisik harddisk.

---

## 2. Pemantauan Global dengan `df` (Disk Free)

Perintah `df` digunakan untuk melihat ringkasan penggunaan ruang simpan pada sistem berkas (*file system*) yang sedang aktif atau terpasang (*mounted*).

### A. Perintah Dasar `df -h`

Secara default, `df` menampilkan angka dalam ukuran blok yang sulit dibaca manusia. Tambahan opsi `-h` (*human-readable*) mengubah angka tersebut menjadi satuan Megabyte (M) atau Gigabyte (G).

```bash
df -h

```

### B. Cara Membaca Output `df -h`

Saat perintah dijalankan, akan muncul tabel dengan kolom sebagai berikut:

* **Filesystem:** Nama perangkat keras atau partisi (misal: `/dev/sda1` atau `/dev/nvme0n1p2`).
* **Size:** Total kapasitas penyimpanan pada partisi tersebut.
* **Used:** Ruang penyimpanan yang sudah terpakai.
* **Avail:** Sisa ruang penyimpanan yang masih tersedia.
* **Use%:** Persentase ruang yang telah digunakan.
* **Mounted on:** Lokasi direktori tempat partisi tersebut ditempelkan di dalam struktur Linux (misal: `/` untuk root, atau `/home`).

### C. Variasi Perintah `df` Penting Lainnya

* **`df -T`** : Menambahkan kolom jenis *file system* (seperti ext4, xfs, atau vfat). Sangat berguna untuk mengetahui format partisi.
* **`df -i`** : Menampilkan penggunaan **Inode**.
> **Catatan Penting:** Inode adalah indeks file. Jika jumlah Inode habis (100%), kamu tidak akan bisa membuat file baru lagi, meskipun kapasitas penyimpanan (Gigabyte) di `df -h` masih tersisa banyak.



---

## 3. Investigasi File & Folder dengan `du` (Disk Usage)

Jika `df` memberi tahu bahwa disk kamu penuh, perintah `du` adalah alat investigasi untuk mencari tahu **folder atau file mana yang memakan tempat paling banyak**.

### A. Mengetahui Ukuran Folder Spesifik

Gunakan kombinasi opsi `-s` (*summary*) dan `-h` (*human-readable*) agar Linux hanya menampilkan total ukuran dari folder tersebut tanpa menjabarkan seluruh isinya.

```bash
du -sh /var/log

```

### B. Membatasi Kedalaman Folder (`--max-depth`)

Jika kamu ingin melihat ukuran masing-masing sub-folder di direktori saat ini tanpa pusing melihat sub-folder yang terlalu dalam, gunakan:

```bash
du -h --max-depth=1

```

### C. Trik Mencari 10 File/Folder Terbesar

Gunakan teknik *piping* (`|`) dan *sorting* untuk mengurutkan hasil dari yang terbesar ke terkecil:

```bash
du -ah /home | sort -rh | head -n 10

```

* **`-a`**: Menampilkan file dan folder (bukan folder saja).
* **`sort -rh`**: Mengurutkan berdasarkan angka secara terbalik (*reverse human-numeric* dari besar ke kecil).
* **`head -n 10`**: Hanya mengambil 10 baris teratas hasil urutan.

---

## 4. Navigasi Interaktif dengan `ncdu`

`ncdu` (*NCurses Disk Usage*) adalah utilitas berbasis teks namun bersifat interaktif. Ini adalah cara termudah dan paling efisien untuk bersih-bersih disk lewat terminal karena kita bisa melihat visualisasi grafik batang sederhana.

### A. Instalasi `ncdu`

Karena biasanya tidak terinstall secara bawaan, kamu perlu menginstallnya terlebih dahulu:

* **Ubuntu/Debian:** `sudo apt install ncdu`
* **RHEL/CentOS/Fedora:** `sudo dnf install ncdu`

### B. Cara Penggunaan

Cukup ketik perintah berikut di direktori yang ingin kamu periksa (misalnya di root `/` atau di `/home`):

```bash
ncdu

```

* **Navigasi:** Gunakan panah **Atas/Bawah** untuk memilih folder, dan panah **Kanan (atau Enter)** untuk masuk ke dalam folder tersebut. Gunakan panah **Kiri** untuk kembali ke folder sebelumnya.
* **Menghapus File:** Pilih file/folder sampah yang ingin dibuang, lalu tekan tombol **`d`** pada keyboard. `ncdu` akan meminta konfirmasi sebelum menghapusnya.

---

## 5. Memahami Struktur Fisik dengan `lsblk` & `fdisk`

Sebelum file bisa disimpan, harddisk fisik harus dibagi menjadi partisi. Di bagian ini, kita melihat bagaimana Linux membaca perangkat keras (*hardware*) tersebut.

### A. Perintah `lsblk` (List Block Devices)

Menampilkan struktur disk fisik beserta partisinya dalam bentuk pohon/hierarki yang rapi. Sangat berguna untuk melihat di mana sebuah harddisk atau flashdisk terpasang.

```bash
lsblk

```

* Opsi tambahan **`lsblk -f`** digunakan untuk melihat UUID (ID unik partisi) dan jenis *file system* secara cepat.

### B. Perintah `fdisk -l`

Perintah tingkat lanjut untuk melihat detail teknis seperti ukuran sektor, ID partisi, tipe tabel partisi (GPT atau MBR), dan total kapasitas disk murni sebelum di-*mount*.

```bash
sudo fdisk -l

```

> **Peringatan Keamanan:** `fdisk` sebenarnya adalah alat untuk membuat dan menghapus partisi. Pastikan kamu hanya menggunakan opsi `-l` (*list*) untuk melihat. Jangan mengubah tabel partisi jika belum mahir karena berisiko menghilangkan seluruh data di dalam disk.

---

## 6. Lembar Sontekan (Cheatsheet) Studi Kasus

Berikut adalah ringkasan cepat tindakan yang harus diambil saat menghadapi masalah disk di lapangan:

| Masalah / Skenario | Perintah Solusi | Kegunaan |
| --- | --- | --- |
| Disk tiba-tiba penuh, ingin cek kapasitas global. | `df -h` | Melihat sisa ruang simpan secara umum. |
| Ingin mencari folder mana yang paling boros memori. | `du -h --max-depth=1 | sort -rh` | Mengurutkan folder dari yang terbesar. |
| Ingin bersih-bersih file dengan cepat secara visual. | `ncdu` | Navigasi interaktif dan hapus file via terminal. |
| Baru mencolokkan Harddisk/Flashdisk baru, ingin cek apakah terbaca. | `lsblk` | Melihat daftar perangkat blok (hardware) yang terdeteksi. |
| Muncul error *No space left on device*, tapi kapasitas disk masih sisa. | `df -i` | Memeriksa apakah jumlah *Inode* sudah habis (100%). |

---

## 7. Lembar Latihan Mandiri (Praktikum)

Untuk memperdalam pemahaman, silakan buka terminal Linux kamu dan kerjakan latihan berikut:

1. Jalankan `df -h`. Tuliskan nama partisi yang memiliki persentase penggunaan (`Use%`) paling tinggi di sistemmu!
2. Masuk ke direktori log sistem dengan ketik `cd /var/log`, lalu jalankan `du -sh *`. File atau folder log apa yang ukurannya paling besar?
3. Install `ncdu` di Linux kamu. Buka perintah `ncdu` di dalam folder *home*, lalu coba navigasikan menggunakan tombol panah keyboard untuk melihat file terbesar yang kamu miliki.
