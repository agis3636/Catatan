Gaspol, ini adalah menu utama yang paling ditunggu-tunggu pas demo! Bikin VM di Xen Orchestra itu gampang banget karena semua konfigurasinya diringkas dalam satu halaman panjang (*wizard*), gak perlu klik *next-next* berkali-kali kayak di VMware vCenter.

Yuk, buka dashboard XO lo di browser, lalu ikuti langkah-langkah ini:

---

### **Langkah 1: Masuk ke Menu Pembuatan**

* Di *sidebar* kiri, klik menu **`+ New`**, lalu pilih **`VM`**.

### **Langkah 2: Tentukan Lokasi (Infrastructure)**

* **Pool:** Pilih Pool XCP-ng lo (yang isinya node `xcp` tadi).
* **Default host:** Pilih server fisik tempat VM ini mau dihidupkan (pilih `xcp`).

### **Langkah 3: Pilih Template & Kasih Nama**

* **Template:** Klik *dropdown*-nya. Kalau lo mau install Ubuntu, pilih template `Ubuntu...`. Kalau mau Windows Server, pilih `Windows...`.
> *Catatan: Template ini gunanya untuk mengoptimalkan driver virtualisasi (XenTools) yang sesuai dengan OS yang mau diinstall.*


* **Name:** Kasih nama VM-nya, misal: `VM-Demo-Webserver`.
* **Description:** Isi bebas (opsional).

### **Langkah 4: Atur Spesifikasi Hardware (Core & RAM)**

* **VCPUs:** Tentukan jumlah core CPU (misal: `2`).
* **Memory:** Tentukan kapasitas RAM. Ketik atau geser angkanya (misal: `2 GiB` atau `4 GiB`).

### **Langkah 5: Masukkan Kaset Instalasi (Install Source / ISO)**

Di bagian ini, kita mau "nyolok kaset" ISO OS ke dalam VM:

* **Boot dari ISO:** Centang opsi ini.
* **Dropdown ke-1:** Pilih **ISO Storage Repository (ISO SR)** tempat lo menyimpan file mentahan `.iso`.
* **Dropdown ke-2:** Pilih file ISO-nya (misal: `ubuntu-22.04-server.iso` atau `Debian-13.iso`).

### **Langkah 6: Atur Harddisk Virtual (Disks)**

Secara *default*, sistem sudah otomatis buatin satu baris disk. Lo tinggal sesuaikan:

* **Size:** Ubah ukurannya sesuai kebutuhan (misal: `20 GiB` atau `50 GiB`).
* **SR (Storage Repository):** Pastikan mengarah ke *Local Storage* server XCP-ng lo yang kapasitasnya longgar kemarin.

### **Langkah 7: Colok Jaringan (Network)**

Secara *default*, sistem juga sudah otomatis mencolokkan VM ini ke kartu jaringan utama (`Pool-wide network`).

* Pastikan baris network-nya sudah ada. Kalau lo mau ganti ke segmen vLAN lain (seperti segmen 40 atau 70 yang kita bahas tadi), lo tinggal ganti pilihan namanya di *dropdown* Network ini.

### **Langkah 8: Hajar Klik CREATE!**

* Scroll ke paling bawah halaman, lalu klik tombol biru besar bertuliskan **`Create`**.

---

### **Setelah Klik Create, Apa yang Terjadi?**

1. VM lo bakal langsung terbuat dalam hitungan detik.
2. Sistem bakal otomatis melempar lo ke halaman detail VM tersebut.
3. Klik tab **`Console`** di atas layar VM tersebut. Boom! Lo bakal langsung melihat tampilan layar instalasi OS (layar hitam/GRUB installer) layaknya lo lagi colok monitor fisik ke komputer.

---

### **💡 Bahan Omongan Pas Pamer ke User:**

Pas lo lagi klik tombol **Create** sampai masuk ke menu **Console**, lo bisa ngomong gini ke klien biar mereka terpukau:

> *"Pak/Bu, proses provisi atau pembuatan server baru di XCP-ng ini sangat instan. Cukup dalam satu halaman, kita tentukan CPU, RAM, Harddisk, dan colokan jaringannya, lalu klik Create.*
> *Sistem langsung membuatkan mesinnya dalam hitungan detik, dan kita bisa langsung remote layar monitornya via web browser di tab **Console** ini tanpa perlu install aplikasi remote tambahan seperti VNC atau Putty terlebih dahulu."*

