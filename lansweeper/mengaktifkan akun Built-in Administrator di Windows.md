Untuk mengaktifkan akun **Built-in Administrator** di Windows (yang biasanya tersembunyi dan memiliki hak akses penuh tanpa batasan UAC), Anda bisa menggunakan beberapa metode di bawah ini.

### Metode 1: Menggunakan Command Prompt (Paling Cepat)

Ini adalah cara yang paling sering digunakan oleh IT admin:

1. Klik **Start**, ketik `cmd`.
2. Klik kanan pada **Command Prompt** dan pilih **Run as Administrator**.
3. Ketik perintah berikut dan tekan Enter:
```cmd
net user administrator /active:yes

```

4. *(Opsional)* Jika Anda ingin memberi password langsung pada akun tersebut, ketik:
```cmd
net user administrator *

```

*(Lalu masukkan password baru saat diminta).*

5. To disable the account later, run: 
```cmd
net user administrator /active:no

```

---

### Metode 2: Menggunakan Computer Management (Visual)

Jika Anda lebih suka menggunakan tampilan jendela:

1. Tekan tombol `Windows + R`, ketik `lusrmgr.msc` dan tekan Enter.
2. Klik folder **Users** di sebelah kiri.
3. Klik kanan pada user **Administrator**, lalu pilih **Properties**.
4. **Hapus centang** pada opsi **Account is disabled**.
5. Klik **OK**.

---

### Method 3: If You Are Locked Out

If you cannot log in and need the admin account to fix your PC, you can enable it through the Recovery Environment.

1. On the login screen, hold the Shift key and click Restart (Power icon > Restart).
2. Navigate to Troubleshoot > Advanced Options > Command Prompt.
3. Type the same command: net user administrator /active:yes.
4. Close the Command Prompt and click Continue to boot back into Windows. The Administrator account should now appear on the login screen.

---

### Mengapa ini berguna untuk Lansweeper?

Akun *Built-in Administrator* ini sangat ampuh digunakan sebagai **Scanning Credential** di Lansweeper karena:

* Memiliki hak akses penuh ke *Registry* dan *File System*.
* Secara default tidak terhalang oleh notifikasi UAC (User Account Control) yang seringkali menggagalkan instalasi software secara remote.

### Tips Tambahan untuk "Instalasi Diam-diam"

Setelah akun administrator ini aktif, pastikan Anda menambahkannya ke dalam menu **Scanning > Scanning Credentials** di web console Lansweeper.

Gunakan akun ini untuk melakukan deployment ke PC teman Anda. Dengan parameter `--silent` yang kita bahas sebelumnya, proses instalasi Brave akan berjalan di *background* menggunakan otoritas akun Administrator ini tanpa mengganggu aktivitas teman Anda.

**Apakah Anda ingin saya bantu membuatkan script .bat otomatis untuk mengaktifkan admin sekaligus menjalankan instalasi?**
