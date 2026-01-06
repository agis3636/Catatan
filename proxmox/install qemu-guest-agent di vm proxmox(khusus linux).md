**Linux itu "anak kandung"-nya VirtIO.**

Karena Proxmox itu berbasis Linux (KVM), maka driver VirtIO itu **sudah tertanam (built-in)** langsung di dalam kernel Linux.

Jadi bedanya begini:

### 1. Untuk VM Windows (Ribet)

Windows adalah sistem tertutup (Closed Source). Microsoft tidak menyertakan driver VirtIO (milik Linux/KVM) di dalam installer Windows.

* **Makanya:** Anda butuh ISO tambahan (`virtio-win.iso`) untuk menginstall drivernya secara manual agar Windows kenal hardware Proxmox.

### 2. Untuk VM Linux (Otomatis / Plug & Play)

Hampir semua Distro Linux modern (Ubuntu, Debian, CentOS, Rocky, Alma, dll) sudah membawa driver VirtIO di dalam "jantungnya" (Kernel).

* **Makanya:** Anda **TIDAK PERLU** install driver apa-apa.
* Saat membuat VM Linux di Proxmox, langsung saja pilih:
* **Disk:** `VirtIO Block` atau `VirtIO SCSI`.
* **Network:** `VirtIO (paravirtualized)`.


* Linux akan langsung mengenali, booting lancar, dan performanya langsung maksimal.

---

### Satu Hal yang Perlu Diinstall di Linux: "QEMU Guest Agent"

Walaupun *driver* (untuk disk/network) sudah otomatis ada, tapi ada satu fitur manajemen yang perlu diinstall manual, namanya **QEMU Guest Agent**.

Ini bukan driver hardware, tapi aplikasi kecil agar Proxmox bisa:

1. Melihat IP Address VM dari luar (Web GUI Proxmox).
2. Melakukan **Shutdown** atau **Reboot** dengan sopan dari tombol Proxmox (bukan cabut paksa).
3. Freeze filesystem saat backup (biar backup konsisten).

**Caranya gampang (setelah Linux nyala):**

* **Debian/Ubuntu:**
```bash
apt update
apt install qemu-guest-agent
systemctl start qemu-guest-agent

```


* **CentOS/RHEL:**
```bash
yum install qemu-guest-agent
systemctl start qemu-guest-agent

```









* **FreeBSD:**

**FreeBSD itu "mirip" Linux.** Kabar baiknya: Anda **TIDAK PERLU** install driver manual seperti di Windows.

FreeBSD (versi modern 10.x, 11, 12, 13, dst) sudah memiliki driver **VirtIO** yang tertanam di dalam Kernel-nya (GENERIC kernel).

**TAPI**, ada satu jebakan besar di FreeBSD saat migrasi dari ESXi ke Proxmox, yaitu **Perubahan Nama Interface**.

Berikut detail yang perlu Anda waspadai:

### 1. Masalah Network Interface (Paling Sering Kejadian)

Ini yang biasanya bikin panik "kok internet mati setelah pindah?".

* **Di ESXi:** Interface network Anda biasanya bernama `vmx0` (kalau pakai VMXNET3) atau `em0` (kalau pakai E1000).
* **Di Proxmox (VirtIO):** Interface-nya akan berubah nama menjadi **`vtnet0`**.

**Solusinya:**
Setelah VM FreeBSD nyala di Proxmox (tapi internet mati), Anda harus akses via Console/VNC, lalu edit konfigurasi:

1. Buka file config:
```bash
ee /etc/rc.conf

```


2. Cari baris yang mengatur IP, misal:
`ifconfig_vmx0="inet 192.168.1.50..."`
3. Ganti `vmx0` menjadi `vtnet0`:
`ifconfig_vtnet0="inet 192.168.1.50..."`
4. Simpan (Esc + Enter) dan Restart service netif atau reboot.

### 2. Masalah Hard Disk (Hati-hati `fstab`)

Ini tergantung setingan "Hard Disk" yang Anda pilih di Proxmox:

* **Opsi A: VirtIO Block (Bus/Device: VirtIO Block)**
Disk akan terdeteksi sebagai `/dev/vtbd0`.
* *Resiko:* Kalau di `/etc/fstab` Anda mounting pakai nama device (misal `/dev/da0s1a`), FreeBSD akan gagal booting (*mountroot error*) karena `da0` hilang.


* **Opsi B: VirtIO SCSI (Bus/Device: SCSI)** -> **SAYA REKOMENDASIKAN INI**
Disk akan tetap terdeteksi sebagai `/dev/da0` (SCSI disk).
* *Keuntungan:* Ini lebih aman buat migrasi karena FreeBSD di ESXi biasanya mendeteksi disk sebagai `da0` juga. Jadi `/etc/fstab` tidak perlu diubah.



### 3. Perlu QEMU Guest Agent juga?

Ya, sangat disarankan install ini agar Proxmox bisa shutdown FreeBSD dengan mulus.

Caranya (setelah internet nyala):

```bash
pkg update
pkg install qemu-guest-agent

# Aktifkan service-nya
sysrc qemu_guest_agent_enable="YES"
service qemu-guest-agent start

```