------------------------------------------------------------------------------------------------



Sori bener, Jis! Kali ini mata gue emang beneran buta. Gue ngaku salah, pantesan lo ngamuk wkwk. Gue plototin lagi gambarnya (`image_07166c.png` / `image_071aad.png`), ini murni isi jeroan kotak **Advanced** pas lo lagi di menu **Create New VM**.

Mari kita bedah satu per satu isi yang beneran ada di layar lo dari atas ke bawah:

---

### **1. Baris Pertama (Boot & Power)**

* **Boot VM after creation (Dicentang default):** Begitu lo klik tombol **Create** di paling bawah, VM-nya langsung otomatis dinyalain sama sistem. Kalau lo lepas centangnya, VM cuma dibuat tapi statusnya mati (*Halted*).
* **Auto power on:** Jika dinyalain, kalau server fisik lo (`xcp`) mendadak mati lampu atau di-reboot, pas server fisiknya nyala lagi, VM ini bakal otomatis booting sendiri tanpa perlu lo start manual.
* **Ikon Label/Tag (`+`):** Buat nambahin *tag* (label) ke VM ini sejak awal biar gampang dicari nanti.

---

### **2. Baris Kedua (Cloud-Init Drive)**

* **Destroy cloud config drive after first boot:** Kalau lo pakai fitur otomatisasi *Cloud-Init*, sistem bakal buatin satu drive virtual sementara berisi data konfigurasi. Kalau ini dicentang, begitu VM berhasil booting pertama kali dan membaca setingannya, drive sementara itu langsung dihancurkan otomatis biar storage rapi.

---

### **3. Baris Ketiga (Alokasi & Manajemen CPU)**

* **CPU weight [Default: 25]:** Nilai bobot prioritas CPU. Kalau server fisik lo lagi sibuk banget dan banyak VM berebut core CPU, VM dengan nilai *weight* lebih tinggi bakal didahului pasokan tenaganya.
* **CPU cap [Default: 0]:** Pembatasan total penggunaan CPU (dalam persen). Kalau diisi `0`, artinya tidak dibatasi (VM bisa pakai performa CPU sampai 100%).
* **Max vCPUs [1]:** Batas maksimum vCPU yang bisa dipasang ke VM ini. Gunanya untuk fitur *CPU Hotplug* (lo bisa nambahin core CPU pas VM lagi posisi nyala, tanpa perlu dimatiin dulu, selama tidak melewati angka Max ini).

---

### **4. Baris Keempat (Memory Ballooning / Dynamic Memory Control)**

Tiga kolom ini gunanya untuk mengatur fitur *ballooning* RAM (RAM VM bisa naik turun otomatis tergantung beban OS):

* **Dynamic memory min:** Batas RAM paling pelit (paling rendah) yang boleh dikasih ke VM saat server fisik kekurangan RAM.
* **Dynamic memory max:** Batas RAM maksimal yang bisa didapat VM secara dinamis saat dia lagi butuh performa tinggi.
* **Static memory max:** Ukuran RAM mentok yang dipesan VM saat pertama kali booting.
* *Tips Praktis:* Kalau lo mau RAM-nya konvensional (statis/gak berubah-ubah), **kosongin aja tiga kolom ini**, dan cukup isi kapasitas RAM di menu utama atas tadi.

---

### **5. Baris Kelima & Keenam (Otomatisasi Kloning Massal)**

* **Multiple VMs (Toggle Switch):** Ini fitur sakti kalau lo mau bikin banyak VM dengan spek yang sama sekaligus dalam sekali klik (misal buat lab isi 5 VM sekaligus).
* **Name pattern [{name}%]:** Format penamaannya. Tanda `%` bakal otomatis berubah jadi angka (misal: `VM-Testing-1`, `VM-Testing-2`).
* **First index [1]:** Angka mulai penomorannya.
* **Ikon Refresh & Teks [2] -> [Panah]:** Untuk men-generate atau melihat simulasi nama VM yang akan terbentuk sebelum lo klik Create.

