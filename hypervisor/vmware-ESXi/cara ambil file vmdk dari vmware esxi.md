Memindahkan *virtual machine* (VM) dari VMware ESXI ke Proxmox adalah langkah yang umum dilakukan saat migrasi. Untuk mendapatkan file `.vmdk` yang "bersih" dan siap dikonversi, ada beberapa metode yang bisa Anda gunakan.

Berikut adalah langkah-langkahnya:

---

## 1. Persiapan Penting

Sebelum mengambil file, pastikan hal berikut:

* **Matikan VM (Power Off):** Jangan mengambil file saat VM sedang berjalan atau dalam status *suspend* agar tidak terjadi korupsi data.
* **Hapus Snapshot:** Pastikan tidak ada *snapshot* yang aktif. Jika ada, lakukan "Delete All Snapshots" agar semua data menyatu ke file disk utama.

---

## 2. Cara Mengambil File VMDK via ESXi Web GUI

Ini adalah cara termudah jika ukuran file tidak terlalu besar dan koneksi jaringan Anda stabil.

1. Buka **ESXi Host Client** melalui browser.
2. Pilih menu **Storage** di panel sebelah kiri.
3. Klik pada **Datastore** tempat VM Anda berada, lalu klik **Datastore Browser**.
4. Cari folder nama VM Anda.
5. Anda akan melihat dua file terkait disk (biasanya hanya satu yang terlihat di GUI, yaitu file `.vmdk`).
6. Pilih file tersebut dan klik **Download**.

> [!IMPORTANT]
> **Catatan Penting:** ESXi seringkali menyembunyikan fakta bahwa satu disk terdiri dari dua file: `nama-vm.vmdk` (deskriptor kecil) dan `nama-vm-flat.vmdk` (data asli yang besar). Jika Anda men-download lewat GUI, ESXi biasanya otomatis menggabungkannya atau memberikan file `-flat.vmdk`.

---

## 3. Cara Mengambil File via WINSCP

Jika file sangat besar (ratusan GB), menggunakan protokol SCP (seperti aplikasi **WinSCP** atau perintah `scp` di terminal) jauh lebih stabil daripada browser.

1. Aktifkan **SSH** di ESXi (Manage > Services > SSH > Start).
2. Buka WinSCP dan konek ke IP ESXi Anda.
3. Masuk ke direktori: `/vmfs/volumes/nama-datastore/nama-vm/`.
4. Download file yang berakhiran **-flat.vmdk** (ini adalah file data mentah yang Anda butuhkan untuk Proxmox).

---

## 4. Cara Mengambil File via SCP Linux

WinSCP berbasis GUI dan seringkali memang lambat karena overhead enkripsi protokol SFTP-nya yang berat di CPU ESXi yang terbatas (ESXi melimitasi resource untuk manajemen).

Menggunakan `scp` langsung dari terminal Linux (misalnya langsung dari terminal Proxmox Anda) jauh lebih "sat-set" (cepat) dan efisien.

Berikut cara **"Tarik" (Pull)** file VMDK langsung dari terminal Proxmox/Linux:

### 1. Cari Lokasi File di ESXi

Anda perlu tahu path lengkap datastore-nya.

1. SSH ke ESXi:
```bash
ssh root@IP-ESXI-ANDA

```


2. Masuk ke folder volumes:
```bash
cd /vmfs/volumes/
ls

```


3. Masuk ke datastore dan folder VM:
```bash
cd nama-datastore-anda/nama-vm-anda/
ls -lh

```


4. **Perhatikan Files-nya:**
Anda akan melihat dua jenis file VMDK (ini yang sering bikin bingung pengguna GUI):
* `nama-vm.vmdk` (Kecil, hanya text descriptor/header).
* `nama-vm-flat.vmdk` (Besar, ini isinya **DATA** asli).


*Catatan path ini baik-baik.* (Contoh: `/vmfs/volumes/datastore1/win-server/win-server-flat.vmdk`)

---

### 2. Eksekusi Copy dari Terminal Proxmox

Sekarang, buka terminal di server **Proxmox** (bukan di ESXi), lalu jalankan perintah ini untuk menarik file.

Kita akan menarik file **flat**-nya saja karena itu yang berisi data.

```bash
# Syntax: scp root@IP_ESXI:/path/lengkap/flat.vmdk /path/tujuan/lokal/

scp -c aes128-ctr root@192.168.1.100:/vmfs/volumes/datastore1/nama-vm/nama-vm-flat.vmdk /root/

```

> **Tips Kecepatan:** Flag `-c aes128-ctr` digunakan untuk memaksa enkripsi yang lebih ringan (low CPU overhead) agar transfer lebih cepat daripada default AES-256 yang berat.

