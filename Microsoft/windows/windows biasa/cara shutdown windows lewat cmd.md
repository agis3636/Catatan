Kalau laptopnya sudah telanjur masuk ke layar setup (Windows Out of Box Experience/OOBE) dan kamu ingin mematikannya tanpa harus mengisi data user (biar nanti usernya sendiri yang set-up dari awal), ini cara paling aman:

### 1. Cara Paling "Bersih" (Keyboard Shortcut)
Cara ini paling direkomendasikan karena mematikan sistem secara normal tanpa merusak file sistem:
* Tekan tombol **`Shift + F10`** di keyboard.
* Akan muncul kotak hitam (Command Prompt).
* Ketik perintah berikut: `shutdown /s /t 0`
* Tekan **Enter**. Laptop akan mati total secara normal.

### 2. Cara Pakai Tombol (Hard Shutdown)
Kalau cara di atas tidak bisa, terpaksa pakai cara manual:
* Tekan dan **tahan tombol Power** (jangan cuma dipencet sekali).
* Tahan terus sekitar **5-10 detik** sampai layar mati total dan lampu indikator padam.
* **Risikonya:** Kadang bisa bikin error kecil di sistem karena mati mendadak, tapi kalau masih di layar awal setup biasanya aman.

### 3. Biarkan di Layar Setup
Sebenarnya kalau kamu biarkan saja dalam kondisi nyala, lalu kamu tutup layarnya (sleep), baterainya akan habis sendiri. Tapi kalau buat dikasih ke user, lebih baik pakai **Cara nomor 1** tadi.

**Catatan buat User:**
Nanti pas usernya nyalain lagi, dia bakal langsung masuk ke layar setup awal lagi (pilih bahasa, region, dll) seolah-olah laptopnya baru pertama kali dinyalain. Jadi tetap terasa pengalaman "Laptop Baru"-nya.

Ada kendala pas tekan Shift + F10 nya? (Kadang di beberapa laptop harus tekan `Fn + Shift + F10`).








---

---

---










# Perbedaannya Command Shutdown nya ada pada parameter **`/f`** yang artinya **Force** (Paksa). Secara teknis, begini bedanya:

### 1. `shutdown /s /t 0` (Normal)
* **Proses:** Windows akan mengirim perintah ke semua aplikasi yang sedang berjalan untuk menutup diri secara sopan (*graceful*).
* **Kendala:** Jika ada aplikasi yang "nyangkut" atau ada dokumen yang belum di-save (muncul pesan *"Save your work?"*), proses shutdown bisa **berhenti atau batal** karena Windows menunggu respon dari aplikasi tersebut.

### 2. `shutdown /s /f /t 0` (Paksa)
* **Proses:** Windows tidak akan bertanya lagi. Semua aplikasi yang masih terbuka, baik yang *hang* maupun yang sedang meminta *save*, akan **dimatikan paksa** seketika.
* **Kegunaan:** Sangat efektif jika kamu sedang di layar setup (OOBE) atau saat ada proses latar belakang yang menghalangi laptop untuk benar-benar mati.

---

### Penjelasan Parameter:
* **`/s` (Shutdown):** Memerintahkan komputer untuk mati (bukan restart).
* **`/f` (Force):** Memaksa penutupan aplikasi tanpa peringatan ke user.
* **`/t 0` (Time):** Menentukan waktu tunggu dalam hitungan detik. Angka **0** berarti eksekusi dilakukan detik itu juga tanpa jeda.

### Mana yang sebaiknya dipakai buat "User"?
Untuk kasus laptop baru yang mau dikasih ke orang, gunakan **`shutdown /s /f /t 0`**. 

**Alasannya:** Kadang di balik layar setup Windows, ada proses *update* atau layanan sistem yang sedang berjalan. Dengan menambahkan `/f`, kamu menjamin laptop benar-benar melakukan urutan mematikan mesin tanpa terinterupsi oleh proses-proses "gaib" itu.

**Ringkasnya:** * Tanpa `/f` = "Tolong matikan ya, kalau bisa." 
* Pakai `/f` = "Mati sekarang, jangan banyak tanya!"
