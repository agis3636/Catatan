Menghubungkan penyimpanan terpusat (SAN) ke hypervisor (Proxmox dan ESXi) menggunakan protokol iSCSI adalah standar industri yang sangat baik untuk dipelajari.

Berikut adalah panduan praktis langkah demi langkah.

---

### Tantangan Kapasitas (Penting!)

Anda memiliki 3 HDD x 20GB = **60GB Total Raw Capacity**.
Anda ingin membuat 2 LUN x 30GB = **60GB Total Request**.

Dalam dunia penyimpanan (terutama ZFS yang digunakan TrueNAS), Anda **tidak bisa** menggunakan 100% kapasitas disk. ZFS butuh ruang untuk *metadata* dan *overhead*.

* Jika Anda memaksa 60GB penuh, performa akan hancur dan risiko data korup tinggi.
* **Solusi:** Saya akan memandu Anda menggunakan metode **Stripe (RAID 0)** untuk mendapatkan kapasitas maksimal, dan menggunakan fitur **Thin Provisioning**.
* *Warning:* Metode Stripe tidak memiliki redundansi. **Jika 1 HDD rusak, semua data di ke-3 HDD hilang.** Jangan simpan data kritis tanpa backup.



---

### Panduan Praktis Konfigurasi

# Tahap 1: Persiapan Storage Pool di TrueNAS Core

<img width="1387" height="709" alt="Image" src="https://github.com/user-attachments/assets/097b8097-3a1d-498f-a3c2-b674f8d1da01" />

Karena HDD belum terbaca/disetting, kita harus membuat "Kolam" (Pool) dulu.

1. Login ke **TrueNAS Web GUI**.
2. Pergi ke menu **Storage** > **Pools**.
3. Klik **ADD** > **Create new pool**.
4. Beri nama, misal: `tank-data`.
5. Centang ke-3 HDD 20GB Anda.
6. Pada bagian **Data VDevs**, pastikan pilihannya adalah **Stripe** (Agar total jadi +/- 60GB). *Ingat risiko RAID 0 yang saya sebut di atas.*
7. Klik **Create**.

# Tahap 2: Membuat Zvol (Block Device untuk LUN)

<img width="1147" height="152" alt="Image" src="https://github.com/user-attachments/assets/6b5df3d2-51b3-432e-862a-85f0e12a68b1" />

iSCSI membutuhkan *block device*, di ZFS ini disebut **Zvol**.

1. Di menu **Storage** > **Pools**, klik titik tiga di samping `tank-data`.
2. Pilih **Add Zvol**.
* **Name:** `zvol-proxmox`
* **Size:** `25 GiB` (Saran saya kurangi jadi 25GB agar muat, atau ketik 30GB tapi centang *Sparse*).
* **Force Size:** Centang jika sistem protes soal kapasitas.
* **Sparse:** Centang (Ini adalah *Thin Provisioning*, artinya space hanya terpakai jika diisi data).
* Klik **Submit**.

<img width="1388" height="582" alt="Image" src="https://github.com/user-attachments/assets/b655923f-07f7-42db-b975-3ae47ee92e3e" />

3. Ulangi langkah di atas untuk membuat `zvol-esxi` dengan ukuran sama.

<img width="1155" height="224" alt="Image" src="https://github.com/user-attachments/assets/6403ead1-3a58-4872-9111-2c4250628b17" />

# Tahap 3: Konfigurasi iSCSI (Sharing)

<img width="1388" height="622" alt="Image" src="https://github.com/user-attachments/assets/21e9714a-0d5a-4a5f-a4ee-b31d5a544724" />

Sekarang kita "bagikan" Zvol tersebut agar bisa diakses jaringan. Pergi ke menu **Sharing** > **Block Shares (iSCSI)**.

Konfigurasi ini memiliki beberapa tab, kerjakan berurutan:

1. **Portals** (Pintu Gerbang):

<img width="769" height="302" alt="Image" src="https://github.com/user-attachments/assets/b0c2d553-a3c7-4ec9-869d-848b250557cd" />

* Klik **Add**.
* **IP Address:** Pilih `0.0.0.0` (atau IP khusus TrueNAS Anda).
* **Save**.


2. **Initiators** (Siapa yang boleh akses):

<img width="1152" height="604" alt="Image" src="https://github.com/user-attachments/assets/b72ced2d-9431-467f-81f7-2613a27183d8" />

