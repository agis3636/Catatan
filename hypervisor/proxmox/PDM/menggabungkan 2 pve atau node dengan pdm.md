PDM memang diciptakan khusus untuk mengatasi kelemahan sistem cluster. Dengan PDM, kamu bisa mengelola banyak server PVE dari satu layar *tanpa* perlu menggabungkan mereka. 

Keuntungannya kalau pakai PDM:
* **Server nggak harus kosong:** Node yang sudah ada VM-nya bisa langsung ditambahkan.
* **Bebas dari syarat jaringan ketat:** Nggak perlu pusing mikirin *latency* jaringan atau masalah *quorum* (kalau satu server mati, server lain nggak akan ikutan *error* atau jadi *read-only*).
* **Bisa beda lokasi:** Server di Jakarta dan server di luar negeri bisa digabung di satu *dashboard*.

### Cara Koneksikan PVE ke Proxmox Datacenter Manager (PDM)

Karena sistemnya bukan cluster, caranya cuma mendaftarkan masing-masing PVE sebagai "Remote" di PDM kamu:

1. Buka *web interface* **Proxmox Datacenter Manager** kamu.
2. Di menu sebelah kiri, cari bagian **Remotes** lalu klik tombol **Add Proxmox VE**.
3. Masukkan **IP Address** atau Hostname dari server PVE kamu.
4. Masukkan **Fingerprint** dari node PVE tersebut. 
   *(Cara lihatnya: Buka PVE kamu > Datacenter > [Nama Node] > System > Certificates > klik dua kali pada `pve-ssl.pem`)*.
5. Masukkan **Remote ID** (nama bebas untuk menandai server ini di PDM).
6. Masukkan detail *login*, sebaiknya menggunakan **API Token** yang sudah kamu buat di PVE (bisa juga pakai *username* dan *password* root, tapi token lebih aman).
7. Klik tombol untuk memverifikasi dan menyambungkan.

---

Mendaftarkan **User** atau **API Token** dari server PVE tersebut ke PDM karena user api tidak memiliki izin yang cukup untuk membaca data dan status sistem (dalam hal ini `Sys.Audit`) di server PVE-nya.

kamu harus memberikan akses ke *user* atau *token* tersebut langsung dari server PVE-nya. Ikuti langkah ini:

1. Buka *web interface* server **Proxmox VE (PVE)** kamu (buka server PVE-nya ya, bukan PDM).
2. Di menu paling kiri, klik **Datacenter**.
3. Pilih menu **Permissions** (ada di bawah grup *Permissions*).
4. Di bagian atas, klik tombol **Add**, lalu pilih:
   * **User Permission:** Jika tadi di PDM kamu *login* menggunakan *username* dan *password*.
   * **API Token Permission:** Jika tadi di PDM kamu menggunakan API Token.
5. Pada kolom **Path**, ketik garis miring `/` (ini fungsinya agar akses diberikan ke seluruh sistem).
6. Pilih *User* atau *API Token* yang kamu gunakan untuk koneksi ke PDM tadi.
7. Pada bagian **Role**, pilih **Administrator** (agar PDM bisa mengelola PVE sepenuhnya) atau minimal **PVEAuditor** (jika PDM hanya digunakan untuk memantau/melihat status saja).
8. Klik **Add**.

Setelah hak aksesnya ditambahkan, PDM biasanya akan langsung mendeteksi perubahannya dan grafik CPU/RAM yang tadinya *error* akan mulai muncul.

---

Ulangi langkah yang sama untuk PVE yang kedua. Nanti keduanya akan langsung muncul di *dashboard* PDM kamu.

Sekadar memastikan, kamu sudah *install* Proxmox Datacenter Manager (PDM) di server atau VM terpisah kan? Karena PDM ini sistem operasinya (ISO) beda dengan Proxmox VE biasa.