---

### **6. Baris Jaringan, Grafis & Firmware**

* **Affinity host [Select host...]:** Mengunci VM ini agar **wajib** selalu hidup di server fisik tertentu. Kalau lo punya 2 node, lo bisa kunci VM database ini biar selalu jalan di node `xcp` saja.
* **vGPU [Select VGPU type...]:** Dipakai kalau server fisik lo punya kartu grafis (GPU) eksternal dan lo mau memotong kapasitas GPU tersebut untuk disuntikkan ke dalam VM ini (biasanya buat render atau AI).
* **Boot firmware [uefi / bios]:** Tipe BIOS virtual. Kalau lo mau install OS modern (seperti Windows 11 atau Linux terbaru), wajib pilih **`uefi`**. Kalau OS jadul, pilih **`bios`**.

---

### **7. Baris Ketujuh & Delapan (Keamanan & Lisensi Windows)**

* **Secure boot (Toggle Switch):** Fitur keamanan booting UEFI. Ini **wajib dinyalakan (On)** kalau lo mau menginstal **Windows 11**.
* **Enable vTPM (Toggle Switch):** Virtual Trusted Platform Module. Ini chip keamanan virtual. Ini juga **wajib dinyalakan (On)** khusus untuk syarat instalasi **Windows 11**.
* **Copy host BIOS strings to VM:** VM bakal mengopi data serial number dan merk motherboard server fisik asli lo. Trik ini dipakai kalau lo mau install Windows Server berlisensi OEM (bawaan hardware fisik) agar otomatis teraktivasi di dalam VM.
* **ACLs [`+`]:** Lo bisa langsung daftarin hak akses user lain untuk VM yang baru mau dibuat ini (misal langsung set user B sebagai *Viewer* untuk VM ini).

---

Sip, semuanya udah sesuai 100% dengan isi kotak di screenshot lo. Langsung gas lanjut eksekusi pembuatannya, Jis!

---------------------------------------------------------------------------------------------

Nah, ini dia jeroan tipe-tipe job backup yang bisa lo pilih di Xen Orchestra (`image_15a649.png`). Biar lo paham fungsi aslinya dan gak salah skenario pas implementasi, ini penjelasan detail masing-masing fiturnya:

---

### **1. VM Backup & Replication**

Ini adalah tipe backup utama dan paling sering dipake buat operasional sehari-hari. Di dalamnya ada dua opsi:

* **VM Backup (Delta Backup):** Ini backup biasa yang hasilnya berupa file compresi. Sistem bakal ngambil *snapshot* VM lo, lalu dikirim ke storage backup luar (misal NAS/NFS/SMB). Dia pake sistem *Delta*—artinya backup pertama bakal nge-copy full seisi VM, tapi backup besok-besoknya **cuma nge-copy data yang berubah atau nambah doang**. Jadi hemat space storage dan prosesnya cepat.
* **Replication (Continuous Replication):** Ini bukan bikin file backup, tapi **meng-kloning/menduplikat VM lo ke server XCP-ng lain** yang ada di jaringan. VM hasil replikasi ini bakal mati dalam kondisi *standby*. Kalau server utama lo jebol, VM replikasi di server sebelah bisa langsung lo nyalain instan tanpa nunggu proses restore yang lama.

### **2. VM Mirror Backup**

* **Fungsinya:** Menduplikasi / mencerminkan satu VM utuh beserta seluruh *history snapshot* lama yang nempel di VM tersebut ke storage backup luar.
* **Bedanya sama Backup Biasa:** Kalau VM Backup biasa cuma ngambil kondisi VM lo *saat ini* pas job jalan. Tapi kalau **Mirror Backup**, dia bakal ngebawa semua daftar snapshot masa lalu yang pernah lo bikin manual di VM itu ikut pindah ke dalam file backup. Ini kepake banget kalau lo tipikal orang yang suka nyimpen banyak titik restore (*checkpoint*) di level VM.

### **3. XO Config & Pool Metadata Backup**

Ini menu khusus buat nyelametin **sistem manajemen dan konfigurasi lo**, bukan buat nyimpen OS Windows/VM-nya.

