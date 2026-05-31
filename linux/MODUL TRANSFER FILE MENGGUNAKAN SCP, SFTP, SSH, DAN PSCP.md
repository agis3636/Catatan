# MODUL PRAKTIKUM

# TRANSFER FILE MENGGUNAKAN SCP, SFTP, SSH, DAN PSCP

## A. Tujuan Pembelajaran

Setelah mengikuti praktikum ini, mahasiswa diharapkan mampu:

1. Memahami konsep dasar transfer file antara komputer lokal dan server.
2. Menggunakan perintah `scp` untuk upload dan download file.
3. Menggunakan opsi penting pada `scp`, seperti `-r`, `-P`, `-i`, dan `-v`.
4. Menggunakan `sftp` sebagai alternatif transfer file secara interaktif.
5. Mengakses server menggunakan `ssh`.
6. Memahami perbedaan direktori lokal dan direktori server.
7. Menggunakan `pscp` di Windows melalui PuTTY.
8. Mengatasi error umum saat transfer file.

---

## B. Konsep Dasar

Sebelum menggunakan `scp`, mahasiswa harus paham dulu dua istilah penting:

### 1. Local

Local adalah komputer yang sedang kita gunakan sekarang.

Contoh direktori lokal:

```bash
/home/user/Desktop/
```

atau pada Windows:

```bash
C:\Users\Public\agis
```

### 2. Remote / Server

Remote atau server adalah komputer tujuan yang diakses melalui jaringan.

Contoh alamat server:

```bash
10.10.10.10
```

Contoh format login ke server:

```bash
user@10.10.10.10
```

Artinya:

* `user` adalah username di server.
* `10.10.10.10` adalah alamat IP server.

---

## C. Format Dasar SCP

SCP digunakan untuk menyalin file secara aman melalui koneksi SSH.

Format dasar:

```bash
scp [opsi] sumber tujuan
```

Contoh pola umum:

```bash
scp file.txt user@10.10.10.10:/home/user/
```

Penjelasan:

* `scp` = perintah untuk menyalin file.
* `file.txt` = file dari komputer lokal.
* `user@10.10.10.10` = username dan alamat server.
* `/home/user/` = folder tujuan di server.

---

## D. Memahami Format Path SCP

Format alamat remote:

```bash
user@IP_SERVER:/lokasi/direktori
```

Contoh:

```bash
user@10.10.10.10:/home/user/
```

Keterangan penting:

| Simbol / Path | Arti                                               |
| ------------- | -------------------------------------------------- |
| `/`           | Root directory atau direktori paling atas di Linux |
| `/home/user/` | Home directory milik user tertentu                 |
| `.`           | Direktori aktif saat ini di komputer lokal         |
| `~`           | Home directory user yang sedang login              |
| `:`           | Pemisah antara alamat server dan path tujuan       |

Contoh root directory:

```bash
/
```

Jadi, kalau ingin upload ke root server, syntax yang benar adalah:

```bash
scp file.txt user@10.10.10.10:/
```

Bukan:

```bash
scp file.txt user@10.10.10.10:-/
```

Catatan penting: biasanya user biasa tidak punya izin menulis langsung ke `/`. Lebih aman upload ke `/home/user/`.

---

## E. Persiapan Server SSH

Agar `scp`, `sftp`, dan `ssh` bisa digunakan, server harus memiliki layanan SSH.

### 1. Install OpenSSH Server

Pada server Ubuntu/Debian:

```bash
sudo apt update
sudo apt install openssh-server
```

Perintah ini digunakan untuk memasang layanan SSH server.

---

### 2. Mengecek Status SSH

```bash
sudo systemctl status ssh
```

Fungsinya untuk mengecek apakah layanan SSH sedang berjalan atau tidak.

Jika aktif, biasanya muncul keterangan:

```bash
active (running)
```

---

### 3. Menjalankan SSH

```bash
sudo systemctl start ssh
```

