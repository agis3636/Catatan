Berdasarkan instruksi dari Pak Iyan mengenai setup **Synology DS925+** untuk proyek di **KLEPU (Jogja)** dan **ARMA BENGKULU**, berikut adalah panduan langkah demi langkah untuk melakukan konfigurasi sesuai permintaan:

---

## 1. Persiapan Hardware & Inisialisasi

Karena unit masih *indent*, pastikan saat barang tiba Anda memasang 3 HDD ke dalam bay yang tersedia. Sesuai instruksi Pak Iyan:

* **Bay 1:** HDD 8TB (untuk Stand Alone).
* **Bay 2 & 3:** HDD 16TB (untuk RAID 1).

### Langkah Awal:

1. Nyalakan Synology dan hubungkan ke jaringan lokal.
2. Gunakan **Synology Assistant** atau buka `find.synology.com` di browser.
3. Instal **DSM (DiskStation Manager)** versi terbaru.

---

## 2. Konfigurasi Storage (PENTING)

Instruksi Pak Iyan meminta dua jenis konfigurasi penyimpanan yang berbeda dalam satu unit:

### A. Setup RAID 1 Mirroring (2x 16TB)

Tujuannya adalah proteksi data; jika satu disk rusak, data tetap aman di disk lainnya.

1. Buka **Storage Manager** > **Storage**.
2. Klik **Create** > **Storage Pool**.
3. Pilih tipe RAID: **RAID 1**.
4. Pilih kedua **HDD 16TB**.
5. Setelah Pool selesai, buat **Volume 1** (disarankan menggunakan format **Btrfs**).

### B. Setup Stand Alone (1x 8TB)

1. Klik **Create** > **Storage Pool** lagi.
2. Pilih tipe RAID: **Basic** (atau **JBOD** jika ingin berdiri sendiri tanpa proteksi).
3. Pilih **HDD 8TB**.
4. Setelah Pool selesai, buat **Volume 2**.

---

## 3. Konfigurasi Jaringan (Network)

Sesuai catatan chat, Anda harus mengatur IP Static sebelum unit dikirim ke lokasi:

| Lokasi | IP Address | Subnet Mask | Gateway | DNS |
| --- | --- | --- | --- | --- |
| **KLEPU** | 192.168.88.14 | 255.255.255.0 | 192.168.88.1 | 192.168.88.1 |
| **BENGKULU** | 192.168.1.231 | 255.255.255.0 | 192.168.1.1 | 8.8.8.8 / 8.8.4.4 |

**Cara Setting:**

1. Buka **Control Panel** > **Network**.
2. Pilih tab **Network Interface** > Pilih **LAN 1** > **Edit**.
3. Pilih **Use manual configuration** dan masukkan data di atas sesuai target lokasi unit tersebut.

---

## 4. Finalisasi di Cervino

Sebelum dikirim, pastikan melakukan hal berikut:

* **Update RAM:** Pastikan RAM 12GB sudah terbaca di *Info Center*.
* **Shared Folder:** Buat folder utama (misal: "Data_Project") di Volume 1 (RAID 1) agar aman.
* **User Account:** Buat akun admin baru dan nonaktifkan akun "admin" bawaan demi keamanan.
* **Health Check:** Pastikan status semua HDD menunjukkan "Healthy" di Storage Manager.

---