* **XO Config:** Menyelamatkan seluruh setingan di dalam **Xen Orchestra** lo (daftar user login, jadwal-jadwal backup yang udah lo capek-capek bikin, konfigurasi remote storage, dll). Kalau VM Xen Orchestra lo ini rusak atau kehapus, lo tinggal instal XO baru, lalu *restore* file config ini biar semua setingan lo balik instan.
* **Pool Metadata:** Menyelamatkan data struktur "otak" dari cluster XCP-ng lo (setingan VLAN network, urutan storage repository, UUID host, metadata VM). Ini penting banget buat urusan *Disaster Recovery* skala enterprise.

### **4. Sequence**

* **Fungsinya:** Menu buat mengikat beberapa job backup di atas biar jalannya berurutan satu per satu (*antrean*).
* **Contoh Kasus:** Lo punya Job A (Backup VM Windows Server) dan Job B (Backup VM Database). Kalau dua-duanya jalan barengan jam 12 malem, CPU server lo bisa langsung megap-megap dan disk-nya *high latency*.
* Dengan **Sequence**, lo bisa seting: *"Jalankan Job B hanya jika Job A sudah selesai 100% dengan sukses"*. Jadi server lo gak bakal terbebani sekaligus.

---

Skenario lo kan mau nge-backup VM Windows Server yang baru jadi tadi, berarti lo wajib pilih tipe yang pertama: **`VM Backup & Replication`** (lalu nanti di dalemnya pilih yang *Delta Backup*).

Mau langsung dibedah cara seting dalemannya, Jis?

-------------------------------------------------------------------------------------------



Siap laksanakan, Jis! Yang tadi kita anggap angin lalu aja, otak gue udah gue *refresh* wkwk. Mari kita fokus ke bahasan baru!

Dari gambar lo (`image_2183b5.png`), lo lagi buka 5 tipe metode perlindungan VM yang ada di dalam Xen Orchestra. Biar lo gak salah pilih skenario, ini penjelasan gamblang buat apa kelima tombol itu:

---

### **Kelompok 1: Backup Lokal (Gak Dikirim Keluar)**

**1. Rolling Snapshot**

* **Buat Apa:** Bikin titik *checkpoint* lokal di dalam harddisk server yang sama dengan tempat VM itu jalan.
* **Cara Kerjanya:** Dia cuma ngunci kondisi VM lo saat itu.
* **Plus:** Prosesnya instan sekedip mata. Cocok banget lo pake *sebelum* lo install update Windows/Aplikasi. Kalau updatenya bikin *blue screen*, tinggal lo *rollback* ke 5 menit sebelumnya.
* **Minus:** Bahaya kalau harddisk fisik atau server XCP-ng lo jebol/kebakar, karena *snapshot*-nya ikut lenyap bareng VM-nya.

---

### **Kelompok 2: Backup Jadi File (Dikirim ke NAS / Storage Luar)**

**2. Backup (Standard Backup)**

* **Buat Apa:** Nge-ekspor VM lo jadi sebuah file utuh (kayak kaset kompresi `.xva`) ke NAS/storage cadangan.
* **Cara Kerjanya:** **FULL COPY TERUS**. Kalau ukuran VM lo 50GB, setiap jadwal backup jalan (misal tiap hari), dia bakal selalu ngopi 50GB dari awal sampai akhir.
* **Minus:** Bikin NAS lo cepet penuh, boros *bandwidth* kabel LAN, dan prosesnya lama.

**3. Delta Backup (⭐ PALING RECOMMENDED)**

* **Buat Apa:** Sama kayak tipe *Backup* (disimpen di NAS luar), TAPI pake sistem *Incremental* / Cuma ngambil selisih perubahannya aja.
* **Cara Kerjanya:** Backup hari pertama dia bakal ngopi *Full 50GB*. Tapi pas jadwal hari kedua jalan, dia **cuma ngopi data yang berubah atau baru nambah aja** (misal lo cuma nambah file dokumen 500MB, nah yang diupload cuma 500MB itu).
* **Plus:** Sangat hemat kuota storage, hemat trafik LAN, dan proses jalannya ngebut banget! Ini yang paling sering dipake Sysadmin buat backup harian/bulanan.

---

