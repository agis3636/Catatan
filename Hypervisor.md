# Pengertian Hypervisor

**Hypervisor** adalah **software atau firmware yang memungkinkan satu komputer fisik menjalankan beberapa sistem operasi sekaligus dalam bentuk mesin virtual (Virtual Machine / VM).**

Hypervisor bertugas **mengelola sumber daya hardware** seperti:

* CPU
* RAM
* Storage
* Network

agar dapat digunakan oleh beberapa **VM secara bersamaan**.

Secara sederhana:

**Hypervisor = pengatur lalu lintas antara hardware dan mesin virtual.**

Tanpa hypervisor, komputer hanya bisa menjalankan **1 sistem operasi langsung di hardware**.

---

# Konsep Virtualisasi

Hypervisor bekerja dengan konsep **virtualisasi**.

Virtualisasi adalah teknologi yang memungkinkan:

* satu server fisik
* menjalankan banyak **server virtual**

Contoh:

1 server fisik spesifikasi:

* CPU 8 core
* RAM 32 GB
* SSD 1 TB

bisa dibagi menjadi:

| VM  | OS             | RAM  |
| --- | -------------- | ---- |
| VM1 | Linux Server   | 8 GB |
| VM2 | Windows Server | 8 GB |
| VM3 | Ubuntu         | 4 GB |
| VM4 | Debian         | 4 GB |

Semua berjalan **di satu hardware yang sama**.

---

# Fungsi Hypervisor

Fungsi utama hypervisor:

### 1 Mengelola Virtual Machine

Hypervisor membuat, menjalankan, dan menghapus VM.

Contoh:

* start VM
* stop VM
* restart VM

---

### 2 Membagi Resource Hardware

Hypervisor membagi:

* CPU
* RAM
* Disk
* Network

ke setiap VM.

Contoh:

VM1 → 2 CPU
VM2 → 4 CPU
VM3 → 1 CPU

---

### 3 Isolasi Sistem

Setiap VM **terisolasi satu sama lain**.

Artinya:

* jika satu VM crash
* VM lain tetap berjalan

Ini penting untuk **keamanan dan stabilitas server**.

---

### 4 Monitoring Resource

Hypervisor bisa memonitor:

* penggunaan CPU
* penggunaan RAM
* penggunaan storage
* network traffic

---

### 5 Manajemen Infrastruktur Virtual

Hypervisor memungkinkan:

* snapshot VM
* cloning VM
* backup VM
* migrasi VM

---

# Jenis Hypervisor

Hypervisor terbagi menjadi **2 jenis utama**.

---

# 1 Hypervisor Type 1 (Bare Metal)

Hypervisor **langsung berjalan di atas hardware** tanpa sistem operasi lain.

Struktur:

```
Hardware
   ↓
Hypervisor
   ↓
Virtual Machine
   ↓
Guest OS
```

Artinya:

Hypervisor **langsung mengontrol hardware**.

---

## Kelebihan Type 1

* performa lebih cepat
* lebih stabil
* lebih aman
* cocok untuk server production

---

## Kekurangan

* instalasi lebih kompleks
* biasanya butuh server khusus

---

## Contoh Hypervisor Type 1

Beberapa hypervisor type 1 yang terkenal:

### 1. VMware ESXi

Hypervisor enterprise dari VMware.

Fitur:

* sangat stabil
* digunakan di data center
* mendukung clustering

---

### 2. Microsoft Hyper‑V

Hypervisor dari Microsoft.

Digunakan pada:

* Windows Server
* data center

---

### 3. Proxmox VE

Hypervisor open source berbasis Linux.

Fitur:

* KVM virtualization
* LXC container
* web management

Banyak dipakai di:

* lab jaringan
* hosting
* cloud server

---

### 4. Citrix Hypervisor

Dulu dikenal sebagai **XenServer**.

Dipakai di enterprise virtualization.

---

# 2 Hypervisor Type 2 (Hosted Hypervisor)

Hypervisor berjalan **di atas sistem operasi**.

Struktur:

