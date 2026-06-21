# MODUL PRAKTIKUM LINUX ADMIN

## Archive, Compress, dan Backup di Linux

### Tujuan Pembelajaran

Setelah praktik ini, mahasiswa diharapkan mampu:

1. Memahami perbedaan **archive**, **compress**, dan **backup**.
2. Membuat file dan direktori untuk bahan praktik.
3. Menggunakan `tar` untuk membungkus banyak file/folder.
4. Menggunakan `gzip` untuk melakukan kompresi.
5. Menggunakan `zip` dan `unzip` untuk membuat dan membuka file `.zip`.
6. Menggunakan `rsync` untuk backup folder secara efisien.
7. Melakukan proses backup dan restore sederhana seperti pekerjaan Linux administrator.

---

# 1. Konsep Dasar

Sebelum praktik, mahasiswa harus paham dulu bedanya.

## A. Archive

**Archive** adalah proses membungkus banyak file atau folder menjadi satu file.

Contoh:

```bash
data/
├── file1.txt
├── file2.txt
└── laporan/
```

Dibungkus menjadi:

```bash
data.tar
```

Perintah yang sering dipakai:

```bash
tar
```

Ingat:

> `tar` itu seperti memasukkan banyak barang ke dalam satu kardus. Ukurannya belum tentu lebih kecil, tapi jadi lebih rapi karena semua file terkumpul jadi satu.

---

## B. Compress

**Compress** adalah proses mengecilkan ukuran file.

Contoh:

```bash
data.tar
```

Dikompres menjadi:

```bash
data.tar.gz
```

Perintah yang sering dipakai:

```bash
gzip
```

Ingat:

> `gzip` itu seperti memvakum kardus supaya ukurannya lebih kecil.

---

## C. Archive + Compress

Kalau digabung:

```bash
tar -czvf data.tar.gz data/
```

Artinya:

* `tar` membungkus folder `data/`
* `gzip` mengompres hasil bungkusannya
* hasil akhirnya menjadi `data.tar.gz`

---

## D. Backup

**Backup** adalah proses menyalin data ke lokasi lain agar data tetap aman.

Contoh:

```bash
rsync -av data/ backup/
```

Perintah yang sering dipakai:

```bash
rsync
```

Ingat:

> `rsync` itu copy yang lebih pintar. Kalau file sudah ada dan belum berubah, dia tidak copy ulang semuanya. Dia hanya copy perubahan baru.

---

# 2. Persiapan Praktikum

Masuk ke home directory:

```bash
cd ~
```

Buat folder praktik:

```bash
mkdir praktikum-archive
cd praktikum-archive
```

Buat beberapa folder:

```bash
mkdir data
mkdir backup
mkdir hasil
mkdir restore
```

Cek isi folder:

```bash
ls
```

Hasil kira-kira:

```bash
backup  data  hasil  restore
```

Penjelasan:

* `data` = folder utama yang akan diarsipkan
* `backup` = tempat hasil backup
* `hasil` = tempat menyimpan file archive/compress
* `restore` = tempat percobaan extract

---

# 3. Membuat File dan Direktori Bahan Praktik

Masuk ke folder `data`:

```bash
cd data
```

Buat beberapa file:

```bash
echo "Ini adalah file laporan 1" > laporan1.txt
echo "Ini adalah file laporan 2" > laporan2.txt
echo "Data mahasiswa Linux Admin" > mahasiswa.txt
echo "Catatan praktikum archive compress backup" > catatan.txt
```

Buat folder tambahan:

```bash
mkdir dokumen
mkdir gambar
mkdir log
```

Isi folder `dokumen`:

```bash
echo "Dokumen tugas Linux" > dokumen/tugas.txt
echo "Dokumen absensi mahasiswa" > dokumen/absensi.txt
```

Isi folder `log`:

```bash
echo "Log sistem hari ini" > log/system.log
echo "Log aplikasi hari ini" > log/app.log
```

Buat file ukuran agak besar untuk melihat efek kompresi:

```bash
yes "Belajar Linux Archive Compress Backup" | head -n 1000 > dokumen/materi.txt
```

Kembali ke folder utama praktikum:

```bash
cd ..
```

Cek struktur folder:

```bash
find data
```

Hasil kira-kira:

```bash
data
data/laporan1.txt
data/laporan2.txt
data/mahasiswa.txt
data/catatan.txt
data/dokumen
data/dokumen/tugas.txt
data/dokumen/absensi.txt
data/dokumen/materi.txt
data/gambar
data/log
data/log/system.log
data/log/app.log
```

---

# 4. Praktik `tar` untuk Archive

