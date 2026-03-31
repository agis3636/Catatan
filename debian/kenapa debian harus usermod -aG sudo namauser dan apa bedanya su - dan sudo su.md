Itu memang perbedaan mendasar dalam filosofi desain antara **Debian** dan **Ubuntu**. Debian dibuat dengan prinsip "Secure by Default," sedangkan Ubuntu dibuat dengan prinsip "User Friendly."

Berikut alasan teknis dan filosofis di baliknya:

---

### 1. Filosofi Keamanan (Debian vs Ubuntu)
* **Debian:** Menganggap `root` dan `user` biasa adalah dua entitas yang harus terpisah sejauh mungkin. Debian tidak ingin user biasa punya "kunci gerbang" ke sistem secara otomatis. Jadi, fitur `sudo` dianggap sebagai opsional.
* **Ubuntu:** Berbasis Debian, tapi dimodifikasi untuk pengguna rumahan/desktop. Mereka ingin user bisa langsung kerja tanpa harus pusing pindah-pindah akun, makanya user pertama otomatis diberi hak akses `sudo`.

### 2. Kenapa Harus `usermod -aG sudo`?
Pada proses instalasi Debian (baik Netinstall maupun DVD):
* **Jika kamu mengisi Password Root:** Debian berasumsi kamu akan mengelola sistem lewat akun `root` murni menggunakan perintah `su -`. Karena ada akun `root` yang aktif, Debian tidak merasa perlu memasukkan user biasa ke grup `sudo`.
* **Jika kamu mengosongkan Password Root:** Nah, ini rahasianya! Kalau saat install Debian kamu **tidak mengisi** password root sama sekali, Debian akan otomatis menginstall `sudo` dan memasukkan user pertama kamu ke grup `sudo` (persis seperti perilaku Ubuntu).

### 3. Masalah "User Biasa vs User Root"
Di Ubuntu, akun `root` aslinya **terkunci** (tidak punya password). Kamu hanya bisa jadi superuser lewat `sudo`. Di Debian, akun `root` itu **hidup** dan punya password sendiri. Itulah kenapa di Debian kamu sering harus ketik:
1. `su -` (pindah ke root)
2. Masukkan password root
3. Baru bisa kerja.

---

### Tips biar Debian "Serasa" Ubuntu:
Kalau kamu malas melakukan `usermod` setiap kali install Debian baru, lakukan satu hal ini saat instalasi berikutnya:
> **Kosongkan kolom "Root Password" saat diminta.**

Dengan begitu, Debian akan:
1. Menonaktifkan akun root langsung.
2. Menginstall paket `sudo`.
3. Menjadikan user buatanmu sebagai admin (bisa pakai `sudo` langsung).

---
---
---

Bedanya cukup krusial di "isi kantong" (variabel lingkungan) yang dibawa oleh user tersebut.

Secara sederhana:

### 1. `su -` (The Clean Switch)
* **Artinya:** "Substitute User" dengan login penuh. Tanda strip (`-`) itu sangat penting.
* **Cara kerja:** Kamu berpindah ke akun `root` seolah-olah kamu baru saja menyalakan komputer dan login sebagai `root` dari awal.
* **Apa yang terjadi:** Sistem akan memuat semua **Environment Variables** milik `root` (seperti `$PATH`, folder rumah `/root`, dll).
* **Password:** Kamu harus memasukkan **password milik root**.
* **Kapan dipakai:** Saat kamu ingin melakukan pemeliharaan sistem yang mendalam di Debian.

### 2. `sudo su` (The Hybrid)
* **Artinya:** "Jalankan perintah `su` menggunakan hak akses `sudo`".
* **Cara kerja:** Kamu meminjam kekuatan "Admin" (sudo) untuk membuka pintu akun `root`.
* **Apa yang terjadi:** Kamu jadi `root`, tapi seringkali kamu masih membawa "barang-barang" dari user biasamu (seperti lokasi folder saat ini atau beberapa pengaturan user).
* **Password:** Kamu memasukkan **password user kamu sendiri**, bukan password root.
* **Kapan dipakai:** Ini gaya khas **Ubuntu**. Karena di Ubuntu password root biasanya dikunci, satu-satunya cara jadi root adalah lewat `sudo`.

---

### Perbedaan Utama dalam Tabel

| Fitur | `su -` | `sudo su` |
| :--- | :--- | :--- |
| **Password yang diminta** | Password **Root** | Password **User Kamu** |
| **Lingkungan (Env)** | Murni milik Root (Bersih) | Campuran/Variabel User |
| **Folder Tujuan** | Langsung ke `/root` | Biasanya tetap di folder saat itu |
| **Syarat** | Akun Root harus punya password | User harus terdaftar di grup `sudo` |

---

### Kenapa di Debian `su -` lebih disarankan?
Di Debian, kalau kamu cuma ketik `su` (tanpa tanda `-`), seringkali perintah seperti `reboot`, `shutdown`, atau `fdisk` tidak bisa dijalankan karena folder `/sbin` (tempat perintah sakti berada) tidak masuk ke dalam `$PATH` kamu.

Dengan menggunakan **`su -`**, Debian akan memastikan semua "kunci sakti" tersebut tersedia di terminal kamu.

---
---
---

Secara fisik kita memang bisa pindah-pindah direktori pakai perintah `cd`. Tapi, perbedaan "daleman"-nya bukan cuma soal posisi folder, melainkan soal **"Tas Belanja" (Environment Variables)** yang dibawa oleh si user.

Begini analogi gampangnya:

---

### 1. Perbedaan "Isi Tas" (PATH)
Di Linux, ada folder khusus bernama `/sbin` dan `/usr/sbin`. Di sinilah tersimpan perintah-perintah "sakti" (seperti `fdisk`, `reboot`, `iptables`, atau `service`).

* **`su -`**: Ibarat kamu ganti baju jadi Root dan membawa tas milik Root. Di dalam tas itu sudah ada kunci ke `/sbin`. Jadi kalau kamu ketik `reboot`, sistem langsung kenal.
* **`sudo su`**: Ibarat kamu user biasa yang pakai topeng Root, tapi tas yang kamu bawa masih tas user biasa. Seringkali, kalau kamu ketik `reboot`, sistem bakal jawab: *"Command not found"*, padahal kamu sudah jadi Root. Kamu harus ketik lengkap `/sbin/reboot` baru dia mau jalan.

### 2. Logika Posisi Folder
Kamu benar soal posisi direktori, tapi ada detail kecil:
* **`su -`**: Otomatis pindah ke `/root` (Home folder-nya Root), bukan ke `/` (Root directory).
* **`sudo su`**: Tetap di posisi terakhir kamu berada (misal di `/home/user/Downloads`). Ini memang praktis kalau kamu mau eksekusi file installer NAKIVO yang baru saja kamu download di folder user tersebut.

---

### Perbandingan Visual Sederhana

| Fitur | `su -` | `sudo su` |
| :--- | :--- | :--- |
| **Ibaratnya** | Ganti Identitas Total | Pinjam Kekuatan (Topeng) |
| **Variabel `$PATH`** | Lengkap (Milik Root) | Terbatas (Milik User) |
| **Home Directory** | Pindah ke `/root` | Tetap di tempat asal |
| **Keamanan** | Lebih "Bersih" & Aman | Lebih praktis tapi "Kotor" |
