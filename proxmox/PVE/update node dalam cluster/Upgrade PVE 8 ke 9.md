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

Selama proses `apt dist-upgrade` berjalan, sistem akan memunculkan layar biru/teks interaktif. Lakukan tindakan berikut:

| Peringatan / Pertanyaan Sistem | Tindakan yang Wajib Dilakukan |
| --- | --- |
| *Restart services during package upgrades without asking?* | Sorot dan pilih **** |
| *Modified configuration file (issue, pve-enterprise, dll)* | Tekan **Enter** (Memilih *default* **N** / *Keep current version*) |
| *Modified configuration file (sshd_config, lvm.conf)* | Tekan **Enter** (Memilih *default* **N** / *Keep current version*) |

### Tahap 6: Finalisasi & Validasi Pasca-Upgrade

1. Setelah instalasi selesai dan terminal kembali ke baris perintah (*root@node~#*), jalankan:
```bash
reboot

```


2. Tunggu node menyala kembali (sekitar 2-5 menit).
3. Verifikasi dari node lain dengan perintah:
```bash
pvecm status

```


*(Pastikan node yang baru di-reboot sudah kembali masuk dalam daftar "Membership information" dan Quorum berstatus OK).*

---

**Catatan Eksekusi:** Lakukan modul ini berurutan dari Node 2, lalu Node 3, dan terakhir Node 4.

Mau langsung sikat eksekusi perintah `apt update` dan `apt dist-upgrade -y` di Node 2 lu sekarang?
