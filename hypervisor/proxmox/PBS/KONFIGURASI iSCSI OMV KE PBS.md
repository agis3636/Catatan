# Target: Membuat 1 iSCSI LUN dari OMV sebesar 200 GB, menghubungkannya ke PBS, membuat filesystem EXT4, sampai muncul sebagai Datastore dan siap digunakan.

<img width="451" height="280" alt="Image" src="https://github.com/user-attachments/assets/fa6f6536-9491-4267-9108-b08429b50467" />

## 1. Persiapan OMV

Pastikan perangkat:

| Komponen   | Kapasitas              |
| ---------- | ---------------------- |
| `/dev/sda` | 20 GB — OS OMV         |
| `/dev/sdb` | 100 GB — Storage iSCSI |

Plugin yang dibutuhkan:

- `openmediavault-lvm2`
- `openmediavault-tgt`

Keduanya sudah terpasang.

Pastikan `/dev/sdb` kosong. Jangan menyentuh `/dev/sda`.

## 2. Membuat partisi disk

CLI — OMV Shell

GUI standar OMV tidak menyediakan pembuatan partisi GPT secara bebas, jadi gunakan CLI.

Jalankan:

```
parted -s /dev/sdb mklabel gpt
parted -s /dev/sdb mkpart pbs 1MiB 100%
parted -s /dev/sdb set 1 lvm on
partprobe /dev/sdb
```

Fungsi:

- `mklabel gpt` — membuat tabel partisi GPT baru.
- `mkpart` — membuat satu partisi menggunakan seluruh kapasitas.
- `set 1 lvm on` — memberi flag LVM.
- `partprobe` — memperbarui informasi partisi kernel.

Cek hasil:

```
lsblk -o NAME,SIZE,TYPE /dev/sdb
```

Target:

```
sdb     100G disk
└─sdb1  100G part
```

## 3. Membuat Physical Volume

GUI — OMV

Buka:

Storage → LVM → Physical Volumes → Create (+)

Isi:

| Field  | Nilai       |
| ------ | ----------- |
| Device | `/dev/sdb1` |

Klik Save → Apply.

Jika `/dev/sdb1` tidak muncul di dropdown, gunakan CLI:

```
pvcreate /dev/sdb1
```

Perintah tersebut menginisialisasi partisi sebagai LVM Physical Volume. Setelah itu refresh halaman OMV.

## 4. Membuat Volume Group

GUI:

Storage → LVM → Volume Groups → Create (+)

Isi:

| Field   | Nilai       |
| ------- | ----------- |
| Name    | `vg_pbs`    |
| Devices | `/dev/sdb1` |

Klik Save → Apply.

## 5. Membuat Logical Volume

GUI:

Storage → LVM → Logical Volumes → Create (+)

Isi:

| Field        | Nilai     |
| ------------ | --------- |
| Name         | `lun_pbs` |
| Volume Group | `vg_pbs`  |
| Size         | 100%      |

Klik Save → Apply.

Device yang terbentuk:

```
/dev/vg_pbs/lun_pbs
```

Hasil: Logical Volume sekitar 200 GB siap menjadi backing store iSCSI.

## 6. Membuat iSCSI Target

GUI — OMV

Buka:

Services → TGT → Settings

Aktifkan TGT, lalu Save → Apply.

Selanjutnya:

Services → TGT → Targets → Create (+)

Isi:

| Field             | Nilai                 |
| ----------------- | --------------------- |
| Name              | `pbs-lun`             |
| Backing Store     | `/dev/vg_pbs/lun_pbs` |
| Initiator Address | `192.168.200.4`       |
| Enabled           | Yes                   |

Klik Save → Apply.

Hasil yang diharapkan:

| Parameter     | Nilai                 |
| ------------- | --------------------- |
| Target        | `pbs-lun`             |
| Backing Store | `/dev/vg_pbs/lun_pbs` |
| Initiator     | `192.168.200.4`       |
| Port          | TCP `3260`            |

Catat IQN yang muncul di tabel Targets. IQN tersebut dipakai untuk koneksi PBS.

TGT menggunakan backing device sebagai LUN dan ACL untuk membatasi initiator yang boleh mengaksesnya.

---
---
---

### 7. Menghubungkan iSCSI ke PBS

A. Install iSCSI Initiator

```
apt update
apt install open-iscsi -y
```

Fungsi: menginstal layanan iSCSI Initiator di PBS.

B. Discovery Target

```
iscsiadm -m discovery -t sendtargets -p 192.168.200.2
```

Fungsi: mencari Target iSCSI dari OMV.

Hasil:

```
192.168.200.2:3260,1 iqn.2026-11.openmediavault:pbs-lun
```

C. Login ke Target

```
iscsiadm -m node -T iqn.2026-11.openmediavault:pbs-lun -p 192.168.200.2 --login
```

Fungsi: menghubungkan PBS ke LUN OMV.

D. Aktifkan koneksi otomatis (WAJIB)

```
iscsiadm -m node -T iqn.2026-11.openmediavault:pbs-lun -p 192.168.200.2 --op update -n node.startup -v automatic
```

Fungsi: membuat PBS otomatis login ke iSCSI Target setiap boot.

Verifikasi:

```
iscsiadm -m node -o show | grep node.startup
```

Hasil wajib:

