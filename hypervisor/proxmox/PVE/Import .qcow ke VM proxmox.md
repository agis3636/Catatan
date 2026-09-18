Oke, ini cara **paling cepat dan pasti berhasil** lewat Command Line (Shell), tanpa pusing mikirin tombol Import yang mati.

Asumsi: Kita akan buat VM baru dengan ID **105** (ganti angkanya kalau mau pakai ID lain).

### Langkah 1: Buat Wadah VM

1. Klik **Create VM** di pojok kanan atas.
2. Isi **VM ID**: `105` (atau angka lain).
3. Tab **OS**: Pilih `Do not use any media`.
4. Tab **Disks**: Hapus disk default (klik ikon tong sampah).
5. Selesaikan (Memory/CPU bebas) -> **Finish**.

### Langkah 2: Masukkan Disk (Lewat Shell)

Buka menu **>_ Shell** di node Proxmox Anda, lalu copy-paste perintah ini satu per satu:

**Cari lokasi file:**

```bash
find / -name "QuTScloud_c5.2.4.3041.qcow2"

```

*(Copy path/lokasi yang muncul dari hasil perintah di atas)*

**Import ke VM (Ganti `PATH_HASIL_CARI` dengan lokasi tadi):**

```bash
qm importdisk 105 PATH_HASIL_CARI local-lvm

```

*Tunggu sampai 100% (Successfully imported).*

### Langkah 3: Aktifkan Disk

1. Buka menu VM **105** -> **Hardware**.
2. Double click **Unused Disk 0** -> Klik **Add**.
3. Ke menu **Options** -> **Boot Order**.
4. Centang disk baru tersebut (`scsi0` atau `ide0`) dan geser ke **paling atas**.

**Selesai.** Tekan **Start**.
