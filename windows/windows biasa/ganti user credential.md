
Untuk login dengan user berbeda:

- Username: `smbamah` (contoh user lain di OMV)
- Password: Password milik `smbamah`
- Remember my credentials: Jangan dicentang.
- Klik OK.

Untuk berganti user berikutnya:

1. Tutup semua jendela File Explorer yang membuka NAS.
2. Buka PowerShell Run as Administrator.
3. Jalankan:

```
Restart-Service LanmanWorkstation -Force
```

4. Buka kembali `\\192.168.200.2\SHARE_SMB`.
5. Masukkan username dan password yang berbeda.

Catatan: Restart Workstation memutus seluruh koneksi SMB Windows, bukan hanya OMV. Jadi koneksi ke network share lain juga bisa terputus.
