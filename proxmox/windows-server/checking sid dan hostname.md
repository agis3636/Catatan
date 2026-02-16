Untuk memastikan bahwa proses **Sysprep** dan **Cloning** Anda berhasil menciptakan dua server yang unik, Anda bisa melakukan pengecekan langsung melalui **PowerShell** atau **Command Prompt** di masing-masing VM.

Berikut adalah cara tercepat untuk memverifikasi perbedaannya:

### 1. Cek Hostname (Nama Komputer)

Buka PowerShell di kedua server, lalu ketik perintah berikut:

```powershell
hostname

```

* **Hasil yang diharapkan:** Seharusnya masing-masing VM menampilkan nama yang berbeda (misalnya: `WIN-A1B2C3D4` dan `WIN-X9Y8Z7W6`) karena Windows memaksa pembuatan nama acak baru setelah proses OOBE.

### 2. Cek SID (Security Identifier)

Ini adalah bagian yang paling krusial untuk memastikan tidak ada bentrok di Active Directory. Di dalam PowerShell, ketik perintah ini:

```powershell
whoami /user

```

Atau jika ingin melihat SID mesin secara keseluruhan:

```powershell
Get-ADComputer -Identity "NamaServer" | Select-Object SID

```

*(Catatan: Perintah kedua hanya jalan jika sudah join domain).*

**Cara Manual tanpa PowerShell (User SID):**

* Lihat deretan angka terakhir di baris SID yang muncul (misalnya `S-1-5-21-xxxx-xxxx-xxxx-1001`).
* Bandingkan angka di bagian tengah dan akhir antara Server 1 dan Server 2. Jika **Sysprep** berhasil, angka-angka tersebut pasti berbeda.

### 3. Cek IP Address

Karena Anda menggunakan bridge `vmbr1` dan model `VirtIO`, pastikan juga mereka tidak mendapatkan IP yang sama (jika ada DHCP) atau segera set IP statis yang berbeda:

```powershell
ipconfig

```

---

### Kesimpulan Hasil Cek

Jika Anda melihat **Hostname** berbeda dan **SID** berbeda, berarti pekerjaan Anda sukses besar. Ketiga server tersebut kini siap dikonfigurasi lebih lanjut tanpa risiko bentrok jaringan.
