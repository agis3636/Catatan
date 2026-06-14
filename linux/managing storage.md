# MODUL PRAKTIK LINUX

## Managing Storage: Mounting, Unmounting, Format, dan Partisi Flashdisk di Ubuntu

---

## A. Tujuan Pembelajaran

Setelah mengikuti modul ini, mahasiswa diharapkan mampu:

1. Memahami konsep dasar storage di Linux.
2. Mengetahui bagaimana flashdisk terdeteksi di sistem Linux.
3. Menampilkan perangkat storage melalui terminal.
4. Melakukan mounting dan unmounting flashdisk.
5. Membagi partisi flashdisk menggunakan terminal.
6. Memformat partisi flashdisk ke berbagai filesystem.
7. Menggunakan GParted sebagai alternatif berbasis GUI.
8. Menghindari kesalahan fatal seperti salah memilih disk utama sistem.

---

## B. Konsep Dasar Storage di Linux

Di Linux, semua perangkat penyimpanan seperti harddisk, SSD, flashdisk, dan memory card akan dikenali sebagai file perangkat di dalam direktori:

```bash
/dev
```

Contoh nama perangkat storage:

```bash
/dev/sda
/dev/sdb
/dev/sdc
```

Biasanya:

```bash
/dev/sda
```

adalah disk utama laptop atau komputer.

Sedangkan flashdisk biasanya muncul sebagai:

```bash
/dev/sdb
```

atau

```bash
/dev/sdc
```

tergantung jumlah storage yang sedang terhubung.

Contoh partisinya:

```bash
/dev/sdb1
/dev/sdb2
```

Artinya:

```bash
/dev/sdb
```

adalah perangkat flashdisk utama.

Sedangkan:

```bash
/dev/sdb1
```

adalah partisi pertama pada flashdisk tersebut.

---

## C. Istilah Penting

### 1. Disk

Disk adalah perangkat penyimpanan utama, misalnya flashdisk 32 GB.

Contoh:

```bash
/dev/sdb
```

### 2. Partisi

Partisi adalah pembagian ruang pada disk.

Contoh:

```bash
/dev/sdb1
/dev/sdb2
```

Satu flashdisk bisa dibuat menjadi beberapa partisi.

Misalnya flashdisk 32 GB dibagi menjadi:

```bash
/dev/sdb1 = 16 GB
/dev/sdb2 = 16 GB
```

### 3. Filesystem

Filesystem adalah format sistem file yang digunakan agar storage bisa menyimpan data.

Contoh filesystem:

```bash
ext4
ntfs
fat32
exfat
```

Keterangan singkat:

| Filesystem | Keterangan                                                                  |
| ---------- | --------------------------------------------------------------------------- |
| ext4       | Cocok untuk Linux                                                           |
| NTFS       | Cocok untuk Windows dan Linux                                               |
| FAT32      | Cocok untuk banyak perangkat, tetapi batas file maksimal sekitar 4 GB       |
| exFAT      | Cocok untuk flashdisk besar dan kompatibel dengan Windows, Linux, dan macOS |

### 4. Mount

Mount adalah proses menghubungkan partisi storage ke direktori tertentu agar bisa diakses.

Contoh:

```bash
sudo mount /dev/sdb1 /mnt/flashdisk
```

Artinya partisi `/dev/sdb1` dipasang ke folder `/mnt/flashdisk`.

### 5. Unmount

Unmount adalah proses melepas partisi dari sistem sebelum flashdisk dicabut.

Contoh:

```bash
sudo umount /dev/sdb1
```

Unmount penting agar data tidak rusak atau corrupt.

---

# BAGIAN 1

## Praktik Melihat Flashdisk Muncul di Terminal

### Studi Kasus

Mahasiswa diminta memasukkan flashdisk ke laptop Ubuntu, lalu melihat bagaimana flashdisk tersebut terdeteksi melalui terminal.

---

