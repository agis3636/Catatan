Secara teknis **BISA**, tapi secara fungsional **SANGAT TIDAK DISARANKAN**.

Jika tujuan Anda hanya ingin menggabungkan disk dari 2 server tanpa mempedulikan HA (*High Availability*), Ceph bukan alat yang tepat. Ceph didesain untuk minimal 3 node agar bisa mencapai "Quorum" (kesepakatan antar server).

Berikut adalah konsekuensi dan solusi terbaiknya:

### 1. Masalah Utama 2 Node di Ceph

* **Split Brain:** Jika kabel LAN antara Server A dan Server B bermasalah, keduanya akan merasa menjadi "pemimpin" dan data Anda bisa rusak (*corrupt*).
* **Data Terkunci (Read-Only):** Secara *default*, Ceph butuh lebih dari separuh suara untuk bekerja. Jika satu server mati, sisa 1 server tidak mencapai >50%, maka storage akan terkunci (Read-only) dan VM Anda akan *hang*.

### 2. Jika Anda Tetap Memaksa (Hanya untuk Tes)

Anda bisa melakukannya dengan mengubah aturan **Size** (jumlah replika data) dan **Min Size**:

* **Size = 2:** Data disimpan di kedua server.
* **Min Size = 1:** Storage tetap boleh jalan meskipun cuma 1 server yang hidup.
* **Risiko:** Jika server yang hidup itu mati sebelum data sinkron, data Anda hilang permanen.

---

### 3. Solusi Lebih Baik (Tanpa HA, Cuma Gabung Disk)

Karena Anda bilang **"cuma mau gabungin disk 2 server"**, ada alternatif yang lebih stabil untuk 2 node di Proxmox:

**A. Proxmox Replication (ZFS)**
Ini fitur bawaan Proxmox yang jauh lebih stabil untuk 2 node daripada Ceph.

* Gunakan format **ZFS** di kedua server.
* Proxmox akan mengirim salinan data dari Server A ke Server B setiap 1 atau 5 menit.
* *Kelebihan:* Ringan, tidak butuh network kencang seperti Ceph, dan sangat stabil di 2 node.

**B. GlusterFS**
Mirip Ceph tapi lebih ramah untuk 2 node jika menggunakan mode **Replicated**. Namun, setup-nya manual lewat CLI (tidak ada di menu Proxmox GUI langsung).

**C. Menggunakan Disk Server B sebagai Storage di Server A (NFS/SMB)**
Jika Anda cuma mau nambah kapasitas:

* Install OS Linux biasa atau OpenMediaVault di Server B.
* Share foldernya via **NFS**.
* Add NFS tersebut sebagai storage di Proxmox Server A.

### Kesimpulan

Jangan pakai Ceph untuk 2 node kecuali Anda hanya untuk belajar. Jika untuk produksi (kerjaan), gunakan **ZFS Replication**.

Apakah Anda mau saya bantu langkah-langkah setting **ZFS Replication** di Proxmox agar data di kedua server sinkron?
