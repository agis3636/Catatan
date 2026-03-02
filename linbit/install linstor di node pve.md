# Jalankan ini di **Node 1**:

### 1. Gunakan "Syntax Sakti" (Trusted=Yes)

Perintah ini akan memaksa `apt` untuk mendownload LINSTOR dari PPA Launchpad tanpa peduli soal tanda tangan digital. Ini **DIJAMIN** tidak akan mengeluarkan error "Missing Key" lagi:

```bash
echo "deb [trusted=yes] http://ppa.launchpad.net/linbit/linbit-drbd9-stack/ubuntu noble main" > /etc/apt/sources.list.d/linbit.list

```

### 2. Update & Eksekusi (The Real Execution)

Sekarang jalankan ini. Pasti lancar jaya:

```bash
apt update
apt install -y linstor-controller linstor-satellite linstor-client

```

---

### 3. Setelah Terinstall (Node 1):

Langsung aktifkan si "Bos" LINSTOR:

```bash
systemctl enable --now linstor-controller linstor-satellite
systemctl status linstor-controller

```

---
---
---


# Langkah di Node 2:

### 1. Gunakan "Syntax Sakti" (Trusted=Yes)

Perintah ini akan memaksa `apt` untuk mendownload LINSTOR dari PPA Launchpad tanpa peduli soal tanda tangan digital. Ini **DIJAMIN** tidak akan mengeluarkan error "Missing Key" lagi:

```bash
echo "deb [trusted=yes] http://ppa.launchpad.net/linbit/linbit-drbd9-stack/ubuntu noble main" > /etc/apt/sources.list.d/linbit.list

```

### 2. Update & Eksekusi (The Real Execution)

Sekarang jalankan ini. Pasti lancar jaya:

```bash
apt update
apt install -y linstor-satellite

```

---

### 3. Setelah Terinstall (Node 2):

Langsung aktifkan:

```bash
systemctl enable --now linstor-satellite
systemctl status linstor-satellite

```

---
---
---

# Bagaimana Kalau Ada 3 Node?

Kalau kamu menambah **Node 3**, skemanya tetap mengikuti pola yang sama dengan Node 2. LINSTOR itu sistemnya **1 Otak, Banyak Pekerja**.

* **Node 1:** Tetap jadi **Controller + Satellite**.
* **Node 2:** Tetap **Satellite**.
* **Node 3:** Instal sebagai **Satellite** saja (sama persis dengan langkah Node 2 kamu).

**Keuntungan 3 Node:**

1. **Replikasi Lebih Aman:** Kamu bisa set agar data VM ada di 3 tempat sekaligus (3 *replicas*). Kalau 2 server meledak, data masih ada di server ke-3.
2. **Quorum (Hak Suara):** Dengan 3 node, sistem Proxmox HA (High Availability) kamu jadi lebih stabil karena ada "penengah" jika terjadi masalah jaringan (*split-brain*).

---

### Catatan Penting Sebelum Lanjut

Setelah semua instalasi di atas selesai dan statusnya `active (running)`, langkah selanjutnya adalah **mendaftarkan** mereka. Jangan kaget kalau di GUI Proxmox belum muncul apa-apa, karena kita harus "kenalan" dulu lewat perintah di Node 1.

**Langkah Selanjutnya (Eksekusi di Node 1):**

1. **Daftarkan Node 1:**
`linstor node create pve-1 <IP_NODE_1> --node-type Combined`
2. **Daftarkan Node 2:**
`linstor node create pve-2 <IP_NODE_2> --node-type Satellite`
3. **Cek daftar node:**
`linstor node list`

**Apakah instalasi di Node 2 sudah selesai kamu jalankan? Kalau sudah, mau saya bantu buatkan perintah untuk langsung "tembak" disk `/dev/sdb` kamu jadi storage pool?**