## Langkah 1: Buka Terminal

Gunakan shortcut:

```bash
Ctrl + Alt + T
```

atau buka dari menu aplikasi dengan nama:

```bash
Terminal
```

---

## Langkah 2: Cek Storage Sebelum Flashdisk Dimasukkan

Sebelum memasukkan flashdisk, jalankan:

```bash
lsblk
```

Contoh output:

```bash
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 238.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
└─sda2   8:2    0   238G  0 part /
```

Keterangan:

```bash
sda
```

adalah disk utama laptop.

Pada tahap ini flashdisk belum terlihat karena belum dimasukkan.

---

## Langkah 3: Pantau Storage Secara Langsung

Agar mahasiswa bisa melihat flashdisk muncul saat dimasukkan, gunakan perintah:

```bash
watch -n 1 lsblk
```

Perintah ini akan menjalankan `lsblk` setiap 1 detik.

Setelah itu masukkan flashdisk ke laptop.

Jika flashdisk berhasil terdeteksi, akan muncul perangkat baru, misalnya:

```bash
sdb      8:16   1  32G  0 disk
└─sdb1   8:17   1  32G  0 part
```

Keterangan:

```bash
sdb
```

adalah flashdisk.

```bash
sdb1
```

adalah partisi pertama flashdisk.

Untuk keluar dari tampilan `watch`, tekan:

```bash
Ctrl + C
```

---

## Langkah 4: Melihat Detail Filesystem Flashdisk

Gunakan perintah:

```bash
lsblk -f
```

Contoh output:

```bash
NAME   FSTYPE LABEL     UUID                                 MOUNTPOINT
sda
├─sda1 vfat             A1B2-C3D4                            /boot/efi
└─sda2 ext4             1234abcd-5678-efgh                   /
sdb
└─sdb1 vfat   FLASHDISK 9A2B-1C3D                            /media/agis/FLASHDISK
```

Keterangan:

| Kolom      | Fungsi                            |
| ---------- | --------------------------------- |
| NAME       | Nama disk atau partisi            |
| FSTYPE     | Jenis filesystem                  |
| LABEL      | Nama flashdisk                    |
| UUID       | Identitas unik partisi            |
| MOUNTPOINT | Lokasi flashdisk sedang ter-mount |

Jika ada mountpoint seperti:

```bash
/media/agis/FLASHDISK
```

berarti flashdisk sudah otomatis ter-mount.

---

## Langkah 5: Melihat Flashdisk dari Log Sistem

Untuk melihat proses flashdisk terdeteksi oleh sistem, gunakan:

```bash
sudo dmesg --follow
```

Lalu cabut dan masukkan kembali flashdisk.

Contoh output:

```bash
usb 2-1: new high-speed USB device number 5
sd 6:0:0:0: [sdb] 62521344 512-byte logical blocks
sdb: sdb1
```

Keterangan:

```bash
[sdb]
```

menunjukkan flashdisk dikenali sebagai `/dev/sdb`.

Untuk keluar, tekan:

```bash
Ctrl + C
```

---

# BAGIAN 2

## Praktik Mounting Flashdisk

Pada Ubuntu desktop, flashdisk biasanya akan otomatis ter-mount ketika dibuka melalui file manager. Namun dalam praktik Linux, mahasiswa perlu memahami cara mount manual melalui terminal.

---

## Langkah 1: Cek Nama Flashdisk

Gunakan:

```bash
lsblk -f
```

Contoh:

```bash
sdb
└─sdb1 vfat FLASHDISK 9A2B-1C3D
```

Dari output tersebut, partisi flashdisk adalah:

```bash
/dev/sdb1
```

---

## Langkah 2: Buat Folder Mount Point

Mount point adalah folder tempat partisi flashdisk akan dipasang.

Buat folder:

```bash
sudo mkdir -p /mnt/flashdisk
```

Keterangan:

```bash
/mnt/flashdisk
```

