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

(Double click → isi value `1` → OK)

Hasil akhirnya harus seperti ini:

```
LabConfig
 ├─ BypassTPMCheck = 1
 ├─ BypassSecureBootCheck = 1
 └─ BypassRAMCheck = 1
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
