Antarmuka Proxmox VE (PVE) saat ini tidak memiliki kolom untuk memasukkan kode TOTP secara langsung saat Anda menambahkan *storage* PBS. Jika akun pengguna di PBS (misalnya `root@pam`) mengaktifkan TOTP (2FA), koneksi menggunakan *password* biasa akan ditolak oleh sistem.

Solusinya adalah Anda **wajib menggunakan API Token** dari PBS sebagai pengganti kata sandi.

1. **Buat API Token:**

Buka antarmuka web PBS Anda. Buka menu **Access Control** -> **API Tokens**, lalu klik tombol **Add**.

* **User:** Pilih akun Anda (misalnya `root@pam`).
* **Token ID:** Beri nama bebas (misalnya `token-pve`).

  Klik **Add**. Layar akan menampilkan **Token Secret** (kumpulan karakter acak). **Salin dan simpan Token Secret ini** karena hanya akan ditampilkan satu kali.


2. **Berikan Izin (Permissions) pada Token:**

Secara default, token baru tidak memiliki hak akses apa pun. Buka menu **Access Control** -> **Permissions**, lalu klik **Add** -> **API Token Permission**.

* **Path:** Pilih `/datastore/nama_datastore_anda` (sesuaikan dengan nama datastore yang ingin ditambahkan).
* **API Token:** Pilih token yang baru saja Anda buat (misal: `root@pam!token-pve`).
* **Role:** Pilih **DatastoreAdmin** atau **DatastoreBackup**.

  Klik **Add**.


3. **Tambahkan Storage PBS di PVE:**

Kembali ke form *Add Proxmox Backup Server* di PVE. Isi form dengan data token:

* **Username:** Masukkan nama user digabung dengan nama token menggunakan tanda seru (`!`). Contoh: **`root@pam!token-pve`**
* **Password:** *Paste* (tempel) **Token Secret** yang sudah Anda salin di Langkah 1.
* Lanjutkan mengisi *Server*, *Datastore*, dan *Fingerprint* seperti biasa, lalu klik **Add**.


Untuk memverifikasi apakah ini berhasil, cek menu **Summary** dari `Storage-PBS` yang baru ditambahkan di PVE. Jika kapasitas (*usage*) dan grafiknya muncul, koneksi sudah berhasil menembus keamanan TOTP.
