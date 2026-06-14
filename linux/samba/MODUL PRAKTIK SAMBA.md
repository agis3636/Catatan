# 📘 MODUL PRAKTIK SAMBA (USER BASED FILE SHARING)
## Linux Server + Windows & Linux Client

---

# PRAKTIK nya apa aja:

1. Menginstall Samba di Ubuntu
2. Membuat folder sharing berbasis user
3. Membuat user Linux + user Samba
4. Mengatur permission folder sharing
5. Mengakses Samba dari Windows dan Linux client
6. Melakukan troubleshooting dasar Samba

---

# KONSEP DASAR SAMBA

Samba adalah layanan di Linux yang memungkinkan folder dibagikan ke jaringan sehingga bisa diakses oleh:

- Windows
- Linux
- perangkat lain dalam jaringan

---

## KONSEP PENTING

| Komponen | Fungsi |
|----------|--------|
| Folder Linux | lokasi file asli |
| Samba Share | nama akses di jaringan |
| User Samba | login akses folder |
| IP Address | alamat server |

---

# 1. INSTALASI SAMBA

```bash
sudo apt update
sudo apt install samba smbclient samba-common-bin -y


### Penjelasan:

* samba = server utama
* smbclient = tool test dari Linux client
* samba-common-bin = tools user & password

---

# 2. CEK SERVICE SAMBA

```bash
systemctl status smbd
```

Jika belum aktif:

```bash
sudo systemctl enable --now smbd nmbd
```

---

# 3. MEMBUAT FOLDER SHARING

```bash
sudo mkdir -p /srv/samba/kelas
```

### Penjelasan:

Folder ini adalah lokasi file yang akan dibagikan ke jaringan.

---

# 4. MEMBUAT USER DAN GROUP

## 4.1 Buat group

```bash
sudo groupadd staf
```

### Fungsi:

Group digunakan untuk mengatur hak akses bersama.

---

## 4.2 Buat user Linux (tanpa login shell)

```bash
sudo useradd -M -s /usr/sbin/nologin -G staf salsa
sudo useradd -M -s /usr/sbin/nologin -G staf andi
```

### Penjelasan:

* user hanya untuk Samba
* tidak bisa login ke Linux
* masuk group `staf`

---

## 4.3 Tambahkan password Samba

```bash
sudo smbpasswd -a salsa
sudo smbpasswd -a andi
```

### Penjelasan:

Ini adalah password khusus untuk akses Samba (bukan password Linux).

Aktifkan user:

```bash
sudo smbpasswd -e salsa
sudo smbpasswd -e andi
```

---

# 5. SET PERMISSION FOLDER

```bash
sudo chown -R root:staf /srv/samba/kelas
sudo chmod -R 2770 /srv/samba/kelas
```

### Penjelasan:

* root = pemilik folder
* staf = group yang boleh akses
* 2770 =

  * owner full akses
  * group full akses
  * user lain tidak bisa akses
  * auto inherit group (penting)

---

# 6. KONFIGURASI SAMBA

Edit file:

```bash
sudo nano /etc/samba/smb.conf
```

Tambahkan di PALING BAWAH:

```ini
[KelasShare]
   path = /srv/samba/kelas
   browseable = yes
   writable = yes
   read only = no
   valid users = @staf
   create mask = 0660
   directory mask = 2770
```

---

## PENJELASAN PENTING

| Konfigurasi    | Arti                   |
| -------------- | ---------------------- |
| [KelasShare]   | nama share di jaringan |
| path           | folder asli Linux      |
| valid users    | hanya user group staf  |
| create mask    | izin file baru         |
| directory mask | izin folder baru       |

---

# 7. CEK KONFIGURASI

```bash
testparm
```

Jika benar:

```
Loaded services file OK
```

---

# 8. RESTART SAMBA

```bash
sudo systemctl restart smbd nmbd
```

---

# 9. TEST DARI WINDOWS

Tekan:

```
Win + R
```

Lalu:

```
\\192.168.100.15\KelasShare
```

Login:

* username: salsa
* password: (samba password)

---

# 10. TEST DARI LINUX CLIENT

## 10.1 Install tools

```bash
sudo apt install smbclient -y
```

---

## 10.2 List share

```bash
smbclient -L //192.168.100.15 -U salsa
```

---

## 10.3 Masuk ke share

```bash
smbclient //192.168.100.15/KelasShare -U salsa
```

---

## 10.4 Perintah dalam Samba

```text
ls     → lihat file
put    → upload file
get    → download file
exit   → keluar
```

---

# TROUBLESHOOTING

## ❌ Tidak bisa akses

Cek:

```bash
ping 192.168.100.15
```

---

## ❌ Port Samba tidak jalan

```bash
ss -tulpn | grep smbd
```

Harus ada:

* 445
* 139

---

## ❌ Service error

```bash
systemctl restart smbd
```

---

## ❌ Salah konfigurasi

```bash
testparm
```

---

Samba bekerja dengan konsep:

1. Folder Linux = lokasi file
2. Share name = nama akses jaringan
3. User Samba = login akses
4. Group = pembatas akses
5. Client = Windows / Linux

---

Jika berhasil:

* Windows bisa akses folder
* Linux bisa login via smbclient
* User hanya bisa akses sesuai permission
* Folder aman dan terkontrol

```
