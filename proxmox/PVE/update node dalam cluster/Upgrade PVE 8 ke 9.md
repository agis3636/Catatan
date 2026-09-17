## Upgrade Proxmox VE 8 ke 9 (No-Subscription)

**Syarat Mutlak Sebelum Mulai:**

* Seluruh node dalam *cluster* harus sudah berada di versi Proxmox 8.4 ke atas.
* Node target yang akan di-upgrade harus dikosongkan dari VM/Container aktif (*Live Migration* ke node lain).

### Tahap 1: Validasi Kesiapan Sistem

Buka terminal pada node target, lalu jalankan alat pengecekan bawaan:

```bash
pve8to9 --full

```

*Tindakan:* Pastikan di bagian **SUMMARY** terbaca `0 FAILURES`. Jika ada *WARNING* terkait `intel-microcode`, abaikan saja.

<img width="129" height="119" alt="Image" src="https://github.com/user-attachments/assets/a5e05a8b-bbc6-45d5-b3c4-635929338de7" />

---

### Tahap 2: Pembersihan Repositori Usang

Langkah ini wajib untuk mencegah *error 404* atau deteksi paket ganda saat pembaruan.

1. Hapus duplikasi repositori Proxmox di daftar utama:
```bash
sed -i '/pve-no-subscription/d' /etc/apt/sources.list

```


2. Hapus sisa konfigurasi Ceph versi lama (bisa diinstal ulang via GUI nanti jika dibutuhkan):
```bash
rm -f /etc/apt/sources.list.d/ceph.list

```



### Tahap 3: Perubahan Basis OS (Bookworm ke Trixie)

Ubah tautan repositori dari Debian 12 (Bookworm) ke Debian 13 (Trixie).

1. Ubah repositori OS utama:
```bash
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list

```


2. Ubah repositori Proxmox gratisan:
```bash
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list.d/pve-no-subscription.list

```



### Tahap 4: Eksekusi Instalasi Mayor

Unduh daftar paket terbaru dan mulai proses instalasi menyeluruh.

1. Sinkronisasi paket baru:
```bash
apt update

```


*(Pastikan proses selesai tanpa ada teks error berwarna merah).*
2. Eksekusi pembaruan inti:
```bash
apt dist-upgrade -y

```



### Tahap 5: Penanganan Layar Interaktif (Krusial)

* pilih English lalu Enter

  <img width="525" height="553" alt="Image" src="https://github.com/user-attachments/assets/5c5422da-f721-44e1-bc4e-b0e0eded560e" />

---

* pilih yes saat ditanya *Restart services during package upgrades without asking*.

  (Gunakan tombol panah kiri di keyboard untuk menggeser sorotan merah dari `<No>` ke `<Yes>`).

  **Penjelasan:**
  Pilihan ini mengizinkan sistem untuk otomatis me-restart *service* atau layanan di *background* (seperti SSH, cron, atau jaringan) saat paketnya sedang di-upgrade, tanpa harus nanya dan nunggu persetujuan satu per satu.

  Kalau pilih `<No>`, proses *upgrade* ini bakal sering berhenti di tengah jalan cuma buat nunggu mencet tombol konfirmasi. Jadi pilih `<Yes>` biar prosesnya mulus dan cepat selesai.

  <img width="981" height="179" alt="Image" src="https://github.com/user-attachments/assets/39885d61-b94f-4080-afda-89275ea6f97d" />

---

* langsung tekan **Enter** aja. Pilihan **keep the local version currently installed** (yang diblok warna merah) itu sudah paling tepat.

  **Penjelasan:**
  File `sshd_config` adalah jantung dari koneksi SSH di server. Proxmox sangat bergantung pada pengaturan spesifik di file ini (seperti mengizinkan *login root* secara langsung) agar antar-node di dalam *cluster* bisa saling berkomunikasi dengan lancar, terutama saat memigrasikan VM.

  Kalau ubah atau timpa dengan versi *maintainer*, akses komunikasi *cluster* bisa terputus dan berpotensi kehilangan akses ke *node* tersebut. Jadi, pertahankan konfigurasi lokal yang sudah ada. Lanjut!

  <img width="967" height="271" alt="Image" src="https://github.com/user-attachments/assets/b3a4a30b-f522-4089-a555-266c168d1e7f" />

