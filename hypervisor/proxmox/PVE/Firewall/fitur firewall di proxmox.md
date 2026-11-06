Firewall di Proxmox adalah fitur keamanan untuk mengatur trafik jaringan yang boleh masuk dan keluar dari Proxmox Host maupun VM/LXC.

Sederhananya, firewall itu seperti satpam yang memeriksa siapa yang boleh masuk dan siapa yang harus ditolak.

## 1. Firewall Proxmox bekerja di mana?

Ada 3 level konfigurasi:

![Proxmox VE - 7.2-3 | SysNetBR](https://images.openai.com/static-rsc-4/dRsa39uGthdkCxm9ieoMaMKhiwFIwF1Byf_fqTHsV4VuXOPG2w6rf9chTkb-LXO1OyBsRjBnqWM0Me4PfF-dxopHSN23bHMQfLUdrXMuIlUY4JsdavAVGEjxRU8gKXMI7kUV2GRQ0VYRRcFzV7DMHHIL8x9nRfD37EGiJhQmdqM?purpose=inline)

![sysnetbr.eng.br](https://www.sysnetbr.eng.br/docs/Linux/intermediario/virtualizacao/Proxmox/?utm_source=chatgpt.com)

| Level      | Fungsi                                           |
| ---------- | ------------------------------------------------ |
| Datacenter | Aturan firewall untuk seluruh lingkungan Proxmox |
| Node       | Aturan firewall untuk Proxmox Host               |
| VM/CT      | Aturan firewall khusus VM atau Container         |

## 2. Contoh penerapan

Misalnya lu punya:

| Perangkat      | VLAN       | IP           |
| -------------- | ---------- | ------------ |
| Ubuntu Server  | 10         | 10.10.10.10  |
| Windows Server | 20         | 20.20.20.10  |
| Proxmox        | Management | 192.168.1.10 |

Lu ingin membuat aturan:

- Ubuntu Server boleh diakses melalui SSH (port 22).
- Windows Server boleh diakses melalui RDP (port 3389).
- VLAN 20 tidak boleh mengakses SSH Ubuntu Server.
- Proxmox Web GUI hanya boleh diakses dari jaringan management (port 8006).

Firewall bisa digunakan untuk menerapkan aturan tersebut.

## 3. Contoh aturan firewall

Misalnya pada VM Ubuntu Server:

| Direction | Action | Protocol | Port | Source         |
| --------- | ------ | -------- | ---- | -------------- |
| IN        | ACCEPT | TCP      | 22   | 192.168.1.0/24 |
| IN        | ACCEPT | TCP      | 80   | 10.10.10.0/24  |
| IN        | DROP   | ALL      | ALL  | 20.20.20.0/24  |

Artinya:

- Jaringan management boleh SSH.
- Jaringan VLAN 10 boleh mengakses web server.
- Jaringan VLAN 20 ditolak.

## 4. Bedanya Firewall MikroTik dan Proxmox

| Firewall MikroTik          | Firewall Proxmox                |
| -------------------------- | ------------------------------- |
| Melindungi jaringan        | Melindungi host dan VM          |
| Mengatur trafik antar-VLAN | Mengatur trafik ke/dari VM      |
| Mengatur akses internet    | Mengatur akses layanan VM       |
| Beroperasi di router       | Beroperasi di host virtualisasi |

Keduanya bisa digunakan bersamaan.

## 5. Hubungannya dengan VLAN

Ini menarik.

Misalnya lu punya VLAN 10 untuk IT dan VLAN 20 untuk Finance.

Tanpa aturan firewall, VLAN 10 dan VLAN 20 tetap terpisah secara Layer 2, tetapi jika MikroTik mengizinkan routing antar-VLAN, keduanya bisa berkomunikasi.

Dengan firewall, lu bisa mengatur komunikasi tersebut.

Kesimpulannya: VLAN membagi jaringan, sedangkan firewall mengatur siapa yang boleh berkomunikasi.

Analogi:

- VLAN = membagi gedung menjadi beberapa ruangan.
- Firewall = mengatur siapa yang boleh masuk ke setiap ruangan.
