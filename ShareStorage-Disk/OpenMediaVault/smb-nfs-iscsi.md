## OpenMediaVault + Proxmox VE

**Implementasi SMB/CIFS, NFS, dan iSCSI**

### 1. TOPOLOGI LAB

**Perangkat dan IP Address**

| Perangkat      | IP Address      | Fungsi                      |
| -------------- | --------------- | --------------------------- |
| OpenMediaVault | `192.168.200.2` | NAS Server                  |
| Proxmox VE     | `192.168.200.1` | Hypervisor / Storage Client |
| Windows 11     | `192.168.2.57`  | SMB dan iSCSI Client        |

**Spesifikasi Storage OpenMediaVault**

| Disk        | Kapasitas | Fungsi             |
| ----------- | --------- | ------------------ |
| `/dev/sda`  | 20 GB     | Sistem operasi OMV |
| `/dev/sdb`  | 150 GB    | Storage data NAS   |
| `/dev/sdb1` | 50 GB     | SMB/CIFS           |
| `/dev/sdb2` | 50 GB     | NFS                |
| `/dev/sdb3` | 50 GB     | iSCSI              |

**Arsitektur storage:**

```
OpenMediaVault (192.168.200.2)
│
├── /dev/sdb1 (50 GB)
│   └── SMB_DATA
│       └── SHARE_SMB
│
├── /dev/sdb2 (50 GB)
│   └── NFS_DATA
│       └── SHARE_NFS
│
└── /dev/sdb3 (50 GB)
    └── iSCSI LUN
        └── win11-lun
            └── Proxmox LVM
```

Catatan: `/dev/sda` merupakan disk sistem OMV. Seluruh konfigurasi storage data dilakukan pada `/dev/sdb`.

---

# 2. PERSIAPAN STORAGE

### A. Menambahkan Disk di Proxmox

1. Buka Proxmox VE.
2. Pilih VM OpenMediaVault.
3. Buka tab **Hardware**.
4. Pilih **Add → Hard Disk**.
5. Tentukan kapasitas disk sebesar 150 GB.
6. Klik Add.

Disk tambahan akan dideteksi oleh OMV sebagai `/dev/sdb`.

### B. Verifikasi Disk

Jalankan di terminal OMV:

```
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

Fungsi:

- `lsblk`: menampilkan perangkat block storage.
- `SIZE`: melihat kapasitas disk.
- `TYPE`: membedakan disk dan partisi.
- `FSTYPE`: melihat jenis filesystem.
- `MOUNTPOINTS`: melihat lokasi disk dipasang.

Pastikan `/dev/sdb` memiliki kapasitas 150 GB.

### C. Membuat Tabel Partisi GPT

Jalankan:

```
parted -s /dev/sdb mklabel gpt
```

Fungsi: membuat tabel partisi GPT pada disk `/dev/sdb`.

Buat tiga partisi:

```
parted -s /dev/sdb mkpart primary ext4 1MiB 50GiB
parted -s /dev/sdb mkpart primary ext4 50GiB 100GiB
parted -s /dev/sdb mkpart primary 100GiB 100%
```

Penjelasan:

| Perintah         | Fungsi                                 |
| ---------------- | -------------------------------------- |
| `mkpart` pertama | Membuat partisi SMB sebesar 50 GiB     |
| `mkpart` kedua   | Membuat partisi NFS sebesar 50 GiB     |
| `mkpart` ketiga  | Menggunakan sisa kapasitas untuk iSCSI |

Perbarui informasi partisi:

```
partprobe /dev/sdb
udevadm settle
```

Verifikasi:

```
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

Hasil yang diharapkan:

```
sdb     150G disk
├─sdb1   50G part
├─sdb2   50G part
└─sdb3   50G part
```

---

# 3. KONFIGURASI SMB/CIFS

**Tujuan:** Membagikan folder dari OpenMediaVault agar dapat diakses Windows melalui jaringan.

Analogi: SMB seperti memberikan akses kepada beberapa orang untuk menggunakan satu lemari bersama. Setiap orang bisa mendapatkan hak akses berbeda.

