Memblokir **Control Panel & Settings** adalah langkah keamanan yang sangat efektif agar user tidak bisa mengubah konfigurasi sistem, seperti mengganti IP address, menghapus aplikasi, atau mengubah pengaturan tampilan yang sudah Anda tetapkan lewat GPO lainnya.

Berikut adalah langkah-langkah lengkapnya:

### 1. Membuat GPO di Server

1. Buka **Group Policy Management**.
2. Cari **OU** tempat user Anda berada (misalnya OU `Marketing` atau `IT_Dept` yang Anda buat sebelumnya).
3. **Klik kanan** pada OU tersebut > Pilih **Create a GPO in this domain, and Link it here...**.
4. Beri nama GPO tersebut: `Blokir_Control_Panel`.
5. **Klik kanan** GPO `Blokir_Control_Panel` yang baru muncul > Pilih **Edit**.

---

### 2. Mengaktifkan Kebijakan (Policy)

Di dalam jendela **Group Policy Management Editor**, arahkan ke path berikut:

* **User Configuration**
* **Policies**
* **Administrative Templates**
* **Control Panel**

Di panel sebelah kanan, cari settingan bernama:

> **Prohibit access to Control Panel and PC settings**

1. **Klik dua kali** pada settingan tersebut.
2. Pilih opsi **Enabled**.
3. Klik **Apply** lalu **OK**.

---

### 3. Penerapan dan Pengetesan di Client

Agar perubahan segera terasa di komputer user `budiit`, lakukan langkah ini:

1. Pindah ke komputer **Client**.
2. Buka **Command Prompt (CMD)**.
3. Ketik perintah: `gpupdate /force` lalu tekan Enter.
4. Tunggu hingga muncul pesan *"User Policy update has completed successfully"*.

**Hasil Praktek:**

* Coba buka **Settings** (ikon gerigi) atau ketik **Control Panel** di menu Start.
* Jika berhasil, Windows akan menampilkan pesan error: *"This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator."*

---

### Tips Penting:

* **Hanya User di OU Tersebut:** GPO ini hanya akan memblokir user yang berada di dalam OU yang Anda pilih. Jika Anda login sebagai `Administrator` di komputer yang sama, Anda tetap bisa membuka Control Panel karena akun Administrator biasanya berada di folder `Users` bawaan, bukan di OU yang Anda pasangi GPO tadi.
* **Cara Mengembalikan:** Jika ingin membuka aksesnya lagi, cukup kembali ke GPO Editor di server, ubah settingannya menjadi **Not Configured**, lalu jalankan `gpupdate /force` lagi di client.
