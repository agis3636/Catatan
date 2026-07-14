
Sekarang kita masuk ke bagian yang paling memuaskan: **menjadi "Satpam" internet.** Kita akan memblokir kategori website tertentu agar tidak bisa diakses oleh Linux-linux tersebut.

Ikuti alur "tiga langkah" ini di FortiGate:

---

## Langkah 1: Buat Web Filter Profile (Aturan Blokir)

Di sini kita menentukan "apa" yang mau diblokir.

1. Buka menu **Security Profiles** > **Web Filter**.
2. Pilih profil `default`, lalu klik **Edit**.
3. Scroll ke bagian **FortiGuard category based filter**.
* Klik tanda centang (enable) di samping **FortiGuard category based filter**.
* Cari kategori yang mau diblokir (Contoh: **Social Networking** atau **Games**).
* Klik pada kolom **Action** untuk kategori tersebut dan ubah menjadi **BLOCK**.


4. Klik **OK** untuk menyimpan profil tersebut.

---

## Langkah 2: Pasang Aturan ke Firewall Policy

Profil tadi hanyalah "dokumen aturan". Agar aturan itu berjalan, kita harus memasangkannya ke "pintu gerbang" (Policy).

1. Buka menu **Policy & Objects** > **Firewall Policy**.
2. Klik **Edit** pada policy `LAN_to_Internet` (policy yang mengizinkan internet ke VPC tadi).
3. Scroll ke bawah ke bagian **Security Profiles**.
4. Klik tombol **Web Filter** untuk mengaktifkannya (toggle ke arah kanan).
5. Pilih profil yang tadi sudah kita edit (default).
6. *Catatan Penting:* Pastikan **SSL Inspection** diaktifkan ke `certificate-inspection` (ini supaya FortiGate bisa "mengintip" URL meskipun pakai HTTPS). Jika muncul *warning*, abaikan saja dulu untuk lab.
7. Klik **OK**.

---

## Langkah 3: Tes "Pecah Telur" (Client Side)



1. Buka **Linux6** (atau salah satu Linux yang sudah terhubung).
2. Buka browser-nya (biasanya di TinyCore ada ikon browser/Dillo).
3. Coba akses website yang masuk kategori tadi (Misal: `facebook.com` kalau tadi memblokir *Social Networking*).
4. **Hasil:**
* Jika berhasil, akan muncul **"FortiGuard Blocked" page**.
* Atau, jika FortiGate memblokir koneksi HTTPS, Mas mungkin akan melihat **"Your connection is not private"** (Certificate Warning). **Ini justru tandanya berhasil!** Itu artinya FortiGate sudah berhasil memotong koneksi web tersebut.



---

### Tips Troubleshooting (Jika tidak terblokir):

* **Sudah ganti browser, masih tembus?**
Kadang browser menyimpan *cache*. Coba **Clear Cache** atau buka di **Incognito/Private Mode**.
* **Masih bisa akses padahal sudah di-block?**
Cek lagi di **Policy & Objects > Firewall Policy**. Pastikan tombol **Web Filter** di policy tersebut sudah berwarna biru (aktif) dan profil yang dipilih sudah benar.