adalah folder tujuan untuk mengakses isi flashdisk.

---

## Langkah 3: Mount Flashdisk

Jalankan:

```bash
sudo mount /dev/sdb1 /mnt/flashdisk
```

Setelah itu cek:

```bash
lsblk
```

Jika berhasil, akan terlihat:

```bash
sdb
└─sdb1   /mnt/flashdisk
```

---

## Langkah 4: Masuk ke Isi Flashdisk

Gunakan:

```bash
cd /mnt/flashdisk
```

Lihat isi flashdisk:

```bash
ls
```

Buat file percobaan:

```bash
touch percobaan.txt
```

Cek lagi:

```bash
ls
```

Jika muncul file `percobaan.txt`, berarti flashdisk berhasil digunakan melalui terminal.

---

## Langkah 5: Unmount Flashdisk

Sebelum mencabut flashdisk, lakukan unmount:

```bash
cd ~
sudo umount /mnt/flashdisk
```

atau bisa juga:

```bash
sudo umount /dev/sdb1
```

Cek lagi:

```bash
lsblk
```

Jika mountpoint sudah kosong, berarti flashdisk sudah berhasil dilepas dari sistem.

---

## Catatan Penting

Jika muncul error seperti:

```bash
target is busy
```

artinya masih ada terminal atau aplikasi yang sedang membuka isi flashdisk.

Solusinya:

```bash
cd ~
```

Lalu ulangi:

```bash
sudo umount /dev/sdb1
```

---

# BAGIAN 3

## Praktik Membagi Partisi Flashdisk Menggunakan fdisk

Pada bagian ini, flashdisk akan dibagi menjadi dua partisi.

Contoh studi kasus:

Flashdisk 32 GB akan dibagi menjadi:

```bash
Partisi 1 = 16 GB
Partisi 2 = sisa kapasitas
```

PERINGATAN:

Jangan salah memilih disk.

Jangan gunakan:

```bash
/dev/sda
```

karena biasanya itu adalah disk utama sistem Ubuntu.

Gunakan flashdisk, misalnya:

```bash
/dev/sdb
```

---

## Langkah 1: Cek Nama Flashdisk

```bash
lsblk
```

Contoh output:

```bash
sda      238G disk
├─sda1   512M part /boot/efi
└─sda2   238G part /

sdb       32G disk
└─sdb1    32G part /media/agis/FLASHDISK
```

Dari output tersebut, flashdisk adalah:

```bash
/dev/sdb
```

---

## Langkah 2: Unmount Flashdisk

Jika flashdisk sedang ter-mount, unmount dulu:

```bash
sudo umount /dev/sdb1
```

Jika ada partisi lain, misalnya `/dev/sdb2`, unmount juga:

```bash
sudo umount /dev/sdb2
```

---

## Langkah 3: Masuk ke fdisk

Jalankan:

```bash
sudo fdisk /dev/sdb
```

Maka akan masuk ke mode interaktif:

```bash
Command (m for help):
```

---

## Langkah 4: Lihat Partisi yang Ada

Tekan:

```bash
p
```

Lalu tekan Enter.

Perintah `p` digunakan untuk menampilkan daftar partisi.

---

## Langkah 5: Hapus Partisi Lama

Tekan:

```bash
d
```

Lalu Enter.

Jika ada lebih dari satu partisi, ulangi perintah `d` sampai semua partisi terhapus.

Catatan:

Partisi belum benar-benar terhapus sebelum kita menekan `w`.

---

## Langkah 6: Buat Partisi Pertama

Tekan:

```bash
n
```

Lalu pilih:

```bash
p
```

untuk primary partition.

Nomor partisi:

```bash
1
```

First sector tekan Enter saja.

Untuk ukuran partisi pertama, ketik:

```bash
+16G
```

Lalu tekan Enter.

---

## Langkah 7: Buat Partisi Kedua

Tekan lagi:

```bash
n
```