### A. Membuat Filesystem

Format partisi pertama:

```
mkfs.ext4 -L SMB_DATA /dev/sdb1
```

Penjelasan:

- `mkfs.ext4`: membuat filesystem EXT4.
- `-L SMB_DATA`: memberikan label SMB_DATA.
- `/dev/sdb1`: partisi yang diformat.

Catatan: perintah ini menghapus data pada partisi tujuan.

### B. Mount Filesystem melalui GUI OMV

1. Buka dashboard OMV.
2. Masuk **Storage → File Systems**.
3. Klik Refresh.
4. Pilih `/dev/sdb1` dengan label `SMB_DATA`.
5. Klik Mount.
6. Usage Warning Threshold: 85%.
7. Klik Save → Apply.

Fungsi Mount: menghubungkan filesystem ke direktori agar dapat digunakan oleh sistem operasi.

### C. Membuat Shared Folder

Masuk ke:

**Storage → Shared Folders → Create**

Isi:

| Kolom         | Nilai                                                           |
| ------------- | --------------------------------------------------------------- |
| Name          | `SHARE_SMB`                                                     |
| File system   | `SMB_DATA`                                                      |
| Relative path | `SHARE_SMB/`                                                    |
| Permissions   | Administrator: Read/Write, Users: Read/Write, Others: Read-only |

Klik Save → Apply.

Hasil:

```
/dev/sdb1
└── SHARE_SMB/
```

### D. Membuat User SMB

Masuk:

**Users → Users → Create**

Contoh:

| Kolom            | Nilai            |
| ---------------- | ---------------- |
| Name             | `smbuser`        |
| Email            | Kosong           |
| Password         | Password sendiri |
| Confirm Password | Ulangi password  |
| Groups           | Default          |
| SSH Public Keys  | Kosong           |

Klik Save → Apply.

Buat user lainnya dengan cara yang sama, misalnya `smbamah`.

### E. Mengatur Hak Akses User

Masuk:

**Storage → Shared Folders**

1. Pilih `SHARE_SMB`.
2. Klik Permissions.
3. Atur hak akses:

| User      | Permission |
| --------- | ---------- |
| `smbuser` | Read/Write |
| `smbamah` | Read-only  |

Klik Save → Apply.

Fungsi permission: menentukan apakah user boleh membaca, menulis, atau tidak mengakses folder melalui layanan sharing.

### F. Mengaktifkan SMB/CIFS

Masuk:

**Services → SMB/CIFS → Settings**

Atur:

| Pengaturan       | Nilai       |
| ---------------- | ----------- |
| Enabled          | ON          |
| Workgroup        | `WORKGROUP` |
| Browseable       | ON          |
| Minimum Protocol | `SMB2`      |

Klik Save → Apply.

### G. Mendaftarkan Shared Folder ke SMB

Masuk:

**Services → SMB/CIFS → Shares**

Klik `+`, kemudian isi:

| Pengaturan    | Nilai       |
| ------------- | ----------- |
| Shared Folder | `SHARE_SMB` |
| Enabled       | ON          |
| Public        | No          |
| Read only     | OFF         |
| Browseable    | ON          |

Klik Save → Apply.

Penjelasan:

- Public No: client harus login menggunakan user.
- Read only OFF: share mengizinkan operasi tulis.
- Browseable ON: folder terlihat saat browsing jaringan.

Akses user tetap mengikuti permission masing-masing.

### H. Mengakses SMB dari Windows 11

Tekan `Win + R`, masukkan:

```
\\192.168.200.2\SHARE_SMB
```

Login menggunakan:

```
Username : smbuser
Password : Password SMB
```

Tes:

1. Buat folder.
2. Buat file TXT.
3. Ubah isi file.
4. Hapus file.

Ulangi menggunakan user `smbamah` untuk memastikan akses Read-only.

**Hasil pengujian:**

- `smbuser`: bisa membaca dan mengubah data.
- `smbamah`: bisa membaca, tetapi tidak bisa membuat atau mengubah data.

