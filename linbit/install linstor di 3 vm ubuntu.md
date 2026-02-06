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




---

---

---

Untuk mendapatkan tampilan Web UI LINSTOR (Dashboard), kamu perlu menginstal paket tambahan bernama **LINSTOR Gateway** atau **LINSTOR GUI**. Karena kamu menggunakan 3 VM berbasis Linux (kemungkinan Ubuntu/Debian berdasarkan gambar sebelumnya), berikut adalah cara mengaktifkannya agar bisa diakses lewat browser.

Semua langkah di bawah ini dilakukan **HANYA di VM 1 (Controller - 192.168.100.10)**.

## 4. Instalasi LINSTOR GUI

Ketik perintah ini di **VM 1 (Controller)**:

```bash
apt install linstor-gui linstor-controller

```

*Catatan: Jika kamu ingin menggunakan versi Docker (lebih praktis), kamu bisa menjalankan image `linbit/linstor-gui`.*

## 5. Jalankan Service

Pastikan layanan controller dan GUI sudah berjalan:

```bash
systemctl enable --now linstor-controller
systemctl enable --now linstor-gui

```

## 6. Cara Akses Web UI

Buka browser (Chrome/Firefox) di komputer kamu, lalu masukkan alamat IP VM 1:

* **Alamat:** `http://192.168.100.10:3370`
* **Port Default:** 3370

---

### **PENTING: Kenapa "No Data" atau Error Muncul Lagi?**

Jika kamu sudah berhasil masuk ke Web UI tapi melihat pesan "No Data" atau tidak bisa klik Submit, itu karena **VM 2 (100.20)** dan **VM 3 (100.30)** kamu belum punya disk cadangan yang siap dipakai LINSTOR.

**Lakukan ini sekarang sebelum setting GUI:**

1. **Di Proxmox:** Tambahkan "Hard Disk" baru (misal 100GB) ke VM 2 dan VM 3.
2. **Cek di Terminal VM 2 & 3:** Ketik `lsblk`. Kamu harus melihat ada `/dev/sdb` (disk 100G) yang muncul di sana.
3. **Hapus Konfigurasi Lama:** Di terminal **VM 1 (Controller)**, ketik ini untuk membersihkan sisa error kemarin:
```bash
linstor node delete satellite-1
linstor node delete satellite-2
linstor node delete controller

```


4. **Daftarkan Ulang:** Gunakan perintah `linstor node create` seperti yang saya berikan sebelumnya agar statusnya `ONLINE` di Dashboard.

---

**Langkah Selanjutnya:**
Setelah `lsblk` di VM 2 dan VM 3 menunjukkan adanya disk baru (sdb), apakah kamu ingin saya pandu membuat **Storage Pool** langsung dari Web UI-nya?
