Wajar sekali jika tidak ada update setelah baris `cdrom` dijadikan komentar, karena saat ini sistem kamu tidak memiliki "peta" atau alamat gudang aplikasi (repository) di internet untuk dituju.

Berikut penjelasan detailnya:

### Mengapa tidak ada update?

File `/etc/apt/sources.list` berfungsi sebagai daftar alamat di mana Debian harus mencari pembaruan.

* **Sebelumnya:** Sistem mencoba mencari di CD-ROM/ISO installer, tapi gagal karena filenya tidak ada (atau tidak terbaca) dan internet kamu bermasalah.
* **Sekarang:** Setelah kamu beri tanda `#` (komentar) pada baris `cdrom`, sistem tidak punya alamat lain untuk dicek. Itulah sebabnya `apt update` tidak menghasilkan apa-apa.

### Apakah nanti dapat pembaruan?

**Bisa**, asalkan kamu menambahkan alamat repository internet secara manual. Karena kamu menggunakan **Debian 13 (Trixie)** yang merupakan versi *testing*, kamu harus mengarahkan alamatnya ke server Debian di internet.

---

### Cara agar dapat pembaruan (Update)

Silakan buka kembali file sources dengan `sudo nano /etc/apt/sources.list`, lalu tambahkan baris-baris ini di bawah baris `cdrom` yang kamu beri komentar tadi:

```text
deb http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free

deb http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free

deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free

```

### Langkah Terakhir

Setelah disimpan, pastikan internet (DNS) sudah aktif seperti yang kita bahas sebelumnya, lalu jalankan:

1. `sudo apt update`
2. `sudo apt upgrade -y`

**Catatan Tambahan:**
Mengingat kamu sering mengerjakan proyek infrastruktur serius seperti setup **Synology DS925+** atau migrasi ke **Proxmox**, menggunakan Debian 13 di server produksi agak berisiko karena statusnya masih "Testing". Jika ini untuk keperluan klien atau produksi, versi **Debian 12 (Bookworm)** jauh lebih disarankan karena kestabilannya.

Apakah kamu ingin saya bantu buatkan konfigurasi IP statis yang lengkap sekalian di file `/etc/network/interfaces` agar DNS-nya tidak hilang lagi?
