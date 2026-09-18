Berikut adalah dokumentasi modul lengkap tahapan instalasi Proxmox Mail Gateway (PMG) beserta kendala teknis dan solusi yang kita hadapi kemarin:

---

# Modul Implementasi: Proxmox Mail Gateway (PMG) di IDCloudHost

## 1. Arsitektur & Informasi Server

* **Server Gateway (PMG):** VPS Debian 12 (`pmg.archtech.consulting`), IP Publik: `103.250.11.18`, Private IP: `10.110.59.252`.
* **Server Backend (iRedMail):** VPS Ubuntu (`mail.archtech.consulting`), IP Publik: `103.250.10.38`, Private IP: `10.110.59.254`.
* **Jaringan Internal:** Memanfaatkan fitur *Private IPv4* bawaan IDCloudHost untuk komunikasi aman antar server tanpa melalui internet publik.

---

## 2. Langkah-Langkah Instalasi Utama

### A. Pengaturan Hostname & Resolusi Lokal

Hostname server PMG harus dibedakan dari server iRedMail (`mail.archtech.consulting`) untuk menghindari konflik *mail loop* pada Postfix.

1. Set hostname VPS PMG:
```bash
hostnamectl set-hostname pmg.archtech.consulting

```


2. Sesuaikan file `/etc/hosts`:
```text
127.0.0.1 localhost
127.0.1.1 pmg.archtech.consulting pmg

```



### B. Menambahkan Repositori Proxmox & Instalasi Paket

PMG versi 8 memerlukan lingkungan **Debian 12 (Bookworm)** secara spesifik.

1. Tambahkan repositori PMG No-Subscription:
```bash
echo "deb http://download.proxmox.com/debian/pmg bookworm pmg-no-subscription" > /etc/apt/sources.list.d/pmg-no-subscription.list

```


2. Unduh GPG Key Proxmox:
```bash
curl -fsSL https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -o /etc/apt/trusted.gpg.d/proxmox-release.gpg

```


3. Update sistem dan instal PMG:
```bash
apt update && apt dist-upgrade -y
apt install proxmox-mailgateway -y

```



---

## 3. Kendala (*Troubleshooting*) & Solusi

### Kendala 1: Error Dependensi Saat Instalasi (`Broken Packages`)

* **Penyebab:** VPS awalnya menggunakan **Debian 13 (Trixie)** yang memiliki versi Perl (`perlapi-5.36.0` tidak cocok) dan pustaka sistem lebih baru yang belum didukung oleh PMG 8.
* **Solusi:** Melakukan *re-install* OS VPS di panel IDCloudHost menjadi **Debian 12 (Bookworm)**.

### Kendala 2: SSH Host Key Identification Changed

* **Penyebab:** Setelah VPS di-*reinstall*, *fingerprint* server berubah sementara komputer lokal masih menyimpan *fingerprint* lama.
* **Solusi:** Menghapus cache kunci lama di terminal lokal:
```bash
ssh-keygen -R 103.250.11.18

```



### Kendala 3: Prompt Konfigurasi Paket Saat Instalasi

Saat `apt install proxmox-mailgateway` berjalan, muncul beberapa interaksi terminal:

* **Postfix Configuration (General mail configuration type):** Pilih **`Internet Site`**.
* **System mail name:** Masukkan `pmg.archtech.consulting`.
* **Konfigurasi GRUB:** Muncul peringatan file `/etc/default/grub` termodifikasi oleh cloud provider. Pilih **`keep the local version currently installed`** agar *booting* VPS tetap aman sesuai setelan IDCloudHost.

### Kendala 4: Gagal Login ke Web GUI Proxmox (`:8006`)

* **Penyebab:** Akun `root` pada VPS tidak memiliki *password* karena instalasi awal menggunakan autentikasi *SSH Key*.
* **Solusi:**
1. Masuk ke terminal SSH VPS, lalu buat password root:
```bash
passwd

```


2. Pada halaman login Web GUI (`[https://103.250.11.18:8006](https://103.250.11.18:8006)`), pastikan opsi **Realm** diatur ke **`Linux PAM standard authentication`**, lalu masukkan *username* `root` dan password yang baru dibuat.



---

## 4. Status Saat Ini

* Panel Web GUI PMG (`[https://103.250.11.18:8006](https://103.250.11.18:8006)`) sudah berhasil diakses dan aktif dengan normal.
* Jaringan privat antar server siap digunakan via *Private IP* (`10.110.59.x`).
* Sistem siap dilanjutkan ke tahap konfigurasi *Mail Proxy* (Inbound/Outbound Relay) dengan iRedMail.
