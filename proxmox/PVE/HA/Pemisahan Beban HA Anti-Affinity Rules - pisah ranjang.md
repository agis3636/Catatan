Mantap! Sesuai gambar `image_55bf07.png`, sekarang kita punya **CT 100** dan **CT 101** yang sama-sama nongkrong di `node4`.

Karena tujuannya mau ngetes aturan pisah ranjang (**Anti-Affinity**), kita bakal mendaftarkan kedua kontainer ini ke dalam pengawasan HA, lalu membuat aturan agar mereka berdua **haram** berjalan di *node* fisik yang sama.

Mari kita eksekusi langkah demi langkah:

### Langkah 1: Daftarkan Kedua Container ke HA Utama

1. Klik menu **Datacenter** di paling atas panel kiri.
2. Pilih sub-menu **HA** (induk).
3. Di tabel bagian bawah (**Resources**), klik **Add**.
* Masukkan **CT 100**, set *Max. Restart: 1*, *Max. Relocate: 1*, centang **Failback** dan **Auto-Rebalance**, lalu klik **Add**.


4. Ulangi langkah yang sama untuk mendaftarkan **CT 101** ke HA (tambahkan dengan setelan parameter yang identik).

*Catatan:* Sekarang kedua kontainer sudah diawasi oleh HA. Kalau `node4` mati, keduanya pasti bakal diselamatkan. Tapi kalau dibiarkan *default*, Proxmox bisa saja menaruh mereka berdua di `node2` secara bersamaan. Di sinilah aturan Anti-Affinity kita perlukan.

### Langkah 2: Buat Aturan Anti-Affinity (Pemisahan Beban)

1. Masih di dalam menu **Datacenter -> HA**, pindah ke tab **Affinity Rules**.
2. Fokus ke tabel bagian **BAWAH** yang namanya **HA Resource Affinity Rules** (ini khusus buat ngatur hubungan antar-VM/Container).
3. Klik tombol **Add** di tabel bawah tersebut.
4. Pada jendela yang muncul, atur parameternya:
* **Enable:** Centang (aktif).
* **Affinity:** Buka *dropdown* dan pilih **Avoid Resources (negative)**. *(Artinya: Tolak-menolak / jangan biarkan mereka seruangan).*
* **HA Resources:** Centang **kedua kontainer** sekaligus (`ct:100` dan `ct:101`).
* **Comment:** Isi catatan bebas, misal: `pisah-ct100-ct101`.


5. Klik tombol **Add**.

---

### Cara Pengujiannya (Uji Coba Lapangan)

Sekarang aturan Anti-Affinity sudah aktif mengunci mereka berdua. Mari kita buktikan:

1. Pastikan saat ini CT 100 dan CT 101 sama-sama menyala di `node4`.
2. Matikan paksa `node4` (atau cabut simulasinya).
3. Amati ke mana mereka mengungsi.

Karena ada aturan *Avoid Resources*, Proxmox **tidak boleh** menaruh CT 100 dan CT 101 di *node* pengungsian yang sama. Satu kontainer harus mengungsi ke `node2`, dan kontainer satunya lagi wajib lari ke `node3`.

Coba laksanakan dan lihat hasilnya di layar *server* lu!