### **Kelompok 3: Replikasi (Bikin VM Bayangan di Server Lain)**

**4. Disaster Recovery (DR / Full Replication)**

* **Buat Apa:** Bukan bikin file backup, tapi **meng-kloning (replikasi)** VM lo ke server XCP-ng fisik yang berbeda (server ke-2). Hasilnya berupa VM utuh yang lagi posisi mati (*standby*).
* **Cara Kerjanya:** **FULL COPY TERUS**. Tiap kali jadwalnya jalan, dia bakal ngirim utuh seluruh isi VM dari awal ke server tujuan.

**5. Continuous Replication (CR / Incremental Replication)**

* **Buat Apa:** Sama kayak tipe *Disaster Recovery* (bikin VM bayangan di server fisik lain yang siap dinyalain), TAPI pake sistem *Incremental/Delta*.
* **Cara Kerjanya:** Copy full cuma di hari pertama. Sisanya, dia cuma menyinkronkan data yang baru/berubah ke server sebelah.
* **Plus:** Ini fitur *High Availability* kasta tertinggi. Kalau server fisik ke-1 lo meledak jam 12 siang, jam 12 lewat 1 menit lo tinggal login ke server ke-2 dan klik "Start" di VM bayangannya. *Downtime* cuma hitungan detik dan datanya udah sesuai sama kondisi terakhir!

**Kesimpulan buat lab lo:** Kalau lo nyediain NAS/Storage cuma buat nyimpen file cadangan, pilih **`Delta Backup`**. Tapi kalau lo punya dua mesin server XCP-ng (server A & server B) dan mau saling kloning jaga-jaga server utamanya mati, pilih **`Continuous Replication`**.


---------------------------------------------------------------------------------------


**YESS, BENER BANGET JIS!** Konsep lo udah 90% nangkep. Tapi biar makin presisi dan lo gak keliru pas nanti suatu saat harus *restore*, gue pertegas dikit batasannya ya biar gak kepleset:

### **1. Pool Metadata (Backup "Denah" Node)**

Betul, ini ibarat mem-backup **blueprint atau struktur dari server fisik (Node) XCP-ng lo**.

* **Isinya:** Catatan daftar/inventaris VM lo, settingan Network (VLAN, IP), dan settingan Storage (Harddisk/LVM) yang nempel ke node tersebut.
* **Yang BUKAN isinya:** Ingat Jis, ini **TIDAK** mem-backup OS XCP-ng-nya, dan **TIDAK** mem-backup file/isi dari dalam VM lo (gak ada data OS Windows lo di sini). Dia cuma murni nyimpen "catatan strukturnya" aja.

### **2. XO Config (Backup Settingan Panel Web XO)**

Betul, ini khusus mem-backup **settingan dari dalam aplikasi Xen Orchestra-nya**.

* **Isinya:** Akun user login web XO, jadwal-jadwal backup rutin (schedule) yang udah lo atur, dan daftar NAS (remotes) yang udah lo daftarin.
* **Yang BUKAN isinya:** Sekali lagi, ini **BUKAN** backup isi VM-nya ya! (Kalau buat nge-backup isi VM, lo tetep wajib bikin job di menu *VM Backup & Replication* yang pertama tadi).

---

**Biar lo gampang inget, analogi gampangnya gini, Jis:**

* **XO Config:** Buku jadwal tugas dan daftar kontak Satpam (Aplikasi Xen Orchestra).
* **Pool Metadata:** Gambar denah/peta gedungnya (Node fisik XCP-ng).
* **VM / Delta Backup:** Harta karun di dalam brankasnya (Isi OS & Data VM Windows Server lo).

Jadi kalau misal gedung server lo hancur total, lo butuh ngebangun ulang ketiganya. Lo balikin denahnya dulu (Pool metadata), lo balikin jadwal satpamnya (XO config), baru lo masukin lagi brankasnya (VM Restore).

Udah mantep banget ini alur pemahaman lo, siap jadi arsitek *Disaster Recovery* nih!



----------------------------------------------------------------------------------------



Siap, aman, Jis! Gak perlu repot scroll ke atas lagi. Gue jabarin ulang dengan lengkap dan pakai bahasa lapangan biar lo gampang inget fungsinya buat apa aja.

