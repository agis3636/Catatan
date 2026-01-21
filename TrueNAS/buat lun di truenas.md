kita akan eksekusi pembuatan **4 Zvol** secara berurutan. Karena kamu menggunakan **TrueNAS**, ingatlah bahwa Zvol ini akan dideteksi oleh Proxmox, ESXi, dan OS lainnya sebagai **Raw Disk** (seperti menancapkan hard disk baru lewat kabel).

Silakan buka menu **Storage**, klik pada titik tiga di sebelah Pool kamu, dan pilih **Add Zvol**. Lakukan langkah ini 4 kali dengan panduan setting berikut:

---

### 1. Zvol untuk Proxmox (LUN 1)

* **Zvol Name:** `LUN_Proxmox`
* **Size:** Sesuaikan kebutuhan (misal: 500 GB).
* **Sparse:** **Centang (Check)**. (Ini agar kapasitas hanya terpakai saat ada isinya, sangat berguna karena kamu punya disk yang ukurannya campur-campur).
* **Block Size:** Biarkan default (biasanya 16K atau 32K).
* **Sync:** `Standard` atau `Always`.

### 2. Zvol untuk VMware ESXi (LUN 2)

* **Zvol Name:** `LUN_ESXi`
* **Size:** Sesuaikan kebutuhan.
* **Sparse:** **Centang (Check)**.
* **Block Size:** Untuk ESXi, disarankan **64K** agar lebih optimal dengan sistem file VMFS milik VMware.
* **Sync:** `Standard`.

### 3. Zvol untuk Windows OS (LUN 3)

* **Zvol Name:** `LUN_Windows`
* **Size:** Sesuaikan kebutuhan.
* **Sparse:** **Centang (Check)**.
* **Block Size:** Biarkan default.
* **Sync:** `Standard`.
* *Catatan: Nanti di Windows, disk ini akan muncul di "Disk Management" dan harus kamu format ke NTFS.*

### 4. Zvol untuk Linux (LUN 4)

* **Zvol Name:** `LUN_Linux`
* **Size:** Sesuaikan kebutuhan.
* **Sparse:** **Centang (Check)**.
* **Block Size:** Biarkan default.
* **Sync:** `Standard`.

---

Untuk membuat iSCSI LUN yang bisa dibaca oleh Proxmox, ESXi, dan Windows, kita akan menggunakan fitur **Wizard** agar konfigurasinya otomatis terhubung antara Portal, Target, dan Extent.

Berikut adalah langkah-langkah untuk membuat keempat LUN tersebut:

---

### Langkah 1: Jalankan iSCSI Wizard

Di kotak **Block (iSCSI) Shares Targets**, klik tombol **Wizard**. Ini jauh lebih cepat daripada membuat manual satu per satu.

### Langkah 2: Isi Detail LUN Pertama (Proxmox)

Ulangi langkah ini untuk setiap LUN. Untuk yang pertama (Proxmox):

1. **Name:** Beri nama share, misal `iscsi_proxmox`.
2. **Type:** Pilih **Device**.
3. **Device:** Pilih Zvol yang sudah kamu buat tadi (`pool/LUN_Proxmox`).
4. **Sharing Platform:** Pilih **Proxmox** (atau Modern OS jika tidak ada pilihan spesifik).
5. **Portal:** Jika belum ada, klik **Create New**. Masukkan IP TrueNAS kamu di bagian *IP Address*.
6. **Initiator:** Biarkan kosong atau pilih **Allow All** untuk tahap awal agar koneksi tidak ditolak.

Ulangi proses Wizard ini untuk `LUN_ESXi`, `LUN_Windows`, dan `LUN_Linux`.

---

### Langkah 3: Nyalakan Service iSCSI

Lihat pada gambar ketiga kamu, ada tulisan **STOPPED** berwarna merah di sebelah *Block (iSCSI) Shares Targets*.

1. Klik titik tiga (**⋮**) di pojok kanan kotak iSCSI tersebut.
2. Pilih **Turn On Service**.
3. Pastikan indikator berubah menjadi **RUNNING** (Hijau).
