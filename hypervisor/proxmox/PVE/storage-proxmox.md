# daftar **storage types** yang ada di **Proxmox VE** (PVE). penjelasan lengkap untuk masing-masing storage type, termasuk **cara kerjanya, kelebihan, kekurangan, dan contoh penggunaannya**.
---

## **1. Directory**

* **Jenis:** Local storage berbasis folder/direktori di host node.
* **Cara Kerja:** Menyimpan VM disk image, ISO, template, backup, dan container secara langsung di folder pada filesystem lokal (misal: `/var/lib/vz`).
* **Kelebihan:**

  * Mudah di-setup.
  * Tidak perlu konfigurasi khusus.
* **Kekurangan:**

  * Hanya bisa diakses oleh node lokal (tidak shared).
  * Skalabilitas terbatas.
* **Penggunaan:** Cocok untuk lab kecil atau single-node server.

---

## **2. LVM (Logical Volume Manager)**

* **Jenis:** Block storage berbasis logical volume.
* **Cara Kerja:** Membagi storage fisik (disk) menjadi logical volumes. VM disk ditempatkan di logical volume ini.
* **Kelebihan:**

  * Mendukung snapshot (jika LVM thin).
  * Performa tinggi.
* **Kekurangan:**

  * Tidak langsung bisa di-share ke node lain tanpa clustering (kecuali LVM thin pool + shared storage).
* **Penggunaan:** Cocok untuk production node dengan kebutuhan performa tinggi.

---

## **3. LVM-Thin**

* **Jenis:** LVM dengan thin provisioning.
* **Cara Kerja:** Disk virtual VM dialokasikan secara dinamis sesuai kebutuhan, tidak langsung full size.
* **Kelebihan:**

  * Efisien untuk storage karena tidak menghabiskan semua kapasitas di awal.
  * Mendukung snapshot.
* **Kekurangan:**

  * Monitoring penggunaan penting agar tidak over-provisioned.
* **Penggunaan:** VM dengan disk yang fleksibel dan sering di-snapshot.

---

## **4. BTRFS**

* **Jenis:** Filesystem modern dengan fitur advanced.
* **Cara Kerja:** Menyimpan VM disk dan data langsung di filesystem BTRFS.
* **Kelebihan:**

  * Mendukung snapshot dan subvolume.
  * Mendukung compression.
* **Kekurangan:**

  * Performa tidak sebaik LVM atau ZFS untuk beberapa kasus.
  * Kurang mature dibanding ZFS untuk production.
* **Penggunaan:** Cocok untuk eksperimen atau lab yang ingin fitur snapshot cepat tanpa LVM.

---

## **5. NFS**

* **Jenis:** Network File System (shared storage).
* **Cara Kerja:** Mount folder dari server NFS lain ke node Proxmox.
* **Kelebihan:**

  * Bisa diakses multi-node (shared storage).
  * Mendukung VM disk, template, ISO, backup.
* **Kekurangan:**

  * Performa bergantung pada jaringan.
  * Tidak mendukung block-level snapshot (hanya file copy snapshot).
* **Penggunaan:** Cocok untuk cluster dengan shared storage sederhana.

---

## **6. SMB/CIFS**

* **Jenis:** Network file sharing berbasis Windows/Samba.
* **Cara Kerja:** Mount shared folder dari server Windows atau Samba.
* **Kelebihan:**

  * Mudah integrasi dengan Windows environment.
  * Bisa multi-node jika share dapat diakses semua node.
* **Kekurangan:**

  * Tidak secepat NFS.
  * Tidak mendukung snapshot.
* **Penggunaan:** Backup VM atau ISO di lingkungan Windows.

---

## **7. GlusterFS**

* **Jenis:** Distributed storage / clustered file system.
* **Cara Kerja:** Menggabungkan beberapa server menjadi satu volume shared.
* **Kelebihan:**

  * Redundansi data.
  * Bisa diakses multi-node.
* **Kekurangan:**

  * Setup lebih kompleks.
  * Performa jaringan mempengaruhi VM.
* **Penggunaan:** Cocok untuk cluster besar dengan kebutuhan shared storage yang redundant.

---

## **8. iSCSI**

* **Jenis:** Block-level network storage.
* **Cara Kerja:** Node Proxmox mengakses storage blok melalui jaringan IP.
* **Kelebihan:**

  * Bisa di-share multi-node.
  * Mendukung VM dengan performa block storage.
* **Kekurangan:**

  * Perlu jaringan yang stabil dan cepat.
  * Memerlukan setup target iSCSI di storage server.
* **Penggunaan:** Enterprise storage, SAN virtual, VM disk performa tinggi.

---

## **9. CephFS**

* **Jenis:** Part dari Ceph cluster (distributed storage).
* **Cara Kerja:** Menyimpan file di Ceph cluster dengan redundansi.
* **Kelebihan:**

  * Redundansi tinggi, scalable.
  * Bisa diakses semua node cluster.
* **Kekurangan:**

  * Setup kompleks.
  * Performa dipengaruhi cluster dan jaringan.
* **Penggunaan:** Cluster besar dengan kebutuhan HA dan shared storage.

---

## **10. RBD (RADOS Block Device)**

* **Jenis:** Block storage dari Ceph.
* **Cara Kerja:** Menggunakan Ceph untuk menyediakan disk virtual VM.
* **Kelebihan:**

  * Redundansi Ceph.
  * Mendukung snapshot dan thin provisioning.
  * Bisa diakses multi-node.
* **Kekurangan:**

  * Memerlukan setup Ceph yang stabil.
* **Penggunaan:** VM di cluster Proxmox dengan Ceph backend.

---

## **11. ZFS over iSCSI**

* **Jenis:** ZFS sebagai backend, diakses lewat iSCSI.
* **Cara Kerja:** Storage ZFS di server lain di-expose sebagai iSCSI ke Proxmox.
* **Kelebihan:**

  * Fitur ZFS lengkap (snapshot, compression).
  * Bisa multi-node via iSCSI.
* **Kekurangan:**

  * Perlu server tambahan untuk ZFS + jaringan cepat.
* **Penggunaan:** VM dengan fitur ZFS tapi storage terpisah.

---

## **12. ZFS**

* **Jenis:** Local filesystem dengan fitur canggih.
* **Cara Kerja:** Menyimpan VM disk, template, backup di pool ZFS lokal.
* **Kelebihan:**

  * Mendukung snapshot, compression, deduplication.
  * Performa bagus untuk SSD.
* **Kekurangan:**

  * Menghabiskan RAM (ARC).
  * Perlu planning kapasitas dan pool.
* **Penggunaan:** Local VM storage dengan fitur snapshot lengkap.

---

## **13. Proxmox Backup Server**

* **Jenis:** Storage khusus backup.
* **Cara Kerja:** Menyimpan backup VM dan container di Proxmox Backup Server.
* **Kelebihan:**

  * Deduplication.
  * Enkripsi, versi backup, efficient storage.
* **Kekurangan:**

  * Hanya untuk backup, bukan running VM.
* **Penggunaan:** Backup VM cluster, disaster recovery.

---