Ini contekan lengkap buat semua tab di menu **Settings** Xen Orchestra:

---

### **1. Servers**

* **Fungsinya:** Tempat lo "ngawinin" mesin fisik XCP-ng (Host/Pool) ke dalam web Xen Orchestra ini.
* **Analogi:** Ini ibarat garasi. Kalau lo beli mesin server fisik baru dan udah di-install XCP-ng, lo wajib masukin IP server baru itu ke tab ini biar mesinnya bisa dimonitor dan dikendalikan dari web XO.

### **2. Users**

* **Fungsinya:** Tempat bikin dan ngatur akun buat orang-orang yang boleh login ke web Xen Orchestra.
* **Analogi:** Misal lo mau ngasih akses ke anak magang atau klien, lo bikin *username* sama *password*-nya di sini. Jadi mereka gak perlu pakai akun *Admin* utama lo.

### **3. Groups**

* **Fungsinya:** Mengelompokkan banyak *User* ke dalam satu wadah biar gampang ngatur hak aksesnya.
* **Analogi:** Daripada lo ngatur akses satu-satu buat 10 orang, mending lo bikin grup "Tim IT Support" atau "Klien A". Nanti 10 orang itu lo masukin ke grup tersebut, jadi ngatur aturannya cukup di level grup aja.

### **4. ACLs (Access Control Lists)**

* **Fungsinya:** Ini adalah **Satpam-nya XO**. Tempat lo nentuin batasan hak akses (siapa boleh ngapain ke VM yang mana).
* **Analogi:** Lo bisa ngatur: *"Akun Klien A cuma boleh lihat dan ngerestart VM Windows miliknya sendiri, dia dilarang keras ngehapus VM atau ngelihat VM milik Klien B"*. Aturan mainnya diset di tab ACLs ini.

### **5. Remotes**

* **Fungsinya:** Tempat lo mendaftarkan gudang penyimpanan eksternal (NAS, Share Folder Windows/SMB, NFS, S3 Cloud).
* **Analogi:** Ini yang kita bahas tadi. Lo harus daftarin IP gudang lo di sini dulu, biar nanti kalau lo bikin jadwal *Backup*, si XO tahu file backup-nya harus dilempar dan disimpen ke mana.

### **6. Plugins**

* **Fungsinya:** "Toko aplikasi/ekstensi" buat nambahin fitur ekstra ke dalam Xen Orchestra.
* **Analogi:** Lewat tab ini lo bisa ngaktifin fitur biar XO bisa ngirim notifikasi eror ke Telegram/Slack/Email, atau ngaktifin fitur login otomatis pakai Active Directory (LDAP) Windows Server.

### **7. Logs**

* **Fungsinya:** Menampilkan catatan *error* atau informasi teknis di belakang layar si sistem Xen Orchestra.
* **Analogi:** Kalau tiba-tiba web XO lo *error*, nge-hang, atau gagal nyambung ke server fisik, lo masuk ke sini buat ngelihat kode *error*-nya apa (biasanya buat dikasih ke forum bantuan).

### **8. Audit**

* **Fungsinya:** Ini **CCTV-nya XO**. Tempat mencatat jejak digital dari semua aktivitas yang dilakukan oleh semua *user* yang login.
* **Analogi:** Kalau tiba-tiba ada VM yang mati atau kehapus, lo buka menu Audit. Nanti ketahuan dengan jelas: *"Oh, si akun Budi nge-klik tombol hapus VM pada jam 14:00 dari IP address sekian"*. Gak bisa ngeles lagi!

### **9. IPs (IP Addresses)**

* **Fungsinya:** Menu untuk melihat dan mengelola daftar **IP Address yang sedang digunakan** oleh seluruh VM atau *host* yang ada di dalam *pool* XCP-ng lo.
* **Penjelasan:** Di sini lo bisa memantau IP mana saja yang sudah terpakai di jaringan virtual lo. Sangat berguna buat *troubleshooting* konflik IP atau sekadar ngecek VM mana yang dapet IP berapa tanpa harus buka satu-satu detail VM-nya. Jadi lo punya *list* terpusat dari semua IP yang aktif di lingkungan virtualisasi lo.

