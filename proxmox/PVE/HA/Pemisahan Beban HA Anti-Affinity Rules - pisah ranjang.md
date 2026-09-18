# Konfigurasi: Pemisahan Beban (Anti-Affinity / Pisah Ranjang)

**Tujuan:** Mendaftarkan VM/CT ke dalam sistem HA dan memastikan dua atau lebih VM/CT target dilarang keras berjalan di *node* fisik yang sama.

### Langkah 1: Daftarkan VM/CT ke HA Resources

Langkah ini wajib agar VM/CT dipantau oleh sistem High Availability.

1. Buka menu **Datacenter** > klik **HA**.
2. Di tabel bawah (**Resources**), klik tombol **Add**.
3. Isi form untuk CT 100, lalu ulangi proses yang sama untuk CT 101.

**Penjelasan Parameter HA Resources:**

| Parameter | Pengaturan | Penjelasan Efek |
| --- | --- | --- |
| **VM** | Pilih ID (`100` / `101`) | Menentukan target mesin yang akan diawasi. |
| **Max. Restart** | Isi angka `1` | Proxmox akan mencoba me-*restart* mesin 1x di *node* yang sama jika *hang*. Jika gagal, langsung diungsikan. |
| **Max. Relocate** | Isi angka `1` | Membatasi mesin agar maksimal hanya pindah 1x antar-node saat darurat untuk mencegah *looping error*. |
| **Failback** | **Dicentang (True)** | Mengizinkan mesin ditarik pulang otomatis ke *node* asalnya saat *node* tersebut sudah nyala kembali. |
|  | **Tidak Dicentang** | Mesin akan menetap selamanya di *node* pengungsian meskipun *node* asal sudah normal. |
| **Auto-Rebalance** | **Dicentang (True)** | Menyalakan sensor agar sistem membagi rata beban RAM/CPU antar-node secara otomatis saat kondisi normal. |
|  | **Tidak Dicentang** | Sensor pasif. Mesin hanya dipindah saat *node* mati, bukan untuk menyeimbangkan beban. |

*Klik **Add** setelah selesai.*


<img width="479" height="176" alt="Image" src="https://github.com/user-attachments/assets/b0527fa0-ce87-48cc-af9a-27ed46ce083f" />


<img width="480" height="175" alt="Image" src="https://github.com/user-attachments/assets/3e1a31a1-dd78-4c31-8354-d1b29d88d2de" />


<img width="743" height="133" alt="Image" src="https://github.com/user-attachments/assets/71c8de70-d7f2-4ebe-88e9-60c5ae2628b0" />


---

### Langkah 2: Buat Aturan HA Resource Affinity Rules

Langkah ini untuk mengunci aturan "Pisah Ranjang" pada mesin yang sudah didaftarkan di Langkah 1.

1. Masih di menu **Datacenter** > **HA**, klik sub-menu **Affinity Rules** di panel kiri.
2. Fokus ke tabel bagian **BAWAH** (**HA Resource Affinity Rules**).
3. Klik tombol **Add**.

**Penjelasan Parameter HA Resource Affinity:**

| Parameter | Pilihan / Status | Penjelasan Efek |
| --- | --- | --- |
| **Enable** | **Dicentang (✔)** | Aturan langsung aktif, dipantau, dan dieksekusi oleh Proxmox saat itu juga. |
|  | **Tidak Dicentang** | Aturan dimatikan (hanya jadi *draft*). Sistem akan mengabaikannya. |
| **Affinity** | **Keep Separate (negative)** | **[PILIH INI]** Memaksa mesin-mesin terpilih wajib BERPISAH di *node* yang berbeda. |
|  | **Keep Together (positive)** | Memaksa mesin-mesin terpilih wajib MENEMPEL di satu *node* yang sama. |
| **HA Resources** | Centang ID `100` & `101` | Memilih mesin mana saja yang akan dikenakan aturan pemisahan ini. (Hanya muncul jika sudah lewat Langkah 1). |
| **Comment** | Bebas diisi teks | Hanya berfungsi sebagai label/catatan teknisi (contoh: `Pisah-CT100-CT101`). Tidak ngefek ke sistem. |

*Klik **Add** setelah selesai.*

<img width="479" height="157" alt="Image" src="https://github.com/user-attachments/assets/da598434-d0e2-4bbf-8ebd-cc55258b154e" />

---

**Hasil Akhir di Lapangan:**
Pengaturan selesai. Jika *node* tempat CT 100 dan CT 101 mati, Proxmox akan memindahkan mereka ke dua *node* yang berbeda (misal satu ke `node2`, satu ke `node3`). Mereka dijamin tidak akan pernah berkumpul di satu *node* yang sama.
