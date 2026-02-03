Pilihan yang sangat bagus untuk tahap belajar. Menggunakan Ubuntu di dalam VM (Nested Virtualization) adalah cara paling aman untuk "mengoprek" LINSTOR tanpa risiko merusak storage SAN atau OS Proxmox utama Anda.

Ubuntu sangat disarankan karena LINBIT menyediakan repository resmi yang sangat stabil untuk keluarga Debian/Ubuntu.

---

## Arsitektur Lab yang Perlu Anda Siapkan

Untuk simulasi yang realistis, sebaiknya Anda membuat **3 VM Ubuntu** di dalam Proxmox Anda.

* **VM 1 (Controller + Satellite):** Berperan sebagai "Otak" sekaligus penyimpan data.
* **VM 2 (Satellite):** Berperan sebagai penyimpan data.
* **VM 3 (Satellite):** Berperan sebagai penyimpan data.

---

## Spesifikasi Per VM (Rekomendasi)

Karena Anda memiliki sisa 50GB di disk lokal dan ada SAN, gunakan SAN untuk membuat disk VM ini agar lebih leluasa:

* **OS:** Ubuntu 22.04 LTS atau 24.04 LTS.
* **CPU/RAM:** 2 Core / 2 GB RAM (sudah cukup untuk belajar).
* **Disk 1 (OS):** 20 GB (untuk sistem Ubuntu).
* **Disk 2 (Data):** 20-50 GB (Biarkan **KOSONG/Unformatted**. Disk inilah yang akan dikelola LINSTOR).

---

## Langkah-Langkah Instalasi di Ubuntu

### 1. Tambahkan Repository LINBIT (Di semua VM)

Jalankan perintah ini di ketiga VM Ubuntu Anda:

```bash
sudo apt update && sudo apt install -y software-properties-common
sudo add-apt-repository ppa:linbit/linbit-drbd9-stack
sudo apt update

```

### 2. Install Komponen (Di VM 1 saja)

VM 1 akan menjadi Controller (pengatur):

```bash
sudo apt install -y linstor-controller linstor-satellite linstor-client drbd-utils

```

### 3. Install Komponen (Di VM 2 & VM 3)

VM lainnya cukup menjadi Satellite (penyimpan):

```bash
sudo apt install -y linstor-satellite drbd-utils

```

---
