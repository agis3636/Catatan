Karena Anda sudah berhasil melakukan *mount* HDD eksternal ke direktori `/mnt/disk`, langkah selanjutnya adalah mendaftarkan direktori tersebut ke dalam Proxmox GUI agar file backup (`.vzdump`) bisa terbaca dan di-*restore*.

Berikut adalah langkah-langkah lengkapnya:

### 1. Pastikan Mounting Sudah Siap

Anda sudah menjalankan perintah ini, tapi untuk memastikan HDD masih ter-mount dengan benar, jalankan:

```bash
mkdir -p /mnt/disk
mount /dev/sdb1 /mnt/disk/
cd /mnt/disk/
ls /mnt/disk/dump

```

Pastikan file `vzdump-qemu-100-2026_01_14-14_55_56.vma.zst` terlihat di sana.

---

### 2. Tambahkan HDD External ke Storage Proxmox

Agar Proxmox mengenali file backup di dalam `/mnt/disk`, Anda harus menambahkannya sebagai storage tipe **Directory**:

1. Buka Proxmox Web GUI.
2. Pilih **Datacenter** > **Storage**.
3. Klik **Add** > **Directory**.
4. Isi konfigurasinya sebagai berikut:
* **ID:** `HDD-Eksternal` (atau nama bebas tanpa spasi).
* **Directory:** `/mnt/disk` (lokasi mount Anda).
* **Content:** Pilih **VZDump backup file** (ini sangat penting agar file backup muncul).


5. Klik **Add**.

### 3. Pastikan File Backup Berada di Folder yang Benar

Proxmox hanya akan mendeteksi file backup jika file tersebut diletakkan di dalam folder bernama `dump` di lokasi mount tersebut.

* Pastikan file `.vma.zst` atau `.tar.gz` Anda berada di: `/mnt/disk/dump/`
* Jika file backup masih di luar folder tersebut, pindahkan dulu melalui terminal:
```bash
mkdir -p /mnt/disk/dump
mv /mnt/disk/vzdump-qemu-XXX.vma.zst /mnt/disk/dump/

```



### 4. Proses Restore via GUI

Setelah storage ditambahkan dan file sudah di dalam folder `dump`:

1. Di menu sebelah kiri (Server View), klik storage baru Anda: **HDD-Eksternal**.
2. Pilih tab **Backups**. Sekarang daftar file backup Anda seharusnya sudah muncul di sana.
3. Klik pada file backup yang ingin di-restore.
4. Klik tombol **Restore** di bagian atas.
5. Pada jendela yang muncul:
* **Storage:** Pilih tempat penyimpanan disk VM yang baru (misalnya `local` atau `local-lvm`).
* **VM ID:** Masukkan ID VM yang diinginkan (bisa ID asli atau ID baru).


6. Klik **Restore**.

### 5. Alternatif: Restore via Terminal (Lebih Cepat)

Jika Anda lebih suka menggunakan command line (shell), Anda bisa langsung menjalankan perintah ini tanpa harus menambah storage di GUI:

```bash
# Untuk Virtual Machine (QM)
qmrestore /mnt/disk/dump/vzdump-qemu-ID-NYA.vma.zst 101 --storage local-lvm

# Untuk Container (LXC)
pct restore 102 /mnt/disk/dump/vzdump-lxc-ID-NYA.tar.gz --storage local-lvm

```

*Ganti `101/102` dengan ID VM baru yang Anda inginkan, dan `local-lvm` dengan storage tujuan Anda.*

**Tips Penting:**
Setelah selesai restore, jika Anda ingin mencabut HDD eksternal, pastikan untuk menghapus dulu storage `HDD-Eksternal` dari menu **Datacenter > Storage** agar tidak muncul error "Storage not available" di GUI Proxmox Anda.



**Tips Tambahan:**
Jika Anda lupa storage apa saja yang tersedia untuk menampung hasil restore, ketik:

```bash
pvesm status

```

Lihat di kolom "Name" untuk mengetahui nama storage tujuan yang bisa Anda pakai (seperti `local` atau `local-lvm`).

### 6. Langkah Pembersihan (Opsional tapi Penting)

Setelah proses restore selesai dan VM sudah bisa menyala, Anda sebaiknya melepas HDD eksternal secara aman:

1. Keluar dari direktori mount (agar tidak *busy*):
```bash
cd /

```


2. Lepas HDD eksternal:
```bash
umount /mnt/disk

```