Fungsinya untuk menjalankan layanan SSH.

---

### 4. Menghentikan SSH

```bash
sudo systemctl stop ssh
```

Fungsinya untuk menghentikan layanan SSH.

---

### 5. Restart SSH

```bash
sudo systemctl restart ssh
```

Fungsinya untuk memulai ulang layanan SSH, biasanya setelah mengubah konfigurasi.

---

### 6. Reload SSH

```bash
sudo systemctl reload ssh
```

Fungsinya untuk memuat ulang konfigurasi SSH tanpa menghentikan layanan secara penuh.

---

### 7. Mengedit Port SSH

File konfigurasi SSH berada di:

```bash
/etc/ssh/sshd_config
```

Edit dengan perintah:

```bash
sudo nano /etc/ssh/sshd_config
```

Cari bagian:

```bash
#Port 22
```

Ubah menjadi contoh berikut:

```bash
Port 1234
```

Setelah itu restart SSH:

```bash
sudo systemctl restart ssh
```

Kemudian login menggunakan port baru:

```bash
ssh -p 1234 user@10.10.10.10
```

---

## F. Login ke Server Menggunakan SSH

Sebelum melakukan transfer file, sebaiknya mahasiswa mencoba login ke server terlebih dahulu.

### 1. Login SSH biasa

```bash
ssh user@10.10.10.10
```

Fungsi:

Masuk ke server menggunakan username `user`.

---

### 2. Login SSH dengan port tertentu

```bash
ssh -p 1234 user@10.10.10.10
```

Fungsi:

Masuk ke server menggunakan port SSH selain default `22`.

Catatan:

* Pada `ssh`, opsi port memakai huruf kecil `-p`.
* Pada `scp` dan `sftp`, opsi port memakai huruf besar `-P`.

---

### 3. Login SSH dengan format `-l`

```bash
ssh -p 1234 -l user 10.10.10.10
```

Fungsi:

Sama seperti:

```bash
ssh -p 1234 user@10.10.10.10
```

Keterangan:

* `-l user` berarti login sebagai user tertentu.
* `-p 1234` berarti menggunakan port 1234.

---

### 4. Menjalankan perintah langsung di server

```bash
ssh -p 1234 user@10.10.10.10 "pwd"
```

Fungsi:

Menjalankan perintah `pwd` di server tanpa harus masuk ke shell interaktif.

Contoh lain:

```bash
ssh user@10.10.10.10 "ls -lah"
```

Fungsi:

Menampilkan isi direktori server.

---

## G. Membuat File untuk Latihan

Sebelum upload file, mahasiswa bisa membuat file latihan terlebih dahulu.

### 1. Membuat file dengan `echo`

```bash
echo "halo semua ini catatan saya" > welcome.txt
```

Fungsi:

Membuat file bernama `welcome.txt` berisi teks:

```bash
halo semua ini catatan saya
```

---

### 2. Melihat isi file

```bash
cat welcome.txt
```

Fungsi:

Menampilkan isi file di terminal.

---

### 3. Membuat file dengan `cat`

```bash
cat > file.txt
```

Lalu ketik isi file:

```bash
hallo semua
saya agis
```

Untuk menyimpan, tekan:

```bash
CTRL + D
```

Lihat hasilnya:

```bash
cat file.txt
```

---

## H. Upload File ke Server Menggunakan SCP

Upload artinya mengirim file dari komputer lokal ke server.

### 1. Upload file ke home directory server

```bash
scp file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim `file.txt` dari komputer lokal ke folder `/home/user/` di server.

---

### 2. Upload file ke root directory server

```bash
scp file.txt user@10.10.10.10:/
```

Fungsi:

Mengirim file ke direktori root `/`.

Catatan:

Biasanya perintah ini gagal untuk user biasa karena tidak memiliki izin menulis ke root directory. Jika gagal, upload dulu ke home directory:

```bash
scp file.txt user@10.10.10.10:/home/user/
```

Lalu pindahkan dari server dengan `sudo`:

```bash
ssh user@10.10.10.10
sudo mv /home/user/file.txt /
```

---

### 3. Upload file dari lokasi tertentu di komputer lokal

```bash
scp /home/user/Desktop/file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file dari folder Desktop lokal ke server.

