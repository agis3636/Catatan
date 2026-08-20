Intinya: **Pilihan yang sangat tepat!** Memahami isi dan fungsi dari menu-menu di Proxmox Mail Gateway (PMG) ini akan membuat Anda benar-benar menguasai sistem keamanan email Anda sendiri, bukan sekadar "ikut-ikutan setting".

Berdasarkan `image_98cb03.png`, panel PMG dibagi menjadi beberapa blok menu utama. Mari kita bedah fungsi masing-masing blok secara ringkas dan praktis:

---

### 1. Dashboard (Menu Paling Atas)

* **Fungsi:** Sebagai halaman utama atau "kokpit" pemantauan secara *real-time*.
* **Yang bisa dilihat:** Grafik lalu lintas email (berapa banyak email masuk/keluar), status *load* server, penggunaan memori/CPU, serta ringkasan jumlah email yang bersih, spam, atau mengandung virus hari ini.

### 2. Mail Filter (Mesin Penyaring & Aturan)

Menu ini adalah "otak" dari PMG untuk menentukan apa yang harus dilakukan terhadap sebuah email. Di dalamnya terdapat konsep logika **"If [Kondisi] Then [Aksi]"**:

* **Action Objects:** Menentukan *tindakan* apa yang harus diambil (misalnya: *Accept* / terima, *Reject* / tolak, *Quarantine* / masukkan ke karantina, atau *Block*).
* **Who Objects:** Menentukan *siapa* pengirim atau penerimanya (bisa berdasarkan alamat email spesifik, domain, atau jaringan IP).
* **What Objects:** Menentukan *apa* isi atau muatan email tersebut (misalnya berdasarkan ekstensi lampiran file yang berbahaya seperti `.exe`, `.bat`, atau teks tertentu di dalam subjek/badan email).
* **When Objects:** Menentukan *kapan* aturan itu berlaku (misalnya hanya di luar jam kerja atau hari libur, seperti yang tampak aktif di gambar).

### 3. Configuration (Setelan Utama & Jaringan)

Menu ini berisi semua fondasi teknis server tempat kita bekerja kemarin:

* **Mail Proxy:** Tempat mengatur relai, domain, *Transports* (jalur ke iRedMail), port, dan kunci **DKIM**.
* **Spam Detector & Virus Detector:** Mengatur tingkat sensitivitas mesin pemindai spam (menggunakan *SpamAssassin*) dan antivirus (ClamAV).
* **User Management & Cluster:** Untuk manajemen hak akses admin atau jika Anda ingin menggabungkan beberapa server PMG dalam satu kendali (klaster).
* **Certificates / Backup / Certificates:** Mengatur sertifikat SSL/TLS untuk enkripsi web GUI dan pencadangan sistem.

### 4. Administration (Karantina & Operasional Harian)

Menu ini digunakan sehari-hari untuk memantau "korban" atau antrean email:

* **Spam / Virus / Attachment Quarantine:** Tempat penampungan email yang dicurigai sebagai spam atau berbahaya. Admin atau user bisa mereviewnya di sini untuk melepas (*release*) atau menghapusnya.
* **User Whitelist / Blacklist:** Daftar pengecualian manual untuk email/domain tertentu yang ingin selalu diizinkan atau selalu diblokir.
* **Tracking Center:** Menu paling penting yang sering kita buka kemarin untuk melacak riwayat perjalanan setiap email (apakah *delivered*, *deferred*, atau *bounced* beserta alasan error-nya).
* **Queues:** Tempat melihat antrean email yang sedang tertunda (*deferred*) atau belum terkirim.

### 5. Statistics (Laporan & Grafik)

* **Fungsi:** Menyajikan data analitik lengkap dalam bentuk tabel atau grafik.
* **Isi:** Menampilkan statistik skor spam, distribusi trafik per jam, laporan berdasarkan domain, pengirim (*sender*), penerima (*receiver*), hingga kontak yang paling sering berkomunikasi.

---

Dari semua kelompok menu di atas, **Tracking Center** dan **Mail Proxy** adalah dua area yang paling sering Anda gunakan sebagai admin.

Ada menu spesifik di gambar tersebut yang ingin Anda bedah lebih dalam cara kerjanya?
