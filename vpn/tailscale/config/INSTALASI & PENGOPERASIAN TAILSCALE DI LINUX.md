panduan resmi instalasi Tailscale di Linux sesuai teks di atas secara ringkas dan praktis:

---

# INSTALASI & PENGOPERASIAN TAILSCALE DI LINUX

## 1. Instalasi Utama (Distribusi Mainstream seperti Ubuntu/Debian)

Jalankan perintah otomatis berikut di terminal:

```bash
curl -fsSL https://tailscale.com/install.sh | sh

```

## 2. Menjalankan Klien Tailscale

Setelah instalasi selesai, aktifkan klien dengan perintah:

```bash
sudo tailscale up

```

* Buka URL yang muncul di terminal untuk autentikasi ke jaringan Tailnet Anda.
* Periksa halaman *Machines* di admin console untuk memastikan perangkat sudah terhubung.

## 3. Verifikasi Instalasi

* **Melihat Alamat IP (IPv4 & IPv6):**
```bash
tailscale ip

```


* **Mengecek Status Koneksi:**
```bash
tailscale status

```



## 4. Fitur Tambahan & Troubleshooting

* **Mengaktifkan Tailscale SSH:**
```bash
tailscale set --ssh

```


* **Mengatasi Modul Kernel `tun` yang Hilang:**
Jika terjadi error terkait modul `tun`, muat secara manual dengan:
```bash
sudo modprobe tun

```
