Untuk mengubah dari DHCP ke statis, hapus atau ubah bagian ini:
Yang Asal nya:

    iface ens18 inet dhcp
Ganti Jadi:

    iface ens18 inet static
        address 192.168.1.100
        netmask 255.255.255.0
        gateway 192.168.1.1
        dns-nameservers 8.8.8.8 1.1.1.1

Sesuaikan IP, netmask, gateway, dan DNS sesuai jaringan kamu.
---
p﻿ertama-tama ketik perintah untuk mengubah file network interface debian

    sudo nano /etc/network/interfaces
---
🔧 File /etc/network/interfaces Hasil Akhir:

    source /etc/network/interfaces.d/*
    
    # The loopback network interface
    auto lo
    iface lo inet loopback
    
    # The primary network interface
    auto ens18
    iface ens18 inet static
        address 192.168.1.100
        netmask 255.255.255.0
        gateway 192.168.1.1
        dns-nameservers 8.8.8.8 1.1.1.1
---
Tambahkan auto ens18 supaya interface otomatis aktif saat boot.
---
🔁 Restart Jaringan:
Setelah menyimpan file (Ctrl+O, lalu Ctrl+X), restart jaringan:

    sudo systemctl restart networking
    atau
    sudo ifdown ens18 && sudo ifup ens18
Kalau pakai SSH, jangan restart dari jauh sebelum yakin IP baru benar, bisa putus koneksi.
---
✅ Cek:

    ip a
    ip route
    ping 8.8.8.8
Kalau semua lancar, berarti IP statis sudah berhasil diatur.
