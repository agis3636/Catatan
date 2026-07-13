# Konfigurasi Dasar FortiGate VM

### Langkah 1: Konfigurasi Interface (IP Address)

**Tujuan:** Memberikan identitas jaringan pada *port*. Setiap *port* butuh akses (`allowaccess`) agar Anda bisa me-*remote* perangkat (Web/SSH/Ping).

* **Mode DHCP (Contoh untuk WAN/Port1):**
Agar port mendapatkan IP otomatis dari modem/router ISP.
```bash
config system interface
    edit "port1"
        set mode dhcp
        set allowaccess ping https ssh http fgfm
    next
end

```


* **Mode Static (Contoh untuk LAN/Port2):**
Agar port menjadi *Gateway* tetap untuk komputer di sisi LAN.
```bash
config system interface
    edit "port2"
        set mode static
        set ip 192.168.10.1 255.255.255.0
        set allowaccess ping https ssh
    next
end

```



---

### Langkah 2: Setup DNS & Default Gateway

**Tujuan:** Memastikan FortiGate bisa menerjemahkan domain (DNS) dan tahu jalan keluar menuju internet (Gateway).

* **Setting DNS:** (Penting untuk update database & lisensi).
```bash
config system dns
    set primary 8.8.8.8
    set secondary 1.1.1.1
end

```


* **Menambah Static Route:** (Penting agar trafik dari LAN bisa keluar ke Internet).
```bash
config router static
    edit 1
        set gateway 192.168.1.1    # Ganti dengan IP Gateway router/ISP Anda
        set device "port1"         # Interface WAN
    next
end

```



---

### Langkah 3: Verifikasi & Cek Hardware

**Tujuan:** Memastikan konfigurasi sudah "nempel" dan perangkat terbaca dengan benar.

* **Cek IP yang didapat:**
```bash
diagnose ip address list

```


* **Cek status fisik interface & MAC Address:**
```bash
get system interface physical

```


* **Cek tabel routing:**
```bash
get router info routing-table all

```


* **Cek MAC Address mendalam (opsional):**
```bash
diagnose hardware deviceinfo nic port1

```



---

### Langkah 4: Manajemen Konfigurasi (Penting!)

FortiGate tidak memiliki perintah `write memory`.

* **Cara Simpan:** Begitu Anda mengetik `end` setelah masuk ke mode `config`, perubahan tersebut **otomatis tersimpan permanen**.
* **Peringatan:** Karena sifatnya *auto-save*, selalu periksa perintah dengan `show` sebelum menekan `end` untuk memastikan tidak ada kesalahan ketik.

---

### Langkah 5: Troubleshooting Dasar

Jika internet tidak jalan atau lisensi gagal, lakukan *debugging* dengan urutan ini:

1. **Test Koneksi:**
```bash
execute ping 8.8.8.8

```


* Jika gagal: Periksa kabel/link di EVE-NG dan pastikan *Gateway* sudah benar.
* Jika sukses: Lanjut ke tes domain.


2. **Test DNS:**
```bash
execute ping google.com

```


* Jika gagal: Periksa konfigurasi `config system dns`.


3. **Bersihkan Layar:**
Jika terminal sudah penuh dengan log atau perintah lama, cukup ketik:
```bash
execute clear-screen

```

