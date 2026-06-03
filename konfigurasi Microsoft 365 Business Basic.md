Siap, Mas. Berikut adalah tutorial praktis (*step-by-step*) cara melakukan konfigurasi "Jalur Cepat" Microsoft 365 Business Basic, khusus untuk klien yang **hanya butuh Office Web, OneDrive, dan Teams (tanpa mengganggu email lama mereka)**.

### Persiapan

Pastikan Mas sudah memiliki akun **Global Admin** (biasanya formatnya `admin@namaklien.onmicrosoft.com`) yang dibuat saat pertama kali *tenant* Microsoft dibeli.

---

### Langkah 1: Pasang Domain (Hanya Verifikasi TXT)

Tujuannya agar akun karyawan terlihat profesional (pakai `@pt-klien.com`, bukan bawaan Microsoft). **Hati-hati di langkah ini agar email lama klien tidak mati.**

1. Buka browser dan login ke **[admin.microsoft.com](https://www.google.com/search?q=https://admin.microsoft.com)**.
2. Di panel sebelah kiri, buka menu **Settings** > **Domains**.
3. Klik tombol **Add domain**.
4. Ketik nama domain klien (misal: `pt-klien.com`), lalu klik *Use this domain*.
5. Pilih opsi **Add a TXT record to the domain's DNS records**, lalu klik *Continue*.
6. Microsoft akan menampilkan nilai TXT (biasanya diawali `MS=ms...`).
* *Tugas Mas:* *Copy* nilai tersebut dan masukkan ke DNS Management domain klien (lewat cPanel, Cloudflare, dll).


7. Setelah TXT dimasukkan di DNS, kembali ke portal Microsoft dan klik **Verify**.
8. **⚠️ BAGIAN PALING KRUSIAL:** Pada halaman *How do you want to connect your domain?*, klik *Continue*. Nanti akan muncul daftar *record* (Exchange/MX, SharePoint, dll). **HILANGKAN CENTANG / SKIP** pada bagian Exchange dan MX record.
* *(Kalau MX record Microsoft dimasukkan ke DNS, email lama mereka akan error/mati).*


9. Selesaikan prosesnya. Domain status akan menjadi *Healthy*.

---

### Langkah 2: Buat User & Pasang Lisensi

Sekarang kita buatkan "kunci masuk" untuk karyawan-karyawannya.

1. Di portal Admin, masuk ke menu **Users** > **Active users**.
2. Klik **Add a user**.
3. Di tab *Set up basics*:
* Isi *First name* dan *Last name*.
* Pada kolom *Username*, ketik nama depan, dan pastikan *dropdown* domain di sebelahnya sudah menggunakan domain resmi mereka (misal: `budi@pt-klien.com`).
* Pilih **Auto-generate password** dan centang kotak **Require this user to change their password when they first sign in**.


4. Di tab *Product licenses*:
* Centang lisensi **Microsoft 365 Business Basic**. (Jika tidak dicentang, *user* tidak punya kuota OneDrive/Teams).


5. Di tab *Optional settings*: Bisa dilewati atau isi divisi/departemen jika perlu.
6. Klik **Next** lalu **Finish adding**. *Copy* data *username* dan *password* sementaranya untuk diserahkan ke klien.

---

### Langkah 3: Mengunci Keamanan Berbagi File (OneDrive/SharePoint)

Karena mereka akan fokus pakai ini untuk data *cloud*, kita harus batasi agar karyawan tidak sembarangan membagikan *file* perusahaan ke publik.

1. Di portal Admin kiri bawah, klik **Show all**.
2. Pilih menu **SharePoint** (ini akan membuka tab baru ke *SharePoint admin center*).
3. Di menu sebelah kiri, buka **Policies** > **Sharing**.
4. Pada bagian *External sharing*, geser *slider* SharePoint dan OneDrive ke bawah menuju opsi:
* **New and existing guests** (jika karyawan boleh berbagi *file* dengan klien luar pakai *password*).
* Atau mentok ke **Only people in your organization** (jika *cloud* hanya khusus untuk internal PT tersebut).


5. Jangan biarkan *slider* berada di opsi paling atas *(Anyone)*.
6. *Scroll* ke bawah dan klik **Save**.

---

### Langkah 4: Aktifkan MFA (Autentikasi Dua Langkah)

Biar *tenant* yang Mas kerjakan tidak gampang kena *hack*, aktifkan keamanan standar dari Microsoft.

1. Buka tab baru dan masuk ke **[entra.microsoft.com](https://www.google.com/search?q=https://entra.microsoft.com)** (Microsoft Entra admin center).
2. Di menu kiri, perluas bagian **Identity**, lalu klik **Overview**.
3. Pilih tab **Properties**.
4. *Scroll* ke paling bawah, cari tulisan biru **Manage security defaults** dan klik.
5. Di panel kanan yang muncul, ubah *Security defaults* menjadi **Enabled**.
6. Klik **Save**.
* *(Efeknya: Saat karyawan login nanti, mereka akan diminta mengunduh aplikasi Microsoft Authenticator di HP sebagai pengaman tambahan).*



---

### Langkah 5: Proses Serah Terima (Handover)

Pekerjaan teknis Mas sudah selesai. Selanjutnya tinggal lapor ke klien:

1. Buat daftar Excel berisi *Username* dan *Password* sementara dari Langkah 2.
2. Kirimkan ke klien beserta instruksi singkat:
> *"Pak/Bu, akun Microsoft 365 Business Basic sudah siap digunakan. Silakan akses portal **[portal.office.com](https://www.google.com/search?q=https://portal.office.com)** dan masukkan username/password di lampiran. Anda akan diminta mengganti password saat pertama kali login."*



Sudah, cukup jalankan 5 tahapan di atas. Pekerjaan rapi, *cloud* aktif, klien puas, dan *server* email lama mereka dijamin tetap aman tidak terganggu.
