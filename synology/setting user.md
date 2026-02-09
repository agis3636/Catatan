Untuk menambahkan user agar bisa mengakses folder (hak akses baca saja) di Synology, silakan ikuti langkah-langkah praktik berikut di DSM Demo atau unit asli nanti:

### 1. Membuat User Baru

1. Buka **Control Panel**.
2. Pilih menu **User & Group**.
3. Klik tombol **Create**.
4. Masukkan **Username** (misal: `staf_bengkulu`) dan **Password**.
5. Klik **Next** terus sampai ke bagian **Permissions**.

### 2. Mengatur Hak Akses "Baca Saja" (Read Only)

Pada jendela **Permissions** saat membuat user (atau edit user yang sudah ada):

1. Cari folder yang ingin Anda bagikan (misal folder di **Volume 1** yang tadi Anda lihat).
2. Centang kolom **Read Only** (Hanya Baca).
* **No Access:** User tidak bisa lihat folder sama sekali.
* **Read/Write:** User bisa tambah, edit, dan hapus file.
* **Read Only:** User hanya bisa buka dan copy file, tapi tidak bisa hapus atau ubah isinya.


3. Klik **Next** dan **Done**.



### Tips Penting:

sebaiknya buatkan user yang berbeda untuk masing-masing lokasi agar keamanan data terjaga.

* **Folder di Volume 1 (RAID 1):** Berikan akses **Read/Write** hanya untuk admin, dan **Read Only** untuk staf biasa.
* **Folder di Volume 2 (Stand Alone):** Bisa Anda gunakan untuk folder publik atau pertukaran data biasa.

**Langkah Terakhir:** Jangan lupa pastikan user tersebut juga diberi izin untuk melihat folder `#snapshot` (di menu Advanced folder tadi) agar mereka bisa mengambil file lama secara mandiri tanpa perlu lapor ke Anda.
