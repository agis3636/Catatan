# Modul Install VirtualBox di Ubuntu & Zorin OS

Panduan ini menggunakan repository resmi dari [Oracle VM VirtualBox](https://www.virtualbox.org?utm_source=chatgpt.com) dan mendukung:

* Ubuntu 22.04 (Jammy)
* Ubuntu 24.04 (Noble)
* Ubuntu 25.04
* Ubuntu 25.10
* Ubuntu 26.04
* Zorin OS 18 (Base Ubuntu Noble/24.04)

---

# 1. Cek Versi OS

Cek codename Linux:

```bash
cat /etc/os-release
```

Atau:

```bash
lsb_release -a
```

Contoh hasil Zorin:

```bash
Distributor ID: Zorin
Description:    Zorin OS 18.1
Release:        18
Codename:       noble
```

Karena Zorin OS 18 menggunakan base Ubuntu 24.04, maka gunakan repository `noble`.

---

# 2. Install Dependency Dasar

```bash
sudo apt update
sudo apt install -y wget gpg
```

---

# 3. Tambahkan GPG Key Oracle VirtualBox

Download dan registrasi key resmi Oracle:

```bash
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | \
sudo gpg --yes --dearmor -o /usr/share/keyrings/oracle-virtualbox-2016.gpg
```

---

# 4. Tambahkan Repository Sesuai Versi Ubuntu

## Ubuntu 22.04 (Jammy)

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian jammy contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list
```

---

## Ubuntu 24.04 / Zorin OS 18 (Noble)

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian noble contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list
```

---

## Ubuntu 25.04

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian plucky contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list
```

---

## Ubuntu 25.10

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian questing contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list
```

---

## Ubuntu 26.04

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian resolute contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list
```

---

# 5. Update Repository

```bash
sudo apt update
```

---

# 6. Install VirtualBox

## Install VirtualBox 7.1

```bash
sudo apt install virtualbox-7.1
```

---

## Cek Versi Tersedia

```bash
apt search virtualbox
```

---

# 7. Verifikasi Instalasi

Cek versi VirtualBox:

```bash
VBoxManage --version
```

Atau buka GUI:

```bash
virtualbox
```

---

# 8. Install Extension Pack (Opsional Tapi Disarankan)

Fungsi:

* USB 2.0 / 3.0
* RDP
* Webcam passthrough
* NVMe
* PXE boot tambahan

Download:

* [VirtualBox Downloads](https://www.virtualbox.org/wiki/Download_Old_Builds_7_1)

Install:

```bash
sudo VBoxManage extpack install Oracle_VirtualBox_Extension_Pack-7.1.18.vbox-extpack
```

---

# 9. Tambahkan User ke Group vboxusers

Agar:

* USB passthrough jalan
* Device detection normal

Jalankan:

```bash
sudo usermod -aG vboxusers $USER
```

Lalu logout/login kembali.

---

# 10. Install Build Tools (Penting)

Agar:

* Kernel module bisa build
* VirtualBox tetap jalan setelah update kernel

Install:

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

---

# 11. Cek Module VirtualBox

```bash
lsmod | grep vbox
```

Kalau belum muncul:

```bash
sudo /sbin/vboxconfig
```

---

# 12. Fix Error Umum

## Error:

```text
Kernel driver not installed (rc=-1908)
```

Install dependency kernel:

```bash
sudo apt install build-essential dkms linux-headers-$(uname -r)
sudo /sbin/vboxconfig
```

---

# 13. Menghapus VirtualBox

## Uninstall

```bash
sudo apt remove --purge virtualbox-*
```

## Hapus repository

```bash
sudo rm /etc/apt/sources.list.d/virtualbox.list
```

## Hapus key

```bash
sudo rm /usr/share/keyrings/oracle-virtualbox-2016.gpg
```

---

# 14. Quick Install Ubuntu 24.04 / Zorin 18

```bash
sudo apt update

sudo apt install -y wget gpg build-essential dkms linux-headers-$(uname -r)

wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | \
sudo gpg --yes --dearmor -o /usr/share/keyrings/oracle-virtualbox-2016.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
https://download.virtualbox.org/virtualbox/debian noble contrib" | \
sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update

sudo apt install virtualbox-7.1

sudo usermod -aG vboxusers $USER
```

Logout/login setelah selesai.

---

# 15. Catatan Penting

## Secure Boot

Kalau Ubuntu/Zorin menggunakan Secure Boot:

* Module VirtualBox kadang gagal load
* Perlu disable Secure Boot di BIOS
  ATAU
* Sign module manual

---

# 16. Rekomendasi

| Sistem       | Rekomendasi        |
| ------------ | ------------------ |
| Ubuntu 22.04 | VirtualBox 7.1     |
| Ubuntu 24.04 | VirtualBox 7.1     |
| Zorin 18     | VirtualBox 7.1     |
| Ubuntu 25.x  | VirtualBox 7.1     |
| Ubuntu 26.x  | VirtualBox terbaru |

---

# Referensi Resmi

* [VirtualBox Linux Downloads](https://www.virtualbox.org/wiki/Linux_Downloads?utm_source=chatgpt.com)
* [Oracle VM VirtualBox](https://www.virtualbox.org?utm_source=chatgpt.com)
