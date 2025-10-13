# lsblk -o NAME,SIZE,MODEL,MOUNTPOINT tapi masih ada ceph

## output `lvdisplay` **udah bersih dari volume Ceph**, tinggal ada VG `pve` doang (buat OS di `/dev/sdb`).

Tapi karena di `lsblk` masih muncul:

```
└─ceph--6f4118ee--4249--4299--8bfd--452ecdd6fd92-osd--block--88f58fd2--5b52--45dd--89d4--e65cbf397060
```

berarti **LVM-nya belum dihapus dari metadata kernel**, alias masih “nyangkut” di device mapper walau udah gak aktif di LVM database.

---

### 💣 Langsung bersihin sisa mapping-nya

1️⃣ Lihat device mapper aktif:

```bash
dmsetup ls
```

2️⃣ Kalau muncul entri `ceph--6f4118ee--...`, hapus manual:

```bash
dmsetup remove /dev/mapper/ceph--6f4118ee--4249--4299--8bfd--452ecdd6fd92-osd--block--88f58fd2--5b52--45dd--89d4--e65cbf397060
```

> Kadang nama panjang, jadi biar aman:
>
> ```
> dmsetup remove $(dmsetup ls | grep ceph | awk '{print $1}')
> ```

---

3️⃣ Lalu hapus volume group sisa (kalau masih ke-detect):

```bash
vgremove -f ceph--6f4118ee--4249--4299--8bfd--452ecdd6fd92 || true
```

4️⃣ Bersihin partisi dan signature:

```bash
pvremove -ff /dev/sda
sgdisk --zap-all /dev/sda
wipefs -a /dev/sda
dd if=/dev/zero of=/dev/sda bs=1M count=10
partprobe /dev/sda
```

---

5️⃣ Terakhir, cek ulang:

```bash
lsblk -o NAME,SIZE,MODEL,MOUNTPOINT
```

Kalau udah sukses, hasilnya harus tinggal:

```
sda   223.6G  ADATA SU650
```

(gak ada indentasi LV lagi di bawahnya)

---

Kalau **masih nongol** meski udah dihapus semua di atas, kirim hasil:

```bash
dmsetup ls
blkid
pvs
vgs
```

Biar gue tunjuk bagian mana yang masih nyangkut di metadata kernel.