---

### 4. Upload folder/direktori ke server

```bash
scp -r nama_direktori user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim satu folder beserta seluruh isi di dalamnya ke server.

Keterangan:

* `-r` berarti recursive.
* Recursive artinya folder dan seluruh isinya ikut dikirim.

Contoh:

```bash
scp -r project-web user@10.10.10.10:/home/user/
```

---

### 5. Upload file dengan port tertentu

```bash
scp -P 1234 file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file ke server yang SSH-nya berjalan pada port `1234`.

Catatan penting:

Pada `scp`, port menggunakan `-P` huruf besar, bukan `-p`.

---

### 6. Upload file menggunakan private key

```bash
scp -i /path/private_key file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file menggunakan autentikasi private key, bukan password biasa.

Contoh:

```bash
scp -i ~/.ssh/id_rsa file.txt user@10.10.10.10:/home/user/
```

---

### 7. Upload file dengan port dan private key

```bash
scp -P 1234 -i ~/.ssh/id_rsa file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file ke server dengan port khusus dan private key.

---

### 8. Upload file dengan mode verbose

```bash
scp -v file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Menampilkan proses detail saat koneksi berlangsung.

Biasanya digunakan untuk mencari penyebab error koneksi.

---

### 9. Upload file ZIP

```bash
scp file.zip user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file ZIP ke server.

Catatan:

File `.zip` adalah file biasa, jadi tidak perlu `-r`.

Gunakan `-r` hanya untuk folder.

---

## I. Download File dari Server Menggunakan SCP

Download artinya mengambil file dari server ke komputer lokal.

### 1. Download file ke direktori aktif saat ini

```bash
scp user@10.10.10.10:/home/user/file.txt .
```

Fungsi:

Mengambil `file.txt` dari server dan menyimpannya ke direktori lokal saat ini.

Keterangan:

* `.` berarti folder aktif saat ini di komputer lokal.

---

### 2. Download file ke Desktop lokal

```bash
scp user@10.10.10.10:/home/user/file.txt /home/user/Desktop/
```

Fungsi:

Mengambil file dari server dan menyimpannya ke Desktop komputer lokal.

---

### 3. Download file dengan port tertentu

```bash
scp -P 1234 user@10.10.10.10:/home/user/file.txt /home/user/Desktop/
```

Fungsi:

Mengambil file dari server yang memakai port SSH `1234`.

---

### 4. Download folder dari server

```bash
scp -r user@10.10.10.10:/home/user/directory /home/user/Desktop/
```

Fungsi:

Mengambil folder `directory` dari server ke Desktop lokal.

---

### 5. Download folder dengan port tertentu

```bash
scp -r -P 1234 user@10.10.10.10:/home/user/directory /home/user/Desktop/
```

Fungsi:

Mengambil folder dari server menggunakan port SSH tertentu.

---

### 6. Download file dengan private key

```bash
scp -i ~/.ssh/id_rsa user@10.10.10.10:/home/user/file.txt /home/user/Desktop/
```

Fungsi:

Mengambil file dari server menggunakan private key.

---

### 7. Download file dengan port dan private key

```bash
scp -P 1234 -i ~/.ssh/id_rsa user@10.10.10.10:/home/user/file.txt /home/user/Desktop/
```

Fungsi:

Mengambil file dari server menggunakan port khusus dan private key.

---

## J. Perbedaan Posisi Source dan Destination

Dalam `scp`, posisi sangat penting.

Format:

```bash
scp sumber tujuan
```

Jika sumber berada di lokal, berarti upload:

```bash
scp file.txt user@10.10.10.10:/home/user/
```

Jika sumber berada di server, berarti download:

