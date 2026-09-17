# Konfigurasi: High Availability (HA) - Skenario Failback

**Tujuan:**
Mengatur mesin *Cluster Resource Scheduler* (CRS) agar Virtual Machine (VM) yang mengungsi akibat kegagalan *server* (*node* mati) akan otomatis ditarik kembali ke *server* fisik asalnya (rumah utama) ketika *server* tersebut telah diperbaiki dan menyala normal.

**Prasyarat Wajib:**
Seluruh komponen *Virtual Disk* (OS, data, ISO yang di-mount) milik VM target wajib tersimpan 100% di dalam *Shared Storage* (contoh: NAS/SAN). Jika ada komponen yang tersisa di penyimpanan lokal (seperti `local` atau `local-lvm`), proses migrasi HA akan gagal (*error migration aborted*).

---

### Tahap 1: Mendaftarkan VM ke Pengawasan HA Utama

Tahap ini bertujuan untuk menyerahkan kendali perpindahan VM kepada sistem *High Availability* Proxmox.

1. Pada panel kiri (*Server View*), klik menu **Datacenter**.
2. Luaskan menu **HA** dengan mengklik tanda panah di sebelahnya, lalu klik menu **HA** (induk) tersebut untuk membuka dasbor Status.
3. Perhatikan area bawah layar pada bagian tabel **Resources**. Klik tombol **Add**.
4. Sebuah jendela pengaturan (*Add: Resource: Container/Virtual Machine*) akan muncul. Isi parameter berikut:
* **VM:** Buka *dropdown* dan pilih ID VM target (contoh: `100`).
* **Max. Restart:** Isi dengan angka `1`. (Berfungsi membatasi Proxmox agar hanya mencoba me-*restart* VM di tempat sebanyak 1 kali jika terjadi *error/hang* pada *guest* OS. Jika tetap gagal, VM langsung diungsikan).
* **Max. Relocate:** Isi dengan angka `1`. (Berfungsi membatasi lompatan pengungsian VM maksimal 1 kali antar-node untuk mencegah sistem *looping error*).
* **Failback:** Wajib **DICENTANG**.
* *Jika dicentang:* Mengaktifkan izin sistem untuk menarik pulang VM ke *node* asalnya nanti.
* *Jika tidak dicentang:* VM akan nyangkut dan menetap selamanya di *node* pengungsian meskipun *node* asalnya sudah diperbaiki.


* **Auto-Rebalance:** Wajib **DICENTANG**.
* *Jika dicentang:* Mengaktifkan sensor pemantauan beban dan status *node* secara *real-time*. Ini krusial agar sistem menyadari momen ketika *node* utama sudah hidup kembali.
* *Jika tidak dicentang:* Sensor akan pasif saat kondisi *server* sedang normal, sehingga jadwal "pulang kampung" bisa tertunda atau gagal dieksekusi.




5. Klik tombol **Add** berwarna biru.

---

### Tahap 2: Menetapkan "Rumah Utama" dengan Node Affinity Rules

Tahap ini bertujuan untuk menentukan target kepulangan VM. Pastikan lu melakukan ini di tabel paling atas (**HA Node Affinity Rules**).

1. Masih di dalam menu **Datacenter** -> **HA**, pilih sub-menu **Affinity Rules**.
2. Pada tabel atas (**HA Node Affinity Rules**), klik tombol **Add**.
3. Di jendela *Add: HA Node Affinity* (`image_544c61.png`), atur parameter berikut:
* **Enable:** Wajib **DICENTANG**.
* *Jika dicentang:* Aturan prioritas yang akan kita buat di bawah ini aktif dan langsung dieksekusi oleh sistem.
* *Jika tidak dicentang:* Aturan ini sekadar disimpan sebagai *draft* mati dan sistem akan mengabaikannya.


* **HA Resources:** Pilih **ID VM** target lu (contoh: `100`).
* **Strict:** Pastikan **KOSONG** (jangan dicentang).
* *Jika tidak dicentang:* Mengizinkan VM kabur/mengungsi ke *node* lain (misal `node2`) saat `node4` meledak.
* *Jika dicentang (AWAS):* Aturan berubah menjadi "harga mati". VM 100 diharamkan menyentuh *node* selain `node4`. Konsekuensinya, jika `node4` mati, VM 100 dibiarkan ikut mati total dan fitur *failover* (pengungsian) dibatalkan.


* **Affinity:** Buka *dropdown* dan pilih **Prefer Nodes (positive)** sesuai `image_545020.png`.
* *Prefer Nodes (positive):* Bersifat seperti magnet. Sistem akan **menarik/memprioritaskan** VM untuk selalu berusaha hidup di *node* yang kita tandai di bawah. Ini adalah opsi yang benar untuk skenario "pulang kampung".
* *Avoid Nodes (negative):* Bersifat seperti penolak. Sistem akan **menjauhkan/melarang** VM mendekati *node* yang kita tandai di bawah (biasanya dipakai jika ada satu *server* fisik yang rentan rusak/kepanasan).


* **Comment:** (Opsional) Isi dengan catatan pribadi agar teknisi lain paham tujuan aturan ini. Contoh: `Aturan Pulang Kampung VM Abuntu`.


4. Pada tabel daftar *Node* di bagian bawah jendela:
* **Centang kotak (Checkbox):** Centang *node* yang akan dijadikan rumah utama (misal: `node4`). Biarkan `node2` dan `node3` kosong.
* **Priority:** Pada baris `node4`, klik panah atas di kolom **Priority** hingga muncul angka **1**.
* *Penjelasan Angka:* Semakin tinggi angkanya, semakin tinggi status "VIP"-nya. Jika suatu saat lu mencentang dua *node* (misalnya `node4` prioritas 2, dan `node3` prioritas 1), sistem akan tahu: "Rumah utama adalah `node4`. Tapi kalau `node4` hancur, usahakan mengungsi ke `node3` dulu. `node2` adalah pilihan paling akhir."
* Karena lu cuma butuh menunjuk satu rumah utama, cukup berikan angka **1** pada `node4`.




5. Klik tombol **Add**.

---

**Skenario Berhasil:**
Matikan paksa daya listrik `node4`. VM 100 akan langsung bermigrasi dan menyala di `node2` atau `node3` secara mandiri. Begitu listrik `node4` dipulihkan dan berstatus *Online* di dasbor Proxmox, sistem akan langsung mengeksekusi *live migration* untuk menarik pulang VM 100 kembali ke `node4` tanpa sentuhan manual sedikit pun.
