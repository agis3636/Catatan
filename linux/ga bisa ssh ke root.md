Masalahnya adalah secara *default*, Ubuntu (dan kebanyakan distro Linux modern) **melarang login SSH langsung sebagai `root**` demi alasan keamanan.

Meskipun kamu tahu password root-nya, server bakal tetap bilang `Permission denied`. Kamu punya dua pilihan untuk membereskan ini:

---

### Opsi 1: Login dengan User Lain (Paling Disarankan)

Gunakan user yang kamu buat saat pertama kali menginstall Ubuntu (misalnya user `admin`, `ubuntu`, atau user selain `zimbra`).

```cmd
ssh namauser@192.168.100.65

```

Setelah masuk, baru ketik `sudo -i` untuk pindah ke mode root.

---

### Opsi 2: Izinkan Login Root (Jika Terpaksa)

Kalau kamu benar-benar ingin bisa login langsung sebagai root, kamu harus mengubah settingan SSH di server:

1. **Login dulu ke server** (lewat console atau user lain).
2. Buka file konfigurasi SSH:
`sudo nano /etc/ssh/sshd_config`
3. Cari baris `#PermitRootLogin prohibit-password` atau `PermitRootLogin no`.
4. Ubah menjadi:
**`PermitRootLogin yes`**
*(Hapus tanda pagar `#` di depannya jika ada)*.
5. Simpan (Ctrl+O, Enter) dan keluar (Ctrl+X).
6. **Restart servis SSH**:
`sudo systemctl restart ssh`