---

* Langsung tekan **Enter** aja (itu otomatis memilih *default*-nya yaitu **N**).

  **Penjelasan singkat:**
  File `/etc/issue` itu isinya teks *welcome screen* yang muncul di layar monitor fisik/konsol saat server Proxmox lu baru nyala (yang biasanya ada tulisan *Welcome to the Proxmox Virtual Environment* beserta IP address-nya).

  Kalau pilih Y (di- *replace* pakai versi *maintainer*), nanti layar *welcome* khas Proxmox malah hilang ketimpa tulisan standar bawaan OS Debian. Jadi pilih **N** (*keep your currently-installed version*) adalah pilihan yang paling tepat.

  <img width="413" height="150" alt="Image" src="https://github.com/user-attachments/assets/36c2477d-68b3-4d8f-b32c-c17c64ac581d" />

---

* Seperti biasa, tekan **Enter** lagi aja (pilihan *default* **N**).

  **Penjelasan:**
  File `lvm.conf` ini berhubungan dengan pengaturan penyimpanan *Logical Volume Manager* (LVM). Proxmox punya konfigurasi khusus di file ini untuk mengelola `local-lvm` (tempat menyimpan disk VM lokal). Kalau timpa pakai konfigurasi standar bawaan Debian, sistem penyimpanan lokal Proxmox bisa kacau dan disk VM malah berisiko tidak terbaca. Jadi, tetap pertahankan versi lokalnya.

  <img width="425" height="157" alt="Image" src="https://github.com/user-attachments/assets/f3b3f1a0-044f-4db1-9b66-83c66200103e" />

---

* Masih sama, langsung tekan **Enter** (pilihan **N**) lagi!

  **Penjelasan:**
  Ingat kan di langkah sebelumnya sempat menjalankan perintah `rm -f /etc/apt/sources.list.d/pve-enterprise.list`?

  Nah, sistem `apt` mendeteksi kalau file bawaan pabrik tersebut sudah dihapus, dan dia menawarkan apakah mau memulihkannya kembali (Y) atau membiarkannya tetap terhapus (N).

  Karena menggunakan repositori *No-Subscription* (gratisan), **wajib** memilih **N** agar file *enterprise* itu tidak dipulihkan. Kalau sampai file itu kembali, nanti setiap kali menjalankan `apt update` di masa depan, akan terus-terusan diganggu oleh *error* "401 Unauthorized".

  Jadi, biarkan dia tetap terhapus. Lanjut hajar Enter!

  <img width="468" height="161" alt="Image" src="https://github.com/user-attachments/assets/f15c09bc-75b3-4ffa-a9eb-5f4abc9ee36b" />

---

---



### Tahap 6: Finalisasi & Validasi Pasca-Upgrade

1. Setelah instalasi selesai dan terminal kembali ke baris perintah (*root@node~#*), jalankan:
```bash
reboot

```

  <img width="613" height="153" alt="Image" src="https://github.com/user-attachments/assets/d42d5220-0d81-4c6d-bd2d-9a587435009d" />


2. Tunggu node menyala kembali (sekitar 2-5 menit).

  <img width="1008" height="292" alt="Image" src="https://github.com/user-attachments/assets/7f98cf33-eb1a-47e1-9cbb-ded480710855" />
   
3. Verifikasi dari node lain dengan perintah:
```bash
pvecm status

```


*(Pastikan node yang baru di-reboot sudah kembali masuk dalam daftar "Membership information" dan Quorum berstatus OK).*

---

**Catatan Eksekusi:** Lakukan modul ini berurutan dari Node 2, lalu Node 3, dan terakhir Node 4.

Mau langsung sikat eksekusi perintah `apt update` dan `apt dist-upgrade -y` di Node 2 lu sekarang?
