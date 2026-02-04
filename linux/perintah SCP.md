Betul banget, secara bawaan Windows (CMD atau PowerShell) memang tidak punya perintah `rsync`. Perintah itu aslinya milik Linux.

Kalau kamu mau pakai `rsync` di Windows, kamu harus install alat tambahan seperti **Git Bash** atau **WSL**. Tapi kalau tidak mau ribet install apa-apa lagi, kita optimalkan saja `scp` yang sudah ada.

Berikut adalah perintah `scp` yang paling "bandel" agar tidak gampang putus:

---

### 1. Perintah SCP "Anti-Putus" (Windows CMD)

Gunakan opsi `-v` untuk melihat prosesnya secara detail dan `-C` untuk kompresi:

```cmd
scp -v -C zcs-NETWORK-10.1.0_GA_4655.UBUNTU22_64.20240819064312.tgz zimbra@192.168.100.60:/home/zimbra/

```

* **`-v` (Verbose):** Kalau koneksi mati, kamu bisa lihat di baris mana dia berhenti.
* **`-C` (Compress):** Mengecilkan ukuran file saat dikirim supaya lebih cepat sampai.

---

### 2. Solusi Kalau Tetap "Broken Pipe"

Kalau `scp` tetap gagal terus, ada dua cara alternatif yang sering saya pakai di Windows:

* **Gunakan WinSCP atau FileZilla:** Ini cara paling aman. Aplikasi ini punya fitur **"Resume"** otomatis kalau internet kamu bapuk. Kamu tinggal *drag-and-drop* saja filenya.
* **Pindahkan ke Folder `/tmp`:** Kadang `scp` gagal karena masalah izin akses di folder home. Coba kirim ke `/tmp` dulu yang izinnya lebih bebas:
```cmd
scp zcs-NETWORK-10.1.0_GA_4655.UBUNTU22_64.20240819064312.tgz zimbra@192.168.100.60:/tmp/

```



---

### 3. Cek Ulang Setelah Berhasil

Karena file ini untuk **Ubuntu 22.04 LTS** (seperti yang ada di daftar download Zimbra terbaru), pastikan file tidak rusak setelah sampai.

Setelah berhasil upload, masuk ke SSH server dan jalankan:

```bash
md5sum /home/zimbra/zcs-NETWORK-10.1.0_GA_4655.UBUNTU22_64.20240819064312.tgz

```

Cocokkan kodenya dengan nilai **MD5** yang ada di website Zimbra atau di gambar daftar download kamu tadi. Kalau beda satu huruf saja, instalasi pasti gagal.
