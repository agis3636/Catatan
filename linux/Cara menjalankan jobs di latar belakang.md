# Cara menjalankan jobs di latar belakang dengan terminal linux

Berikut rincian fungsinya:

---

### 1. pakai `CTRL+Z` dan `bg`?
Biasanya kalau Abang jalankan `python3 server.py`, terminalnya jadi "kekunci" (nggak bisa ngetik apa-apa lagi kecuali matiin servernya).
* **`CTRL+Z`**: Fungsinya buat mem-*pause* (menghentikan sementara) si Python.
* **`bg`**: Fungsinya buat menyuruh si Python jalan lagi tapi di **latar belakang** (background).
* **Hasilnya**: Server Python tetap nyala, tapi Abang tetap bisa ngetik perintah lain di terminal yang sama. Kayak dengerin Spotify sambil buka WhatsApp di HP.



### 2. Kenapa harus tes pakai `curl`?
`curl` itu ibarat browser tapi versi teks. Perintah ini gunanya untuk **pura-pura jadi `vl-srv2`**. 
* Daripada Abang bolak-balik pindah ke layar `vl-srv2` cuma buat ngetes (yang ternyata sering gagal tadi), mending kita tes langsung dari dalam rumahnya sendiri (si LXC itu).
* **`-X POST`**: Ini bagian paling krusial. Tadi kan instalasi Abang **Aborted** karena si Python nggak mau nerima perintah **POST**. Nah, perintah `curl` ini buat ngetes apakah si Python yang baru sudah pintar dan mau nerima perintah POST atau belum.


---
---
---


# Kalau di-`bg` (background), si Python itu sekarang lari "di belakang layar". Ada tiga cara buat nangkep dan matiinnya, pilih yang menurut Abang paling gampang:

### Cara 1: Balikin ke Depan (`fg`)
Kalau Abang masih di sesi terminal yang sama (nggak ganti tab/nggak keluar SSH):
1. Ketik: **`jobs`** (Ini buat lihat daftar kerjaan di background).
2. Nanti muncul angka, misal `[1] Running`.
3. Ketik: **`fg %1`** (Angkanya sesuaikan sama yang muncul di `jobs`).
4. Sekarang servernya muncul lagi di layar, tinggal tekan **`CTRL + C`** buat matiin.

### Cara 2: "Tembak" Pakai Port (Paling Ampuh)
Karena kita tahu dia pakai port **8000**, kita hajar saja langsung pintunya:
```bash
fuser -k 8000/tcp
```
*Artinya: "Woi Linux, bunuh (kill) siapa pun yang lagi pakai port 8000/tcp!"*

### Cara 3: Cari ID-nya Terus Kill
Kalau `fuser` nggak ada, pakai cara manual cari "KTP" (PID) prosesnya:
1. Cari tahu ID-nya:
   ```bash
   ss -antlp | grep 8000
   ```
2. Lihat di bagian paling kanan, ada tulisan `pid=1234`. Angka **1234** itu ID-nya.
3. Matiin paksa:
   ```bash
   kill -9 1234
   ```
   *(Ganti 1234 dengan angka yang muncul di layar Abang)*.

---

**Saran saya:** Pakai **Cara 2** saja, itu paling cepat dan nggak ribet nyari angka-angka.

Setelah dimatiin, jangan lupa cek lagi pakai `ss -antlp | grep 8000`. Kalau sudah kosong melompong, baru jalankan lagi `python3 server.py` dengan kode yang sudah diperbaiki tadi.

Gimana? Sudah mati belum "hantu" Python-nya?
