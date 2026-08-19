Berikut adalah **Modul Panduan Lengkap Instalasi iRestMail & Integrasi Domain** dari awal, konfigurasi DNS di Cloudflare, hingga berhasil mengirim dan menerima email ke luar (eksternal).

---

# Modul Panduan Lengkap: Instalasi iRedMail & Konfigurasi Domain Publik

## 1. Persiapan Awal di VPS (IDCloudHost)

Sebelum menjalankan instalasi, pastikan sistem operasi VPS Anda bersih dan menggunakan **Ubuntu 24.04**.

### Atur FQDN (Fully Qualified Domain Name)

Hostname VPS harus diubah menggunakan domain asli Anda (misal: `mail.archtech.consulting`). Jalankan perintah ini di terminal SSH:

```bash
sudo hostnamectl set-hostname mail.archtech.consulting

```

Kemudian edit file `/etc/hosts`:

```bash
sudo nano /etc/hosts

```

Ubah baris pertama menjadi:

```text
127.0.0.1   mail.archtech.consulting mail localhost

```

*(Simpan dengan tekan `Ctrl+X`, lalu `Y`, lalu `Enter`)*

---

## 2. Instalasi iRedMail

1. Download dan ekstrak iRedMail di server:
```bash
wget https://github.com/iredmail/iRedMail/archive/refs/tags/1.8.4.tar.gz
tar xzvf 1.8.4.tar.gz
cd iRedMail-1.8.4/

```


2. Instal dependensi pendukung yang wajib ada (seperti `dialog` dan `gnupg2`):
```bash
sudo apt update
sudo apt install dialog gnupg2 -y

```


3. Jalankan skrip instalasi:
```bash
sudo bash iRedMail.sh

```


4. Ikuti wizard instalasi di layar biru (pilih database pilihan Anda, misal MariaDB/MySQL, masukkan password administrator, dan daftarkan domain utama Anda).

---

## 3. Konfigurasi DNS di Cloudflare

Agar email dapat dikenali oleh internet luar (Gmail, Yahoo, dll), tambahkan DNS Records berikut di panel Cloudflare domain Anda dengan status **DNS only** (ikon awan abu-abu):

* **A Record**
* Name: `@` -> IP VPS (`103.250.10.38`)


* **A Record**
* Name: `mail` -> IP VPS (`103.250.10.38`)


* **MX Record**
* Name: `@` -> Value: `mail.archtech.consulting` (Priority: `10`)


* **TXT Record (SPF)**
* Name: `@` -> Value: `"v=spf1 mx ~all"`


* **TXT Record (DMARC)**
* Name: `_dmarc` -> Value: `"v=DMARC1; p=none; sp=none; rua=mailto:admin@archtech.consulting"`



---

## 4. Pembukaan Blokir Port 25

Sebagian besar provider VPS (termasuk IDCloudHost) menutup Port 25 secara *default*.

* Buat tiket dukungan (*support ticket*) ke pihak IDCloudHost untuk meminta **unblock Port 25 (Outbound SMTP)** pada IP publik VPS Anda agar bisa berkomunikasi langsung dengan server email luar.

---

## 5. Pembuatan Akun Email & Pengujian

1. **Login ke Panel Admin:**
Buka browser dan akses `https://IP_VPS_ANDA/iredadmin/` menggunakan akun `postmaster@domain-anda`.
2. **Tambah Domain & User:**
* Masuk ke menu *Domains and Accounts* -> *Add...* lalu daftarkan domain Anda (`archtech.consulting`).
* Klik domain tersebut, lalu buat akun *user* baru (contoh: `agis@archtech.consulting`).


3. **Uji Coba Kirim/Terima Email:**
* Akses Webmail di `https://IP_VPS_ANDA/mail/`.
* Login menggunakan akun yang baru dibuat, lalu coba kirim email ke akun eksternal (seperti Gmail).



Jika ada kendala di langkah tertentu, bagian mana yang ingin kita bedah lebih lanjut?
