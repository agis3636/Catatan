Intinya: **Ini adalah menu "Mail Filter" (Penyaring Surat), yang merupakan inti dari kecerdasan Proxmox Mail Gateway (PMG).**

Menu ini menggunakan konsep logika **If-Then (Jika-Maka)** untuk menentukan bagaimana sistem memperlakukan setiap email yang melintas. Keempat menu di bawahnya (*Action, Who, What, When*) adalah "bahan-bahan" bangunan aturan tersebut.

Mari kita bedah satu per satu secara runut:

### 1. Action Objects (Tindakan)

* **Fungsi:** Menentukan *apa yang harus dilakukan* oleh PMG terhadap sebuah email yang terkena aturan.
* **Contoh pilihan tindakan:**
* **Accept:** Terima dan teruskan email secara normal.
* **Block / Reject:** Tolak email mentah-mentah (pengirim langsung tahu emailnya ditolak).
* **Quarantine:** Masukkan email ke dalam karantina (aman, tidak sampai ke inbox, tapi admin bisa memeriksanya nanti).
* **Modifying actions:** Mengubah isi email, misalnya menambahkan teks peringatan di subjek (contoh: `[SPAM?]`).



### 2. Who Objects (Siapa)

* **Fungsi:** Menentukan *subjek manusia atau server* yang terlibat dalam pengiriman (pengirim atau penerima).
* **Contoh objek yang bisa dibuat:**
* Alamat email spesifik (misal: `bos@perusahaan.com`).
* Domain tertentu (misal `@domain-luar.com`).
* Alamat IP atau jaringan (misal IP server partner bisnis).



### 3. What Objects (Apa / Isi)

* **Fungsi:** Menentukan *muatan atau isi* dari email tersebut.
* **Contoh objek yang bisa dibuat:**
* Ekstensi file lampiran (attachment) yang berbahaya, seperti `.exe`, `.bat`, `.vbs`, atau `.scr` agar otomatis diblokir.
* Kata kunci atau pola teks tertentu di dalam subjek atau badan email (misal kata-kata penipuan atau promosi tertentu).



### 4. When Objects (Kapan / Waktu)

* **Fungsi:** Menentukan *waktu atau jadwal* berlakunya sebuah aturan.
* **Contoh objek yang bisa dibuat:**
* Jam kerja (Office Hours), misalnya Senin sampai Jumat pukul 08:00 - 17:00.
* Di luar jam kerja atau hari libur (seperti objek "Office Hours" yang tampak di sebelah kanan gambar Anda).



---

### Cara Kerjanya Digabung (Contoh Kasus Nyata):

Bayangkan Anda ingin membuat aturan: **"Jika ada email masuk dari domain luar (`@spam-murah.com`) yang membawa lampiran file `.exe` di luar jam kerja, maka langsung masukkan ke karantina."**

* Bagian **Who**: Mengarah ke `@spam-murah.com`
* Bagian **What**: Mengarah ke file `.exe`
* Bagian **When**: Mengarah ke "Di luar jam kerja"
* Bagian **Action**: Mengarah ke "Quarantine"

Keempat menu objek inilah yang nantinya Anda pasangkan di menu aturan utama (*Rule Center*).

Ada bagian dari keempat objek ini yang paling membuat Anda penasaran untuk dicoba buat aturannya?
