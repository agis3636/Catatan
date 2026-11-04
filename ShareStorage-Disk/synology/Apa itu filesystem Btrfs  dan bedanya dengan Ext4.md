Dalam dunia Synology, format file (filesystem) adalah cara sistem mengatur dan menyimpan data di dalam hardisk Anda.

Berikut penjelasan mengenai **Btrfs** dan format lainnya yang tersedia di Synology:

### 1. Apa itu Format Btrfs?

**Btrfs** (B-tree File System) adalah sistem file modern yang dirancang khusus untuk mengatasi masalah pada server penyimpanan besar. Synology sangat menyarankan format ini untuk seri Plus seperti **DS925+** Anda karena memiliki fitur "pintar":

* **Self-Healing (Perbaikan Mandiri):** Btrfs bisa mendeteksi jika ada data yang korup (rusak) secara diam-diam dan memperbaikinya secara otomatis menggunakan salinan data dari mirror RAID 1 Anda.
* **Snapshot:** Anda bisa mengambil "foto" keadaan data pada waktu tertentu dengan sangat cepat. Jika tidak sengaja terhapus atau terkena Ransomware, Anda bisa mengembalikan data ke jam sebelumnya dalam hitungan detik.
* **Kuota Per Folder:** Anda bisa membatasi kapasitas folder tertentu agar tidak memenuhi seluruh hardisk.
* **Kompresi Data:** Membantu menghemat ruang penyimpanan dengan mengecilkan ukuran file secara otomatis.



### 2. Jenis Format File di Synology

Saat Anda membuat **Volume** di Storage Manager, biasanya akan muncul dua pilihan utama:

#### **A. Btrfs (Sangat Direkomendasikan)**

* **Kegunaan:** Standar untuk semua unit seri Plus (DS925+, DS923+, dll).
* **Kelebihan:** Fitur keamanan data paling lengkap, mendukung Snapshot, dan proteksi dari kerusakan data (Data Integrity).
* **Kekurangan:** Sedikit lebih banyak memakan RAM dibandingkan ext4, tapi dengan **RAM 12GB** Anda, ini bukan masalah sama sekali.

#### **B. ext4**

* **Kegunaan:** Sistem file tradisional Linux yang sangat stabil.
* **Kelebihan:** Sangat ringan dan kompatibel dengan hampir semua perangkat Linux.
* **Kekurangan:** Tidak memiliki fitur perbaikan mandiri (Self-healing) dan tidak mendukung Snapshot folder. Biasanya hanya digunakan pada Synology seri lama atau seri Value/J yang speknya rendah.



### Kesimpulan untuk Proyek Anda:

Untuk unit di **KLEPU** dan **BENGKULU**, saya sangat menyarankan Anda memilih **Btrfs** untuk kedua Volume (baik yang RAID 1 maupun Stand Alone).

* **Volume 1 (16TB RAID 1):** Wajib Btrfs agar data penting perusahaan aman dari *silent data corruption*.
* **Volume 2 (8TB Stand Alone):** Tetap gunakan Btrfs agar Anda tetap bisa menikmati fitur Snapshot meskipun disk tersebut tidak memiliki mirroring.

**Langkah Praktik:** Saat muncul jendela *"Choose a file system"* di Storage Manager nanti, pastikan Anda klik **Btrfs**.

Apakah Anda ingin saya jelaskan cara mengatur jadwal **Snapshot** otomatis agar data di Bengkulu dan Jogja aman dari virus atau terhapus tidak sengaja?




---

---



Betul sekali, pemahaman Anda sudah tepat. **Format file** dalam konteks ini memang istilah lain dari **filesystem** (sistem berkas), yaitu metode untuk mengatur bagaimana data disimpan dan diambil dari hardisk.

Berikut adalah penjelasan mengenai asal-usul dan ketersediaan filesystem di Synology:

### 1. Asal-usul Btrfs dan ext4

* **ext4 (Fourth Extended Filesystem):** Ini adalah filesystem standar yang dikembangkan oleh komunitas **Linux**. Ia merupakan evolusi dari ext2 dan ext3 yang sudah sangat tua, stabil, dan teruji selama puluhan tahun.
* **Btrfs (B-tree File System):** Ini juga berasal dari ekosistem **Linux**. Awalnya dikembangkan oleh perusahaan teknologi besar seperti **Oracle** untuk mengatasi keterbatasan ext4 dalam hal skalabilitas dan keamanan data pada server penyimpanan besar. Synology kemudian mengadopsi dan memodifikasi Btrfs agar bekerja maksimal dengan fitur-fitur mereka.

### 2. Apakah di Synology Hanya Ada 2 Filesystem?

Untuk **Internal Drive** (hardisk yang Anda pasang di dalam tray), Synology DSM memang hanya menyediakan **2 pilihan utama**:

* **Btrfs:** Direkomendasikan untuk seri Plus seperti **DS925+** Anda karena fitur keamanannya seperti *Self-healing* dan *Snapshot*.
* **ext4:** Biasanya digunakan sebagai alternatif jika ingin performa yang lebih ringan atau pada unit seri lama.

Namun, jika Anda menghubungkan **Hardisk Eksternal** atau **Flashdisk** melalui port USB di belakang Synology, sistem akan mendukung lebih banyak filesystem agar bisa dibaca di Windows atau Mac, seperti:

* **FAT32**
* **exFAT**
* **NTFS** (Standar Windows)
* **HFS+** (Standar Mac)

### 3. Mengapa Btrfs Sangat Spesial untuk Anda?

Mengingat unit ini akan dikirim ke **Bengkulu** dan **KLEPU** yang mungkin jauh dari jangkauan teknisi harian Anda:

* Btrfs adalah filesystem yang mampu "menjaga dirinya sendiri".
* Jika terjadi kesalahan penulisan data di salah satu HDD 16TB, Btrfs akan tahu disk mana yang datanya salah dan memperbaikinya menggunakan data dari disk pasangannya (RAID 1). ext4 tidak bisa melakukan perbaikan otomatis secerdas ini.



### Kesimpulan Praktik:

Gunakan **Btrfs** untuk semua volume internal Anda (baik RAID 1 maupun Stand Alone) agar manajemen data di Jogja dan Bengkulu lebih tenang.
