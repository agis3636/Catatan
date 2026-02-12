Berikut adalah perbedaan mendasar antara keduanya:

---

### 1. User Configuration (Pengaturan Berbasis Akun)

Ini adalah pengaturan yang "menempel" pada orangnya (akun user).

* **Cara Kerja:** Aturan ini akan aktif di mana pun user tersebut login. Jika `budiit` login di komputer A, komputer B, atau laptop C, wallpaper dan pembatasan Control Panel-nya akan tetap sama.
* **Kenapa Wallpaper ada di sini?** Karena wallpaper dianggap sebagai preferensi pribadi seorang pengguna. Microsoft mendesain agar setiap orang bisa memiliki tampilan desktop yang berbeda di mesin yang sama.
* **Contoh Lain:** Map Network Drive, settingan Internet Explorer/Edge, dan folder redirection.

### 2. Computer Configuration (Pengaturan Berbasis Mesin)

Ini adalah pengaturan yang "menempel" pada perangkat kerasnya (komputer/VM).

* **Cara Kerja:** Aturan ini akan aktif kepada **siapa pun** yang menggunakan komputer tersebut, termasuk akun Administrator sekalipun. Aturan ini mulai berjalan saat komputer dinyalakan (booting), bahkan sebelum ada user yang login.
* **Kenapa Wallpaper tidak ada di sini?** Karena secara logika sistem, "komputer" tidak punya selera wallpaper; yang punya selera adalah "manusia" yang menggunakannya.
* **Contoh Penggunaan:** Mematikan USB Port, mengatur jam Windows Update, mematikan fungsi Windows Defender, atau mengatur password policy (kerumitan password).

---

### Perbandingan Sederhana

| Fitur | **User Configuration** | **Computer Configuration** |
| --- | --- | --- |
| **Fokus** | Orang / Akun User. | Mesin / Hardware. |
| **Waktu Aktif** | Saat User melakukan **Login**. | Saat Komputer **Booting**. |
| **Hierarki** | Jika bentrok, sering kali settingan mesin lebih menang. | Memiliki otoritas lebih tinggi pada sistem. |
| **Isi Folder** | Ada folder Desktop, Start Menu, dll. | Ada folder Windows Settings, Security Settings. |

---

### Tips Penting dalam Praktek

Jika Anda ingin menerapkan kebijakan yang bersifat **keamanan sistem** (seperti blokir USB atau update), gunakanlah **Computer Configuration**. Tapi jika ingin mengatur **kenyamanan/pembatasan user** (seperti wallpaper atau blokir Control Panel), gunakanlah **User Configuration**.

Karena Anda sedang mencoba simulasi di Proxmox dengan user `budiit`, Anda sudah benar menggunakan **User Configuration** karena target Anda adalah membatasi aktivitas `budiit`.

Apakah Anda ingin mencoba setting GPO yang ada di **Computer Configuration**, misalnya membuat pesan teks (legal notice) yang muncul sebelum user login ke Windows?
