# Modul Install Wine di Ubuntu & Zorin OS

Panduan ini menggunakan repository resmi dari [WineHQ](https://winehq.org?utm_source=chatgpt.com) dan mendukung:

* Ubuntu 22.04 (Jammy)
* Ubuntu 24.04 (Noble)
* Ubuntu 25.04 (Plucky)
* Ubuntu 25.10 (Questing)
* Ubuntu 26.04 (Resolute)
* Zorin OS 18 (Base Ubuntu Noble/24.04)

---

# 1. Cek Versi OS

Jalankan:

```bash
cat /etc/os-release
```

Atau:

```bash
lsb_release -a
```

Contoh hasil untuk Zorin:

```bash
Distributor ID: Zorin
Description:    Zorin OS 18.1
Release:        18
Codename:       noble
```

Karena codename-nya `noble`, maka menggunakan repository Ubuntu 24.04 Noble.

---

# 2. Install Dependency Dasar

Install package dasar terlebih dahulu:

```bash
sudo apt update
sudo apt install -y wget gpg software-properties-common
```

---

# 3. Tambahkan Repository Key WineHQ

Buat folder keyring:

```bash
sudo mkdir -pm755 /etc/apt/keyrings
```

Download dan install key repository:

```bash
wget -O - https://dl.winehq.org/wine-builds/winehq.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/winehq-archive.key -
```

---

# 4. Tambahkan Arsitektur 32-bit

WAJIB untuk Ubuntu 22.04, 24.04, 25.04, Debian 12/13, dan Zorin.

```bash
sudo dpkg --add-architecture i386
```

Lalu update:

```bash
sudo apt update
```

---

# 5. Tambahkan Repository Sesuai Versi OS

## Ubuntu 22.04 (Jammy)

```bash
sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/jammy/winehq-jammy.sources
```

---

## Ubuntu 24.04 / Zorin OS 18 (Noble)

```bash
sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/noble/winehq-noble.sources
```

---

## Ubuntu 25.04 (Plucky)

```bash
sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/plucky/winehq-plucky.sources
```

---

## Ubuntu 25.10 (Questing)

> Ubuntu 25.10 sudah menggunakan WoW64 baru, jadi tidak wajib install package i386.

```bash
sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/questing/winehq-questing.sources
```

---

## Ubuntu 26.04 (Resolute)

```bash
sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/resolute/winehq-resolute.sources
```

---

# 6. Update Repository

```bash
sudo apt update
```

---

# 7. Install Wine

## Versi Stable (Disarankan)

```bash
sudo apt install --install-recommends winehq-stable
```

---

## Versi Development

```bash
sudo apt install --install-recommends winehq-devel
```

---

## Versi Staging

```bash
sudo apt install --install-recommends winehq-staging
```

---

# 8. Verifikasi Instalasi

Cek versi Wine:

```bash
wine --version
```

Contoh output:

```bash
wine-10.x
```

---

# 9. Konfigurasi Awal Wine

Jalankan:

```bash
winecfg
```

Saat pertama kali dijalankan:

* Wine akan membuat `~/.wine`
* Install dependency tambahan
* Membuka konfigurasi Windows Version

---

# 10. Install Aplikasi Windows

Contoh install `.exe`:

```bash
wine namafile.exe
```

Contoh:

```bash
wine winrar-x64.exe
```

---

# 11. Lokasi Drive C: Wine

Folder default Wine:

```bash
~/.wine
```

Drive C:

```bash
~/.wine/drive_c
```

---

# 12. Tips Tambahan

## Install Winetricks

Berguna untuk install:

* .NET Framework
* Visual C++
* DirectX
* Fonts Windows

Install:

```bash
sudo apt install winetricks
```

Jalankan:

```bash
winetricks
```

---

# 13. Uninstall Wine

Hapus package Wine:

```bash
sudo apt remove --purge winehq-stable
```

Hapus config user:

```bash
rm -rf ~/.wine
```

---

# 14. Catatan Penting

## Ubuntu 25.10 & 26.04

Wine terbaru menggunakan:

* WoW64 modern
* Tidak membutuhkan package 32-bit penuh

Namun:

* Prefix 32-bit lama (`WINEARCH=win32`) tidak kompatibel
* Aplikasi lama perlu reinstall ke prefix 64-bit

---

# 15. Rekomendasi Versi

| OS           | Rekomendasi   |
| ------------ | ------------- |
| Ubuntu 22.04 | winehq-stable |
| Ubuntu 24.04 | winehq-stable |
| Zorin OS 18  | winehq-stable |
| Ubuntu 25.x  | winehq-devel  |
| Ubuntu 26.x  | winehq-devel  |

---

# 16. Quick Install (Copy Paste)

## Ubuntu 24.04 / Zorin 18

```bash
sudo dpkg --add-architecture i386

sudo mkdir -pm755 /etc/apt/keyrings

wget -O - https://dl.winehq.org/wine-builds/winehq.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/winehq-archive.key -

sudo wget -NP /etc/apt/sources.list.d/ \
https://dl.winehq.org/wine-builds/ubuntu/dists/noble/winehq-noble.sources

sudo apt update

sudo apt install --install-recommends winehq-stable

winecfg
```

---

# Referensi Resmi

* [WineHQ Linux Download Page](https://gitlab.winehq.org/wine/wine/-/wikis/Debian-Ubuntu?utm_source=chatgpt.com)
* [WineHQ Official Website](https://winehq.org?utm_source=chatgpt.com)
