# Pembatasan Akses File Teams

## Skenario A: Membatasi beberapa anggota internal tertentu agar tidak bisa membuka file tertentu di Channel Teams

**Jawaban Singkat**
Bisa, tetapi tidak dilakukan dari Teams. Harus dilakukan dari SharePoint yang menjadi backend penyimpanan file Teams.

Setiap file yang di-upload ke Teams Channel sebenarnya disimpan di SharePoint. Karena itu permission file dapat diatur langsung pada file atau folder di SharePoint.

---

### Opsi 1 (Paling Sederhana)
1. Buka tab **Files** pada channel Teams.
2. Klik **Open in SharePoint**.
3. Pilih file yang ingin dibatasi.
4. Klik **Manage Access**.
5. Hapus akses user tertentu (misalnya A dan B).
6. Berikan akses hanya kepada user yang diperbolehkan.

SharePoint mendukung pengaturan permission sampai level file/folder tertentu sehingga user tertentu dapat dihapus aksesnya tanpa harus mengeluarkannya dari Team. Referensi internal juga menunjukkan permission di level file/library dapat dikelola langsung melalui SharePoint.

---

### Opsi 2 (Untuk Dokumen Sangat Rahasia)
Gunakan **Microsoft Purview Sensitivity Label + Encryption**.

Dengan metode ini pemilik dokumen dapat menentukan user atau grup tertentu yang boleh membuka file, dan proteksi akan tetap berlaku walaupun file di-download atau dikirim ke tempat lain.

Ini biasanya menjadi rekomendasi untuk instansi seperti KPK karena lebih aman dibanding hanya mengandalkan SharePoint permission.

**Catatan Penting**
* Secara teknis bisa menghapus permission individual pada file tertentu.
* Tetapi Microsoft kurang merekomendasikan terlalu banyak "broken inheritance" pada banyak file karena akan menyulitkan administrasi jangka panjang.
* Untuk kasus dokumen rahasia yang hanya boleh diakses sebagian kecil anggota, lebih baik menggunakan:
  * Private Channel
  * Shared Channel
  * Sensitivity Label dengan encryption

---

## Skenario B: Guest / Akun Eksternal Tidak Boleh Melihat atau Download File Tertentu

**Jawaban Singkat**
Bisa. Bahkan ada beberapa level kontrol.

---

### Opsi 1: Batasi Permission File/Folder di SharePoint
Guest Teams sebenarnya mengakses file melalui SharePoint. Permission file dapat diatur agar guest tidak memiliki akses ke folder/file tertentu. Microsoft juga menyatakan bahwa permission file guest mengikuti konfigurasi SharePoint dan dikelola oleh admin.

Contoh:
* Internal user → Full Access
* Guest → Tidak ada akses

---

### Opsi 2: Sensitivity Label "Internal Only"
Sangat direkomendasikan untuk instansi pemerintah.

File diberi label:
* Confidential
* Internal Only
* Employees Only

Maka:
* Guest tidak bisa membuka file.
* Bahkan jika link diberikan, akses tetap ditolak karena hak akses dienforce oleh label. Sensitivity Label dapat menerapkan access control dan conditional access pada Teams, SharePoint, dan Microsoft 365 Group.

---

### Opsi 3: Block Download untuk Guest
Menggunakan kombinasi:
* Sensitivity Label
* Conditional Access
* SharePoint Restricted Access Control

Microsoft Purview mendukung proteksi sehingga file yang dilabeli dapat dicegah untuk didownload dan perlindungan tetap melekat pada file.

Contoh kebijakan:
* Guest hanya boleh baca lewat browser.
* Tidak boleh download.
* Tidak boleh print.
* Tidak boleh copy-paste.

---

### Opsi 4 (Desain yang Paling Bersih)
Gunakan **Private Channel** atau **Shared Channel**

Jika memang ada subset informasi yang tidak boleh terlihat user luar:
* Tim utama tetap berisi seluruh anggota.
* Buat Private Channel khusus internal.
* Simpan dokumen rahasia di Private Channel tersebut.

Karena Private Channel memiliki membership dan SharePoint Site tersendiri. Microsoft menyebut membership private channel dikelola melalui Teams, dan channel tersebut memiliki kontrol akses tersendiri.

---

## Rekomendasi Untuk KPK
Saya biasanya merekomendasikan matriks berikut:

| Kebutuhan | Rekomendasi |
| :--- | :--- |
| 1-2 file rahasia | Manage Access di SharePoint |
| Banyak file rahasia | Private Channel |
| Dokumen sangat sensitif (investigasi, audit, hukum) | Purview Sensitivity Label + Encryption |
| Guest boleh lihat tapi tidak boleh download | Conditional Access + Sensitivity Label |
| Kerja sama dengan instansi luar | Shared Channel / B2B Guest dengan pembatasan akses |

---

## Dokumentasi Microsoft

**SharePoint & Teams Permissions**

[Manage settings and permissions when SharePoint and Teams are integrated](https://learn.microsoft.com/en-us/sharepoint/manage-teams-sharepoint-experiences)

**Guest Access in Teams**

[Guest access in Microsoft Teams](https://learn.microsoft.com/en-us/microsoftteams/guest-access)

**Guest Permissions in Teams**

[Set guest permissions for channels in Microsoft Teams](https://support.microsoft.com/en-us/teams/teams-channels/set-guest-permissions-for-channels-in-microsoft-teams)

**Microsoft Purview Sensitivity Labels**

[Microsoft Purview Information Protection](https://learn.microsoft.com/en-us/purview/information-protection)

**Restrict Access with Sensitivity Labels**

[Learn about sensitivity labels](https://learn.microsoft.com/en-us/purview/sensitivity-labels)
