Ini terjadi karena pada instalasi standar Ubuntu, **user root tidak memiliki password** dan **akses SSH untuk root dikunci** demi keamanan.

Karena kamu "terjebak" di user `zimbra` dan tidak bisa masuk ke root, kamu tidak akan bisa menghapus user `zimbra` itu sendiri. Berikut adalah urutan langkah untuk menjebol aksesnya:

---

### 1. Masuk ke Mode Root dari User Zimbra

Coba gunakan perintah ini di dalam terminal SSH user `zimbra`:

```bash
sudo -i

```

* Jika diminta password, masukkan password user `zimbra`.
* Jika berhasil, tampilan terminal akan berubah dari `zimbra@zimbra:~$` menjadi `root@zimbra:~#`.

**Jika muncul error "zimbra is not in the sudoers file":**
Artinya user `zimbra` yang kamu buat tidak punya izin admin. Kamu harus login menggunakan **user pertama** yang kamu buat saat install Ubuntu (misal: `ubuntu`, `admin`, atau nama kamu sendiri).

---

### 2. Aktifkan Password Root (Opsional tapi Membantu)

Setelah kamu berhasil masuk ke mode root (setelah `sudo -i`), buatlah password untuk root agar kamu bisa login langsung nanti:

```bash
passwd root

```

*(Masukkan password baru 2 kali)*.