## A. Membuat File Archive `.tar`

Perintah:

```bash
tar -cvf hasil/data.tar data/
```

Penjelasan:

```bash
tar -cvf hasil/data.tar data/
```

Artinya:

* `tar` = program untuk archive
* `c` = create, membuat archive baru
* `v` = verbose, menampilkan proses di layar
* `f` = file, menentukan nama file hasil archive
* `hasil/data.tar` = nama file archive yang dibuat
* `data/` = folder yang dimasukkan ke archive

Cek hasilnya:

```bash
ls -lh hasil/
```

Hasil kira-kira:

```bash
-rw-r--r-- 1 user user 30K Jun 21  data.tar
```

---

## B. Melihat Isi File `.tar`

Sebelum extract, admin biasanya mengecek dulu isi file archive.

Gunakan:

```bash
tar -tf hasil/data.tar
```

Penjelasan:

* `t` = list isi archive
* `f` = file archive yang dibaca

Hasil:

```bash
data/
data/laporan1.txt
data/laporan2.txt
data/mahasiswa.txt
data/catatan.txt
data/dokumen/
data/dokumen/tugas.txt
data/dokumen/absensi.txt
data/dokumen/materi.txt
data/log/
data/log/system.log
data/log/app.log
```

Kalau ingin tampil lebih detail:

```bash
tar -tvf hasil/data.tar
```

---

## C. Extract File `.tar`

Buat folder untuk restore:

```bash
mkdir restore/tar
```

Extract ke folder tersebut:

```bash
tar -xvf hasil/data.tar -C restore/tar
```

Penjelasan:

* `x` = extract
* `v` = tampilkan proses
* `f` = file archive
* `-C restore/tar` = hasil extract ditaruh ke folder `restore/tar`

Cek hasilnya:

```bash
find restore/tar
```

Hasilnya akan muncul folder `data` beserta isinya.

---

# 5. Praktik `tar.gz` untuk Archive + Compress

File `.tar` hanya membungkus. Sekarang kita buat versi yang sekaligus dikompres.

## A. Membuat File `.tar.gz`

Perintah:

```bash
tar -czvf hasil/data.tar.gz data/
```

Penjelasan:

* `c` = create
* `z` = compress dengan gzip
* `v` = tampilkan proses
* `f` = nama file hasil
* `hasil/data.tar.gz` = file hasil archive + compress
* `data/` = folder sumber

Cek ukuran file:

```bash
ls -lh hasil/
```

Bandingkan ukuran:

```bash
data.tar
data.tar.gz
```

Biasanya `data.tar.gz` lebih kecil daripada `data.tar`.

Konsepnya:

```bash
data/        → dibungkus jadi data.tar
data.tar     → dikompres jadi data.tar.gz
```

---

## B. Melihat Isi File `.tar.gz`

Gunakan:

```bash
tar -tzvf hasil/data.tar.gz
```

Penjelasan:

* `t` = lihat isi
* `z` = file menggunakan gzip
* `v` = detail
* `f` = nama file

---

## C. Extract File `.tar.gz`

Buat folder restore baru:

```bash
mkdir restore/targz
```

Extract:

```bash
tar -xzvf hasil/data.tar.gz -C restore/targz
```

Penjelasan:

* `x` = extract
* `z` = gzip
* `v` = tampilkan proses
* `f` = file archive
* `-C restore/targz` = lokasi tujuan extract

Cek:

```bash
find restore/targz
```

---

# 6. Praktik `gzip`

`gzip` digunakan untuk mengompres file.

Penting:

> `gzip` biasanya dipakai untuk file tunggal, bukan langsung untuk folder. Kalau mau kompres folder, biasanya foldernya dibungkus dulu pakai `tar`, baru dikompres pakai `gzip`.

---

## A. Kompres File Tunggal

Copy file dulu ke folder `hasil`:

```bash
cp data/catatan.txt hasil/catatan.txt
```

Kompres file:

```bash
gzip hasil/catatan.txt
```

Cek hasil:

```bash
ls -lh hasil/
```

File `catatan.txt` akan berubah menjadi:

```bash
catatan.txt.gz
```

Catatan penting:

> Setelah dikompres menggunakan `gzip`, file asli biasanya hilang dan digantikan oleh file `.gz`.

---

## B. Membuka File `.gz`

Gunakan:

```bash
gunzip hasil/catatan.txt.gz
```

Cek lagi:

```bash
ls -lh hasil/
```

File akan kembali menjadi:

```bash
catatan.txt
```

---

## C. Melihat Isi File `.gz` Tanpa Extract

Buat lagi file `.gz`:

