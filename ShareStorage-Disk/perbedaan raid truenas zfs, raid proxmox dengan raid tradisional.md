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





---

1. **RAID Tradisional:** Biasanya diatur oleh **Hardware** (RAID Card) atau driver standar Linux (`mdadm`). Kaku, butuh disk ukuran sama.
2. **Synology (SHR):** Ini adalah "selimut pintar" buatan Synology. Di dalamnya tetap pakai RAID tradisional, tapi dibungkus manajemen otomatis (LVM) biar bisa campur ukuran disk.
3. **PBS (ZFS):** Ini adalah **File System Canggih**. Dia tidak pakai hardware RAID. Dia mengatur data langsung di level software dengan matematika checksum yang kuat.

Berikut adalah **Tabel "Kamus Terjemahan"** agar Anda langsung paham persamaannya:

### 1. Tabel Persamaan (Kamus Besar RAID)

| Konsep Dasar | RAID Tradisional | Synology | PBS (ZFS) | Butuh Minimal |
| --- | --- | --- | --- | --- |
| **Tidak Aman** (Cepat/Total Size) | RAID 0 | Basic / JBOD | Stripe | 1+ Disk |
| **Duplikat** (Aman, 1 disk mati oke) | **RAID 1** | **SHR** (2 disk) | **Mirror** | 2 Disk |
| **Kombinasi** (Cepat & Aman) | **RAID 10** | RAID 10 | **RAID 10** | 4 Disk |
| **Parity 1** (Hemat, 1 disk mati oke) | **RAID 5** | **SHR** (3+ disk) | **RAIDZ-1** | 3 Disk |
| **Parity 2** (Super Aman, 2 disk mati oke) | **RAID 6** | **SHR-2** | **RAIDZ-2** | 4 Disk |
| **Parity 3** (Benteng Besi, 3 disk mati oke) | RAID 7* (Jarang) | - | **RAIDZ-3** | 5 Disk |

---

### 2. Penjelasan Lengkap Bedanya (Deep Dive)

Mari kita bedah istilah di PBS (ZFS) dan bandingkan dengan yang Anda tahu.

#### A. Single Disk vs. RAID 0

* **Single Disk (PBS):** Ini hanya menggunakan 1 hardisk apa adanya. Kalau rusak, data hilang. Tidak ada peningkatan kecepatan.
* **RAID 0 (Tradisional):** Menggabungkan 2 disk atau lebih jadi satu jalan tol lebar. Cepat sekali, tapi kalau 1 disk rusak, **SEMUA** data di disk lain ikut hancur.
* **Persamaan:** Sama-sama tidak aman (tidak ada redundansi).

#### B. Mirror vs. RAID 1

* **Konsep:** "Cermin". Apa yang ditulis di disk A, ditulis juga di disk B.
* **RAID 1 (Tradisional):** Butuh 2 disk yang ukurannya sama persis. Kapasitas total = ukuran 1 disk.
* **Mirror (PBS/ZFS):** Sama persis dengan RAID 1.
* **Kelebihan:** Proses *rebuild* (pemulihan) cepat jika ada disk rusak. Sangat disarankan untuk disk System/OS.

#### C. RAIDZ-1 vs. RAID 5 vs. SHR

* **Konsep:** "Parity Tunggal". Data disebar ke semua disk, plus 1 jatah "data matematika" (parity) untuk jaga-jaga. Kalau 1 disk jebol, data masih bisa dihitung ulang.
* **RAID 5 (Tradisional):** Rawan masalah "Write Hole" (data korup kalau listrik mati pas lagi nulis). Rebuild sangat lambat dan berisiko disk kedua ikut jebol saat rebuild.
* **SHR (Synology):** Ini sebenarnya RAID 5 yang pintar. Kalau Anda pakai disk 4TB + 4TB + 8TB, SHR bisa membagi-bagi ruang sisa agar terpakai semua.
* **RAIDZ-1 (PBS/ZFS):** Versi canggihnya RAID 5.
* **Bedanya:** Dia punya *variable stripe width* (lebar jalur dinamis) dan **atomic write** (anti data korup kalau mati lampu). Jadi RAIDZ-1 jauh lebih aman daripada RAID 5 biasa.



#### D. RAIDZ-2 vs. RAID 6

* **Konsep:** "Parity Ganda". Sama seperti di atas, tapi ada 2 jatah pengaman.
* **Kekuatan:** Boleh **2 hardisk rusak bersamaan**, data tetap aman.
* **RAID 6 (Tradisional):** Sangat berat di prosesor hardware.
* **RAIDZ-2 (PBS/ZFS):** Standar emas untuk backup server. Karena hardisk jaman sekarang besar-besar (10TB+), proses rebuild bisa berhari-hari. Risiko disk kedua rusak saat rebuild itu tinggi. RAIDZ-2 melindungi Anda dari kejadian apes itu.

#### E. RAIDZ-3 (Spesial ZFS)

* **Konsep:** "Parity Tiga".
* **Kekuatan:** Boleh **3 hardisk rusak bersamaan**.
* **Penggunaan:** Sangat jarang dipakai kecuali Anda punya puluhan hardisk dalam satu server (misal 24 hardisk) dan paranoid tingkat tinggi. Di RAID Tradisional (RAID 7) ini jarang ada standar bakunya, tapi di ZFS ini fitur standar.

---

### 3. Mana yang Harus Anda Pilih?

Karena Anda menggunakan PBS (yang berbasis ZFS), berikut panduannya:

1. **Kalau Anda pakai VM (Storage iSCSI dari TrueNAS/Synology):**
* Pilih **Single Disk** (Format XFS/Ext4).
* *Kenapa?* Karena keamanan (RAID/SHR) sudah diurus oleh TrueNAS/Synology di bawahnya. Jangan bikin RAID di atas RAID.


2. **Kalau Anda merakit PC Server Fisik (Bare Metal) untuk PBS:**
* **Total 2 Disk:** Pilih **Mirror** (Aman & Cepat).
* **Total 3-4 Disk:** Pilih **RAIDZ-1** (Kapasitas lega, keamanan lumayan).
* **Total 6+ Disk:** Pilih **RAIDZ-2** (Sangat aman).



**Kesimpulan:**
Nama-nama di PBS (ZFS) seperti `RAIDZ` itu sebenarnya hanyalah istilah "branding" ZFS untuk membedakan teknologinya dari RAID kuno yang punya kelemahan data korup (bit rot). Secara logika matematika, RAIDZ1 = RAID 5, dan RAIDZ2 = RAID 6.