### I. Mengganti User SMB di Windows

Windows menyimpan sesi koneksi SMB selama koneksi masih aktif.

Untuk melihat sesi:

Buka PowerShell:

```
Get-SmbConnection -ServerName 192.168.200.2
```

Untuk memutus sesi SMB client Windows:

Jalankan PowerShell sebagai Administrator:

```
Restart-Service LanmanWorkstation -Force
```

Perintah ini akan memutus koneksi SMB Windows secara keseluruhan, bukan hanya ke OMV.

Buka kembali:

```
\\192.168.200.2\SHARE_SMB
```

Windows akan meminta autentikasi kembali.

---

# 4. KONFIGURASI NFS

**Tujuan:** Membagikan storage OpenMediaVault ke Proxmox melalui protokol NFS.

Analogi: NFS seperti memberikan akses ke direktori tertentu di server kepada komputer lain. Client melakukan mount dan menggunakan direktori tersebut seolah-olah berada di filesystem lokal.

### A. Membuat Filesystem

Format partisi kedua:

```
mkfs.ext4 -L NFS_DATA /dev/sdb2
```

Penjelasan:

- `mkfs.ext4`: membuat filesystem EXT4.
- `-L NFS_DATA`: memberikan label NFS_DATA.
- `/dev/sdb2`: partisi untuk NFS.

### B. Mount Filesystem

Masuk ke:

**Storage → File Systems**

1. Klik Refresh.
2. Pilih `/dev/sdb2` dengan label `NFS_DATA`.
3. Klik Mount.
4. Usage Warning Threshold: 85%.
5. Klik Save → Apply.

### C. Membuat Shared Folder

Masuk:

**Storage → Shared Folders → Create**

Isi:

| Kolom         | Nilai               |
| ------------- | ------------------- |
| Name          | `SHARE_NFS`         |
| File system   | `NFS_DATA`          |
| Relative path | `SHARE_NFS/`        |
| Permissions   | Sesuaikan kebutuhan |

Klik Save → Apply.

Hasil:

```
/dev/sdb2
└── SHARE_NFS/
```

### D. Mengaktifkan NFS

Masuk:

**Services → NFS → Settings**

Centang Enabled, lalu Save → Apply.

### E. Membuat NFS Share

Masuk:

**Services → NFS → Shares**

Klik `+`, isi:

| Kolom         | Nilai            |
| ------------- | ---------------- |
| Shared Folder | `SHARE_NFS`      |
| Client        | `192.168.200.1`  |
| Privilege     | Read/Write       |
| Extra Options | `no_root_squash` |

Klik Save → Apply.

Konfigurasi export yang berhasil:

```
/export/SHARE_NFS
192.168.200.1
```

Opsi yang digunakan:

```
insecure, no_root_squash, rw, subtree_check
```

Penjelasan:

- `rw`: mengizinkan baca dan tulis.
- `no_root_squash`: mempertahankan hak root dari client yang diizinkan.
- `insecure`: mengizinkan koneksi dari port sumber non-reserved.
- `subtree_check`: melakukan pemeriksaan subdirektori export.

Penting: `no_root_squash` memberikan hak root dari client ke filesystem server. Batasi client hanya ke IP Proxmox yang dipercaya.

### F. Verifikasi Export NFS

Jalankan di OMV:

```
exportfs -v
```

Fungsi: menampilkan direktori yang sedang diekspor oleh NFS beserta opsi aksesnya.

### G. Scan NFS dari Proxmox

Buka:

**Proxmox → Node pve → Shell**

Jalankan:

```
pvesm scan nfs 192.168.200.2
```

Fungsi: meminta daftar export NFS yang tersedia dari server OMV.

Hasil:

```
/export           192.168.200.1
/export/SHARE_NFS 192.168.200.1
```

Pilih `/export/SHARE_NFS`.

### H. Add NFS Storage di Proxmox

Masuk:

**Datacenter → Storage → Add → NFS**

Isi:

