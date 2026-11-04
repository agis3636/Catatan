# INSTALASI OPENVPN ACCESS SERVER

---

## 1. Persiapan Infrastruktur & Sistem Operasi

* **Sistem Operasi**: Didukung pada distribusi Linux seperti Ubuntu, Debian, dan RedHat.
* **Arsitektur**: Mendukung arsitektur **x86_64** atau **ARM64**.

---

## 2. Langkah Instalasi (Option 1: Installation Script)

Metode paling mudah dan otomatis sesuai dokumentasi resmi adalah menggunakan skrip instalasi yang akan mendeteksi sistem operasi Anda secara otomatis.

1. Masuk ke terminal server Anda melalui SSH dengan hak akses root atau sudo.
2. Salin dan jalankan perintah skrip instalasi resmi berikut di terminal:
```bash
bash <(curl -fsS https://packages.openvpn.net/as/install.sh) --yes

```


3. Tunggu hingga proses instalasi selesai dan sistem menampilkan informasi akses (Admin UI URL, Client UI URL, serta kredensial *default*).

---

## 3. Informasi Lisensi & Harga

* **Ketentuan Penggunaan**: Gratis digunakan untuk maksimal **2 koneksi bersamaan** (*concurrent connections*). Jika membutuhkan kapasitas lebih, Anda dapat melakukan pembelian lisensi/subscription.
