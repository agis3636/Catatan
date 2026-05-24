Membangun infrastruktur Windows Server memang harus dilakukan secara berurutan karena setiap komponen saling bergantung. Jika urutannya salah, biasanya akan terjadi error pada resolusi nama domain atau replikasi data.

Berikut adalah urutan instalasi dan konfigurasi yang paling ideal:

---

## 1. Persiapan Awal (Pre-Requisites)

Sebelum menyentuh Active Directory, Anda **wajib** mengatur dua hal ini:

* **Static IP Address:** Domain Controller (DC) tidak boleh menggunakan IP dinamis (DHCP).
* **Computer Name:** Ubah nama komputer menjadi sesuatu yang deskriptif (contoh: `DC-PROD-01`) sebelum di-promote.

---

## 2. Instalasi & Konfigurasi Active Directory (AD DS) & DNS

Secara default, saat Anda menginstal Active Directory, sistem akan otomatis meminta Anda mengonfigurasi DNS. Keduanya berjalan beriringan.

### Langkah-langkah:

1. Buka **Server Manager** > **Add Roles and Features**.
2. Pilih **Active Directory Domain Services**. (Fitur DNS akan otomatis ikut terpilih).
3. Setelah instalasi selesai, klik ikon bendera kuning di Server Manager dan pilih **Promote this server to a domain controller**.
4. Pilih **Add a new forest** dan masukkan nama domain (contoh: `perusahaan.local`).
5. Masukkan Password **DSRM** (ini sangat penting untuk recovery).
6. Restart server.

---

## 3. Organizational Unit (OU)

Setelah AD aktif, jangan langsung membuat User. Buatlah wadah organisasinya terlebih dahulu agar manajemennya rapi.

### Praktek:

1. Buka **Active Directory Users and Computers** (ADUC).
2. Klik kanan pada nama domain Anda > **New** > **Organizational Unit**.
3. Beri nama (contoh: `IT_Dept`, `HRD`, atau `Marketing`).
4. **Tips:** Masukkan user-user ke dalam OU yang sesuai agar nantinya GPO bisa diarahkan tepat sasaran.

---

## 4. Group Policy Object (GPO)

GPO adalah sekumpulan aturan yang Anda terapkan pada User atau Komputer di dalam OU.

### Praktek:

1. Buka **Group Policy Management**.
2. Klik kanan pada OU yang sudah dibuat tadi > **Create a GPO in this domain, and Link it here...**.
3. Beri nama GPO (contoh: `Wallpaper_Policy` atau `Disable_USB`).
4. Klik kanan GPO tersebut > **Edit**.
* Jika ingin mengatur tampilan/aplikasi user: Cek di **User Configuration**.
* Jika ingin mengatur batasan hardware/sistem: Cek di **Computer Configuration**.


5. Gunakan perintah `gpupdate /force` di CMD client untuk memaksa aturan tersebut segera berjalan.

---

## Ringkasan Urutan Setting (Roadmap)

| Urutan | Komponen | Mengapa? |
| --- | --- | --- |
| **1** | **Static IP & Hostname** | Dasar fondasi jaringan agar server mudah ditemukan. |
| **2** | **AD DS & DNS** | Inti dari sistem. Tanpa DNS, AD tidak akan berfungsi. |
| **3** | **Organizational Unit (OU)** | Menyiapkan struktur organisasi sebelum membuat user. |
| **4** | **Users & Groups** | Membuat akun-akun pengguna di dalam OU. |
| **5** | **Group Policy (GPO)** | Menerapkan aturan pada user/komputer yang ada di OU. |

---

## Hal Penting Setelah Setting:

Setelah semua selesai, pastikan di sisi **Client**, settingan DNS-nya diarahkan ke **IP Address si Domain Controller** tadi. Tanpa ini, Client tidak akan pernah bisa "Join Domain".

Apakah Anda ingin saya bantu membuatkan draf skenario spesifik untuk kebijakan GPO (seperti pembatasan Control Panel atau mapping drive otomatis)?
