Metode resmi untuk mengubah edisi Windows dari **Windows 11 Home** ke **Windows 11 Pro** ketika Windows tidak mau langsung menerima product key Pro yang baru dibeli.

Biasanya dilakukan dengan memakai **generic key Windows Pro** terlebih dahulu untuk memicu perubahan edisi (bukan untuk aktivasi), lalu setelah edisi berubah menjadi Pro, baru memasukkan product key Pro yang Anda beli untuk aktivasi.

### Sebelum mulai

Pastikan:

* Lisensi yang Anda beli memang **Windows 11 Pro OEM** yang valid.
* Anda sudah mencatat product key Pro yang dibeli.
* Backup data penting (meskipun proses ini biasanya tidak menghapus data).

### Langkah 1 — Putuskan koneksi internet

* Matikan Wi-Fi atau cabut kabel LAN.
* Tujuannya agar Windows tidak langsung mencoba aktivasi online saat proses perubahan edisi.

### Langkah 2 — Masukkan Generic Key Windows Pro

1. Buka **Settings** → **System** → **Activation**.
2. Pilih **Change product key**.
3. Masukkan generic key Windows Pro berikut:

```text
VK7JG-NPHTM-C97JM-9MPGT-3V66T
```

Ini adalah generic key resmi Microsoft untuk instalasi/upgrade Windows Pro. Key ini **tidak mengaktivasi Windows**, hanya mengubah edisi.

3. Klik **Next** → **Start Upgrade**.
4. Tunggu proses selesai. Komputer mungkin restart.

### Langkah 3 — Pastikan edisi sudah berubah

Setelah masuk kembali ke Windows:

* Buka **Settings** → **System** → **Activation**
* Atau tekan `Win + R`, ketik:

```text
winver
```

Harus terlihat **Windows 11 Pro**.

### Langkah 4 — Hubungkan internet kembali

* Nyalakan Wi-Fi/LAN.

### Langkah 5 — Masukkan product key Pro yang dibeli

1. Kembali ke **Settings** → **System** → **Activation**.
2. Klik **Change product key**.
3. Masukkan key Windows 11 Pro OEM yang Anda beli.
4. Tunggu proses aktivasi online.

### Jika generic key ditolak

Kadang muncul error seperti:

* 0x803fa067
* "The product key didn't work"

Coba:

1. Pastikan internet benar-benar mati.
2. Restart PC.
3. Jalankan Command Prompt sebagai Administrator lalu:

```cmd
changepk.exe /ProductKey VK7JG-NPHTM-C97JM-9MPGT-3V66T
```

atau:

```cmd
slmgr /ipk VK7JG-NPHTM-C97JM-9MPGT-3V66T
```

Kemudian restart dan cek apakah edisi berubah menjadi Pro.

### Hal yang perlu diperhatikan

* Jika laptop memiliki lisensi OEM Home tertanam di BIOS/UEFI, itu normal. Setelah upgrade ke Pro dan aktivasi berhasil dengan key Pro yang valid, Windows akan tetap berjalan sebagai Pro.
* Product key **Windows 10 Pro** yang valid umumnya juga dapat mengaktivasi Windows 11 Pro.
* Jika key yang dibeli berasal dari sumber tidak resmi atau bukan untuk edisi Pro yang sesuai, aktivasi bisa gagal meskipun proses upgrade edisinya berhasil.
