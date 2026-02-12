### 1. Apakah `budiit` jadi sama seperti `Administrator`?

**Benar.** Jika Anda memasukkan `budiit` ke dalam group **Domain Admins**, maka dia memiliki kekuasaan penuh:

* Bisa menghapus OU, mengganti password user lain, hingga mematikan server.
* Bisa login ke komputer mana pun yang sudah join domain dan punya hak akses penuh di sana.

**Baiknya ditambah atau tidak?**

* **Jawabannya: TIDAK.** * **Aturan Emas (Best Practice):** Gunakan prinsip *Least Privilege*. Berikan hak akses seminimal mungkin yang dibutuhkan user untuk bekerja.
* **Saran:** Biarkan `budiit` tetap sebagai **Domain Users**. Jika Anda butuh melakukan instalasi software di client, barulah gunakan akun `Administrator` atau buatkan satu akun khusus IT (misal: `it.admin`) yang masuk group Domain Admins. Akun sehari-hari (untuk buka email, ngetik, dll) jangan dijadikan Admin untuk menghindari risiko virus atau salah hapus data.

---

### 2. Apa Bedanya Format Login?

Mari kita bedah perbedaan ketiganya:

| Format | Contoh | Penjelasan |
| --- | --- | --- |
| **Hanya Nama** | `budiit` | **Default Domain.** Karena komputer sudah join ke `test.local`, otomatis Windows mencari nama ini di database domain tersebut. |
| **UPN** | `budiit@test.local` | **Universal Principal Name.** Format ini paling aman dan pasti. Digunakan jika ada banyak domain (anak perusahaan) agar tidak tertukar. |
| **Format Lama** | `TEST\budiit` | **NetBIOS Style.** Ini cara lama tapi masih sangat sering digunakan. Sama fungsinya dengan UPN. |
| **Lokal** | `.\budiit` | **Local Account.** Tanda titik `.` artinya "Komputer ini". Ini perintah untuk login menggunakan akun yang dibuat di laptop itu sendiri, **bukan** akun server/domain. |

---

### 3. Mengapa tadi ketik `budiit` saja bisa?

Karena di bawah kotak password, Anda pasti melihat tulisan **"Sign in to: TEST"**.
Artinya, Windows sudah pintar. Dia tahu bahwa "Oke, user ini tidak pakai tanda `.` atau `@domain` lain, jadi saya cari saja di domain `TEST` ini."
