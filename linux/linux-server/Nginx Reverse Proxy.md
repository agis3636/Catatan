Oke, mari kita buat praktek **Nginx Proxy** yang sesungguhnya. Kita akan simulasikan arsitektur *Enterprise* kecil-kecilan di Proxmox kamu.

Kita akan pakai **2 Container (LXC)** agar ringan:
1.  **LXC-PROXY (IP .200.91):** Si "Satpam" (Nginx Proxy).
2.  **LXC-APP (IP .100.100):** Si "Aplikasi" (Hanya berisi file HTML di Apache/Nginx).

---

### Langkah 1: Siapkan LXC-APP (Target Aplikasi)
Buat LXC baru atau gunakan yang sudah ada, lalu install web server di dalamnya:

```bash
# Di dalam LXC Aplikasi (100.100)
apt update && apt install apache2 -y
echo "<h1>DOMAIN UTAMA: INI SERVER APLIKASI INTERNAL</h1>" > /var/www/html/index.html
systemctl start apache2
```
*Sekarang, pastikan kamu **TIDAK** membuka IP 100.100 ini di browser luar (anggap saja ini server rahasia di jaringan internal).*

---

### Langkah 2: Konfigurasi LXC-PROXY (Si Satpam)
Sekarang masuk ke Debian/LXC yang sudah ada Nginx-nya tadi (IP 200.91). Kita akan suruh dia mengambil tampilan dari IP 100.100.

1.  Hapus atau cadangkan konfigurasi default:
    ```bash
    cd /etc/nginx/sites-available/
    sudo mv default default.bak
    sudo nano default
    ```

2.  Masukkan konfigurasi **Reverse Proxy** ini:

### Coba Konfigurasi "Paling Jinak" Ini
Ubah file `/etc/nginx/sites-available/default` kamu di server Nginx (`.200.91`) jadi begini:

```nginx
server {
    listen 80;
    server_name 192.168.200.91;

    location / {
        # Arahkan ke IP Apache
        proxy_pass http://192.168.100.100;

        # PAKSA Apache buat mikir dia dipanggil langsung ke IP-nya sendiri
        # Ini trik paling ampuh buat ngatasin Bad Request di Apache
        proxy_set_header Host 192.168.100.100;
        
        # Standar proxy header
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Tambahan biar Nginx nggak "salah paham" soal timeout
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }
}
```

### Kenapa saya minta ganti `Host` jadi IP Apache?
Karena secara default, Nginx bakal ngirim `Host: 192.168.200.91` ke Apache. Apache yang duduk manis di IP `.100.100` bakal teriak: *"Hah? Gue kan .100.100, kenapa lu panggil pake nama .200.91? Gue nggak kenal! Bad Request!"*

Dengan kita paksa `proxy_set_header Host 192.168.100.100`, si Apache bakal merasa dipanggil secara benar.

---

### Langkah Eksekusi:
1.  **Simpan** konfigurasi di atas.
2.  **Cek Syntax:** `nginx -t` (Pastikan muncul *successful*).
3.  **Reload:** `systemctl reload nginx`.
4.  **Tes di Browser Laptop:** Buka lagi `http://192.168.200.91`.

