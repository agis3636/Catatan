<img width="956" height="352" alt="Image" src="https://github.com/user-attachments/assets/8b2bfb69-bb94-48e8-84c7-5917b1058f8e" />

Partisi berukuran 524 MB yang ada di gambar Anda adalah **Recovery Partition** (Partisi Pemulihan) bawaan Windows. Posisinya yang berada di antara drive (C:) dan ruang "Unallocated" (30 GB) memang menjadi penghalang, karena agar drive C: bisa diperluas (*Extend Volume*), ruang kosong (Unallocated) harus berada persis di sebelah kanannya.

**⚠️ Peringatan Penting Sebelum Menghapus:**

Menghapus partisi ini akan menghilangkan fitur **Windows Recovery Environment (WinRE)**. Artinya, jika suatu saat Windows Anda gagal *booting* atau bermasalah, Anda tidak bisa menggunakan fitur bawaan seperti *Reset this PC* atau *Startup Repair* tanpa bantuan Flashdisk Installer Windows. Jika Anda paham risikonya dan memiliki media instalasi Windows untuk berjaga-jaga, silakan lanjutkan.

Windows memproteksi partisi ini, sehingga Anda tidak bisa menghapusnya hanya dengan klik kanan di Disk Management (opsinya akan berwarna abu-abu). Anda harus menggunakan *Command Prompt* (`diskpart`).

Berikut adalah langkah-langkah untuk menghapus partisi 524 MB tersebut:

### Langkah 1: Menghapus Recovery Partition dengan Diskpart

1. Buka **Start Menu** Windows.
2. Ketik **cmd**, lalu pada hasil pencarian *Command Prompt*, klik kanan dan pilih **Run as administrator**.
3. Di dalam jendela Command Prompt yang hitam, ketik perintah berikut satu per satu (tekan **Enter** setiap selesai mengetik satu baris):
   * `diskpart` (untuk masuk ke utilitas manajemen disk).
   * `list disk` (akan menampilkan daftar hard disk/SSD Anda).
   * `select disk 0` (pilih Disk 0 sesuai dengan gambar Anda).
   * `list partition` (akan menampilkan daftar partisi di dalam Disk 0).
4. Perhatikan daftar yang muncul. Cari partisi yang ukurannya sekitar **524 MB** dan tipe/labelnya *Recovery*. Catat **Nomor Partisinya** (biasanya Partition 3 atau 4).
5. Ketik perintah berikut:
   * `select partition X` (ganti **X** dengan nomor partisi 524 MB yang Anda temukan di langkah sebelumnya. **Pastikan nomornya benar!**).
6. Terakhir, ketik perintah untuk menghapusnya secara paksa:
   * `delete partition override`
7. Jika berhasil, akan muncul pesan *"DiskPart successfully deleted the partition"*. Anda bisa mengetik `exit` untuk keluar dari Command Prompt.

### Langkah 2: Menggabungkan (Extend) ke Drive C:

Setelah partisi 524 MB berhasil dihapus, partisi tersebut akan berubah menjadi *Unallocated space* dan otomatis menyatu dengan ruang 30 GB di sebelahnya. Sekarang Anda bisa menggabungkannya ke drive C:.

1. Buka kembali **Disk Management** (seperti yang ada di gambar Anda).
2. Klik kanan pada partisi **(C:)**.
3. Pilih **Extend Volume...**
4. Jendela *Extend Volume Wizard* akan terbuka. Klik **Next**.
5. Sistem akan otomatis memilih semua ruang *Unallocated* yang tersedia (sekitar 30,5 GB). Langsung klik **Next** lagi.
6. Klik **Finish**.

Sekarang ruang 30 GB tersebut sudah berhasil digabungkan, dan kapasitas drive (C:) Anda akan bertambah!
