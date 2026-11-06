### Target: Membuat 1 iSCSI LUN dari OMV sebesar 200 GB, menghubungkannya ke PBS, membuat filesystem EXT4, sampai muncul sebagai Datastore dan siap digunakan.

#### Topologi

OpenMediaVault

IP: 192.168.200.2

iSCSI Target — 200 GB

LVM2 + TGT

Network / TCP 3260

Proxmox Backup Server

IP: 192.168.200.4

iSCSI Initiator → EXT4 → Datastore

## 1. Persiapan OMV

Pastikan perangkat:

| Komponen   | Kapasitas              |
| ---------- | ---------------------- |
| `/dev/sda` | 20 GB — OS OMV         |
| `/dev/sdb` | 200 GB — Storage iSCSI |

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
sdb     200G disk
└─sdb1  200G part
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

TGT menggunakan backing device sebagai LUN dan ACL untuk membatasi initiator yang boleh mengaksesnya.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub



## 7. Menghubungkan iSCSI ke PBS

CLI — PBS Shell

A. Install iSCSI Initiator

```
apt update
apt install open-iscsi -y
```

Fungsi: memasang layanan agar PBS dapat mengakses iSCSI Target OMV.

B. Discovery Target

```
iscsiadm -m discovery -t sendtargets -p 192.168.200.2
```

Fungsi: mencari iSCSI Target yang tersedia di OMV.

Contoh hasil:

```
192.168.200.2:3260,1 iqn.2026-11.openmediavault:pbs-lun
```

C. Login ke Target

Gunakan IQN persis dari hasil discovery:

```
iscsiadm -m node -T IQN_HASIL_DISCOVERY -p 192.168.200.2 --login
```

Fungsi: menghubungkan PBS ke LUN OMV.

D. Aktifkan koneksi otomatis

```
iscsiadm -m node -T IQN_HASIL_DISCOVERY -p 192.168.200.2 --op update -n node.startup -v automatic
```

Fungsi: mengatur agar PBS otomatis login ke target saat startup.

## 8. Identifikasi disk iSCSI

Jalankan:

```
lsblk -o NAME,SIZE,TYPE,TRAN,FSTYPE,MOUNTPOINTS
```

Contoh:

```
sda     15G
sdb    200G
└─sdb1 200G xfs

sdc    200G disk iscsi
```

Penting: Jangan mengandalkan nama `/dev/sdc`. Pastikan disk baru memiliki `TRAN=iscsi` dan kapasitas sekitar 200 GB. Disk Synology PBS yang sudah ada juga jangan disentuh.

Cek path permanen:

```
ls -l /dev/disk/by-path/ | grep iscsi
```

Catat nama symbolic link yang mengarah ke disk iSCSI.

## 9. Membuat filesystem EXT4

CLI — PBS Shell

Gunakan path iSCSI dari hasil langkah 8, bukan menebak nama disk.

Contoh jika path-nya `/dev/disk/by-path/ip-...`:

```
mkfs.ext4 -L PBS_OMV_ISCSI /dev/disk/by-path/PATH_ISCSI
```

Fungsi: membuat filesystem EXT4 pada LUN agar bisa digunakan PBS.

Kemudian cek UUID:

```
blkid /dev/disk/by-path/PATH_ISCSI
```

Catat UUID yang muncul.

## 10. Mount storage di PBS

Buat folder:

```
mkdir -p /mnt/datastore/omv-pbs
```

Edit konfigurasi mount:

```
nano /etc/fstab
```

Tambahkan:

```
UUID=UUID_HASIL_BLKID /mnt/datastore/omv-pbs ext4 defaults,_netdev,nofail 0 2
```

Ganti `UUID_HASIL_BLKID` dengan UUID filesystem sebenarnya.

Simpan:

- `Ctrl + O` → Enter
- `Ctrl + X`

Aktifkan mount:

```
systemctl enable iscsid open-iscsi
mount -a
```

Verifikasi:

```
findmnt --mountpoint /mnt/datastore/omv-pbs
df -hT /mnt/datastore/omv-pbs
```

Hasil yang diharapkan:

- Filesystem: `ext4`
- Source: disk iSCSI OMV
- Mount point: `/mnt/datastore/omv-pbs`
- Kapasitas: sekitar 200 GB nominal.

Jangan mount filesystem LUN ini di OMV. Hanya PBS yang boleh mount filesystem tersebut.

## 11. Add Datastore di PBS GUI

GUI — PBS Web Interface

Buka PBS melalui browser:

```
https://192.168.200.4:8007
```

Klik:

Datastore → Add Datastore

Isi:

| Field          | Nilai                    |
| -------------- | ------------------------ |
| Name           | `omv-pbs`                |
| Datastore Type | `Local`                  |
| Backing Path   | `/mnt/datastore/omv-pbs` |
| GC Schedule    | `daily`                  |
| Prune Schedule | `daily`                  |

Klik Add.

Selesai. Datastore akan muncul di sidebar PBS.

![Proxmox Backup Server | Storagecenter Documentation](https://images.openai.com/static-rsc-4/vCoG43BXwrVAhJzZmh04lbMMFZVaOq184caYXS0g5t5o5YZrwc-tB3V_nyZxoDWN51Aja5gbs2_D-v3ebH0tKaT-qXMjQmG9YZ22_VwRD5SsDxYn7kmLbuCH_5culsQ0kqP-4rG323_kKQ8WD4M3LkjSlk9NvV_wAZhTl8Q4JIw?purpose=inline)

[wiki.storagecenter.ch](https://wiki.storagecenter.ch/de/applications/Proxmox-Backup-Server?utm_source=chatgpt.com)

## 12. Verifikasi akhir

Di PBS GUI buka:

Datastore → omv-pbs → Summary

Pastikan kapasitas menunjukkan sekitar 200 GB nominal, bukan 13 GB.

| Komponen        | Kondisi akhir       |
| --------------- | ------------------- |
| OMV `/dev/sdb1` | LVM Physical Volume |
| Volume Group    | `vg_pbs`            |
| Logical Volume  | `lun_pbs`           |
| TGT Target      | `pbs-lun`           |
| PBS Initiator   | Connected           |
| Filesystem      | EXT4                |
| Datastore       | `omv-pbs`           |
| Status          | Siap digunakan      |

Catatan: Datastore PBS menggunakan filesystem standar seperti EXT4, XFS, atau ZFS. Kapasitas yang terlihat di PBS sedikit lebih kecil dari kapasitas nominal akibat perbedaan satuan dan ruang filesystem.