```
Hardware
   ↓
Host OS
   ↓
Hypervisor
   ↓
Virtual Machine
   ↓
Guest OS
```

Contoh:

Laptop → Windows → VirtualBox → Linux

---

## Kelebihan

* mudah diinstall
* cocok untuk belajar
* cocok untuk development

---

## Kekurangan

* performa lebih lambat
* tergantung OS host
* tidak cocok untuk production server besar

---

## Contoh Hypervisor Type 2

---

### 1. Oracle VM VirtualBox

Hypervisor open source yang populer.

Digunakan untuk:

* belajar Linux
* testing server
* lab jaringan

---

### 2. VMware Workstation

Versi desktop dari VMware.

Digunakan untuk:

* development
* testing OS

---

### 3. Parallels Desktop

Digunakan di komputer Apple Mac.

---

# Perbandingan Hypervisor Type 1 dan Type 2

| Aspek      | Type 1               | Type 2        |
| ---------- | -------------------- | ------------- |
| Lokasi     | langsung di hardware | di atas OS    |
| Performa   | sangat tinggi        | lebih rendah  |
| Penggunaan | data center          | desktop       |
| Stabilitas | sangat stabil        | tergantung OS |
| Contoh     | ESXi, Proxmox        | VirtualBox    |

---

# Cara Kerja Hypervisor

Ketika VM berjalan:

1. Guest OS meminta resource

Contoh:

Linux di VM meminta CPU.

2. Permintaan dikirim ke hypervisor

3. Hypervisor menerjemahkan ke hardware fisik

4. Hardware menjalankan proses

5. Hasil dikembalikan ke VM

---

# Komponen Virtual Machine

Setiap VM memiliki hardware virtual seperti:

* vCPU
* vRAM
* vDisk
* vNIC (network card)

Padahal sebenarnya semuanya **dipinjam dari hardware fisik**.

---

# Teknologi Pendukung Hypervisor

Hypervisor modern menggunakan fitur CPU seperti:

### 1 Intel VT-x

Teknologi virtualisasi CPU dari Intel.

---

### 2 AMD-V

Teknologi virtualisasi CPU dari AMD.

Digunakan oleh:

* KVM
* VMware
* Hyper-V

---

# Contoh Penggunaan Hypervisor

### Data Center

Satu server fisik bisa menjalankan:

* server database
* server web
* server email
* server file

---

### Cloud Computing

Layanan cloud seperti:

* AWS
* Google Cloud
* Azure

menggunakan hypervisor untuk membuat **instance server virtual**.

---

### Lab IT

Kita dapat menjalankan banyak OS di satu laptop.

Contoh:

* Windows
* Ubuntu
* Kali Linux
* Windows Server

---

### Testing Software

Developer bisa:

* test aplikasi di banyak OS
* tanpa banyak komputer fisik

---

# Hypervisor vs Container

Sering mahasiswa bingung dengan container.

Perbedaannya:

| Hypervisor             | Container       |
| ---------------------- | --------------- |
| menjalankan OS lengkap | share kernel OS |
| lebih berat            | lebih ringan    |
| VM                     | container       |

Contoh container:

* Docker
* Kubernetes

---

# Kelebihan Virtualisasi dengan Hypervisor

1. Efisiensi hardware
2. Menghemat biaya server
3. Mudah backup
4. mudah cloning server
5. fleksibel

---

# Kekurangan Hypervisor

1. butuh resource besar
2. konfigurasi cukup kompleks
3. jika hypervisor crash → semua VM bisa ikut down

---

# Contoh Topologi Virtualisasi

Server fisik:

```
Server
CPU 16 core
RAM 64 GB
SSD 2 TB
```

Hypervisor:

```
Proxmox
```

VM:

```
VM1 Web Server
VM2 Database Server
VM3 Mail Server
VM4 Firewall
```

---

# Kesimpulan

Hypervisor adalah teknologi penting dalam dunia:

* cloud computing
* data center
* virtualisasi server
* lab IT

Dengan hypervisor, satu komputer fisik dapat menjalankan banyak sistem operasi secara bersamaan secara efisien.
