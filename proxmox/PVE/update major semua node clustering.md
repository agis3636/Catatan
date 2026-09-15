# sudah cluster 3 node dan mau upgrade versi saat ini

<img width="306" height="335" alt="Image" src="https://github.com/user-attachments/assets/0af6adf1-d6df-4148-9d2b-d584ec3b79d7" />

## 1. Bisa tidak di-upgrade?
Bisa, tapi ada aturan mutlak: Proxmox tidak bisa lompat major version.
Tidak bisa langsung upgrade dari versi 7 ke versi 9. Prosesnya wajib berurutan: selesaikan dulu upgrade ke seluruh sistem versi 8, baru setelah itu bisa lanjut ke versi 9.

## 2. Bisa tidak cluster diisi node beda versi?
Hanya boleh sementara, dan hanya untuk versi yang bersebelahan (contoh: node versi 7 dan node versi 8).

Jika menggabungkan versi yang jaraknya terlalu jauh (misal 7 dan 9), sistem cluster (Corosync) akan gagal sinkronisasi karena perbedaan protokol. Akibatnya, cluster bisa pecah, kehilangan quorum, dan seluruh VM gagal diakses.

### Cara Aman Upgrade Cluster Tanpa Downtime:

Karena ini lingkungan produksi klien, lu harus menggunakan metode rolling upgrade:

- **Persiapan:** Pastikan semua node sudah berada di versi update terakhir dari seri 7.4. Lakukan backup seluruh VM.

- **Kosongkan Node 1:** Migrate (Live Migration) semua VM dari node pertama ke node 2 dan node 3.

- **Eksekusi Upgrade:** Jalankan script pve7to8 di terminal node pertama untuk mengecek error, lalu lakukan upgrade OS (Debian) dan Proxmox ke versi 8.

- **Verifikasi:** Setelah node pertama restart dan menyala dengan versi 8, pastikan statusnya "Online" di dalam cluster. (Di titik ini, wajar jika ada percampuran versi 7 dan 8 sementara waktu).

- **Kembalikan VM:** Migrate kembali VM ke node pertama.

- **Ulangi:** Lakukan langkah 2-5 untuk node kedua dan ketiga secara bergantian.

Setelah seluruh node berhasil berada di versi 8, lu baru bisa merencanakan siklus upgrade yang sama untuk menuju ke versi 9.