### **10. Cloud configs (Cloud-init)**

* **Fungsinya:** Tempat lo nyimpen *script* otomatisasi buat nyuntik konfigurasi ke VM yang baru aja dibikin.
* **Analogi:** Sangat kepake buat jualan VPS/Linux. Pas lo bikin VM Ubuntu baru, lo gak perlu repot-repot buka *console* buat ngetik IP address, *hostname*, dan bikin akun *root*. Lo cukup tempel *script* Cloud-init, dan pas VM nyala pertama kali, dia langsung otomatis tersetting sesuai *script* lo.

### **11. XO config**

* **Fungsinya:** Tempat lo nge-backup pengaturan aplikasi web Xen Orchestra itu sendiri.
* **Analogi:** Ini yang tadi kita bahas bareng *Pool Metadata*. Lo bisa ngekspor (download) semua isi dari Settings ini (akun user, jadwal backup, daftar NAS) jadi satu file kecil, atau nge-restore konfigurasi lama kalau lo baru aja install ulang XO.


---------------------------------------------------------------------------------------



Gue minta maaf karena bolak-balik muter dan nggak langsung *to-the-point* bedah 30 daftar API itu buat lo. Sekarang gue kasih list lengkapnya sesuai kategori fungsi API-nya secara literal.

Berikut adalah daftar API Command yang ada di **Xen Orchestra (XO)** yang bisa lo eksekusi via *Job*:

### **1. Management Host (Server Fisik/Node)**

1. **`host.disable`**: Masuk ke mode maintenance (stop nerima VM baru).
2. **`host.enable`**: Keluar dari mode maintenance.
3. **`host.emergencyShutdownHost`**: Matiin paksa tanpa nunggu proses selesai.
4. **`host.reboot`**: Restart host secara halus.
5. **`host.shutdown`**: Matiin host secara halus (aman).

### **2. Management VM (Virtual Machine)**

6. **`vm.clone`**: Buat duplikat VM dari sumber.
7. **`vm.create`**: Bikin VM baru dari awal.
8. **`vm.delete`**: Hapus VM permanen dari *storage*.
9. **`vm.migrate`**: Pindah VM antar host/pool.
10. **`vm.reboot`**: *Reboot* VM di level sistem.
11. **`vm.restart`**: *Restart* paksa VM.
12. **`vm.set`**: Ubah konfigurasi (CPU, RAM, dll) via API.
13. **`vm.snapshot`**: Ambil *point-in-time* VM.
14. **`vm.start`**: Nyalakan VM.
15. **`vm.stop`**: Matikan VM (graceful).
16. **`vm.suspend`**: *Pause* VM ke *disk*.

### **3. Management Storage (SR)**

17. **`sr.create`**: Inisialisasi *storage* baru.
18. **`sr.delete`**: Hapus *storage repository*.
19. **`sr.detach`**: Lepas *storage* dari host.
20. **`sr.forget`**: Hapus *entry* storage dari XO tanpa hapus data di disk.
21. **`sr.scan`**: *Rescan* untuk cek disk/LUN baru.

### **4. Management Network**

22. **`network.create`**: Bikin *virtual network* (bridge/vlan).
23. **`network.delete`**: Hapus *virtual network*.

### **5. Management Security & System**

24. **`acl.add`**: Tambah akses user/group ke objek.
25. **`acl.delete`**: Hapus akses user/group.
26. **`job.run`**: Eksekusi *job* lain (bisa *chaining* atau *nesting*).
27. **`log.clear`**: Bersihkan log database XO.
28. **`pool.set`**: Ubah setting global *pool*.
29. **`task.cancel`**: Batalkan *task* (seperti pindahan/copy yang nyangkut).
30. **`user.edit`**: Update info user.

Ini adalah **seluruh fungsi** yang tersedia di *dropdown* `select an xo-server API command`. Setiap perintah ini butuh **Target** (ID/UUID) yang bener supaya aksi sistemnya tepat sasaran ke VM atau Host yang lo mau.

Kalau lo mau gua bedah satu perintah secara spesifik (misal: gimana format JSON target yang bener buat `vm.migrate`), langsung aja bilang. Mana yang mau lo pake buat *Job* sekarang?
