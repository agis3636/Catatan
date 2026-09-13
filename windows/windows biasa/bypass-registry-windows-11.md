**Lakukan jikalau di host ESXi kamu memang tidak ada Key Provider sama sekali**, jadi:

*❌ Tidak bisa pakai TPM virtual*
*❌ Tidak bisa Secure Boot Windows 11 secara “resmi”*
👉 **Jalan satu-satunya: BYPASS Windows 11 requirement.**
Tenang, ini aman untuk lab / non-produksi 👍

Kita kerjain **step by step sampai tembus** ya.

---

# 🛠️ **BYPASS Windows 11 requirement di VMware ESXi**

Ikuti ini **persis urutannya** 👇

---

## 1️⃣ Boot ke installer Windows 11 sampai muncul error

Layar ini:

> *This PC doesn’t currently meet Windows 11 system requirements*

---

## 2️⃣ Tekan **Shift + F10**

Akan muncul **Command Prompt (hitam)**

---

## 3️⃣ Ketik:

```
regedit
```

Enter.

---

## 4️⃣ Masuk ke path ini:

```
HKEY_LOCAL_MACHINE\SYSTEM\Setup
```

---

## 5️⃣ Buat key baru

Klik kanan `Setup` → **New → Key**
Nama:

```
LabConfig
```

---

## 6️⃣ Di dalam `LabConfig`, buat 3 DWORD

Klik kanan panel kanan → **New → DWORD (32-bit)**

Buat ini satu per satu:

| Name                  | Value |
| --------------------- | ----- |
| BypassTPMCheck        | 1     |
| BypassSecureBootCheck | 1     |
| BypassRAMCheck        | 1     |
| BypassCPUCheck        | 1     |

(Double click → isi value `1` → OK)

Hasil akhirnya harus seperti ini:

```
LabConfig
 ├─ BypassTPMCheck = 1
 ├─ BypassSecureBootCheck = 1
 ├─ BypassRAMCheck = 1
 └─ BypassCPUCheck = 1
```

---

## 7️⃣ Tutup semuanya

* Tutup Registry Editor
* Tutup Command Prompt
* Klik **Back**
* Klik **Next**

➡️ Installer sekarang **akan lanjut normal** 🎉

---

# 🟢 **Kalau masih mentok, cek ini:**

| Gejala               | Solusi                                       |
| -------------------- | -------------------------------------------- |
| Masih error          | Pastikan DWORD (bukan QWORD)                 |
| Value tetap 0        | Ubah ke 1                                    |
| Tidak bisa buat key  | Pastikan di `SYSTEM\Setup` bukan tempat lain |
| Keyboard tidak masuk | Klik mouse ke console dulu                   |

---

# ⚠️ Catatan penting

* Windows Update tetap jalan
* Fitur utama Windows 11 tetap bisa dipakai
* Tapi secara resmi: status "unsupported"

Untuk lab, home server, testing — **ini totally fine** 👍

---

## 🎯 **Jawaban paling langsung ke kamu:**

> Karena ESXi kamu **tidak punya Key Provider**, TPM memang tidak bisa dipakai.
> Jadi satu-satunya cara install Windows 11 di situ adalah **bypass registry**.

---
---
---

Ini masalah krusial. Jika laptop mahasiswa hanya memiliki RAM 4GB dan Anda mengalokasikan 4GB (atau bahkan 3GB) untuk VirtualBox, laptop utama (Host) akan kehabisan memori dan berisiko *hang* total (Freeze).

Untuk laptop 4GB, pembagian memori harus diatur secara ketat:

| Komponen | Alokasi RAM (Total 4GB) | Keterangan |
| --- | --- | --- |
| **Host (Laptop Asli)** | 2 GB (2048 MB) | Menjaga OS utama tetap berjalan lancar dan tidak *hang*. |
| **Guest (VM Windows 11)** | 2 GB (2048 MB) | Cukup untuk menjalankan proses instalasi secara perlahan. |

**Penyebab Utama Masalah:**
Windows 11 memiliki syarat mutlak RAM minimal 4GB. Jika VM hanya diberi 2GB, proses instalasi akan dicegat dengan *error* *"This PC can't run Windows 11"*.

**Solusi Bertahap (Cocok untuk Praktik Kelas):**
Jadikan kendala ini sebagai tambahan materi *troubleshooting* Basic OS. Ketika masuk ke layar pertama instalasi (pemilihan bahasa), instruksikan ini:

1. Tekan **Shift + F10** pada keyboard untuk membuka Command Prompt.
2. Ketik `regedit` dan tekan Enter.
3. Arahkan ke: `HKEY_LOCAL_MACHINE\SYSTEM\Setup`.
4. Klik kanan folder **Setup** -> pilih **New** -> **Key**. Beri nama `LabConfig`.
5. Di folder LabConfig, klik kanan ruang kosong -> **New** -> **DWORD (32-bit) Value**. Beri nama `BypassRAMCheck`.
6. Klik ganda `BypassRAMCheck`, ubah **Value data** menjadi `1`.
7. Tutup Registry Editor dan Command Prompt, lalu lanjutkan instalasi seperti biasa. Instalasi akan lolos dari pengecekan RAM.

**Solusi Lebih Aman & Efisien (Persiapan Pra-Kelas):**
Jika ingin praktik berjalan lebih cepat tanpa repot masuk ke Registry, suruh mahasiswa mengolah file `win11.iso` asli menggunakan aplikasi **Rufus** di rumah.

* Saat di Rufus, ada pop-up pilihan: centang **"Remove requirement for 4GB+ RAM, Secure Boot and TPM 2.0"**.
* Gunakan file ISO hasil modifikasi Rufus ini ke dalam VirtualBox. Instalasi dengan RAM 2GB akan langsung lolos otomatis.
