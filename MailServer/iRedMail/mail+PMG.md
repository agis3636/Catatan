Intinya: Berikut adalah modul panduan teknis yang lengkap, terstruktur, dan siap Anda bagikan kepada pengguna atau tim. Modul ini merangkum seluruh tahapan operasional dari instalasi awal, penyelesaian masalah arsitektur Proxmox, optimalisasi memori (mematikan beban ganda), hingga integrasi rute dengan Proxmox Mail Gateway.

---

# Modul Praktikum: Konfigurasi iRedMail Teroptimasi sebagai Backend Proxmox Mail Gateway (PMG)

## Tujuan Praktikum

Membangun sebuah *mail server* (iRedMail) di dalam *container* LXC Proxmox yang berfungsi secara eksklusif sebagai mesin penyimpanan kotak masuk (*backend*). Fungsi penyaringan *spam* dan virus (Amavis/ClamAV) pada mesin ini dinonaktifkan untuk menghemat RAM, karena tugas penyaringan akan dialihkan sepenuhnya ke Proxmox Mail Gateway (PMG) di depannya.

---

## Bab 1: Instalasi Dasar iRedMail

### 1.1 Persiapan dan Unduh

1. Masuk ke terminal LXC Ubuntu (disarankan versi LTS terbaru seperti 24.04).
2. Perbarui repositori dan sistem dasar:
```bash
apt update && apt upgrade -y

```


3. Unduh rilis iRedMail terbaru dan ekstrak:
```bash
wget https://github.com/iredmail/iRedMail/archive/refs/tags/1.8.4.tar.gz
tar xvf 1.8.4.tar.gz
cd iRedMail-1.8.4
bash iRedMail.sh

```



### 1.2 Konfigurasi Wizard Instalasi

Saat layar instalasi berbasis teks muncul, konfigurasikan dengan parameter berikut:

* **Mail Storage:** Biarkan *default* (`/var/vmail`).
* **Web Server:** Pilih **Nginx**.
* **Database:** Pilih **MariaDB**.
* **Mail Domain:** Ketik **`lab-email.local`**. *(Catatan: Domain tidak boleh sama persis dengan hostname server/awalan mail).*
* **Administrator Password:** Tentukan kata sandi untuk akun admin (contoh: `Digital2018`).
* **Optional Components:** Gunakan tombol Spasi untuk **mematikan** `netdata`, `SOGo`, dan `Fail2ban`. Biarkan hanya **`Roundcubemail`** dan **`iRedAdmin`** yang memiliki tanda bintang `[*]`.
* Tekan **`y`** lalu **Enter** saat ringkasan konfigurasi muncul untuk menyetujui instalasi.
* Setelah selesai, lakukan *reboot* pada LXC.

---

## Bab 2: Mengatasi Pemblokiran Kernel LXC (Eror 502 Bad Gateway)

Secara bawaan, Proxmox menjalankan LXC dalam mode *Unprivileged*. Hal ini menyebabkan AppArmor milik Proxmox memblokir layanan `uwsgi` (mesin Python di balik iRedAdmin) untuk membuat jalur komunikasi (*socket*), sehingga memunculkan eror `502 Bad Gateway` saat iRedAdmin diakses.

### 2.1 Mengubah Profil AppArmor di Host Proxmox

Langkah ini wajib dilakukan dari **Terminal Host (Node pve) Proxmox**, bukan dari dalam LXC.

1. Buka *Shell* Proxmox Host.
2. Edit file konfigurasi LXC target. Jika ID LXC Anda adalah `100`, jalankan:
```bash
nano /etc/pve/lxc/100.conf

```


3. Tambahkan baris berikut di paling bawah file:
```text
lxc.apparmor.profile: unconfined

```


4. Simpan file.

### 2.2 Proses Cold Boot

1. Masuk ke Web GUI Proxmox.
2. Matikan paksa LXC tersebut (Klik kanan $\rightarrow$ **Stop** atau **Shutdown**).
3. Pastikan ikon LXC berubah menjadi abu-abu.
4. Nyalakan kembali (Klik kanan $\rightarrow$ **Start**).
5. Masuk ke terminal LXC dan verifikasi bahwa layanan sudah berjalan normal:
```bash
systemctl status iredadmin

```


*(Harus berstatus `active (running)` dan memunculkan log `spawned uWSGI worker`).*

---

## Bab 3: Akses Antarmuka Web

Layanan iRedMail memblokir port 80 secara *default* dan memaksa penggunaan koneksi aman HTTPS (Port 443).

### 3.1 Resolusi DNS Lokal (File Hosts)

Karena `lab-email.local` adalah domain fiktif untuk lab, arahkan komputer klien Anda secara manual:

* **Windows:** Edit file `C:\Windows\System32\drivers\etc\hosts` (Run as Administrator).
* **Mac/Linux:** Edit file `/etc/hosts` menggunakan `sudo`.
Tambahkan baris berikut di akhir file:

