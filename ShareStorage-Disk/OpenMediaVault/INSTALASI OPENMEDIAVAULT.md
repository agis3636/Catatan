## 1. TUJUAN

Menginstal OpenMediaVault (OMV) sebagai NAS Server berbasis Debian Linux yang dapat menyediakan layanan penyimpanan jaringan:

- SMB/CIFS untuk Windows.
- NFS untuk Linux dan Proxmox.
- iSCSI untuk menyediakan block storage (LUN).

Metode instalasi yang digunakan adalah memasang OMV di atas Debian 13 minimal menggunakan installer resmi OMV.

## 2. PERSIAPAN VIRTUAL MACHINE DI PROXMOX

Buat VM baru melalui:

Proxmox → Create VM

Konfigurasi dasar:

| Komponen | Konfigurasi            |
| -------- | ---------------------- |
| Name     | `openmediavault`       |
| OS       | Debian 13              |
| BIOS     | SeaBIOS atau OVMF      |
| Machine  | Default                |
| Disk 1   | 20 GB (System)         |
| Disk 2   | 150 GB (Data NAS)      |
| CPU      | Sesuaikan kebutuhan    |
| RAM      | Sesuaikan kebutuhan    |
| Network  | Bridge ke jaringan NAS |

Pembagian disk:

| Disk   | Kapasitas | Fungsi           |
| ------ | --------- | ---------------- |
| Disk 1 | 20 GB     | OS Debian + OMV  |
| Disk 2 | 150 GB    | Storage data NAS |

Catatan: Disk data jangan digunakan untuk instalasi OS. Pastikan kapasitas dan identitas disk benar sebelum melakukan format.

## 3. INSTALASI DEBIAN 13

### A. Download ISO Debian

Download Debian 13 Netinst melalui situs resmi:

[https://www.debian.org/distrib/netinst](https://www.debian.org/distrib/netinst?utm_source=chatgpt.com)

Penting: Jangan menginstal GNOME, KDE, atau desktop environment lainnya. OMV dirancang untuk dikelola melalui Web UI dan instalasi pada Debian dengan desktop environment tidak didukung.

Analogi: Debian adalah fondasi bangunan, sedangkan OMV adalah sistem manajemen NAS yang dipasang di atasnya. Tidak perlu memasang desktop GUI karena pengelolaannya dilakukan melalui browser.

Selesaikan instalasi Debian sampai VM melakukan reboot.

## 4. INSTALASI OPENMEDIAVAULT

Setelah Debian berhasil boot, login menggunakan akun `root` dan password yang dibuat saat instalasi Debian.

### A. Update Repository Debian

Jalankan:

```
apt update
```

Penjelasan:

- `apt`: package manager Debian.
- `update`: memperbarui daftar paket dari repository.
- Tujuannya agar Debian mengetahui versi paket yang tersedia sebelum instalasi.

### B. Install wget

```
apt install wget -y
```

Penjelasan:

- `wget`: aplikasi untuk mengunduh file dari internet.
- `-y`: otomatis menyetujui konfirmasi instalasi.

### C. Install OpenMediaVault

Jalankan perintah resmi:

```
wget -O - https://get.openmediavault.io | sh -
```

Penjelasan setiap bagian:

| Syntax | Fungsi                                                  |
| ------ | ------------------------------------------------------- |
| `wget` | Mengunduh installer OMV                                 |
| `-O -` | Mengirim hasil download ke standard output              |
| `\\|`  | Mengalirkan hasil perintah ke perintah berikutnya       |
| `sh -` | Menjalankan script yang diterima melalui standard input |

Dengan kata lain, Debian mengunduh script installer resmi OMV kemudian menjalankannya secara langsung.

Catatan: Perintah ini harus dijalankan sebagai root dan membutuhkan koneksi internet.

### D. Proses Instalasi

Installer akan mengerjakan beberapa konfigurasi secara otomatis, antara lain:

- Mengunduh dan memasang paket OpenMediaVault.
- Memasang service administrasi NAS.
- Mengatur repository OMV.
- Membuat database konfigurasi.
- Mengatur service NFS, SMB/CIFS, dan sistem jaringan.
- Mengaktifkan engine OMV.
- Mengatur konfigurasi sistem melalui Salt.

Tunggu hingga instalasi selesai dan kembali ke terminal.

Pada lab ini, hasil instalasi menunjukkan:

```
openmediavault 8.6.0-1 (Synchrony)
Succeeded: 6
Failed: 0
```

### E. Reboot Server

```
reboot
```

Fungsi: melakukan restart Debian agar seluruh konfigurasi dan service OMV berjalan pada kondisi sistem yang baru.

## 5. AKSES WEB INTERFACE OMV

Setelah server menyala kembali, lihat IP address melalui console.

Pada lab ini, hasilnya:

```
ens18: 192.168.200.2
```

Penjelasan:

- `ens18`: nama network interface Debian.
- `192.168.200.2`: IP address server OMV.

Buka browser dari komputer client:

```
http://192.168.200.2
```

### A. Login Pertama Kali

Gunakan kredensial awal Web UI:

| Field    | Nilai            |
| -------- | ---------------- |
| Username | `admin`          |
| Password | `openmediavault` |

Password tersebut merupakan kredensial default Web UI, bukan password root Debian.

### B. Ganti Password Admin

Setelah berhasil login:

1. Klik ikon user di kanan atas.
2. Pilih Change Password.
3. Masukkan password baru.
4. Simpan perubahan.

Jangan menggunakan password default untuk penggunaan selanjutnya.
