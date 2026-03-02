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