```
node.startup = automatic
```

Ini adalah perbaikan utama dari masalah kita sebelumnya. Sebelum diperbaiki, nilainya `manual`, sehingga PBS tidak otomatis menemukan LUN setelah reboot. Dokumentasi Debian juga mensyaratkan `automatic` untuk auto-login.



### 8. Identifikasi disk iSCSI

Jalankan:

```
lsblk -o NAME,SIZE,TYPE,TRAN,FSTYPE,MOUNTPOINTS
```

Contoh hasil sesuai konfigurasi kita:

```
sda     15G
sdb    200G
└─sdb1 200G xfs /mnt/datastore/synology

sdc    100G disk iscsi
```

Fungsi: memastikan disk iSCSI 100 GiB sudah terdeteksi.

Catatan: Nama `/dev/sdc` bisa berubah setelah reboot. Gunakan identitas permanen untuk langkah berikutnya.

```
ls -l /dev/disk/by-path/ | grep iscsi
```

### 9. Membuat filesystem EXT4

Hanya dilakukan sekali untuk LUN baru

Gunakan path iSCSI hasil langkah 8.

```
mkfs.ext4 -L PBS_OMV_ISCSI /dev/disk/by-path/PATH_ISCSI
```

Fungsi: membuat filesystem EXT4 pada LUN agar dapat digunakan PBS.

Cek UUID:

```
blkid /dev/disk/by-path/PATH_ISCSI
```

Catat UUID yang muncul.

Jangan ulangi `mkfs.ext4` setelah filesystem berhasil dibuat karena akan menghapus data di dalamnya.

### 10. Mount storage secara permanen

A. Buat folder mount

```
mkdir -p /mnt/datastore/omv-pbs
```

B. Edit konfigurasi mount

```
nano /etc/fstab
```

Tambahkan:

```
UUID=UUID_HASIL_BLKID /mnt/datastore/omv-pbs ext4 defaults,_netdev 0 2
```

Ganti `UUID_HASIL_BLKID` dengan UUID sebenarnya.

C. Aktifkan mount

```
systemctl enable iscsid open-iscsi
systemctl daemon-reload
mount -a
```

Fungsi:

- `enable` memastikan layanan aktif saat startup sesuai konfigurasi sistem.
- `daemon-reload` memuat ulang konfigurasi systemd.
- `mount -a` menjalankan konfigurasi mount dari fstab.

D. Verifikasi

```
findmnt --mountpoint /mnt/datastore/omv-pbs
df -hT /mnt/datastore/omv-pbs
```

Hasil sesuai konfigurasi kita:

```
Filesystem     Type  Size
/dev/sdc       ext4   98G
```

Opsi `_netdev` penting agar mount dikenali sebagai filesystem berbasis jaringan dan dikoordinasikan dengan startup iSCSI.



PENTING: Jangan mount filesystem LUN PBS di OMV. Hanya PBS yang boleh mengakses filesystem EXT4 tersebut.

### 11. Add Datastore di PBS GUI

Buka:

```
https://192.168.200.4:8007
```

Pilih Datastore → Add Datastore.

Isi:

| Field          | Nilai                    |
| -------------- | ------------------------ |
| Name           | `omv-pbs`                |
| Datastore Type | Local                    |
| Backing Path   | `/mnt/datastore/omv-pbs` |
| GC Schedule    | daily                    |
| Prune Schedule | daily                    |

Klik Add.

Selesai

Datastore `omv-pbs` akan muncul di sidebar PBS.

![Proxmox Backup Server | Storagecenter Documentation](https://images.openai.com/static-rsc-4/vCoG43BXwrVAhJzZmh04lbMMFZVaOq184caYXS0g5t5o5YZrwc-tB3V_nyZxoDWN51Aja5gbs2_D-v3ebH0tKaT-qXMjQmG9YZ22_VwRD5SsDxYn7kmLbuCH_5culsQ0kqP-4rG323_kKQ8WD4M3LkjSlk9NvV_wAZhTl8Q4JIw?purpose=inline)

[wiki.storagecenter.ch](https://wiki.storagecenter.ch/de/applications/Proxmox-Backup-Server?utm_source=chatgpt.com)

## 12. Tes auto-connect setelah reboot

Bagian ini saya tambahkan karena kita sempat mengalami masalah datastore tidak tersedia setelah PBS dinyalakan kembali.

Pastikan OMV sudah menyala dan TGT aktif, kemudian reboot PBS.

Setelah PBS hidup, jalankan:

```
iscsiadm -m session
```

```
lsblk -o NAME,SIZE,TRAN,FSTYPE,MOUNTPOINTS
```

```
findmnt /mnt/datastore/omv-pbs
```

Hasil:

| Pemeriksaan        | Hasil                    |
| ------------------ | ------------------------ |
| iSCSI Session      | Connected                |
| Disk iSCSI         | 100 GiB                  |
| Filesystem         | EXT4                     |
| Mount              | `/mnt/datastore/omv-pbs` |
| PBS Datastore      | Available                |
| Synology Datastore | Tetap Available          |


Catatan: Datastore PBS menggunakan filesystem standar seperti EXT4, XFS, atau ZFS. Kapasitas yang terlihat di PBS sedikit lebih kecil dari kapasitas nominal akibat perbedaan satuan dan ruang filesystem.
