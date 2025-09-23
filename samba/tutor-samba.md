# install Samba
Untuk menginstall **Samba** di Debian 11, kamu bisa langsung pakai `apt`. Berikut langkah-langkahnya:

1. **Update repository dulu**

   ```bash
   sudo apt update
   ```

2. **Install paket Samba**

   ```bash
   sudo apt install samba -y
   ```

   Ini akan menginstall layanan utama Samba.

3. **Cek versi Samba** untuk memastikan sudah terpasang:

   ```bash
   samba --version
   ```

4. (Opsional) Kalau mau install juga **utilitas manajemen Samba** seperti `smbclient` untuk testing koneksi, bisa tambahkan:

   ```bash
   sudo apt install samba-common-bin smbclient -y
   ```

5. Setelah itu, service Samba bisa dicek statusnya:

   ```bash
   systemctl status smbd
   ```

---

# Share Folder Samba

Oke, saya bikinkan step by step konfigurasi dasar Samba di **Debian 11** supaya bisa share folder dan diakses dari Windows/Linux client 🚀

---

## 1. Buat folder untuk share

Misalnya kita bikin folder di `/srv/samba/share`

```bash
sudo mkdir -p /srv/samba/share
sudo chown -R nobody:nogroup /srv/samba/share
sudo chmod -R 0775 /srv/samba/share
```

---

## 2. Backup config Samba lama

Sebelum edit, backup dulu:

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
```

---

## 3. Edit konfigurasi Samba

Buka file:

```bash
sudo nano /etc/samba/smb.conf
```

Tambahkan di bagian paling bawah:

```ini
[Share]
   path = /srv/samba/share
   browseable = yes
   writable = yes
   guest ok = yes
   read only = no
```

> Konfigurasi di atas membuat folder share bisa diakses tanpa password (guest).

Kalau mau pakai user login, saya bisa kasih versi yang secure juga.

---

## 4. Restart Samba

```bash
sudo systemctl restart smbd nmbd
```

Cek status:

```bash
systemctl status smbd
```

---

## 5. Cek firewall (kalau aktif)

Pastikan port Samba terbuka:

```bash
sudo ufw allow samba
```

---

## 6. Akses dari Windows

* Tekan **Win + R**, ketik:

  ```
  \\IP_DEBIAN\Share
  ```

  contoh: `\\192.168.1.20\Share`

* Kalau guest mode: langsung masuk.

* Kalau pakai user/password: nanti akan diminta login.

---

# File Sharing Samba Dengan Hak Akses User

Sip 🔐 kita bikin Samba share dengan autentikasi user, jadi tiap staf punya username/password. Berikut langkah-langkahnya:

---

## 1. Buat folder share

Misalnya folder khusus untuk staf:

```bash
sudo mkdir -p /srv/samba/staf
sudo chown -R root:staf /srv/samba/staf
sudo chmod -R 2770 /srv/samba/staf
```

> Keterangan:

* `staf` nanti kita bikin group khusus untuk user Samba.
* `2770` artinya owner & group bisa read/write/execute, orang lain ditolak, dan **setgid** supaya file baru otomatis ikut group `staf`.

---

## 2. Buat group staf

```bash
sudo groupadd staf
```

---

## 3. Buat user di Linux & Samba

Misalnya bikin user `andi` dan `budi`:

```bash
sudo useradd -M -s /sbin/nologin -G staf andi
sudo passwd andi

sudo useradd -M -s /sbin/nologin -G staf budi
sudo passwd budi
```

> `-M` = tanpa home directory
> `-s /sbin/nologin` = user tidak bisa login shell, hanya dipakai Samba
> `-G staf` = masuk ke group staf

Lalu tambahkan user ke Samba database:

```bash
sudo smbpasswd -a andi
sudo smbpasswd -a budi
```

Aktifkan:

```bash
sudo smbpasswd -e andi
sudo smbpasswd -e budi
```

---

## 4. Edit konfigurasi Samba

Buka file config:

```bash
sudo nano /etc/samba/smb.conf
```

Tambahkan di bawah:

```ini
[StafShare]
   path = /srv/samba/staf
   browseable = yes
   writable = yes
   valid users = @staf
   create mask = 0660
   directory mask = 2770
```

> `valid users = @staf` artinya hanya user di group `staf` yang bisa akses.

---

## 5. Restart Samba

```bash
sudo systemctl restart smbd nmbd
```

---

## 6. Akses dari Windows

* Tekan **Win + R**, ketik:

  ```
  \\IP_DEBIAN\StafShare
  ```
* Masukkan username/password Samba (`andi` / password).

---

## 7. Uji coba dari Debian

```bash
smbclient //localhost/StafShare -U andi
```

---