```bash
gzip hasil/catatan.txt
```

Lihat isi tanpa extract:

```bash
zcat hasil/catatan.txt.gz
```

Penjelasan:

* `zcat` digunakan untuk membaca isi file `.gz` tanpa harus membukanya secara permanen.

---

# 7. Praktik `zip`

`zip` sering dipakai karena cocok untuk lintas sistem operasi.

File `.zip` bisa dibuka di:

* Linux
* Windows
* macOS

---

## A. Membuat File `.zip`

Perintah:

```bash
zip -r hasil/data.zip data/
```

Penjelasan:

* `zip` = membuat file zip
* `-r` = recursive, artinya folder dan isi di dalamnya ikut dimasukkan
* `hasil/data.zip` = nama file zip
* `data/` = folder yang dikompres

Cek hasil:

```bash
ls -lh hasil/
```

---

## B. Melihat Isi File `.zip`

Gunakan:

```bash
unzip -l hasil/data.zip
```

Penjelasan:

* `unzip -l` = melihat daftar isi file zip tanpa extract

---

## C. Extract File `.zip`

Buat folder restore:

```bash
mkdir restore/zip
```

Extract:

```bash
unzip hasil/data.zip -d restore/zip
```

Penjelasan:

* `unzip` = membuka file zip
* `hasil/data.zip` = file yang dibuka
* `-d restore/zip` = tujuan extract

Cek hasil:

```bash
find restore/zip
```

---

# 8. Perbandingan `tar`, `gzip`, dan `zip`

| Perintah | Fungsi Utama                            | Cocok Untuk                                |
| -------- | --------------------------------------- | ------------------------------------------ |
| `tar`    | Membungkus banyak file/folder jadi satu | Backup folder Linux                        |
| `gzip`   | Mengompres file                         | Mengecilkan file tunggal atau hasil `.tar` |
| `tar.gz` | Archive + compress                      | Backup folder yang lebih kecil             |
| `zip`    | Archive + compress                      | File yang ingin dibuka juga di Windows     |
| `unzip`  | Membuka file `.zip`                     | Extract file zip                           |

Contoh mudah:

```bash
tar -cvf data.tar data/
```

Membungkus saja.

```bash
tar -czvf data.tar.gz data/
```

Membungkus dan mengompres.

```bash
zip -r data.zip data/
```

Membuat file zip.

---

# 9. Praktik Backup dengan `rsync`

Sekarang masuk ke bagian yang penting untuk Linux admin.

`rsync` digunakan untuk copy dan backup data dengan lebih pintar.

---

## A. Backup Folder `data` ke Folder `backup`

Pastikan posisi masih di:

```bash
~/praktikum-archive
```

Jalankan:

```bash
rsync -av data/ backup/
```

Penjelasan:

* `rsync` = tools untuk sinkronisasi file
* `-a` = archive mode, menjaga struktur folder, permission, timestamp
* `-v` = verbose, tampilkan proses
* `data/` = sumber
* `backup/` = tujuan

Cek isi backup:

```bash
find backup
```

Hasilnya isi folder `data` akan masuk ke dalam `backup`.

---

## B. Memahami Tanda Slash `/` pada `rsync`

Ini penting banget.

### Contoh 1

```bash
rsync -av data/ backup/
```

Artinya:

> Isi dari folder `data` disalin ke dalam `backup`.

Hasil:

```bash
backup/laporan1.txt
backup/laporan2.txt
backup/dokumen/
backup/log/
```

---

### Contoh 2

```bash
rsync -av data backup/
```

Artinya:

> Folder `data` beserta namanya disalin ke dalam `backup`.

Hasil:

```bash
backup/data/laporan1.txt
backup/data/laporan2.txt
backup/data/dokumen/
backup/data/log/
```

Kesimpulan:

```bash
data/  = isi foldernya
data   = foldernya ikut dibawa
```

Ini sering bikin mahasiswa bingung, jadi bagian ini wajib ditekankan.

---

## C. Simulasi Perubahan Data

Tambahkan file baru:

```bash
echo "File baru setelah backup pertama" > data/file-baru.txt
```

Edit file lama:

```bash
echo "Tambahan isi laporan" >> data/laporan1.txt
```

Jalankan backup ulang:

```bash
rsync -av data/ backup/
```

Perhatikan output-nya.

`rsync` tidak menyalin semuanya dari awal. Dia hanya menyalin file yang baru atau berubah.

Ini bedanya dengan `cp`.

---

## D. Simulasi `rsync --dry-run`

Sebelum benar-benar backup, admin bisa mengetes dulu.

```bash
rsync -av --dry-run data/ backup/
```

