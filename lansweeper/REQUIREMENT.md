Berikut adalah spesifikasi **Hardware & Software** untuk server Lansweeper (On-Premise) secara lengkap.

---

### 1. SOFTWARE REQUIREMENT (Wajib)

Ini syarat mutlak sistem operasi dan pendukungnya:

* **Operating System (Server):**

1 **Rekomendasi:** Windows Server 2016, 2019, atau 2022 (64-bit).

2 *Boleh (tapi tidak disarankan untuk produksi besar):* Windows 10 atau Windows 11 (64-bit).


* **Architecture:** Wajib **x64 (64-bit)**. Lansweeper tidak lagi mendukung 32-bit.
* **Dependency (Otomatis diinstall, tapi wajib support):**

1 .NET Framework 4.8 atau lebih baru.

2 Visual C++ Redistributable.


* **Database:**

1 *Bawaan (Gratis):* SQL LocalDB (Hanya untuk < 1.000 aset).

2 *Produksi (Rekomendasi):* Microsoft SQL Server (Express/Standard/Enterprise).



---

### 2. HARDWARE REQUIREMENT (Berdasarkan Jumlah Aset)

Pilih spesifikasi di bawah ini sesuai jumlah aset kantor Anda:

#### A. Spek MINIMAL (Hanya untuk Tes / < 1.000 Aset)

Cocok jika kantor Anda kecil atau hanya untuk *trial*.

* **CPU:** 2 Cores (Virtual CPU).
* **RAM:** 4 GB (Jangan kurang, karena Windows Server sendiri sudah makan 2GB+).
* **Disk:** 40 GB SSD.
* **Database:** Bisa pakai SQL LocalDB (bawaan installer Lansweeper).

#### B. Spek REKOMENDASI (Standar Kantor / 1.000 - 5.000 Aset)

Ini adalah "Sweet Spot" untuk performa yang lancar dan tidak lemot saat buka web console.

* **CPU:** 4 Cores.
* **RAM:** 8 GB - 16 GB.
* **Disk:** 100 GB SSD (Log database akan membengkak seiring waktu).
* **Database:** **WAJIB** pakai SQL Server Express (Gratis) atau SQL Standard. *Jangan pakai LocalDB karena ada limit ukuran file 10GB.*

#### C. Spek MAKSIMAL / ENTERPRISE (> 10.000 Aset)

Untuk perusahaan besar dengan puluhan cabang.

* **CPU:** 8 Cores atau lebih (Biar scanning thread bisa jalan banyak sekaligus).
* **RAM:** 32 GB atau lebih (SQL Server sangat lapar RAM).
* **Disk:** 500 GB Enterprise SSD.
* **Database:** Wajib SQL Server Standard/Enterprise (Berbayar).

---

### 3. PENTING: Soal Database (SQL)

Ini yang sering bikin orang salah install. Saat instalasi Lansweeper, Anda akan ditanya pilihan database:

1. **Easy Install (LocalDB):**
* **Kelebihan:** Klik-klik langsung jadi. Tidak perlu setting SQL.
* **Kekurangan:** Lambat. Maksimal database cuma 10GB. Kalau aset Anda banyak, nanti error "Database Full".
* *Saran:* Hanya pakai ini kalau aset Anda di bawah 500 unit.


2. **Advanced Install (SQL Server):**
* **Kelebihan:** Cepat, stabil, muat jutaan data.
* **Kekurangan:** Anda harus install **MS SQL Server Express** dulu secara manual sebelum install Lansweeper.
* *Saran:* **Gunakan opsi ini.** Install SQL Express (Gratis) dulu di VM Windows tersebut, baru install Lansweeper dan arahkan ke sana.



### Kesimpulan untuk Setup:

Karena Anda admin IT yang paham infrastruktur:

1. Buat VM **Windows Server 2019/2022**.
2. Kasih **4 vCPU** dan **8GB RAM**.
3. Install **SQL Server Express 2019** dulu.
4. Baru download & install Lansweeper (pilih opsi *Advanced Install* -> connect ke SQL Express tadi).

