## Modul Rolling Upgrade Proxmox 7 ke 8 untuk no-subscription

| Fase | Fokus Utama | Status VM |
| --- | --- | --- |
| **Persiapan** | Pengosongan node target | Dipindahkan ke node aktif lain |
| **Eksekusi** | Perubahan repositori dan instalasi sistem | Berjalan normal di node lain |
| **Selesai** | Penyesuaian kernel dan kembalinya *cluster* | Siap ditarik kembali ke node asal |

### 1. Evakuasi Node

* Lakukan *Live Migration* seluruh VM/Container dari node target ke node penyokong.
* Pastikan node target kosong 100% dari VM yang berjalan untuk mencegah *downtime*.

### 2. Eksekusi Upgrade di Terminal

Buka *Shell* pada node target dan jalankan eksekusi ini secara berurutan:

1. `rm -f /etc/apt/sources.list.d/pve-enterprise.list`
2. `sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list`
3. `echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list`
4. `echo "deb http://download.proxmox.com/debian/ceph-quincy bookworm no-subscription" > /etc/apt/sources.list.d/ceph.list`
5. `apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6ED0E7B82643E131 78DBA3BC47EF2265 F8D2585B8783D481`
6. `apt update && apt dist-upgrade -y`

### 3. Penanganan Layar & Finalisasi

* Sorot dan pilih **** saat ditanya *Restart services during package upgrades without asking*.
* Tekan **Enter (opsi bawaan N / Keep current version)** setiap kali sistem menanyakan modifikasi file konfigurasi (contoh: `/etc/issue`, `sshd_config`, `lvm.conf`, `pve-enterprise.list`) agar koneksi *cluster* aman.
* Ketik `reboot` setelah proses instalasi selesai dan kembali ke baris perintah.
* Tunggu node kembali *online*, tarik kembali VM ke node tersebut, lalu mulai ulangi siklus dari tahap 1 untuk node selanjutnya.

---
---
---

### Perbandingan Upgrade: Enterprise vs No-Subscription

| Parameter | Enterprise Repository (Klien) | No-Subscription (Lab Lu) |
| --- | --- | --- |
| **Status Lisensi** | Berbayar, terikat pada mesin (node) | Gratis, tanpa jaminan dukungan resmi |
| **File Target PVE** | `/etc/apt/sources.list.d/pve-enterprise.list` | `/etc/apt/sources.list.d/pve-no-subscription.list` |
| **Tindakan Repositori** | **Edit** (ubah *bullseye* jadi *bookworm*) | **Hapus** file *enterprise* bawaan, lalu **buat** file baru |
| **Stabilitas Paket** | Sangat teruji (*Production-ready*) | Paket terbaru, rentan *bug* minor |

---

### Modul Eksekusi: Upgrade Proxmox 7 ke 8 (Enterprise)

**1. Evakuasi & Validasi Awal**

* Lakukan *Live Migration* seluruh VM ke node lain. Pastikan node target kosong dari proses komputasi aktif.
* Periksa kesiapan sistem:
`pve7to8 --full`
*(Pastikan tidak ada status `FAIL`. Jika ada `WARN`, pelajari dampaknya sebelum lanjut).*

**2. Penyesuaian Repositori OS & Proxmox**
Ubah basis OS dari Debian 11 (Bullseye) ke Debian 12 (Bookworm) pada seluruh sumber repositori.

* Sistem utama: `sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list`
* Proxmox: `sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list.d/pve-enterprise.list`
* Ceph *(Jika pakai)*: `sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list.d/ceph.list`

**3. Pembaruan dan Unduh Paket**
Jalankan pengecekan repositori baru:
`apt update`
*(Jika sukses tanpa error, langsung lanjut ke tahap 4. Jika muncul pesan `NO_PUBKEY`, lihat bagian Troubleshooting di bawah).*

**4. Eksekusi Instalasi Mayor**
Mulai proses *upgrade* sistem secara menyeluruh:
`apt dist-upgrade -y`

**5. Penanganan Prompt Konfigurasi (Kritis)**
Selama instalasi berjalan, layar akan beberapa kali memunculkan interaksi. Lakukan tindakan ini:

* **Restart services without asking?**: Pilih **** (geser dengan panah kiri, tekan Enter).
* **Modified configuration file (issue, sshd_config, lvm.conf, dll)**: Selalu tekan **Enter** (memilih opsi *default* **N** atau *keep your currently-installed version*) agar konfigurasi *cluster* tidak rusak tertimpa setelan pabrik.

**6. Finalisasi**
Setelah instalasi selesai dan kembali ke baris perintah (*shell*):
`reboot`
*(Setelah node menyala, verifikasi versi pada GUI, kembalikan VM, lalu eksekusi node selanjutnya).*

---

### Troubleshooting Kedaruratan

**Masalah:** Saat `apt update`, muncul pesan `W: GPG error... NO_PUBKEY 6ED0E7B82643E131...`
**Penyebab:** Node klien tertinggal *patch* lama, sehingga tidak memiliki kunci sertifikat Debian 12.
**Solusi:** Tarik kunci GPG secara manual dengan perintah berikut, lalu ulangi `apt update`:
`apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6ED0E7B82643E131 78DBA3BC47EF2265 F8D2585B8783D481`

Apakah infrastruktur Proxmox di tempat klien lu nanti menggunakan *storage* lokal LVM/ZFS biasa, atau menggunakan Ceph *Shared Storage* bawaan Proxmox?