```bash
scp user@10.10.10.10:/home/user/file.txt .
```

Kesimpulan:

* Remote di bagian akhir = upload.
* Remote di bagian awal = download.

---

## K. Contoh Transfer Antarserver

SCP juga bisa digunakan untuk menyalin file dari satu server ke server lain.

```bash
scp user1@10.10.10.10:/home/user1/file.txt user2@10.10.10.20:/home/user2/
```

Fungsi:

Mengirim file dari server `10.10.10.10` ke server `10.10.10.20`.

---

## L. Mengirim Isi File Lokal ke Server dengan SSH

Contoh:

```bash
ssh -p 1234 user@10.10.10.10 "cat > file.txt" < welcome.txt
```

Fungsi:

Mengirim isi file lokal `welcome.txt` ke server dan menyimpannya sebagai `file.txt`.

Penjelasan:

* `ssh -p 1234 user@10.10.10.10` = koneksi ke server.
* `"cat > file.txt"` = perintah di server untuk membuat file.
* `< welcome.txt` = isi file lokal dikirim ke perintah tersebut.

---

## M. SFTP

SFTP adalah metode transfer file yang berjalan di atas SSH. Berbeda dengan `scp`, SFTP bersifat interaktif seperti masuk ke mode khusus transfer file.

### 1. Masuk ke SFTP

```bash
sftp -P 22 user@10.10.10.10
```

Fungsi:

Masuk ke server menggunakan SFTP melalui port 22.

Catatan:

Pada `sftp`, port memakai `-P` huruf besar.

---

### 2. Melihat direktori aktif di server

```bash
sftp> pwd
```

Contoh output:

```bash
Remote working directory: /root
```

Fungsi:

Menampilkan posisi direktori saat ini di server.

---

### 3. Melihat direktori aktif di lokal

```bash
sftp> lpwd
```

Contoh output:

```bash
Local working directory: /home/user
```

Fungsi:

Menampilkan posisi direktori komputer lokal.

---

### 4. Melihat isi folder server

```bash
sftp> ls
```

Fungsi:

Menampilkan isi direktori server.

---

### 5. Melihat isi folder lokal

```bash
sftp> lls
```

Fungsi:

Menampilkan isi direktori lokal.

---

### 6. Pindah folder di server

```bash
sftp> cd /home/user/
```

Fungsi:

Berpindah direktori di server.

---

### 7. Pindah folder di lokal

```bash
sftp> lcd /home/user/Desktop/
```

Fungsi:

Berpindah direktori lokal.

---

### 8. Upload file ke server

```bash
sftp> put file.txt
```

Fungsi:

Mengirim `file.txt` dari komputer lokal ke direktori aktif server.

---

### 9. Upload folder ke server

```bash
sftp> put -r nama_direktori
```

Fungsi:

Mengirim folder beserta isinya ke server.

---

### 10. Melanjutkan upload yang terputus

```bash
sftp> reput file.txt
```

Fungsi:

Melanjutkan proses upload file yang sebelumnya terputus.

---

### 11. Download file dari server

```bash
sftp> get file.txt
```

Fungsi:

Mengambil `file.txt` dari server ke komputer lokal.

---

### 12. Download folder dari server

```bash
sftp> get -r nama_direktori
```

Fungsi:

Mengambil folder dari server ke komputer lokal.

---

### 13. Melanjutkan download yang terputus

```bash
sftp> reget file.txt
```

Fungsi:

Melanjutkan proses download file yang sebelumnya terputus.

---

### 14. Keluar dari SFTP

```bash
sftp> exit
```

atau:

```bash
sftp> quit
```

Fungsi:

Keluar dari mode SFTP.

---

## N. Mengakses Server Menggunakan File Manager Nautilus

Pada Linux desktop seperti Ubuntu, server juga bisa dibuka melalui file manager.

Langkah:

1. Buka File Manager atau Nautilus.
2. Tekan:

```bash
CTRL + L
```