Pilih:

```bash
p
```

Nomor partisi:

```bash
2
```

First sector tekan Enter.

Last sector tekan Enter saja agar memakai sisa kapasitas flashdisk.

---

## Langkah 8: Cek Hasil Partisi

Tekan:

```bash
p
```

Contoh hasil:

```bash
Device      Start      End  Sectors Size Type
/dev/sdb1    2048 33556479 33554432  16G Linux filesystem
/dev/sdb2 33556480 62521343 28964864  14G Linux filesystem
```

---

## Langkah 9: Simpan Perubahan

Tekan:

```bash
w
```

Lalu Enter.

Perintah `w` artinya write, yaitu menyimpan perubahan partisi ke flashdisk.

---

## Langkah 10: Refresh Informasi Partisi

Kadang sistem belum langsung membaca partisi baru.

Gunakan:

```bash
sudo partprobe /dev/sdb
```

Lalu cek:

```bash
lsblk
```

Hasilnya seharusnya:

```bash
sdb      32G disk
├─sdb1   16G part
└─sdb2   14G part
```

---

# BAGIAN 4

## Praktik Format Partisi Flashdisk

Setelah partisi dibuat, partisi belum bisa digunakan dengan baik sebelum diformat.

---

## Format Partisi ke ext4

Filesystem ext4 cocok untuk Linux.

```bash
sudo mkfs.ext4 /dev/sdb1
```

Contoh untuk partisi kedua:

```bash
sudo mkfs.ext4 /dev/sdb2
```

---

## Format Partisi ke FAT32

Filesystem FAT32 cocok untuk flashdisk yang ingin dibaca di banyak perangkat.

```bash
sudo mkfs.vfat -F 32 /dev/sdb1
```

---

## Format Partisi ke NTFS

Filesystem NTFS cocok jika flashdisk sering digunakan di Windows.

```bash
sudo mkfs.ntfs -f /dev/sdb1
```

Jika perintah `mkfs.ntfs` belum tersedia, install dulu:

```bash
sudo apt update
sudo apt install ntfs-3g
```

---

## Format Partisi ke exFAT

Filesystem exFAT cocok untuk flashdisk besar dan file berukuran besar.

```bash
sudo mkfs.exfat /dev/sdb1
```

Jika belum tersedia, install:

```bash
sudo apt update
sudo apt install exfatprogs
```

---

## Memberi Nama Label Flashdisk

Agar nama flashdisk mudah dikenali, bisa diberi label.

Untuk FAT32:

```bash
sudo fatlabel /dev/sdb1 DATAKU
```

Untuk ext4:

```bash
sudo e2label /dev/sdb1 DATAKU
```

Untuk NTFS:

```bash
sudo ntfslabel /dev/sdb1 DATAKU
```

Setelah itu cek:

```bash
lsblk -f
```

---

# BAGIAN 5

## Praktik Mount Dua Partisi Flashdisk

Misalnya flashdisk sudah memiliki:

```bash
/dev/sdb1
/dev/sdb2
```

Kita akan mount keduanya ke folder berbeda.

---

## Langkah 1: Buat Folder Mount Point

```bash
sudo mkdir -p /mnt/flash1
sudo mkdir -p /mnt/flash2
```

---

## Langkah 2: Mount Partisi

```bash
sudo mount /dev/sdb1 /mnt/flash1
sudo mount /dev/sdb2 /mnt/flash2
```

---

## Langkah 3: Cek Mount Point

```bash
lsblk
```

Contoh:

```bash
sdb
├─sdb1 /mnt/flash1
└─sdb2 /mnt/flash2
```

---

## Langkah 4: Buat File Tes

Pada partisi pertama:

```bash
cd /mnt/flash1
touch data-partisi-1.txt
```

Pada partisi kedua:

```bash
cd /mnt/flash2
touch data-partisi-2.txt
```

Cek:

```bash
ls /mnt/flash1
ls /mnt/flash2
```

