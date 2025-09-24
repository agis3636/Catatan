# Cara Benerin IP & Hostname Nested Proxmox

### 1. Cara Fix Hostname Proxmox

Ada 2 file yang perlu dicek dan disamakan:

- Cek hostname sistem

```bash
hostnamectl
```

Pastikan sesuai, misalnya proxmox-node1.

- Edit file /etc/hostname

```bash
nano /etc/hostname
```

Ubah isinya jadi nama host sesuai node.
Contoh Node1:

```bash
proxmox-node1
```

- Edit file /etc/hosts

Pastikan sudah seperti yang kita bahas sebelumnya:

```bash
127.0.0.1       localhost
10.20.30.11     proxmox-node1
```

ini jika ada 3 vm proxmox, nanti bisa saling terhubung (opsional):

```bash
127.0.0.1       localhost
10.20.30.11     proxmox-node1
10.20.30.12     proxmox-node2
10.20.30.13     proxmox-node3
```

- Restart PVE services

Setelah itu, jalankan:

```bash
systemctl restart pveproxy
systemctl restart pvedaemon
```

Kalau masih ngeyel, bisa sekalian reboot node:

```bash
reboot
```

👉 Setelah langkah ini, di Web GUI tiap node akan tampil dengan nama unik (proxmox-node1).

---

### 2. Edit `/etc/hosts`

Buka file:

```bash
nano /etc/hosts
```

Sesuaikan misalnya seperti ini (anggap pakai jaringan 192.168.100.x):

```
127.0.0.1       localhost
192.168.100.11  proxmox-node1
```

perintah di bawah ini, kalau ingin saling terhubung dengan vm proxmox lain (optional):

```
127.0.0.1       localhost
192.168.100.11  proxmox-node1
192.168.100.12  proxmox-node2
192.168.100.13  proxmox-node3
```

**Catatan**: IP & hostname harus konsisten di semua node.

---

### 3. Ganti IP Address Tiap VM

Edit network config di:

```bash
nano /etc/network/interfaces
```

Contoh Node1 (IP 192.168.100.11):

```
auto lo
iface lo inet loopback

auto ens18
iface ens18 inet static
    address 192.168.100.11/24
    gateway 192.168.100.1
```

> `ens18` = nama NIC di VM. Bisa jadi `eth0` atau lain → cek dulu pakai `ip a`.

---

### 4. Restart Network / Reboot

Setelah diubah, restart network:

```bash
systemctl restart networking
atau reboot VM.
```

---

Kalau bisa, berarti jaringan & hostname sudah beres.
