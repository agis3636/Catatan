Masalah IP hilang sendiri setelah restart/shutdown di Ubuntu umumnya terjadi karena konfigurasi IP yang Anda lakukan bersifat sementara (menggunakan perintah ifconfig atau ip addr), atau konfigurasi Netplan yang tidak tersimpan dengan benar.

Di Ubuntu (18.04 ke atas), manajemen jaringan diatur oleh Netplan (YAML file di /etc/netplan/). 

Berikut adalah cara memperbaiki agar IP Address tetap permanen/tidak hilang:

# 1. Konfigurasi IP Tetap (Static) di Netplan 

Pastikan Anda mengatur IP statis melalui file konfigurasi YAML. 

1 Cari file konfigurasi netplan:

```bash
ls /etc/netplan/

```

Biasanya bernama 01-netcfg.yaml, 50-cloud-init.yaml, atau 00-installer-config.yaml.

2 Edit file tersebut (gunakan sudo):

```bash
sudo nano /etc/netplan/01-netcfg.yaml  # Sesuaikan dengan nama file hasil ls

```

3 Ubah atau tambahkan konfigurasi (Pastikan menggunakan spasi, bukan tab, dan formatnya menjorok dengan benar/YAML sintaks):

```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0: # SESUAIKAN DENGAN NAMA INTERFACE ANDA (cek pake 'ip a')
      dhcp4: no
      addresses: [192.168.1.100/24] # IP yg diinginkan
      gateway4: 192.168.1.1 # IP Router/Gateway
      nameservers:
        addresses:

```

4 Simpan dan Keluar (Ctrl+O, Enter, Ctrl+X).

5 Terapkan konfigurasi:

---

# 2. Matikan DHCP jika Sebelumnya Aktif

Jika file netplan masih tertulis dhcp4: yes atau tidak diatur ke no, IP akan berubah setiap kali restart. Pastikan dhcp4: no atau hapus baris dhcp jika sudah menentukan IP statis. 

# 3. Cek Network Manager (Jika menggunakan Ubuntu Desktop)

Jika Anda menggunakan Ubuntu Desktop, kadang Network Manager bentrok dengan Netplan.

- Cek /etc/netplan/01-network-manager-all.yaml.
- Jika Anda ingin mengatur IP melalui GUI, biarkan saja.
- ika ingin mengatur via terminal, ubah renderer menjadi networkd di file netplan. 

# 4. Alternatif: Buat File Disable Cloud-init

Jika Anda menggunakan server, cloud-init terkadang mereset jaringan.

```bash
echo "network: {config: disabled}" | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg

```

Setelah langkah-langkah di atas dilakukan, restart komputer Anda:

```bash
sudo reboot

```

Cek IP kembali dengan ip a.