| Kolom         | Nilai                 |
| ------------- | --------------------- |
| ID            | `nfs-omv`             |
| Server        | `192.168.200.2`       |
| Export        | `/export/SHARE_NFS`   |
| Content       | Disk image, Container |
| Nodes         | `pve`                 |
| Enable        | ON                    |
| NFS Version   | Default               |
| Preallocation | Default               |

Klik Add.

**Hasil akhir:**\
Storage `nfs-omv` muncul di Proxmox dengan status Active: Yes dan dapat digunakan untuk menyimpan disk VM maupun Container.

---

# 5. KONFIGURASI iSCSI + LVM

**Tujuan:** Menyediakan block storage melalui jaringan agar client dapat mengenalinya sebagai disk.

Analogi: Berbeda dari SMB/NFS yang membagikan folder, iSCSI memberikan sebuah disk virtual kepada client. Client kemudian mengatur partisi dan filesystem sendiri.

Pada praktik ini, disk 50 GB dari OMV awalnya diberikan ke Windows 11 sebagai drive E:, kemudian dilepas dan dialihkan ke Proxmox.

### A. Menyiapkan Partisi iSCSI

Partisi ketiga:

```
/dev/sdb3
50 GB
Raw Block Device
```

Jangan menjalankan:

```
mkfs.ext4 /dev/sdb3
```

Jangan mount partisi ini.

Alasannya: iSCSI menggunakan partisi tersebut secara langsung sebagai backing device LUN, bukan sebagai filesystem biasa.

### B. Install Plugin TGT

Jika OMV-Extras belum tersedia, repository OMV-Extras perlu dipasang terlebih dahulu.

Di OMV:

1. Buka System → Plugins.
2. Cari `openmediavault-tgt`.
3. Pilih plugin versi 8.x.
4. Klik Install.
5. Tunggu sampai status Installed.

Plugin TGT digunakan untuk mengelola iSCSI Target melalui GUI OMV.

### C. Mengaktifkan TGT

Masuk:

**Services → TGT → Settings**

Centang:

```
Enable
```

Klik Save jika ada perubahan, kemudian Apply.

### D. Membuat iSCSI Target

Masuk:

**Services → TGT → Targets**

Klik `+`.

Isi:

| Kolom             | Nilai                    |
| ----------------- | ------------------------ |
| Enabled           | ON                       |
| Name              | `win11-lun`              |
| Backing Store     | `/dev/sdb3`              |
| Initiator Address | IP client yang diizinkan |

Saat pengujian Windows, Initiator Address awalnya `192.168.2.57`. Setelah LUN dipindahkan ke Proxmox, ubah menjadi `192.168.200.1`.

OMV akan menghasilkan IQN target secara otomatis:

```
iqn.2026-11.openmediavault:win11-lun
```

Klik Save → Apply.

### E. Pengujian iSCSI dari Windows 11

Sebelum target dialihkan ke Proxmox, LUN diuji terlebih dahulu dari Windows.

Tes jaringan:

```
Test-NetConnection 192.168.200.2 -Port 3260
```

Fungsi:

- Menguji konektivitas TCP ke server.
- Port 3260 adalah port standar iSCSI.

Hasil yang berhasil:

```
TcpTestSucceeded : True
```

Buka iSCSI Initiator:

Tekan `Win + R`:

```
iscsicpl.exe
```

Langkah GUI:

1. Klik Yes saat Windows meminta mengaktifkan Microsoft iSCSI Service.
2. Buka tab Discovery.
3. Klik Discover Portal.
4. Isi IP `192.168.200.2`.
5. Port `3260`.
6. Klik OK.
7. Buka tab Targets.
8. Pilih IQN `win11-lun`.
9. Klik Connect.
10. Tambahkan ke Favorite Targets bila ingin koneksi otomatis saat Windows restart.

### F. Menjadikan LUN sebagai Drive E:

Buka:

```
diskmgmt.msc
```

Langkah:

1. Cari Disk 1 dengan kapasitas sekitar 50 GB.
2. Initialize Disk.
3. Pilih GPT.
4. Klik kanan Unallocated.
5. Pilih New Simple Volume.
6. Assign drive letter `E:`.
7. Format NTFS.
8. Volume label: `ISCSI_DATA`.