---

## 5. Cara Convert ke QCOW2 (Penting!)

Karena Anda hanya mengambil file `-flat.vmdk` (yang struktur isinya sebenarnya adalah RAW binary), cara convertnya sedikit berbeda agar Proxmox paham.

Gunakan perintah ini di terminal Proxmox:

```bash
# Format input dianggap 'raw' karena kita pakai file flat langsung
qemu-img convert -f raw nama-vm-flat.vmdk -O qcow2 nama-vm-baru.qcow2

```

Kalau ada 2 file `name.vmdk dan name-flat.vmdk` .

Gunakan perintah ini di terminal Proxmox:

```bash
qemu-img convert -f vmdk name.vmdk -O qcow2 nama-vm-baru.qcow2

```

---

## 6. QCOW2 masukkan ke proxmox

Mantap, file `deb.qcow2` sudah siap. Sekarang kita masukkan ke Proxmox agar bisa *booting*.

Asumsi:

* File ada di: `/root/deb.qcow2`
* ID VM yang mau dibuat: `105` (Ganti jika ID ini sudah terpakai)
* Storage Proxmox Anda: `local-lvm` (Ini default Proxmox, sesuaikan jika pakai ZFS atau Ceph).

---

### Langkah 1: Buat VM "Kosongan" (Skeleton)

Kita butuh wadah untuk disk tersebut. Jalankan ini di terminal Proxmox:

```bash
# Membuat VM ID 105, nama 'debian-server', RAM 2GB, Network VirtIO
qm create 105 --name debian-server --memory 2048 --net0 virtio,bridge=vmbr0

```

*Sekarang VM 105 sudah ada, tapi belum punya Hard Disk.*

---

### Langkah 2: Import Disk QCOW2 ke VM

Ini perintah kuncinya. Perintah ini akan menyalin file qcow2 Anda ke dalam storage system Proxmox dan mendaftarkannya ke VM 105.

```bash
# Syntax: qm importdisk <vmid> <source-file> <storage-target>
qm importdisk 105 deb.qcow2 local-lvm

```

*Tunggu proses copy selesai. Nanti akan muncul pesan "Successfully imported disk as 'unused0'".*

---

### Langkah 3: Pasang (Attach) Disk ke VM

Setelah di-import, disk tersebut statusnya masih **Unused** (belum dicolok). Kita harus pasang sebagai disk utama (SCSI).

```bash
# Set disk unused0 menjadi scsi0
qm set 105 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-105-disk-0

```

*(Catatan: Bagian `vm-105-disk-0` biasanya otomatis terdeteksi, tapi jika Anda ragu nama filenya apa setelah import, cek GUI atau ls di storage).*

**Alternatif Cara Attach (via GUI - Lebih Aman):**

1. Buka Web Proxmox > Klik VM 105 > **Hardware**.
2. Anda akan melihat **Unused Disk 0**.
3. Klik 2x pada Unused Disk tersebut > Klik **Add**.

---

### Langkah 4: Atur Boot Order

VM tidak akan jalan kalau dia tidak tahu harus booting dari disk yang baru dipasang tadi.

```bash
# Set boot order ke scsi0
qm set 105 --boot c --bootdisk scsi0

```

---

### Langkah 5: Jalankan VM

```bash
qm start 105

```

### Troubleshooting (PENTING)

Karena ini migrasi dari ESXi (nama file Anda `deb` asumsinya Debian/Linux):

1. **Nama Interface Berubah:**
Di ESXi mungkin namanya `ens192` (VMXNET3), di Proxmox jadi `ens18` (VirtIO).
* Jika network mati, masuk console, cek `ip a`.
* Edit `/etc/network/interfaces` (atau Netplan) sesuaikan nama interfacenya.


2. **Jika Windows:**
Jika `deb` tadi ternyata Windows, ganti controller disk dari VirtIO SCSI ke **LSI Logic** atau **SATA** dulu biar tidak *Blue Screen* (BSOD) `INACCESSIBLE_BOOT_DEVICE`. VirtIO butuh driver tambahan.

Sudah berhasil `qm start`?

---

## 7. Tips Tambahan: Gunakan Proxmox Import Wizard

Di versi Proxmox terbaru (7.4 atau 8.x), sudah ada fitur **ESXi Import Provider**. Anda tidak perlu lagi download/upload file secara manual.

1. Di Proxmox, pergi ke **Datacenter > Storage > Add > ESXi**.
2. Masukkan IP ESXi dan kredensialnya.
3. Setelah terhubung, Anda bisa langsung melakukan "Pull" VM dari ESXi ke Proxmox. Sistem akan otomatis melakukan konversi disk untuk Anda.
