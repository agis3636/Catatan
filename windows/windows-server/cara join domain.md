Langkah ini adalah momen pembuktian apakah konfigurasi AD dan DNS yang Anda buat sudah sinkron. Karena Anda menggunakan **Red Hat VirtIO Ethernet Adapter**, pastikan VM client dan VM server berada dalam satu *network bridge* atau *vlan* yang sama di Proxmox agar bisa saling berkomunikasi.

Berikut adalah langkah-langkah **Join Domain** pada komputer Client (Windows 10/11):

---

### 1. Verifikasi Akhir di Sisi Client

Pastikan kembali settingan IP di Client sesuai dengan gambar yang Anda kirim tadi:

* **IP Address:** `192.168.100.43`
* **Preferred DNS:** `192.168.100.100` (Wajib IP Server AD)
* **Test:** Buka CMD, ketik `ping test.local`. Harus me-reply ke `192.168.100.100`.

---

### 2. Proses Join Domain

1. Di komputer Client, klik **Start** dan ketik **"View advanced system settings"**, lalu tekan Enter.
2. Pilih tab **Computer Name**, lalu klik tombol **Change...**.
3. Pada bagian **Member of**, pilih opsi **Domain**.
4. Ketik nama domain Anda: `test.local`, lalu klik **OK**.
5. Akan muncul jendela login keamanan Windows:
* **Username:** `Administrator | user yang tadi di buat di OU`
* **Password:** (Password yang Anda buat saat instalasi AD DS | Password User OU)


6. Jika berhasil, akan muncul pesan: **"Welcome to the test.local domain."**
7. Klik **OK** dan komputer akan meminta **Restart**.

---

### 3. Login Menggunakan User OU

Setelah restart, jangan login menggunakan akun lokal lagi.

1. Di layar login, pilih **Other User**.
2. Masukkan salah satu user yang sudah Anda buat di OU tadi (misal: `it_dept\user.admin` atau `user.admin@test.local`).
3. Masukkan password-nya.
4. Windows akan menyiapkan profil baru untuk user domain tersebut.

---

### Tips Troubleshooting

* **Pesan "Domain not found":** Biasanya karena DNS di client belum mengarah ke `192.168.100.100`.
* **Waktu Tidak Sinkron:** Pastikan jam di Server dan Client sama. Jika selisih lebih dari 5 menit, Join Domain akan gagal karena masalah keamanan Kerberos.