---

## Langkah 5: Unmount Semua Partisi

```bash
cd ~
sudo umount /mnt/flash1
sudo umount /mnt/flash2
```

atau:

```bash
sudo umount /dev/sdb1
sudo umount /dev/sdb2
```

---

# BAGIAN 6

## Membagi Partisi Flashdisk Menggunakan parted

Selain `fdisk`, Linux juga menyediakan `parted`.

`parted` cocok digunakan untuk membuat tabel partisi GPT dan mengatur partisi dengan ukuran persen.

---

## Langkah 1: Cek Disk

```bash
sudo parted -l
```

Cari flashdisk, misalnya:

```bash
Disk /dev/sdb: 32GB
```

Jangan memilih `/dev/sda` jika itu disk utama sistem.

---

## Langkah 2: Masuk ke parted

```bash
sudo parted /dev/sdb
```

Maka akan muncul:

```bash
(parted)
```

---

## Langkah 3: Buat Tabel Partisi Baru

Untuk membuat tabel partisi GPT:

```bash
mklabel gpt
```

Jika diminta konfirmasi, pilih:

```bash
Yes
```

Peringatan:

Perintah ini akan menghapus semua partisi lama di flashdisk.

---

## Langkah 4: Buat Partisi Pertama

Misalnya membuat partisi pertama dari awal sampai 50% kapasitas flashdisk:

```bash
mkpart primary ext4 0% 50%
```

---

## Langkah 5: Buat Partisi Kedua

Membuat partisi kedua dari 50% sampai 100%:

```bash
mkpart primary ext4 50% 100%
```

---

## Langkah 6: Cek Hasil Partisi

```bash
print
```

Contoh hasil:

```bash
Number  Start   End     Size    File system  Name
 1      1049kB  16.0GB  16.0GB
 2      16.0GB  32.0GB  16.0GB
```

---

## Langkah 7: Keluar dari parted

```bash
quit
```

---

## Langkah 8: Format Partisi

Format partisi pertama:

```bash
sudo mkfs.ext4 /dev/sdb1
```

Format partisi kedua:

```bash
sudo mkfs.vfat -F 32 /dev/sdb2
```

---

# BAGIAN 7

## Mount Otomatis Flashdisk

Di Ubuntu Desktop, flashdisk biasanya otomatis ter-mount saat dibuka melalui file manager.

Lokasi mount otomatis biasanya berada di:

```bash
/media/nama_user/nama_flashdisk
```

Contoh:

```bash
/media/agis/DATAKU
```

Untuk melihatnya:

```bash
ls /media/$USER
```

Masuk ke flashdisk:

```bash
cd /media/$USER/DATAKU
```

Lihat isi flashdisk:

```bash
ls
```

Jika nama flashdisk memiliki spasi, gunakan tanda kutip.

Contoh:

```bash
cd "/media/$USER/FLASH DISK"
```

---

# BAGIAN 8

## Mount Otomatis Saat Boot Menggunakan /etc/fstab

Bagian ini bersifat opsional.

Biasanya flashdisk tidak wajib dimasukkan ke `/etc/fstab`, karena flashdisk adalah perangkat removable. Namun untuk latihan, mahasiswa perlu tahu konsepnya.

Lebih aman menggunakan UUID daripada `/dev/sdb1`, karena nama `/dev/sdb1` bisa berubah menjadi `/dev/sdc1`.

---

## Langkah 1: Cek UUID

```bash
sudo blkid
```

Contoh output:

```bash
/dev/sdb1: UUID="9A2B-1C3D" TYPE="vfat" LABEL="DATAKU"
```

UUID-nya adalah:

```bash
9A2B-1C3D
```

---

## Langkah 2: Buat Folder Mount

```bash
sudo mkdir -p /mnt/datakuflash
```

---

## Langkah 3: Edit File fstab

```bash
sudo nano /etc/fstab
```

