Mengenai VM dengan (ID) yang *error* di `pve-utama`, tulisan `Error: VM is locked (migrate)` di bawah itu artinya sistem Proxmox masih "menggembok" VM tersebut. 

Ini wajar terjadi kalau proses migrasi antar-server via PDM memakan waktu lama, sempat *timeout*, atau sistem gagal membersihkan VM sumber secara otomatis setelah data berhasil disalin ke server tujuan. Proxmox sengaja menguncinya supaya data tidak bentrok.

Cara membuka gemboknya sangat mudah, kamu hanya perlu menjalankan satu perintah ini:

1. Buka menu **>_ Shell** di server **pve-utama** (tempat VM yang terkunci itu berada).
2. Ketik perintah berikut dan tekan **Enter**:
   ```bash
   qm unlock 101
   ```

Setelah perintah itu dijalankan, ikon gembok di VM 101 akan hilang dan kamu bisa mengelolanya lagi.

**Saran tambahan:**
Karena kamu bilang VM tersebut sudah berhasil jalan dan bisa dibuka di `pve-vm` (server tujuan), berarti migrasinya sebenarnya sukses. VM 101 yang tertinggal di `pve-utama` ini hanyalah "sisa" atau salinan lama. 

Setelah berhasil di-*unlock* nanti, apakah kamu mau menghapus (*Destroy*) VM 101 yang ada di `pve-utama` ini agar tidak boros *storage*? Pastikan dulu data di `pve-vm` sudah benar-benar aman ya sebelum menghapusnya!
