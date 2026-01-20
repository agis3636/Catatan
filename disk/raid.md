Ada banyak jenis RAID (Redundant Array of Independent Disks), dan masing-masing punya keseimbangan yang berbeda antara **kecepatan (performance)**, **kapasitas (storage)**, dan **keamanan (fault tolerance)**.

Berikut adalah daftar lengkap opsi RAID standar, RAID kombinasi, hingga opsi khusus milik Synology.

---

## 1. Opsi RAID Standar

### RAID 0 (Striping)

* **Cara Kerja:** Data dipecah dan disebar ke semua disk tanpa perlindungan sama sekali.
* **Kapasitas:** 100% dari total disk.
* **Kelebihan:** Sangat cepat untuk baca/tulis.
* **Kekurangan:** Jika **1 disk saja rusak**, semua data hilang.
* **Minimal Disk:** 2 unit.

### RAID 1 (Mirroring)

* **Cara Kerja:** Data di-copy identik ke disk lain.
* **Kapasitas:** Hanya 50% dari total disk (jika pakai 2 disk).
* **Kelebihan:** Paling aman untuk data krusial. Jika satu disk mati, sistem tetap jalan.
* **Kekurangan:** Boros kapasitas (bayar 2 disk cuma dapat kapasitas 1 disk).
* **Minimal Disk:** 2 unit.

### RAID 5 (Striping + Parity)

* **Cara Kerja:** Seperti yang kita bahas sebelumnya, menggunakan striping dengan data paritas yang tersebar.
* **Kapasitas:** .
* **Kelebihan:** Keseimbangan terbaik antara kecepatan dan kapasitas.
* **Minimal Disk:** 3 unit.

### RAID 6 (Striping + Double Parity)

* **Cara Kerja:** Mirip RAID 5, tapi punya **dua lapis paritas**.
* **Toleransi:** Bisa tahan jika **2 disk mati sekaligus**.
* **Kekurangan:** Proses tulis lebih lambat karena harus menghitung dua paritas.
* **Minimal Disk:** 4 unit.

### RAID 10 (1+0 / Mirroring + Striping)

* **Cara Kerja:** Disk di-mirror dulu (RAID 1), lalu hasil mirror-nya di-striping (RAID 0).
* **Kelebihan:** Performa sangat tinggi dan sangat aman.
* **Kekurangan:** Sangat mahal karena kapasitas yang bisa dipakai hanya 50%.
* **Minimal Disk:** 4 unit.

---

## 2. Opsi Khusus Synology (SHR)

Jika kamu menggunakan NAS Synology, mereka punya teknologi bernama **SHR (Synology Hybrid RAID)**. Ini adalah opsi yang paling direkomendasikan bagi pengguna umum.

### SHR (Synology Hybrid RAID)

* **Kelebihan Utama:** Kamu bisa mencampur disk dengan kapasitas berbeda (misal: 1TB, 2TB, dan 4TB). Di RAID standar, semua disk akan dianggap seukuran disk terkecil (semua jadi 1TB). Di SHR, sisa kapasitasnya tetap bisa diolah agar tidak mubazir.
* **Toleransi:** Tahan 1 disk rusak (setara RAID 5).

### SHR-2

* **Cara Kerja:** Mirip SHR tapi dengan toleransi **2 disk rusak** (setara RAID 6).
* **Minimal Disk:** 4 unit.

---

## Tabel Perbandingan Cepat

| Tipe RAID | Minimal Disk | Toleransi Kerusakan | Kapasitas Terpakai |
| --- | --- | --- | --- |
| **RAID 0** | 2 | 0 Disk | 100% |
| **RAID 1** | 2 | 1 Disk | 50% |
| **RAID 5** | 3 | 1 Disk | 67% - 90% |
| **RAID 6** | 4 | 2 Disk | 50% - 80% |
| **RAID 10** | 4 | 1-2 Disk* | 50% |
| **SHR** | 1 | 1 Disk | Maksimal (Optimized) |

---

**Kesimpulan untuk kamu:**
Jika kamu punya **5 disk @ 1 TB**:

1. Pakai **RAID 5** untuk dapat 4 TB (Standar server/Proxmox).
2. Pakai **RAID 6** untuk dapat 3 TB tapi lebih aman (Tahan 2 disk mati).
3. Jika di **Synology**, gunakan **SHR** agar nantinya jika kamu ingin upgrade salah satu disk ke 4TB atau 8TB, kapasitasnya tidak terbuang.
