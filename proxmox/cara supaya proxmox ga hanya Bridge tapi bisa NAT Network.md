Konfigurasi yang Anda lampirkan adalah skrip untuk membuat **Virtual Switch (Bridge)** yang berfungsi sebagai **Gateway NAT**. Ini memungkinkan VM Anda memiliki segmen IP sendiri (192.168.100.x) dan tetap bisa akses internet melalui IP asli Proxmox (192.168.2.247).

Berikut adalah langkah-langkah lengkap dan cara menerapkannya agar tidak error.

---

### 1. Edit File Konfigurasi Network

Buka terminal Proxmox Anda (bisa via SSH atau Shell di GUI) dan edit file `/etc/network/interfaces`:

```bash
nano /etc/network/interfaces

```

### 2. Tambahkan Konfigurasi NAT

Scroll ke bagian paling bawah, lalu tempelkan konfigurasi berikut. Saya sudah menyesuaikan `-o vmbr0` karena IP utama Proxmox Anda (192.168.2.247) berada di `vmbr0`.

```text
auto vmbr1
iface vmbr1 inet static
        address 192.168.100.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        
        # NAT SETTINGS
        # Mengaktifkan IP Forwarding di sistem
        post-up echo 1 > /proc/sys/net/ipv4/ip_forward
        
        # Rule Iptables agar segmen 100.0 bisa internetan lewat vmbr0 (IP Proxmox)
        post-up iptables -t nat -A POSTROUTING -s '192.168.100.0/24' -o vmbr0 -j MASQUERADE
        
        # Menghapus rule saat interface mati agar tidak terjadi duplikasi/error
        post-down iptables -t nat -D POSTROUTING -s '192.168.100.0/24' -o vmbr0 -j MASQUERADE

```

*Simpan dengan menekan `Ctrl+O`, `Enter`, lalu `Ctrl+X`.*

---

### 3. Terapkan Perubahan (Tanpa Restart Host)

Agar konfigurasi langsung jalan tanpa mematikan Proxmox, jalankan perintah ini:

```bash
ifup vmbr1

```

*(Jika muncul error "already exist", jalankan `ifdown vmbr1` baru `ifup vmbr1`)*.

---

### 4. Cara Setting di Sisi VM (Guest)

Setelah `vmbr1` aktif, Anda harus mengatur network di dalam VM (Windows/Linux) agar bisa terhubung:

1. **Di Proxmox GUI:** Ubah Network Device VM tersebut ke **Bridge: vmbr1**.
2. **Di Dalam OS VM:** Atur IP Statis sebagai berikut:
* **IP Address:** `192.168.100.x` (misal 192.168.100.10)
* **Subnet Mask:** `255.255.255.0`
* **Gateway:** `192.168.100.1` (Ini adalah IP si `vmbr1` tadi)
* **DNS:** `8.8.8.8` atau `1.1.1.1`



---

### Penjelasan Logika (Kenapa Begitu?)

* **`address 192.168.100.1/24`**: Proxmox akan bertindak sebagai "Router" dengan IP 100.1 di dalam jaringan internal tersebut.
* **`bridge-ports none`**: Berbeda dengan `vmbr0` yang terhubung ke kabel fisik (eth0), `vmbr1` adalah switch virtual murni.
* **`MASQUERADE`**: Ini adalah fungsi NAT. Proxmox akan "topeng-kan" semua request dari VM (192.168.100.x) seolah-olah berasal dari dirinya sendiri (192.168.2.247) agar bisa diterima oleh router utama Anda.

---
