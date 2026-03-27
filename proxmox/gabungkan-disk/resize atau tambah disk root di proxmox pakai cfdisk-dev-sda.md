Supaya tulisan *Free space* yang 20G di bawah itu bisa ditambahkan, kamu harus "menyorot" partisi `/dev/sda3` terlebih dahulu sebelum memilih *Resize*.

Yuk, kita setting bagian `cfdisk`-nya pelan-pelan:

1. Ketik lagi perintah ini dan tekan Enter: 
   ```bash
   cfdisk /dev/sda
   ```
2. Tekan **Panah Bawah** di *keyboard* kamu sampai tanda `>>` berpindah menyorot baris **`/dev/sda3`** (yang ukurannya 31.5G).
3. Setelah `/dev/sda3` tersorot, gunakan **Panah Kanan** untuk memilih menu **[ Resize ]** di bagian bawah, lalu tekan **Enter**.
4. Nanti akan muncul angka ukurannya (dia otomatis mengusulkan ukuran maksimal untuk mengambil 20G tersebut). Langsung tekan **Enter** saja.
5. Gunakan **Panah Kanan** lagi untuk memilih menu **[ Write ]**, lalu tekan **Enter**.
6. Akan ada peringatan, ketik kata `yes` lalu tekan **Enter**.
7. Terakhir, pilih menu **[ Quit ]** dan tekan **Enter**.

Nah, setelah partisinya benar-benar berhasil diperbesar di dalam *cfdisk*, baru kamu jalankan ulang 3 perintah sakti tadi secara berurutan:

```bash
pvresize /dev/sda3
lvextend -l +100%FREE /dev/pve/root
resize2fs /dev/pve/root
```

Coba pastikan kursor panahnya sudah turun ke `sda3` ya kali ini. Mau dicoba sekarang?
