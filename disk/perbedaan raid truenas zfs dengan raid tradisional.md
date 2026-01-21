Berdasarkan daftar pilihan di TrueNAS SCALE yang Anda tampilkan, TrueNAS menggunakan sistem **ZFS** yang memiliki penamaan sedikit berbeda dengan RAID tradisional, namun konsepnya sangat mirip.

Berikut adalah padanannya agar Anda mudah memilih untuk 6 disk 100GB tersebut:

### 1. Daftar Persamaan (ZFS vs RAID Tradisional)

| Nama di TrueNAS (ZFS) | Persamaan RAID Tradisional | Penjelasan Singkat |
| --- | --- | --- |
| **Stripe** | **RAID 0** | Menggabungkan kapasitas semua disk. Kencang, tapi **satu disk rusak = semua data hilang**. |
| **Mirror** | **RAID 1 / 10** | Data disalin (cloning) antar disk. Sangat aman dan kencang, tapi kapasitas terbuang banyak. |
| **RAIDZ1** | **RAID 5** | Tahan jika **1 disk rusak**. Kapasitas yang didapat: Total Disk - 1. |
| **RAIDZ2** | **RAID 6** | Tahan jika **2 disk rusak sekaligus**. Sangat direkomendasikan untuk keamanan data. |
| **RAIDZ3** | **RAID 7 (Triple Parity)** | Tahan jika **3 disk rusak sekaligus**. Sangat aman tapi pemrosesan lebih berat. |

---

### 2. Apa itu dRAID (dRAID 1, 2, 3)?

Opsi **dRAID** (Distributed RAID) adalah teknologi terbaru yang biasanya digunakan untuk server dengan **jumlah disk yang sangat banyak** (misalnya puluhan atau ratusan disk).

* **Fungsinya:** Mempercepat proses pemulihan (rebuild) saat ada disk yang rusak.

---

Pilihan **dRAID** (Distributed RAID) pada TrueNAS SCALE sebenarnya memiliki konsep perlindungan data yang sama dengan **RAIDZ**, namun dengan cara penyebaran data yang berbeda di dalam disk.

Berikut adalah persamaannya dengan RAID tradisional jika dilihat dari jumlah toleransi kerusakan disknya:

### 1. Persamaan Toleransi Kerusakan

* **dRAID 1**: Sama dengan **RAID 5** (atau RAIDZ1).
* Menggunakan 1 paritas, artinya hanya tahan jika **1 disk rusak**.


* **dRAID 2**: Sama dengan **RAID 6** (atau RAIDZ2).
* Menggunakan 2 paritas, artinya tahan jika **2 disk rusak sekaligus**.


* **dRAID 3**: Sama dengan **RAID 7** / Triple Parity (atau RAIDZ3).
* Menggunakan 3 paritas, artinya tahan jika **3 disk rusak sekaligus**.



---

### 2. Apa Perbedaan "d" di Depan Namanya?

Meskipun toleransi kerusakannya sama dengan RAID 5/6, cara kerja **dRAID** berbeda dengan RAID tradisional dalam hal pemulihan (rebuild):

* **Distributed Spare**: Pada RAID tradisional, jika ada disk rusak, sistem akan menulis ulang data ke satu disk "Hot Spare" yang menganggur.
* **dRAID**: Ruang kosong (spare) disebar ke **seluruh disk** yang ada di pool.
* **Keuntungan**: Proses *rebuild* (memperbaiki data setelah disk diganti) menjadi **jauh lebih cepat** karena semua disk bekerja bersamaan untuk mengisi ruang kosong tersebut.

---

### .

* **Saran:** **Tidak disarankan** menggunakan dRAID untuk jumlah disk sesedikit.
* **Alasannya:** dRAID dirancang untuk sistem besar dengan puluhan disk (misal 20-100 disk) agar proses *rebuild* tidak memakan waktu berhari-hari.