Hasil pengujian:

Windows 11 mendapatkan drive E: dengan kapasitas sekitar 50 GB.

### G. Melepaskan iSCSI dari Windows

Sebelum LUN dialihkan ke Proxmox:

1. Tutup aplikasi yang menggunakan drive E:.
2. Buka Disk Management.
3. Klik kanan Disk 1.
4. Pilih Offline.
5. Buka `iscsicpl.exe`.
6. Pilih tab Targets.
7. Pilih `win11-lun`.
8. Klik Disconnect.
9. Periksa Favorite Targets dan hapus target jika tidak ingin Windows tersambung kembali otomatis.

Jangan menghubungkan satu LUN NTFS secara bersamaan ke beberapa host yang melakukan akses tulis biasa.

### H. Alihkan Initiator ke Proxmox

Kembali ke OMV:

**Services → TGT → Targets**

Edit target `win11-lun`.

Ubah:

```
Initiator lama : 192.168.2.57
Initiator baru : 192.168.200.1
```

Backing Store tetap:

```
/dev/sdb3
```

Klik Save → Apply.

### I. Scan iSCSI dari Proxmox

Buka:

**Proxmox → Node pve → Shell**

Jalankan:

```
pvesm scan iscsi 192.168.200.2
```

Hasil:

```
iqn.2026-11.openmediavault:win11-lun 192.168.200.2:3260
```

Artinya Proxmox sudah dapat menemukan target iSCSI dari OMV.

### J. Menambahkan iSCSI Storage di Proxmox

Masuk:

**Datacenter → Storage → Add → iSCSI**

Isi:

| Kolom             | Nilai                                  |
| ----------------- | -------------------------------------- |
| ID                | `iscsi-omv`                            |
| Portal            | `192.168.200.2`                        |
| Target            | `iqn.2026-11.openmediavault:win11-lun` |
| Nodes             | `pve`                                  |
| Enable            | ON                                     |
| Use LUNs directly | OFF                                    |

Klik Add.

Penjelasan:

`iscsi-omv` adalah koneksi dasar menuju LUN. Storage iSCSI standar ini sendiri tidak berfungsi seperti NFS yang menyediakan direktori untuk banyak jenis file.

Untuk mengelola kapasitas LUN menjadi beberapa volume disk VM, gunakan LVM di atasnya.

### K. Membuat LVM di Atas iSCSI

Masuk:

**Datacenter → Storage → Add → LVM**

Isi:

| Kolom                           | Nilai                 |
| ------------------------------- | --------------------- |
| ID                              | `lvm-iscsi-omv`       |
| Base Storage                    | `iscsi-omv`           |
| Base Volume                     | `CH 00 ID 0 LUN 1`    |
| Volume Group                    | `vg_iscsi_omv`        |
| Content                         | Disk image, Container |
| Nodes                           | `pve`                 |
| Enable                          | ON                    |
| Shared                          | OFF                   |
| Wipe Removed Volumes            | OFF                   |
| Allow Snapshots as Volume-Chain | OFF                   |

Klik Add.

Penjelasan:

- Base Storage: koneksi iSCSI yang sudah dibuat.
- Base Volume: LUN fisik yang disediakan oleh OMV.
- Volume Group: wadah LVM untuk mengelola alokasi disk.
- Disk image: untuk disk VM.
- Container: untuk root filesystem LXC.

**Catatan penting:** ketika Windows sebelumnya membuat GPT dan NTFS pada LUN, Proxmox tidak dapat langsung membuat Physical Volume LVM karena LUN masih memiliki tabel partisi lama.

Setelah Windows diputus dan LUN dipastikan tidak digunakan, signature GPT lama dibersihkan dari Proxmox.

Identifikasi device:

```
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

Pastikan LUN 50 GB terlihat sebagai `/dev/sdb`.

Periksa signature melalui ID stabil yang diperoleh dari error Proxmox:

```
wipefs -n /dev/disk/by-id/scsi-360000000000000000e00000000010001
```

Fungsi `-n`: melihat signature tanpa menghapusnya.

Setelah dipastikan bahwa device tersebut benar-benar LUN iSCSI 50 GB dan tidak sedang digunakan, bersihkan signature GPT lama:

```
wipefs -a /dev/disk/by-id/scsi-360000000000000000e00000000010001
```

Verifikasi:

```
wipefs -n /dev/disk/by-id/scsi-360000000000000000e00000000010001
```

Jika tidak menampilkan signature, LUN siap digunakan untuk inisialisasi LVM.

**PERINGATAN:** Perintah `wipefs -a` menghapus signature filesystem dan tabel partisi. Data NTFS Windows sebelumnya tidak lagi dapat digunakan. Jangan pernah menjalankannya pada disk sistem atau LUN yang masih digunakan host lain.

Hasil akhir yang berhasil:

```
iscsi-omv
    └── LUN 50 GB

lvm-iscsi-omv
    └── vg_iscsi_omv
        └── VM Disk / Container
```

Storage `lvm-iscsi-omv` akhirnya berstatus Active: Yes dengan kapasitas sekitar 53,68 GiB.

---

# 6. HASIL AKHIR LAB

| Teknologi   | Partisi     | Storage OMV | Proxmox / Client        | Status   |
| ----------- | ----------- | ----------- | ----------------------- | -------- |
| SMB/CIFS    | `/dev/sdb1` | `SMB_DATA`  | Windows 11              | Berhasil |
| NFS         | `/dev/sdb2` | `NFS_DATA`  | Proxmox `nfs-omv`       | Berhasil |
| iSCSI + LVM | `/dev/sdb3` | `win11-lun` | Proxmox `lvm-iscsi-omv` | Berhasil |

### Perbedaan Ketiga Teknologi

| Aspek          | SMB/CIFS                    | NFS                 | iSCSI                   |
| -------------- | --------------------------- | ------------------- | ----------------------- |
| Jenis          | File-level                  | File-level          | Block-level             |
| Yang dibagikan | Folder/file                 | Direktori/file      | Disk/LUN                |
| Client melihat | Network folder              | Mounted directory   | Disk baru               |
| Client umum    | Windows                     | Linux/Proxmox       | Windows, Linux, Proxmox |
| Contoh         | `\\192.168.200.2\SHARE_SMB` | `/export/SHARE_NFS` | LUN 50 GB               |

**Analogi:**

- SMB: membagikan folder kepada orang lain.
- NFS: memberikan akses mount ke direktori server.
- iSCSI: meminjamkan sebuah hard disk virtual kepada client.

### Catatan Operasional

1. Jangan menggunakan `/dev/sda` sebagai disk data karena merupakan disk sistem OMV.
2. Jangan memformat atau me-mount `/dev/sdb3` setelah digunakan sebagai iSCSI backing device.
3. Jangan memberikan akses tulis LUN yang sama ke beberapa Windows biasa secara bersamaan.
4. Batasi export NFS `no_root_squash` dan iSCSI Initiator hanya untuk client yang dipercaya.
5. `iscsi-omv` dan `lvm-iscsi-omv` menggunakan LUN fisik yang sama, bukan dua kapasitas terpisah.
6. Backup tetap diperlukan. RAID, NFS, dan iSCSI bukan pengganti backup.

---

# 7. REFERENSI DOKUMENTASI

- OpenMediaVault 8 – [Shared Folders](https://docs.openmediavault.org/en/stable/administration/storage/sharedfolders.html)
- OpenMediaVault 8 – [Samba](https://docs.openmediavault.org/en/latest/administration/services/samba.html)
- OpenMediaVault 8 – [NFS](https://docs.openmediavault.org/en/stable/administration/services/nfs.html)
- OpenMediaVault TGT Plugin – [GitHub](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-tgt)
- Proxmox VE – [Administration Guide](https://pve.proxmox.com/pve-docs/)
- Microsoft – [iSCSI Initiator](https://learn.microsoft.com/en-us/windows-server/storage/iscsi/iscsi-target-server)
