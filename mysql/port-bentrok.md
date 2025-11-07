# **MySQL di XAMPP gagal dijalankan**.
Biasanya penyebabnya bisa karena **port bentrok, data corrupt, atau service belum benar**.
Berikut langkah-langkah penanganannya dalam **bahasa Indonesia** 👇

### 🧩 1. **Cek apakah port MySQL bentrok**

Biasanya MySQL pakai **port 3306**. Bisa saja sudah dipakai oleh aplikasi lain (misalnya MySQL dari instalasi lain, Skype, atau Docker).

#### Langkah:

1. Buka **XAMPP Control Panel → Config → Service and Port Settings → MySQL**
2. Lihat di kolom **Main Port** (biasanya `3306`)
3. Ubah misalnya jadi `3307`
4. Klik **Save**, lalu **Start** ulang MySQL

### 🧹 2. **Cek file `mysql_error.log`**

1. Di XAMPP Control Panel, klik tombol **Logs** di sebelah MySQL → pilih **mysql_error.log**
2. Lihat bagian bawah file log itu — biasanya ada pesan seperti:

   * `InnoDB: Unable to lock ibdata1`
   * `InnoDB: Log file size mismatch`
   * atau `Tablespace corrupted`

Kalau kamu bisa **copy beberapa baris terakhir dari log itu**, aku bisa bantu jelaskan spesifik error-nya.

### 🧱 3. **Perbaiki folder `data` MySQL (kalau crash)**

Kalau MySQL sempat crash, file data-nya bisa rusak.

#### Cara aman:

1. Tutup XAMPP sepenuhnya.
2. Buka folder:

   ```
   C:\xampp\mysql\
   ```
3. Rename folder `data` → jadi `data-old`
4. Copy isi folder `C:\xampp\mysql\backup\` ke folder baru bernama `data`
5. Jalankan lagi MySQL dari XAMPP
6. Jika berhasil, kamu bisa coba pindahkan database lama (dari `data-old`) ke yang baru (satu per satu).

### 🧰 4. **Jalankan XAMPP sebagai Administrator**

Kadang MySQL gagal karena kurang hak akses.
→ Klik kanan **XAMPP Control Panel → Run as Administrator**
→ Lalu klik **Start** pada MySQL.

### ⚡ 5. **Cek di Windows Services**

1. Tekan `Win + R` → ketik `services.msc`
2. Cari service bernama `mysql` atau `xamppmysql`
3. Kalau ada yang sudah berjalan, **Stop** dulu, lalu coba Start dari XAMPP lagi.


---


# **Port MySQL berubah** (phpMyAdmin masih pakai `3306`, tapi MySQL mungkin pakai `3307`)
# **Konfigurasi phpMyAdmin salah** (di `config.inc.php`)

### ✅ Langkah-langkah perbaikan

#### **1. Pastikan MySQL sedang berjalan**

* Buka **XAMPP Control Panel**
* Lihat baris **MySQL**

  * Kalau statusnya **merah / belum jalan**, klik **Start**
  * Kalau gagal start → kirim isi log error MySQL (klik tombol **Logs** di sebelah kanan)

#### **2. Cek port MySQL**

1. Di XAMPP → klik **Config → Service and Port Settings → MySQL**
2. Lihat di bagian **Main Port** → misalnya tertulis `3307`
3. Catat angka port-nya.

#### **3. Sesuaikan phpMyAdmin**

Kalau port MySQL bukan `3306`, kamu harus ubah pengaturan di phpMyAdmin.

1. Buka file ini:

   ```
   C:\xampp\phpMyAdmin\config.inc.php
   ```
2. Cari baris ini:

   ```php
   $cfg['Servers'][$i]['host'] = '127.0.0.1';
   ```
3. Ganti jadi (misalnya MySQL pakai port 3307):

   ```php
   $cfg['Servers'][$i]['host'] = '127.0.0.1:3307';
   ```
4. Simpan file, lalu **restart Apache dan MySQL** di XAMPP.
5. Buka lagi phpMyAdmin di browser:
   👉 [http://localhost/phpmyadmin](http://localhost/phpmyadmin)

#### **4. Jika masih error**

Coba langkah tambahan ini:

* Jalankan **XAMPP sebagai Administrator**
* Pastikan tidak ada MySQL lain di Windows Service:

  * Tekan `Win + R`, ketik `services.msc`
  * Cari `MySQL` atau `MariaDB`, **Stop** kalau aktif (bukan dari XAMPP)
* Lalu coba Start MySQL lagi dari XAMPP