```text
192.168.2.32    mail.lab-email.local

```

### 3.2 Tautan Akses

Akses melalui *browser* klien, abaikan peringatan *SSL Self-Signed* (*Proceed to unsafe*):

* **iRedAdmin (Manajemen User/Domain):** `[https://mail.lab-email.local/iredadmin/](https://mail.lab-email.local/iredadmin/)`
* **Roundcube (Webmail):** `[https://mail.lab-email.local/mail/](https://mail.lab-email.local/mail/)`

Gunakan kredensial yang dibuat pada tahap 1.2 (misal: `postmaster@lab-email.local`).

---

## Bab 4: Optimalisasi RAM (Menonaktifkan Amavis dan ClamAV)

Karena Proxmox Mail Gateway (PMG) akan menjadi garda depan pelindung dari *spam* dan virus, membiarkan Amavis dan ClamAV tetap hidup di iRedMail hanya akan membuang sumber daya memori secara sia-sia.

### 4.1 Memutus Jalur Pemindaian di Postfix

Dari dalam terminal LXC iRedMail, jalankan perintah berikut:

1. **Nonaktifkan di `main.cf`:**
```bash
nano /etc/postfix/main.cf

```


Cari baris `content_filter = smtp-amavis:[127.0.0.1]:10024` dan ubah menjadi komentar:
`#content_filter = smtp-amavis:[127.0.0.1]:10024`
2. **Nonaktifkan di `master.cf` secara massal:**
Jalankan skrip `sed` berikut untuk memberikan komentar pada seluruh rute Amavis:
```bash
sed -i 's/.*-o content_filter=smtp-amavis.*/#-o content_filter=smtp-amavis:[127.0.0.1]:10026/g' /etc/postfix/master.cf

```



### 4.2 Mematikan Layanan Terkait

Hentikan mesin *antivirus/antispam* yang sedang berjalan dan matikan fungsi *autostart*-nya:

```bash
systemctl stop amavis clamav-daemon clamav-freshclam
systemctl disable amavis clamav-daemon clamav-freshclam

```

### 4.3 Terapkan Perubahan

Restart Postfix agar memuat konfigurasi perutean tanpa filter:

```bash
systemctl restart postfix

```

---

## Bab 5: Integrasi Proxmox Mail Gateway (PMG)

Konfigurasi ini memastikan lalu lintas email yang bersih dari PMG diteruskan ke iRedMail. Buka Web GUI PMG (Port 8006).

### 5.1 Pengaturan Relay Domain

Berfungsi agar PMG mau menangkap dan menyaring email yang ditujukan ke alamat perusahaan Anda (mencegah eror *Relay access denied*).

* Navigasi: **Configuration $\rightarrow$ Mail Proxy $\rightarrow$ Relay Domains**
* Tindakan: Klik **Create**, masukkan `lab-email.local`, lalu klik **OK**.

### 5.2 Pengaturan Transport

Berfungsi menginstruksikan PMG ke mana email bersih harus dikirimkan.

* Navigasi: **Configuration $\rightarrow$ Mail Proxy $\rightarrow$ Transports**
* Tindakan: Klik **Create**, isikan parameter berikut:
* **Domain:** `lab-email.local`
* **Host:** `192.168.2.32` *(IP Mesin iRedMail)*
* **Protocol:** `SMTP`
* **Port:** `25`
* **Use MX:** Hilangkan tanda centangnya (karena menggunakan IP langsung, bukan DNS).


* Klik **OK**.

---

## Bab 6: Pemahaman Port Arsitektur & Pengujian

Proxmox Mail Gateway menggunakan dua buah port SMTP untuk mengisolasi lalu lintas:

* **Port 25 (Pintu Eksternal):** Pintu masuk untuk email dari internet global. Pengamanan ketat.
* **Port 26 (Pintu Internal):** Pintu masuk khusus untuk *mail server* internal (*Trusted Network*) agar PMG mau merutekan (*relay*) email keluar ke internet.

### 6.1 Uji Koneksi PMG $\rightarrow$ iRedMail (Jalur Masuk)

Dari terminal *shell* PMG, pastikan PMG bisa menjangkau pintu penyimpanan iRedMail:

```bash
telnet 192.168.2.32 25

```

*(Indikator Sukses: Muncul pesan `220 mail.lab-email.local ESMTP Postfix`)*.

### 6.2 Uji Koneksi iRedMail $\rightarrow$ PMG (Jalur Keluar)

Dari terminal *shell* iRedMail, pastikan iRedMail bisa menjangkau pintu internal PMG (Port 26):

```bash
telnet <IP-SERVER-PMG> 26

```

*(Indikator Sukses: Muncul pesan `220 <hostname-pmg> ESMTP Proxmox`)*.
