Secara praktik di lapangan, HA Proxmox itu bukan cuma sekadar "kalau mati, pindah". Kita punya kendali penuh untuk mengatur **skenario perilakunya**.

Berikut adalah 4 praktik utamanya:

**1. Aturan Pulang Kampung (Failback)**

* **Praktiknya:** Menentukan apakah VM harus kembali ke *node* asalnya setelah *node* tersebut hidup lagi.
* **Contoh:** Setelan *Failback* untuk VM 100 (abuntu) adalah **true**. Artinya, kalau `node4` mati dan VM 100 ngungsi ke `node3`, begitu `node4` selesai diperbaiki dan menyala, VM 100 akan otomatis "pulang" dipindahkan kembali ke `node4`.

**2. Batas Toleransi Error (Max Restart & Max Relocate)**

* **Praktiknya:** Mencegah Proxmox kehabisan tenaga hanya untuk mengurus 1 VM yang *error* atau korup.
* **Contoh:** Setelan *Max Restart* = 1 dan *Max Relocate* = 1. Kalau VM 100 *error* dan mati sendiri, Proxmox akan coba *restart* di tempat 1 kali. Kalau masih gagal, Proxmox akan pindahkan (*relocate*) ke *node* lain 1 kali. Kalau di *node* baru tetap gagal *booting*, Proxmox menyerah dan membiarkan VM itu mati agar sistem tidak *hang*.

**3. Pemisahan Beban (Anti-Affinity Rules)**

* **Praktiknya:** Mencegah dua VM "berat" atau VM kembar menumpuk di satu server fisik yang sama.
* **Contoh:** Punya VM Web Server Utama dan VM Web Server Cadangan. Bisa bikin aturan *Anti-Affinity*: "Kedua VM ini dilarang keras berjalan di *node* yang sama". Jadi kalau ada pemindahan otomatis, Proxmox akan memastikan mereka berdua selalu terpisah. Kalau satu *node* meledak, kita nggak akan kehilangan dua-duanya sekaligus.

**4. Prioritas Pengungsian (Node Affinity Rules)**

* **Praktiknya:** Mengarahkan VM spesifik ke server fisik yang speknya paling cocok.
* **Contoh:** Katakanlah `node2` dan `node3` RAM-nya cuma 16GB, tapi `node4` RAM-nya 64GB. Kita punya VM Database raksasa. Kita bisa bikin aturan preferensi: "Kalau VM ini harus pindah, prioritaskan ngungsi ke `node4`. Jangan ke `node2` atau `node3` kecuali terpaksa."

