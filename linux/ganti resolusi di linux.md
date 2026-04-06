Untuk mengetahui nama display (output) di Linux, cara yang paling standar dan akurat adalah menggunakan perintah **`xrandr`** tanpa tambahan opsi apa pun.

Ketik perintah ini di terminal:

```bash
xrandr | grep " connected"
```

### Cara Membaca Hasilnya:
Setelah Anda menekan Enter, akan muncul baris yang tampilannya mirip seperti ini:

> **Virtual-1** connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm

* Kata pertama (**Virtual-1**) adalah **nama display** Anda. 
* Di Proxmox dengan driver SPICE, namanya biasanya adalah `Virtual-1`, `Virtual1`, atau `GVP`.

---

### Cara Cepat Mengubah Resolusi (Jika Nama Sudah Ketemu)
Misalnya nama display Anda adalah `Virtual-1`, Anda bisa langsung "menembak" resolusi yang diinginkan dengan satu baris ini:

```bash
xrandr --output Virtual-1 --mode 1920x1080
```

### Jika Resolusi yang Anda Inginkan Tidak Ada di Daftar:
Kalau Anda ketik `xrandr` dan resolusi $1920 \times 1080$ (atau lainnya) tidak muncul dalam list, itulah saatnya Anda harus menggunakan perintah `cvt` dan `newmode` untuk "mendaftarkan" resolusi baru tersebut ke nama display Anda.
