Karena **Office LTSC 2024** tidak menggunakan tombol instalasi biasa, kita akan menggunakan cara manual yang resmi dari Microsoft (menggunakan **Office Deployment Tool**).

Ikuti langkah-langkah ini secara berurutan:

### Langkah 1: Ambil Product Key (Kunci Aktivasi)

1. Di halaman **Your products**, klik nama **Office LTSC Professional Plus 2024**.
2. Di panel sebelah kanan yang muncul, klik tab **Downloads & Keys**.
3. Salin dan simpan **Product Key** (25 digit angka dan huruf) yang muncul di sana. Anda akan membutuhkannya nanti setelah aplikasi terinstal.

---

### Langkah 2: Download Office Deployment Tool (ODT)

1. Buka browser dan cari "Download Office Deployment Tool" atau klik [link resmi Microsoft ini](https://www.microsoft.com/en-us/download/details.aspx?id=49117).
2. Klik **Download** dan simpan file `officedeploymenttool.exe`.
3. Buat sebuah folder baru di Drive **C:** dengan nama **`OfficeLTSC`**.
4. Jalankan file `.exe` yang tadi didownload, lalu **Extract** isinya ke folder `C:\OfficeLTSC` tersebut.

---

### Langkah 3: Membuat File Konfigurasi (`configuration.xml`)

LTSC 2024 membutuhkan instruksi khusus agar sistem tahu apa yang harus diunduh.

1. Di dalam folder `C:\OfficeLTSC`, klik kanan > **New** > **Text Document**.
2. Buka file teks tersebut dan **Paste (Tempel)** kode di bawah ini:

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="PerpetualVL2024">
    <Product ID="ProPlus2024Volume">
      <Language ID="en-us" />
    </Product>
  </Add>
  <Property Name="SharedComputerLicensing" Value="0" />
  <Property Name="SCLCacheOverride" Value="0" />
  <Property Name="AUTOACTIVATE" Value="1" />
  <Updates Enabled="TRUE" />
</Configuration>

```

3. Simpan file tersebut (Save As) dengan nama **`configuration.xml`** (pastikan ekstensinya `.xml`, bukan `.txt`).

---

### Langkah 4: Proses Instalasi (Lewat Command Prompt)

Di sinilah antivirus (seperti McAfee) terkadang ikut campur. **Saran: Matikan McAfee sementara selama 15 menit saja.**

1. Klik tombol **Start**, ketik **CMD**, lalu klik kanan dan pilih **Run as Administrator**.
2. Ketik perintah berikut satu per satu dan tekan Enter:

```xml
C:\Windows\System32 > cd C:\OfficeLTSC

C:\OfficeLTSC > setup.exe /download Configuration.xml

C:\OfficeLTSC > setup.exe /configure Configuration.xml

```

3. Jendela instalasi Office akan muncul. Tunggu sampai selesai.

---

### Langkah 5: Aktivasi

Setelah instalasi selesai, buka salah satu aplikasi (misalnya Word):

1. Masuk ke menu **File** > **Account**.
2. Klik **Change Product Key**.
3. Masukkan 25 digit **Product Key** yang Anda ambil di Langkah 1 tadi.

---

**Tips Keamanan:**
Jika McAfee memblokir langkah ke-4, biasanya muncul notifikasi "Access Denied". Jika itu terjadi, cukup matikan fitur *Real-time Scanning* di McAfee sebentar saja sampai proses instalasi berjalan.

Apakah Anda menemui kendala saat mencoba menjalankan perintah di Command Prompt tersebut?