* Klik **Add**.
* Biarkan `ALL` (kosong/default) agar Proxmox dan ESXi bisa akses tanpa filter rumit dulu.
* **Save**.


3. **Targets** (Nama share):

<img width="767" height="314" alt="Image" src="https://github.com/user-attachments/assets/0004e429-24c3-4005-8fe9-6ad5df27d423" />

* Klik **Add**.
* **Name:** `target-san`
* **Portal Group ID:** Pilih ID dari langkah 1.
* **Initiator Group ID:** Pilih ID dari langkah 2.
* **Save**.


4. **Extents** (Isi share/Mapping ke Zvol):

* *Buat Extent untuk Proxmox:*
* **Name:** `extent-proxmox`
* **Device:** Pilih `zvol-proxmox` yang dibuat di Tahap 2.
* **Save**.

<img width="767" height="572" alt="Image" src="https://github.com/user-attachments/assets/2e232367-8321-4c7e-b8a9-d8fa2284c365" />

* *Buat Extent untuk ESXi:*
* **Name:** `extent-esxi`
* **Device:** Pilih `zvol-esxi`.
* **Save**.




5. **Associated Targets** (Menghubungkan Target dengan Extent):

* Klik **Add**.
* **Target:** `target-san`
* **LUN ID:** `0`
* **Extent:** `extent-proxmox`
* **Save**.

<img width="768" height="211" alt="Image" src="https://github.com/user-attachments/assets/8aab2272-35a4-452f-b25c-8a8be97602b1" />

* Klik **Add** lagi.
* **Target:** `target-san`
* **LUN ID:** `1`
* **Extent:** `extent-esxi`
* **Save**.



*Catatan: Sekarang LUN 0 adalah jatah Proxmox, LUN 1 adalah jatah ESXi.*


---


# Tahap 4: Hidupkan Service iSCSI

<img width="1391" height="900" alt="Image" src="https://github.com/user-attachments/assets/627e2a93-f22b-403e-8653-18b17abd85dc" />

Ini sering lupa.

1. Ke menu **Services** (di menu kiri).
2. Cari **iSCSI**.
3. Geser tombolnya jadi **ON** (Running).
4. Centang **Start Automatically**.


---


# Tahap 5: Menghubungkan ke Proxmox VE

<img width="454" height="178" alt="Image" src="https://github.com/user-attachments/assets/045013c3-d676-40da-b301-7fcb91c059b5" />

1. Login ke **Proxmox GUI**.
2. Klik **Datacenter** (di menu kiri atas) > **Storage**.
3. Klik **Add** > **iSCSI**.
* **ID:** `truenas-iscsi`
* **Portal:** Masukkan IP Address TrueNAS.
* **Target:** (Akan muncul otomatis setelah memasukkan IP, pilih `target-san`).
* **Uncheck** "Use LUNs directly" (Kita ingin membuat LVM di atasnya agar lebih fleksibel).
* Klik **Add**.


4. Sekarang buat volume group agar bisa dipakai VM. Klik **Add** > **LVM**.
* **ID:** `san-proxmox`
* **Base Storage:** Pilih `truenas-iscsi` (yang baru dibuat).
* **Base Volume:** Pilih LUN 0 (yang ukurannya 30GB/25GB tadi).
* **Content:** Disk image, Container.
* Klik **Add**.



*Hasil: Storage `san-proxmox` siap digunakan untuk membuat VM.*

---

# Tahap 6: Menghubungkan ke VMware ESXi

1. Login ke **ESXi Web Client**.
2. Masuk ke **Storage** > **Adapters**.
3. Klik **Software iSCSI**.
* Ubah status menjadi **Enabled**.
* Di bagian **Dynamic Targets**, klik **Add dynamic target**.
* Masukkan IP Address TrueNAS.
* Klik **Save Configuration**.


4. Klik **Rescan Adapter** (di menu atas).
5. Masuk ke menu **Storage** > **Devices**. Anda harusnya melihat disk baru (FreeBSD iSCSI Disk).
6. Masuk ke menu **Storage** > **Datastores**.
* Klik **New Datastore**.
* Pilih **VMFS**.
* Beri nama, misal `san-esxi`.
* Pilih device iSCSI (pastikan pilih LUN 1 / atau bedakan berdasarkan ukuran jika sama). *Hati-hati jangan format LUN punya Proxmox*.
* Pilih **Full disk** > **Next** > **Finish**.