Penjelasan:

* `--dry-run` = simulasi saja
* belum benar-benar copy
* berguna supaya admin bisa cek dulu file apa yang akan diproses

Ini penting untuk mencegah salah backup atau salah hapus.

---

## E. Backup dengan `--delete`

Misalnya hapus file dari folder sumber:

```bash
rm data/file-baru.txt
```

Lalu jalankan:

```bash
rsync -av data/ backup/
```

Cek folder backup:

```bash
ls backup/
```

File `file-baru.txt` masih ada di backup.

Kenapa?

Karena secara default, `rsync` hanya menambahkan atau memperbarui file. Dia tidak otomatis menghapus file di tujuan.

Kalau ingin folder backup benar-benar sama persis dengan sumber, gunakan:

```bash
rsync -av --delete data/ backup/
```

Penjelasan:

* `--delete` = menghapus file di tujuan jika file tersebut sudah tidak ada di sumber

Peringatan untuk mahasiswa:

> Hati-hati menggunakan `--delete`. Kalau salah menentukan folder tujuan, data bisa ikut terhapus.

Sebelum pakai `--delete`, lebih aman tes dulu:

```bash
rsync -av --delete --dry-run data/ backup/
```

---

# 10. Praktik Backup Harian dengan Nama Tanggal

Dalam pekerjaan admin, backup biasanya diberi nama tanggal.

Buat folder backup berdasarkan tanggal hari ini:

```bash
mkdir -p backup/$(date +%F)
```

Cek:

```bash
ls backup/
```

Hasil contoh:

```bash
2026-06-21
```

Backup folder `data` ke folder tanggal:

```bash
rsync -av data/ backup/$(date +%F)/
```

Cek:

```bash
find backup/$(date +%F)
```

Penjelasan:

```bash
$(date +%F)
```

akan menghasilkan format tanggal:

```bash
YYYY-MM-DD
```

Contoh:

```bash
2026-06-21
```

Jadi perintah ini:

```bash
rsync -av data/ backup/$(date +%F)/
```

sama seperti:

```bash
rsync -av data/ backup/2026-06-21/
```

---

# 11. Praktik Backup Menjadi File `.tar.gz`

Selain copy folder biasa, admin juga sering membuat backup dalam bentuk file `.tar.gz`.

Buat file backup:

```bash
tar -czvf hasil/backup-data-$(date +%F).tar.gz data/
```

Cek hasil:

```bash
ls -lh hasil/
```

Hasil contoh:

```bash
backup-data-2026-06-21.tar.gz
```

Penjelasan:

File ini lebih rapi karena:

* satu file saja
* ukurannya lebih kecil
* mudah dipindahkan ke flashdisk/server/cloud
* cocok untuk backup arsip

---

# 12. Praktik Restore dari Backup `.tar.gz`

Buat folder restore khusus:

```bash
mkdir restore/backup-harian
```

Extract file backup:

```bash
tar -xzvf hasil/backup-data-$(date +%F).tar.gz -C restore/backup-harian
```

Cek hasil:

```bash
find restore/backup-harian
```

Konsep restore:

> Backup tidak ada gunanya kalau tidak pernah diuji restore-nya. Admin yang baik bukan cuma bisa backup, tapi juga bisa memastikan backup tersebut bisa dibuka kembali.

---

# 13. Praktik `rsync` dengan Exclude

Kadang ada file yang tidak perlu dibackup, misalnya file log atau cache.

Contoh: kita tidak ingin membackup folder `log`.

Jalankan:

```bash
rsync -av --exclude='log/' data/ backup/no-log/
```

Cek:

```bash
find backup/no-log
```

Folder `log` tidak ikut tersalin.

Penjelasan:

```bash
--exclude='log/'
```

artinya folder `log` dilewati saat proses backup.

Contoh lain:

```bash
rsync -av --exclude='*.log' data/ backup/no-file-log/
```

Artinya semua file dengan akhiran `.log` tidak ikut dibackup.

---

# 14. Praktik Gabungan: Skenario Linux Admin

## Skenario

Seorang admin memiliki folder kerja bernama `data`. Folder tersebut berisi laporan, dokumen, dan log. Admin harus:

1. Membuat archive biasa.
2. Membuat archive terkompresi.
3. Membuat file zip untuk dikirim ke user Windows.
4. Melakukan backup ke folder lain.
5. Melakukan restore untuk memastikan backup bisa dibuka.

---

## Langkah 1: Archive Biasa

```bash
tar -cvf hasil/admin-data.tar data/
```

Cek isi:

```bash
tar -tf hasil/admin-data.tar
```

---

## Langkah 2: Archive + Compress

