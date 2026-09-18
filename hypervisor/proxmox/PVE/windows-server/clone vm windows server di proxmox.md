Karena Anda sudah berhasil menginstal satu server sampai masuk ke Desktop, jangan ulangi proses instalasi dari nol (ISO) untuk dua server sisanya. Itu buang-buang waktu.

Cara paling cepat dan profesional di Proxmox adalah menggunakan fitur **Clone** dikombinasikan dengan **Sysprep**.

Berikut langkah-langkahnya:

### 1. Jalankan Sysprep (Wajib!)

Jangan langsung dikloning begitu saja. Setiap Windows punya ID unik bernama **SID**. Kalau Anda langsung kloning tanpa Sysprep, server-server tersebut akan bentrok saat dihubungkan ke Active Directory atau jaringan yang sama.

* Di dalam VM Windows Server yang baru jadi, tekan `Win + R`, ketik `sysprep`, lalu Enter.
* Buka aplikasi `sysprep.exe`.
* Pilih **Enter System Out-of-Box Experience (OOBE)**.
* **PENTING:** Centang kotak **Generalize**.
* Pilih Shutdown Option: **Shutdown**.
* Klik **OK**. VM akan mati secara otomatis.

### 2. Buat Template (Opsional tapi Bagus)

Agar VM pertama Anda tidak rusak dan bisa dipakai berkali-kali di masa depan:

* Klik kanan pada VM tersebut di menu Proxmox.
* Pilih **Convert to Template**.
* VM ini sekarang jadi "master" (ikonnya berubah jadi gambar kertas).

### 3. Clone Menjadi 2 Server Baru

Sekarang, buat 2 server sisanya dalam hitungan detik:

* Klik kanan pada Template/VM master tadi.
* Pilih **Clone**.
* **Mode:** Pilih **Full Clone** (agar VM baru punya file disk sendiri dan tidak tergantung pada master).
* Isi **Name** (misal: `WinSer-02`) dan klik **Clone**.
* Ulangi sekali lagi untuk server ketiga (`WinSer-03`).

---

### Kenapa cara ini paling cepat?

1. **Full Driver:** Semua driver VirtIO yang sudah Anda susah payah instal tadi otomatis sudah ada di VM hasil kloning.
2. **Tanpa Loading ISO:** Anda tidak perlu lagi melewati proses pilih bahasa, pilih edisi, dan klik-klik "Next" yang membosankan.
3. **Identitas Unik:** Karena tadi kita pakai **Sysprep Generalize**, saat VM kloning dinyalakan, Windows akan meminta nama komputer dan password baru, sehingga tidak akan ada bentrok SID di jaringan.

---

### Tips Tambahan buat User:

Begitu kedua VM baru nyala, Anda hanya perlu setting IP Address yang berbeda untuk masing-masing server.
