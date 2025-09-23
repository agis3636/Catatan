Cara Export Manual dari VMware Workstation / Player

1.	Matikan VM
Pastikan statusnya Powered Off, bukan suspend.

2.	Cari Folder VM
Biasanya ada di:
- Windows:
  ```bash
  Documents\Virtual Machines\<Nama VM>\
  ```

- Linux:
  ```bash
  ~/vmware/<Nama VM>/
  ```

Di dalam folder ini biasanya ada file seperti:
  ```bash
  NamaVM.vmx
  NamaVM.vmdk
  NamaVM.nvram
  ```

3.	Ambil File Disk (.vmdk)
Copy file `.vmdk` yang jadi harddisk utama VM. Kalau ada beberapa `.vmdk`, ambil yang ukurannya besar (itu disk utama, bukan descriptor kecil).

4.	Upload ke Proxmox
Gunakan WinSCP atau scp:
  ```bash
  scp NamaVM.vmdk root@IP-PROXMOX:/var/lib/vz/template/
  ```

5.	Convert ke Format Proxmox (QCOW2)
Di server Proxmox:
  ```bash
  cd /var/lib/vz/template/
  qemu-img convert -f vmdk -O qcow2 NamaVM.vmdk NamaVM.qcow2
  ```

6.	Buat VM Baru di Proxmox (tanpa disk)
- Gunakan Web UI → Create VM
- Pilih “Do not use any media”
- Jangan tambahkan Disks saat buat
- Finish → catat VM ID (misal 105)

7.	Import Disk ke Storage yang Benar
Gunakan perintah qm importdisk supaya Proxmox otomatis taruh di storage yang benar dan update VM:
  ```bash
  qm importdisk 105 Debian.qcow2 local-lvm
  ```

Setelah itu, edit VM (bisa lewat Web UI atau CLI):
  ```bash
  nano /etc/pve/qemu-server/105.conf
  ```

Tambahkan:
  ```bash
  scsi0: local-lvm:vm-105-disk-0
  ```

Lalu:
  ```bash
  qm start 105
  ```

Inilah langkah lengkap dari VMware Player langsung ke Proxmox pakai qm importdisk ini, jadi nggak perlu ribet pindah-pindah folder lagi. Itu akan bikin prosesnya lebih cepat dan aman.