```bash
tar -czvf hasil/admin-data.tar.gz data/
```

Cek ukuran:

```bash
ls -lh hasil/admin-data.tar hasil/admin-data.tar.gz
```

---

## Langkah 3: Buat ZIP

```bash
zip -r hasil/admin-data.zip data/
```

Cek isi zip:

```bash
unzip -l hasil/admin-data.zip
```

---

## Langkah 4: Backup Pakai `rsync`

```bash
rsync -av data/ backup/admin-data/
```

Cek:

```bash
find backup/admin-data
```

---

## Langkah 5: Restore dari `.tar.gz`

```bash
mkdir restore/admin-restore
tar -xzvf hasil/admin-data.tar.gz -C restore/admin-restore
```

Cek:

```bash
find restore/admin-restore
```

---

# 15. Command Penting yang Harus Dihafal

## Membuat `.tar`

```bash
tar -cvf data.tar data/
```

## Extract `.tar`

```bash
tar -xvf data.tar
```

## Melihat isi `.tar`

```bash
tar -tf data.tar
```

## Membuat `.tar.gz`

```bash
tar -czvf data.tar.gz data/
```

## Extract `.tar.gz`

```bash
tar -xzvf data.tar.gz
```

## Melihat isi `.tar.gz`

```bash
tar -tzvf data.tar.gz
```

## Membuat `.zip`

```bash
zip -r data.zip data/
```

## Extract `.zip`

```bash
unzip data.zip
```

## Melihat isi `.zip`

```bash
unzip -l data.zip
```

## Backup dengan `rsync`

```bash
rsync -av data/ backup/
```

## Simulasi backup

```bash
rsync -av --dry-run data/ backup/
```

## Backup mirror dengan delete

```bash
rsync -av --delete data/ backup/
```

---

# 16. Kesalahan Umum Mahasiswa

## 1. Lupa `-r` saat pakai `zip`

Salah:

```bash
zip data.zip data/
```

Benar:

```bash
zip -r data.zip data/
```

Karena kalau tanpa `-r`, isi folder bisa tidak ikut masuk.

---

## 2. Salah posisi nama file pada `tar`

Salah:

```bash
tar -cvf data/ hasil/data.tar
```

Benar:

```bash
tar -cvf hasil/data.tar data/
```

Formatnya:

```bash
tar -cvf nama-file-archive sumber-folder
```

---

## 3. Bingung Slash di `rsync`

```bash
rsync -av data/ backup/
```

Menyalin isi folder `data`.

```bash
rsync -av data backup/
```

Menyalin folder `data` beserta namanya.

---

## 4. Extract di Folder yang Salah

Kalau langsung menjalankan:

```bash
tar -xzvf data.tar.gz
```

hasil extract akan keluar di folder saat ini.

Lebih rapi pakai:

```bash
tar -xzvf data.tar.gz -C restore/
```

---

## 5. Pakai `--delete` Tanpa Cek

Berbahaya:

```bash
rsync -av --delete data/ backup/
```

Lebih aman cek dulu:

```bash
rsync -av --delete --dry-run data/ backup/
```

---

# 17. Latihan Mandiri Mahasiswa

## Latihan 1

Buat folder:

```bash
latihan-backup
```

Di dalamnya buat:

```bash
projek/
backup/
restore/
hasil/
```

Di dalam folder `projek`, buat minimal:

* 5 file `.txt`
* 2 folder
* 2 file `.log`

Lalu lakukan:

1. Archive folder `projek` menjadi `projek.tar`
2. Compress menjadi `projek.tar.gz`
3. Buat file `projek.zip`
4. Extract semua file tersebut ke folder `restore`
5. Backup folder `projek` ke folder `backup` menggunakan `rsync`

---

## Latihan 2

Buat perubahan pada folder `projek`:

1. Tambah 1 file baru.
2. Edit 1 file lama.
3. Hapus 1 file lama.
4. Jalankan `rsync` biasa.
5. Jalankan `rsync --dry-run --delete`.
6. Jalankan `rsync --delete`.

Lalu jawab:

1. Apa bedanya `rsync` biasa dan `rsync --delete`?
2. Kenapa `--dry-run` penting?
3. Apa fungsi tanda `/` pada `projek/`?

---

## Latihan 3

Buat backup dengan format tanggal:

```bash
projek-2026-06-21.tar.gz
```

Gunakan command otomatis dengan:

```bash
$(date +%F)
```

Command:

```bash
tar -czvf hasil/projek-$(date +%F).tar.gz projek/
```

Lalu extract ke folder:

```bash
restore/backup-tanggal/
```

---
