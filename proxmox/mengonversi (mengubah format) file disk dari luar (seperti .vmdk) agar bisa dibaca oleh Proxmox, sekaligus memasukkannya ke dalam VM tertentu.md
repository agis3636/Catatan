Untuk mengimpor file disk format **.vmdk** (biasanya dari VMware) ke dalam Proxmox, cara paling akurat, efisien, dan minim error adalah menggunakan perintah bawaan Proxmox yaitu `qm importdisk`.

Perintah ini akan otomatis mengonversi format `.vmdk` menjadi format raw atau qcow2 sesuai penyimpanan Proxmox Anda, sekaligus memindahkannya ke storage yang benar.

Berikut adalah langkah-langkah detailnya:

### 1. Persiapan: Pastikan File Terbaca

Pastikan HDD yang berisi file `.vmdk` tersebut sudah ter-mount di Proxmox (seperti langkah pada jawaban sebelumnya).

* Contoh lokasi file: `/mnt/hdd-saya/server-lama.vmdk`
* Jika file ada di laptop/komputer lain, Anda perlu upload dulu ke Proxmox menggunakan **WinSCP** atau **FileZilla** ke folder `/root/` atau `/tmp/`.

### 2. Siapkan VM Wadahnya

Anda harus memiliki VM (Virtual Machine) yang akan menampung disk ini.

* Jika belum ada, buat VM baru di Web GUI Proxmox (**Create VM**).
* Catat **VM ID**-nya (Contoh: `100`, `101`, dst).
* *Tips:* Saat membuat VM, pada tab **Disks**, Anda bisa menghapus disk default jika tujuannya memang ingin menggantinya dengan disk `.vmdk` ini.

### 3. Jalankan Perintah Import (Via Shell/CLI)

Buka **Shell** di node Proxmox Anda, lalu ketik perintah berikut. Sesuaikan bagian yang ditulis kapital dengan kondisi Anda.

**Format Perintah:**

```bash
qm importdisk <VM-ID> <LOKASI-FILE-VMDK> <TARGET-STORAGE>

```

**Contoh Nyata:**
Misalkan:

* ID VM Anda: `105`
* Lokasi file: `/mnt/hdd-saya/windows.vmdk`
* Storage Proxmox tujuan: `local-lvm` (biasanya ini default untuk disk VM)

Maka perintahnya:

```bash
qm importdisk 105 /mnt/hdd-saya/windows.vmdk local-lvm

```

**Penjelasan Proses:**
Setelah di-enter, Proxmox akan menampilkan progress bar (`Transferring...`). Tunggu sampai selesai (`Successfully imported disk as '...'`). Lama waktu tergantung ukuran file dan kecepatan disk.

### 4. Mengaktifkan Disk di Web GUI (Attach)

Setelah proses import di Shell selesai, disk tersebut **belum otomatis aktif**. Statusnya masih "Unused Disk". Anda harus mengaktifkannya ("attach") secara manual.

1. Buka Proxmox Web GUI.
2. Pilih VM tersebut (misal VM 105) -> Klik menu **Hardware**.
3. Anda akan melihat item baru bernama **Unused Disk 0**.
4. Klik dua kali (double click) pada **Unused Disk 0** tersebut.
5. Akan muncul jendela popup:
* **Bus/Device:** Biasanya pilih `SCSI` atau `SATA` (rekomendasi: SCSI + centang VirtIO SCSI controller di opsi VM untuk performa terbaik).
* Klik **Add**.



### 5. Atur Boot Order (PENTING!)

Jika file `.vmdk` yang Anda import berisi Sistem Operasi (Windows/Linux) dan Anda ingin booting darinya:

1. Masih di menu VM tersebut, pindah ke tab **Options**.
2. Klik dua kali pada **Boot Order**.
3. Pastikan disk baru (misal `scsi0`) berada di urutan paling atas atau dicentang (Enabled).
4. Klik **OK**.

### Ringkasan Error yang Sering Terjadi & Solusinya

* **Error: "file not found"**
* Pastikan Anda mengetik path/lokasi file `.vmdk` dengan benar. Gunakan fitur *tab completion* (ketik sebagian nama folder lalu tekan tombol Tab) untuk menghindari typo.


* **Error: "storage does not exist"**
* Pastikan nama storage tujuan (misal `local-lvm`) benar. Cek nama storage yang tersedia di menu Datacenter -> Storage.


* **Windows Bluescreen/Linux Kernel Panic saat Booting**
* Ini sering terjadi karena perbedaan driver controller.
* **Solusi:** Ubah **Bus/Device** disk dari `VirtIO Block` menjadi `SATA` atau `IDE` di menu **Hardware** -> Detach disknya -> Double click Unused Disk -> Add lagi dengan tipe SATA/IDE.



Sekarang VM Anda sudah siap dinyalakan dengan disk hasil import tersebut.
