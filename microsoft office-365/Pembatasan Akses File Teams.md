# Panduan Pembatasan Akses File di Microsoft Teams

Dokumen ini berisi panduan teknis dan skenario pengelolaan hak akses file pada Microsoft Teams. Secara arsitektur dasar, setiap file yang diunggah ke *channel* Teams akan disimpan secara otomatis di SharePoint[cite: 3]. Oleh karena itu, pengaturan izin (*permissions*) harus dilakukan langsung pada file atau folder di SharePoint[cite: 3].

## Skenario A: Membatasi Akses Anggota Internal

Skenario ini digunakan ketika ada kebutuhan agar beberapa anggota internal tertentu tidak dapat membuka file spesifik di dalam *channel* Teams[cite: 3].

### Opsi 1: Kelola Akses SharePoint (Paling Sederhana)
Cara ini mengelola izin pada level file/folder tertentu tanpa harus mengeluarkan pengguna dari keanggotaan *Team*[cite: 3].
1. Buka tab **Files** pada *channel* Teams[cite: 3].
2. Klik **Open in SharePoint**[cite: 3].
3. Pilih file yang ingin dibatasi hak aksesnya[cite: 3].
4. Klik **Manage Access**[cite: 3].
5. Hapus akses *user* tertentu yang tidak berkepentingan[cite: 3].
6. Berikan akses hanya kepada *user* yang diperbolehkan[cite: 3].

### Opsi 2: Microsoft Purview (Untuk Dokumen Sangat Rahasia)
Gunakan *Microsoft Purview Sensitivity Label + Encryption*[cite: 3]. Proteksi ini akan menentukan *user* atau grup yang boleh membuka file, dan perlindungan tersebut akan tetap berlaku meskipun file diunduh atau dikirim ke tempat lain[cite: 3]. Ini adalah rekomendasi utama untuk instansi pemerintahan atau lembaga dengan tingkat keamanan tinggi[cite: 3].

> **Catatan Penting:** Menghapus izin individual per file secara teknis bisa dilakukan, namun Microsoft tidak merekomendasikan terlalu banyak *"broken inheritance"* pada banyak file karena akan menyulitkan administrasi jangka panjang[cite: 3].

---

## Skenario B: Membatasi Akses Akun Eksternal (Guest)

Skenario ini diterapkan agar pengguna tamu (*guest*) tidak dapat melihat atau mengunduh file tertentu di dalam Teams[cite: 3].

### Opsi 1: Batasi Izin File/Folder di SharePoint
Konfigurasi file dapat diatur agar *internal user* memiliki *Full Access*, sementara *guest* tidak memiliki akses sama sekali[cite: 3].

### Opsi 2: Sensitivity Label "Internal Only"
Dokumen dapat diberi label spesifik seperti *Confidential*, *Internal Only*, atau *Employees Only*[cite: 3]. Dengan label ini, *guest* tidak akan bisa membuka file, bahkan jika mereka mendapatkan *link* dokumen tersebut (hak akses di-*enforce* oleh label)[cite: 3]. 

### Opsi 3: Block Download untuk Guest
Menggunakan kombinasi *Sensitivity Label*, *Conditional Access*, dan *SharePoint Restricted Access Control*[cite: 3]. Kebijakan ini memungkinkan *guest* hanya bisa membaca dokumen melalui *browser*[cite: 3]. *Guest* tidak diperbolehkan melakukan *download*, *print*, atau *copy-paste*[cite: 3].

### Opsi 4: Private / Shared Channel (Desain Paling Bersih)
Jika terdapat dokumen yang tidak boleh dilihat oleh pihak luar, disarankan membuat **Private Channel** khusus internal dan menyimpan dokumen tersebut di sana[cite: 3]. *Private Channel* memiliki keanggotaan dan situs SharePoint yang terpisah dan dikelola tersendiri dari *channel* utama[cite: 3].

---

## Matriks Rekomendasi Implementasi

Tabel berikut merangkum rekomendasi solusi berdasarkan kebutuhan spesifik:

| Kebutuhan | Rekomendasi Solusi |
| :--- | :--- |
| 1-2 file rahasia | Manage Access di SharePoint[cite: 3] |
| Banyak file rahasia | Private Channel[cite: 3] |
| Dokumen sangat sensitif (investigasi, audit, hukum) | Purview Sensitivity Label + Encryption[cite: 3] |
| Guest boleh lihat tapi tidak boleh download | Conditional Access + Sensitivity Label[cite: 3] |
| Kerja sama dengan instansi luar | Shared Channel / B2B Guest dengan pembatasan akses[cite: 3] |

---
**Referensi:** Berdasarkan panduan arsitektur keamanan Microsoft Teams dan SharePoint, serta kebijakan akses informasi lintas lembaga.
