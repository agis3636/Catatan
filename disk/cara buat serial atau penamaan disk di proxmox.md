### 1. Buka Shell Proxmox

* Klik pada nama **Node Proxmox** Anda.
* Pilih menu **>_ Shell** di bagian atas.

### 2. Masukkan Perintah Ini

Ketik atau *copy-paste* perintah berikut satu per satu (asumsi ID VM Anda adalah **102**):

```bash
qm set 102 --scsi1 synology:102/vm-102-disk-0.qcow2,discard=on,iothread=1,serial=SN001
qm set 102 --scsi2 synology:102/vm-102-disk-1.qcow2,discard=on,iothread=1,serial=SN002
qm set 102 --scsi3 synology:102/vm-102-disk-2.qcow2,discard=on,iothread=1,serial=SN003
qm set 102 --scsi4 synology:102/vm-102-disk-3.qcow2,discard=on,iothread=1,serial=SN004
qm set 102 --scsi5 synology:102/vm-102-disk-4.qcow2,discard=on,iothread=1,serial=SN005
qm set 102 --scsi6 synology:102/vm-102-disk-5.qcow2,discard=on,iothread=1,serial=SN006

```

*(Catatan: Perintah ini menambahkan `,serial=SN00x` secara paksa ke file konfigurasi VM Anda.)*

### 3. Cek Kembali di Proxmox

Setelah Anda jalankan perintah di atas:

* Kembali ke menu **Hardware** VM 102.
* Sekarang di kolom **Description** untuk scsi1 sampai scsi6 seharusnya sudah muncul tulisan **serial=SN001**, dst.

### 4. Restart dan Cek VM SAN

* **Shutdown** VM SAN, lalu **Start** lagi.

---

### Kenapa harus lewat Shell?

Terkadang tampilan Web GUI Proxmox menyembunyikan opsi tertentu jika konfigurasi disk awalnya dibuat secara manual atau berasal dari storage eksternal seperti Synology Anda. Dengan perintah `qm set`, kita menulis langsung ke sistem konfigurasi Proxmox tanpa lewat menu yang "buta" tadi.
