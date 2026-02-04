Di Ubuntu 22.04 (Jammy Jellyfish), pengaturan IP tidak lagi menggunakan `/etc/network/interfaces` (cara lama), melainkan menggunakan **Netplan**.

Berikut adalah cara setting IP Static yang **akurat** dan sesuai standar Ubuntu 22.04 agar tidak muncul *warning* (terutama terkait gateway).

### Langkah 1: Cek Nama Interface Network

Sebelum mengedit, Anda harus tahu nama adapter jaringan Anda (misal: `ens18`, `eth0`, atau `enp3s0`).

1. Buka terminal/console.
2. Ketik:
```bash
ip link

```


3. Catat nama interface yang ingin disetting (Biasanya urutan ke-2, contoh: **`ens18`**).

---

### Langkah 2: Edit Konfigurasi Netplan

File konfigurasi ada di folder `/etc/netplan/`. Namanya bisa beda-beda, tapi biasanya berakhiran `.yaml`.

1. Cek nama filenya:
```bash
ls /etc/netplan/

```


*(Biasanya namanya `00-installer-config.yaml` atau `50-cloud-init.yaml`)*.
2. Edit file tersebut (ganti nama file sesuai hasil di atas):
```bash
sudo nano /etc/netplan/00-installer-config.yaml

```


3. **Hapus semua isinya** (atau sesuaikan), lalu ganti dengan format di bawah ini.
**PENTING:** YAML sangat sensitif terhadap spasi. **Gunakan Spasi (Space), JANGAN TAB.** Gunakan 2 spasi untuk setiap level indentasi.
**Template IP Static (Copy & Sesuaikan):**
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses: [192.168.100.65/24]
      routes: [{to: default, via: 192.168.100.1}]
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]

```


*Catatan: Di Ubuntu 22.04, penggunaan `gateway4` sudah dianggap "deprecated" (usang). Gunakan blok `routes` seperti di atas agar konfigurasi "clean" dan tahan lama.*
4. Simpan file: Tekan `Ctrl + X`, lalu `Y`, lalu `Enter`.

---

### Langkah 3: Terapkan Konfigurasi (Apply)

Sebelum menerapkan, disarankan cek dulu apakah ada error penulisan (syntax).

1. Cek konfigurasi (Trial):
```bash
sudo netplan try

```


*Jika tidak ada error, hitungan mundur akan muncul. Tekan `Enter` untuk menerima.*
2. Jika yakin sudah benar, langsung terapkan permanen:
```bash
sudo netplan apply

```



---

### Langkah 4: Verifikasi

Pastikan IP sudah berubah dan koneksi internet lancar.

1. Cek IP:
```bash
ip a

```


*(Pastikan IP `192.168.1.10` sudah muncul di `ens18`).*
2. Cek Gateway:
```bash
ip route

```


*(Pastikan ada baris `default via 192.168.1.1 ...`).*
3. Ping ke Google (Cek Internet & DNS):
```bash
ping google.com

```



### Troubleshooting (Jika Error)

* **YAML Error / Indentation Error:** Ini paling sering terjadi. Netplan melarang penggunaan tombol **TAB**. Anda harus mengetik spasi manual (biasanya 2 spasi per geseran masuk).
* **Permission Denied:** Pastikan menggunakan `sudo` saat mengedit atau apply.
* **IP Tidak Berubah:** Coba restart service network manual: `sudo systemctl restart systemd-networkd` atau reboot servernya `sudo reboot`.