Tambahkan baris berikut:

```bash
UUID=9A2B-1C3D /mnt/datakuflash vfat defaults,nofail 0 0
```

Keterangan:

| Bagian           | Fungsi                                               |
| ---------------- | ---------------------------------------------------- |
| UUID             | Identitas unik partisi                               |
| /mnt/datakuflash | Lokasi mount                                         |
| vfat             | Jenis filesystem                                     |
| defaults         | Opsi standar                                         |
| nofail           | Sistem tetap boot walaupun flashdisk tidak terpasang |
| 0 0              | Opsi dump dan check filesystem                       |

---

## Langkah 4: Tes fstab

Jalankan:

```bash
sudo mount -a
```

Jika tidak ada error, berarti konfigurasi benar.

Cek:

```bash
lsblk
```

---

# BAGIAN 9

## Menggunakan GParted

GParted adalah aplikasi GUI untuk mengatur partisi storage di Linux.

---

## Langkah 1: Install GParted

```bash
sudo apt update
sudo apt install gparted
```

---

## Langkah 2: Jalankan GParted

```bash
sudo gparted
```

---

## Langkah 3: Pilih Flashdisk

Di pojok kanan atas, pilih perangkat flashdisk.

Contoh:

```bash
/dev/sdb
```

Pastikan bukan:

```bash
/dev/sda
```

karena `/dev/sda` biasanya disk utama sistem.

---

## Langkah 4: Unmount Jika Masih Aktif

Jika partisi masih aktif, klik kanan pada partisi, lalu pilih:

```bash
Unmount
```

---

## Langkah 5: Hapus Partisi Lama

Klik kanan pada partisi lama, lalu pilih:

```bash
Delete
```

Jika ada lebih dari satu partisi, hapus semuanya.

---

## Langkah 6: Buat Partisi Baru

Klik kanan pada ruang kosong:

```bash
New
```

Atur ukuran partisi.

Pilih filesystem, misalnya:

```bash
fat32
ntfs
ext4
```

Klik:

```bash
Add
```

Ulangi jika ingin membuat partisi kedua.

---

## Langkah 7: Terapkan Perubahan

Klik tombol centang hijau:

```bash
Apply
```

Tunggu sampai proses selesai.

---

# BAGIAN 10

## Latihan Praktik Mahasiswa

### Latihan 1: Deteksi Flashdisk

1. Jalankan perintah:

```bash
watch -n 1 lsblk
```

2. Masukkan flashdisk.
3. Catat nama perangkat flashdisk.
4. Catat nama partisinya.

Pertanyaan:

1. Flashdisk terdeteksi sebagai apa?
2. Berapa ukuran flashdisk?
3. Apakah flashdisk otomatis ter-mount?

---

### Latihan 2: Mount Manual

1. Buat folder mount:

```bash
sudo mkdir -p /mnt/latihanflash
```

2. Mount flashdisk:

```bash
sudo mount /dev/sdb1 /mnt/latihanflash
```

3. Masuk ke folder:

```bash
cd /mnt/latihanflash
```

4. Buat file:

```bash
touch latihan.txt
```

5. Cek isi folder:

```bash
ls
```

6. Unmount:

```bash
cd ~
sudo umount /mnt/latihanflash
```

---

### Latihan 3: Membagi Partisi Flashdisk

Buat flashdisk menjadi dua partisi:

```bash
Partisi 1 = 50%
Partisi 2 = 50%
```

Gunakan salah satu:

```bash
fdisk
```

atau:

```bash
parted
```

---

### Latihan 4: Format Partisi

Format:

```bash
/dev/sdb1
```

menjadi FAT32.

Format:

```bash
/dev/sdb2
```

menjadi ext4.

Perintah:

```bash
sudo mkfs.vfat -F 32 /dev/sdb1
sudo mkfs.ext4 /dev/sdb2
```

---

### Latihan 5: Mount Dua Partisi

