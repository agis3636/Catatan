Siap, mari kita hubungkan TrueNAS kamu ke **Proxmox**. Pastikan status iSCSI di TrueNAS sudah **RUNNING** (hijau) sebelum memulai langkah ini.

### Cara Menambah iSCSI ke Proxmox

1. **Buka Web GUI Proxmox** kamu.
2. Pergi ke menu **Datacenter** > **Storage** > **Add** > **iSCSI**.
3. Isi kolom yang muncul sebagai berikut:
* **ID:** Beri nama bebas (misal: `iSCSI_TrueNAS`).
* **Portal:** Masukkan **IP Address TrueNAS** kamu.
* **Target:** Klik tanda panah bawah (drop-down). Jika koneksi lancar, Proxmox akan otomatis mendeteksi nama target yang kamu buat di TrueNAS tadi (misal: `iqn.2026-01.truenas:storage`).
* **Nodes:** Pilih `All` agar semua node di cluster kamu bisa melihat storage ini.
* **Enable:** Pastikan dicentang.
* **Content:** Biarkan default (Disk Image).



---

### Langkah Penting: Menambah LVM di Atas iSCSI

Setelah iSCSI terhubung, storage tersebut belum bisa langsung dipakai untuk simpan VM karena statusnya masih "Raw Block". Kamu harus melapisinya dengan **LVM**:

1. Klik lagi **Add** > **LVM**.
2. **ID:** Beri nama (misal: `VM_Storage_TrueNAS`).
3. **Base Storage:** Pilih ID iSCSI yang baru saja kamu buat (`iSCSI_TrueNAS`).
4. **Base Volume:** Klik drop-down, lalu pilih **LUN 0** (ini adalah `LUN_Proxmox` yang kita buat di TrueNAS).
5. **Volume Group:** Beri nama baru (misal: `vg_proxmox`).
6. **Content:** Pilih **Disk Image** dan **Container**.
7. **Shared:** **WAJIB CENTANG**. Karena kamu menggunakan TrueNAS sebagai SAN, ini memungkinkan fitur *Live Migration* antar node Proxmox tanpa mematikan VM.

---

### Cek Hasilnya

Jika berhasil, di sebelah kiri (tree menu) Proxmox kamu akan muncul storage baru bernama `VM_Storage_TrueNAS`. Sekarang kamu bisa membuat VM baru dan mengarahkan "Hard Disk"-nya ke storage tersebut.