3. Ketik alamat:

```bash
sftp://user@10.10.10.10
```

4. Masukkan password jika diminta.

Fungsi:

Mengakses folder server melalui tampilan GUI, bukan terminal.

---

## O. Menggunakan PSCP di Windows dengan PuTTY

Jika menggunakan Windows, mahasiswa bisa memakai `pscp.exe` dari PuTTY.

### 1. Buka CMD sebagai Administrator

Klik kanan Command Prompt, lalu pilih:

```text
Run as administrator
```

---

### 2. Masuk ke folder PuTTY

```cmd
cd C:\Program Files\PuTTY
```

---

### 3. Download file dari server ke Windows

```cmd
pscp.exe -P 22 user@10.10.10.10:/home/user/file.txt C:\Users\Public\agis
```

Fungsi:

Mengambil file dari server Linux ke folder Windows.

---

### 4. Upload file dari Windows ke server

```cmd
pscp.exe -P 22 C:\Users\Public\agis\file.txt user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim file dari Windows ke server Linux.

---

### 5. Upload folder dari Windows ke server

```cmd
pscp.exe -r -P 22 C:\Users\Public\agis\folder user@10.10.10.10:/home/user/
```

Fungsi:

Mengirim folder dari Windows ke server Linux.

---

## P. Opsi Penting pada SCP

| Opsi | Fungsi                            | Contoh                                              |
| ---- | --------------------------------- | --------------------------------------------------- |
| `-r` | Mengirim folder secara recursive  | `scp -r folder user@IP:/home/user/`                 |
| `-P` | Menentukan port SSH pada SCP      | `scp -P 1234 file.txt user@IP:/home/user/`          |
| `-i` | Menggunakan private key           | `scp -i ~/.ssh/id_rsa file.txt user@IP:/home/user/` |
| `-v` | Menampilkan detail proses koneksi | `scp -v file.txt user@IP:/home/user/`               |
| `-C` | Mengaktifkan kompresi             | `scp -C file.txt user@IP:/home/user/`               |
| `-p` | Menjaga permission dan waktu file | `scp -p file.txt user@IP:/home/user/`               |
| `-l` | Membatasi bandwidth               | `scp -l 1000 file.txt user@IP:/home/user/`          |

Catatan penting:

* `scp -P` huruf besar = port.
* `scp -p` huruf kecil = preserve permission dan timestamp.
* `ssh -p` huruf kecil = port.

---

## Q. Kesalahan Umum dan Cara Mengatasinya

| Error                        | Penyebab                                                | Solusi                                    |
| ---------------------------- | ------------------------------------------------------- | ----------------------------------------- |
| `Permission denied`          | Password salah, key salah, atau tidak punya izin folder | Cek username, password, permission folder |
| `Connection refused`         | SSH server mati atau port salah                         | Cek `systemctl status ssh`, cek port      |
| `No such file or directory`  | Path file salah                                         | Pastikan lokasi file benar                |
| `Could not resolve hostname` | Salah format alamat server                              | Pastikan format `user@IP:/path` benar     |
| `Operation timed out`        | Server tidak bisa dijangkau                             | Cek koneksi jaringan dan firewall         |
| `Permission denied /`        | User tidak bisa menulis ke root directory               | Upload ke `/home/user/` dulu              |
| `Port salah`                 | Menggunakan `-p` pada SCP                               | Gunakan `-P` huruf besar untuk SCP        |

---

## R. Best Practice Keamanan

Agar penggunaan SCP dan SSH lebih aman, perhatikan hal berikut:

1. Hindari login langsung sebagai `root`.
2. Gunakan user biasa, lalu gunakan `sudo` jika butuh akses administrator.
3. Gunakan private key untuk autentikasi.
4. Jangan membagikan private key kepada orang lain.
5. Atur permission private key:

```bash
chmod 600 ~/.ssh/id_rsa
```

6. Gunakan port khusus jika diperlukan.
7. Pastikan firewall mengizinkan port SSH.
8. Selalu cek fingerprint server saat pertama kali login.

---

## S. Latihan Praktikum

### Latihan 1: Membuat File Lokal

Buat file bernama `latihan.txt`:

```bash
echo "Ini file latihan SCP" > latihan.txt
```

Cek isi file:

```bash
cat latihan.txt
```

---

### Latihan 2: Upload File ke Server

Upload file ke server:

```bash
scp latihan.txt user@10.10.10.10:/home/user/
```

Masuk ke server:

```bash
ssh user@10.10.10.10
```

Cek file:

```bash
ls -lah /home/user/
cat /home/user/latihan.txt
```

---

### Latihan 3: Download File dari Server

Keluar dari server:

```bash
exit
```

Download file dari server:

```bash
scp user@10.10.10.10:/home/user/latihan.txt .
```

---

### Latihan 4: Upload Folder

Buat folder:

```bash
mkdir folder_latihan
echo "file pertama" > folder_latihan/file1.txt
echo "file kedua" > folder_latihan/file2.txt
```

Upload folder:

```bash
scp -r folder_latihan user@10.10.10.10:/home/user/
```

---

### Latihan 5: Menggunakan SFTP

Masuk ke SFTP:

```bash
sftp user@10.10.10.10
```

Cek direktori server:

```bash
sftp> pwd
```

Cek direktori lokal:

```bash
sftp> lpwd
```

Upload file:

```bash
sftp> put latihan.txt
```

Download file:

```bash
sftp> get latihan.txt
```

Keluar:

```bash
sftp> exit
```

---

## T. Ringkasan Materi

SCP digunakan untuk menyalin file secara aman antara komputer lokal dan server melalui koneksi SSH. Format utamanya adalah:

```bash
scp sumber tujuan
```

Jika file dikirim dari lokal ke server, maka itu disebut upload:

```bash
scp file.txt user@10.10.10.10:/home/user/
```

Jika file diambil dari server ke lokal, maka itu disebut download:

```bash
scp user@10.10.10.10:/home/user/file.txt .
```

Untuk folder, gunakan opsi `-r`:

```bash
scp -r folder user@10.10.10.10:/home/user/
```

Untuk port tertentu, gunakan `-P` huruf besar pada SCP:

```bash
scp -P 1234 file.txt user@10.10.10.10:/home/user/
```

Untuk SSH, gunakan `-p` huruf kecil:

```bash
ssh -p 1234 user@10.10.10.10
```

SFTP dapat digunakan jika ingin transfer file secara interaktif:

```bash
sftp user@10.10.10.10
```

---

## U. Mini Quiz

1. Apa perbedaan upload dan download pada SCP?
2. Apa fungsi tanda titik `.` pada perintah SCP?
3. Apa perbedaan `scp -P` dan `ssh -p`?
4. Mengapa upload langsung ke `/` sering gagal?
5. Kapan kita harus menggunakan opsi `-r`?
6. Apa fungsi `sftp> put file.txt`?
7. Apa fungsi `sftp> get file.txt`?
8. Apa fungsi `scp -i ~/.ssh/id_rsa file.txt user@IP:/home/user/`?
9. Apa yang harus dicek jika muncul error `Connection refused`?
10. Apa perbedaan `pwd` dan `lpwd` pada SFTP?

Bagian yang paling penting untuk ditekankan ke mahasiswa: **urutan `scp` adalah sumber dulu, tujuan belakangan**. Jadi kalau remote ada di belakang berarti upload, kalau remote ada di depan berarti download.

# modul ini diambil dari :

[1]: https://man.openbsd.org/scp.1 "scp(1) - OpenBSD manual pages"
[2]: https://man.openbsd.org/sftp.1 "sftp(1) - OpenBSD manual pages"
[3]: https://ubuntu.com/server/docs/how-to/security/openssh-server/?utm_source=chatgpt.com "OpenSSH server"
