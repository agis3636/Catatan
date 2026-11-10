Membuat GPO Wallpaper adalah cara terbaik untuk melihat bagaimana Active Directory mengontrol tampilan komputer client secara massal. Kuncinya bukan hanya di setting GPO, tapi di **hak akses file gambar** tersebut agar bisa dibaca oleh komputer client.

Berikut adalah langkah-langkah lengkap dan urutannya:

---

## 1. Persiapan File Gambar (Shared Folder)

Client tidak bisa mengambil gambar dari folder lokal di Server (seperti `C:\Pictures`) kecuali folder tersebut dibagikan (shared) ke jaringan.

1. Buat folder baru di drive C server, misalnya: `C:\GPO_Assets`.
2. Masukkan file gambar wallpaper Anda ke sana (contoh: `perusahaan.jpg`).
3. **Klik kanan** folder `G_Assets` > **Properties** > tab **Sharing** > klik **Share**.
4. Tambahkan group **Everyone** atau **Domain Users**, lalu beri level **Read**.
5. Catat **Network Path**-nya, contohnya: `\\SERVER-AD\GPO_Assets\perusahaan.jpg`.
* *Tes dari client:* Buka Windows Explorer di client, ketik path tersebut. Jika gambar terbuka, berarti jalur file sudah benar.



---

## 2. Membuat dan Konfigurasi GPO

Sekarang kita buat aturannya di sistem.

1. Buka **Group Policy Management**.
2. Klik kanan pada **OU** tempat user Anda berada (misal: `IT_Dept`) > **Create a GPO in this domain, and Link it here...**.
3. Beri nama GPO: `Wallpaper_Pusat`.
4. Klik kanan GPO tersebut > **Edit**.
5. Telusuri folder berikut:
* **User Configuration**
* **Policies**
* **Administrative Templates**
* **Desktop**
* **Desktop** (klik folder Desktop di dalam folder Desktop)


6. Di panel sebelah kanan, klik dua kali pada **Desktop Wallpaper**.
7. Pilih **Enabled**.
8. Di bagian **Wallpaper Name**, masukkan **Network Path** yang Anda catat tadi: `\\SERVER-AD\GPO_Assets\perusahaan.jpg`.
9. Di bagian **Wallpaper Style**, pilih **Fill** atau **Fit** agar gambar rapi.
10. Klik **OK**.

---

## 3. Penerapan di Sisi Client (Praktek)

Kebijakan GPO biasanya diperbarui setiap 90 menit, tapi kita bisa memaksanya sekarang juga.

1. Buka komputer Client (login sebagai `budiit`).
2. Buka **Command Prompt (CMD)**.
3. Ketik perintah: `gpupdate /force`.
4. Jika ada pesan *"User Policy update has completed successfully"*, biasanya wallpaper belum langsung berubah.
5. **Log off (Sign out)** user `budiit`, lalu **Login kembali**.
6. Wallpaper akan berubah otomatis sesuai gambar yang Anda tentukan di server.

---

## Troubleshooting GPO Wallpaper

* **Wallpaper Hitam:** Biasanya terjadi karena Client tidak punya izin membaca file di Shared Folder. Pastikan izin Sharing dan Security pada folder di server sudah memberikan akses **Read** untuk "Everyone" atau "Domain Users".
* **Tidak Berubah:** Pastikan User tersebut benar-benar berada di dalam OU yang Anda pasangi GPO. Jika user di OU `Marketing` tapi GPO dipasang di `IT_Dept`, maka tidak akan ngefek.
* **GPO Terhalang:** Cek apakah ada GPO lain yang bertabrakan.