Buat mount point:

```bash
sudo mkdir -p /mnt/data1
sudo mkdir -p /mnt/data2
```

Mount:

```bash
sudo mount /dev/sdb1 /mnt/data1
sudo mount /dev/sdb2 /mnt/data2
```

Buat file:

```bash
touch /mnt/data1/file-partisi-1.txt
touch /mnt/data2/file-partisi-2.txt
```

Cek:

```bash
ls /mnt/data1
ls /mnt/data2
```

Unmount:

```bash
sudo umount /mnt/data1
sudo umount /mnt/data2
```

---

# BAGIAN 11

## Troubleshooting

### 1. Flashdisk Tidak Muncul

Cek dengan:

```bash
lsblk
```

atau:

```bash
sudo dmesg | tail -30
```

Jika tetap tidak muncul, coba:

1. Cabut dan pasang ulang flashdisk.
2. Pindah port USB.
3. Coba flashdisk lain.
4. Cek apakah flashdisk rusak.

---

### 2. Tidak Bisa Unmount

Jika muncul:

```bash
target is busy
```

Solusi:

```bash
cd ~
sudo umount /dev/sdb1
```

Jika masih gagal, cek proses yang memakai flashdisk:

```bash
sudo lsof /dev/sdb1
```

---

### 3. Permission Denied Saat Menulis File

Jika filesystem ext4 dan file tidak bisa dibuat oleh user biasa, ubah kepemilikan folder mount:

```bash
sudo chown -R $USER:$USER /mnt/flashdisk
```

---

### 4. Salah Pilih Disk

Ini kesalahan paling berbahaya.

Selalu cek dengan:

```bash
lsblk -f
```

Jangan langsung menjalankan format sebelum yakin.

Perintah seperti ini sangat berbahaya jika salah target:

```bash
sudo mkfs.ext4 /dev/sda1
```

Karena `/dev/sda1` bisa saja partisi sistem Ubuntu.

---

# BAGIAN 12

## Rangkuman

Managing storage di Linux meliputi proses mengenali perangkat, melihat partisi, melakukan mount, unmount, membuat partisi, dan memformat filesystem.

Perintah penting yang digunakan:

| Perintah       | Fungsi                                          |
| -------------- | ----------------------------------------------- |
| lsblk          | Melihat daftar disk dan partisi                 |
| lsblk -f       | Melihat filesystem, label, UUID, dan mountpoint |
| dmesg --follow | Melihat log perangkat saat flashdisk dimasukkan |
| mount          | Memasang partisi ke folder                      |
| umount         | Melepas partisi dari sistem                     |
| fdisk          | Membuat dan menghapus partisi melalui terminal  |
| parted         | Membuat partisi dengan mode interaktif          |
| mkfs           | Memformat partisi                               |
| blkid          | Melihat UUID partisi                            |
| gparted        | Mengatur partisi melalui GUI                    |

Hal paling penting dalam praktik ini adalah memastikan nama perangkat dengan benar. Untuk flashdisk biasanya menggunakan `/dev/sdb` atau `/dev/sdc`, sedangkan `/dev/sda` biasanya adalah disk utama sistem. Kesalahan memilih disk dapat menyebabkan data sistem terhapus.

---

# BAGIAN 13

## Kesimpulan

Flashdisk di Linux tidak hanya dapat digunakan melalui file manager, tetapi juga dapat dikelola langsung melalui terminal. Dengan memahami perintah seperti `lsblk`, `mount`, `umount`, `fdisk`, `parted`, dan `mkfs`, mahasiswa dapat memahami bagaimana Linux mengenali dan mengelola media penyimpanan.

Praktik ini penting karena konsep storage management merupakan dasar dari administrasi sistem Linux. Mahasiswa tidak hanya belajar menggunakan flashdisk, tetapi juga memahami cara kerja disk, partisi, filesystem, dan proses mounting di sistem operasi Linux.
