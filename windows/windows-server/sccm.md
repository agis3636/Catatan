Sempurna! Kondisi lo udah bagus banget. Langsung gas! 🚀

Gua kasih panduan lengkap dari awal. Perhatiin baik-baik **di server mana** setiap langkah dijalankan ya.

---

# Panduan Lengkap: DC + SCCM (2 VM)

## Gambaran Besar

```
VM 1 (DC-SERVER)          VM 2 (SCCM-SERVER)
┌─────────────────┐        ┌─────────────────────┐
│ Windows Server  │        │ Windows Server 2022  │
│ 2022            │        │                      │
│                 │◄──────►│                      │
│ • Active Dir.   │        │ • SQL Server         │
│ • DNS           │        │ • Windows ADK        │
│                 │        │ • SCCM               │
│ IP: 192.168.1.10│        │ IP: 192.168.1.11     │
└─────────────────┘        └─────────────────────┘
```

---

# 🖥️ MULAI DI VM 1 (Yang Jadi DC)

## Langkah 1 — Setup Dasar VM 1

### Ganti Nama Server
1. Klik kanan **This PC** → **Properties**
2. Klik **Rename this PC**
3. Ketik: `DC-SERVER` → **Next** → **Restart Now**

### Set IP Statis (setelah restart)
1. Klik kanan ikon network di taskbar → **Open Network & Internet Settings**
2. **Change adapter options** → klik kanan adapter → **Properties**
3. Pilih **IPv4** → **Properties** → isi:

```
IP Address  : 192.168.1.10
Subnet Mask : 255.255.255.0
Gateway     : 192.168.1.1
DNS         : 127.0.0.1
```
4. **OK** → **Close**

---

## Langkah 2 — Install Active Directory di VM 1

Buka **PowerShell as Administrator**, jalankan:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

Tunggu sampai selesai ✅

---

## Langkah 3 — Bikin Domain (Promote jadi DC)

Masih di PowerShell VM 1:

```powershell
Install-ADDSForest `
  -DomainName "lab.local" `
  -DomainNetbiosName "LAB" `
  -InstallDns `
  -Force
```

> 📝 Nanti diminta bikin **password Safe Mode** — isi dan catat baik-baik!

Server akan **restart otomatis** — ini normal, tunggu saja.

### Verifikasi setelah restart
Login, buka PowerShell lagi:
```powershell
Get-ADDomain
```
Kalau muncul info domain → ✅ **DC berhasil!**

---

# 🖥️ PINDAH KE VM 2 (Yang Jadi SCCM)

## Langkah 4 — Setup Dasar VM 2

### Ganti Nama Server
1. Klik kanan **This PC** → **Properties** → **Rename this PC**
2. Ketik: `SCCM-SERVER` → **Next** → **Restart Now**

### Set IP Statis (setelah restart)
Sama seperti tadi, tapi isi:

```
IP Address  : 192.168.1.11
Subnet Mask : 255.255.255.0
Gateway     : 192.168.1.1
DNS         : 192.168.1.10   ← arahkan ke DC-SERVER
```

---

## Langkah 5 — Join Domain di VM 2

1. Klik kanan **This PC** → **Properties**
2. Klik **Rename this PC (advanced)**
3. Tab **Computer Name** → klik **Change**
4. Pilih **Domain** → ketik: `lab.local`
5. Klik **OK**
6. Masukkan kredensial:
   - Username: `LAB\Administrator`
   - Password: password Administrator DC lo
7. Klik **OK** → muncul "Welcome to lab.local" → **OK** → **Restart**

### Verifikasi
Setelah restart, di login screen harusnya ada tulisan `LAB\...` → ✅

---

## Langkah 6 — Install SQL Server di VM 2

### Download SQL Server
Buka browser di **VM 2**, pergi ke:
👉 `https://www.microsoft.com/en-us/sql-server/sql-server-downloads`

Scroll ke bawah → pilih **Developer** → **Download now**

### Install
1. Jalankan file yang didownload
2. Pilih **Download Media** → **ISO** → pilih folder → **Download**
3. Setelah selesai, dobel klik ISO → jalankan **setup.exe**
4. Pilih **Installation** → **New SQL Server stand-alone installation**
5. Pilih **Developer** → **Next**
6. Centang **I accept** → **Next**
7. Di **Feature Selection**, centang:
   - ✅ Database Engine Services
   - ✅ Full-Text and Semantic Extractions
8. **Next** terus sampai **Server Configuration** → biarkan default
9. Di **Database Engine Configuration**:
   - Pilih **Windows authentication mode**
   - Klik **Add Current User**
10. **Next** → **Install** → tunggu sampai semua ✅ → **Close**

---

## Langkah 7 — Install Windows ADK di VM 2

### Download (2 file, keduanya wajib!)
👉 `https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install`

Download:
1. **Windows ADK** → `adksetup.exe`
2. **Windows PE add-on** → `adkwinpesetup.exe`

### Install ADK dulu
1. Jalankan `adksetup.exe`
2. **Next** → **Accept**
3. Centang **hanya ini:**
   - ✅ Deployment Tools
   - ✅ User State Migration Tool (USMT)
4. **Install** → tunggu → **Close**

### Install WinPE add-on
1. Jalankan `adkwinpesetup.exe`
2. **Next** → **Accept** → **Install** → tunggu → **Close**

---

## Langkah 8 — Aktifkan Fitur Windows di VM 2

Buka **PowerShell as Administrator** di VM 2, copy-paste semua sekaligus:

```powershell
Install-WindowsFeature -Name `
  Web-Server, Web-Asp-Net, Web-Asp-Net45, `
  Web-Net-Ext45, Web-Mgmt-Console, Web-Scripting-Tools, `
  Web-Windows-Auth, Web-ISAPI-Ext, Web-ISAPI-Filter, `
  Web-Static-Content, Web-Http-Errors, Web-Http-Logging, `
  Web-Http-Tracing, BITS, RDC, `
  NET-Framework-45-Core, NET-WCF-HTTP-Activation45 `
  -IncludeManagementTools
```

Tunggu sampai selesai ✅

---

## Langkah 9 — Download & Extend AD Schema

### Download SCCM Evaluation (di VM 2)
👉 `https://www.microsoft.com/en-us/evalcenter/evaluate-microsoft-endpoint-configuration-manager`

Klik **Start your free trial** → isi form → download **ISO** (~3-4 GB)

### Mount ISO & Extend Schema
Setelah download, dobel klik ISO (misal ke-mount di drive `E:`).

Buka **PowerShell as Administrator** di VM 2:

```powershell
E:\SMSSETUP\BIN\X64\extadsch.exe
```

Cek hasilnya:
```powershell
Get-Content C:\ExtADSch.log | Select-String "Successfully"
```
Harus muncul tulisan **Successfully extended...** → ✅

---

## Langkah 10 — Buat System Management Container

*Langkah ini di VM 2, tapi efeknya ke AD di VM 1*

1. Tekan **Win+R** → ketik `adsiedit.msc` → Enter
2. Klik kanan **ADSI Edit** → **Connect to** → **OK**
3. Expand hingga ketemu `CN=System`
4. Klik kanan `CN=System` → **New** → **Object** → pilih **container** → **Next**
5. Ketik: `System Management` → **Next** → **Finish**
6. Klik kanan **System Management** → **Properties** → tab **Security** → **Add**
7. Klik **Object Types** → centang **Computers** → **OK**
8. Ketik `SCCM-SERVER` → **Check Names** → **OK**
9. Centang **Full Control** → **Advanced**
10. Pilih entry SCCM-SERVER → **Edit** → di **Applies to** pilih **This object and all descendant objects** → **OK** → **OK** → **Apply** → **OK**

---

## Langkah 11 — Install SCCM di VM 2 🎉

1. Buka ISO SCCM yang tadi (drive `E:`)
2. Jalankan **splash.hta**
3. Klik **Install**
4. Pilih **Install a Configuration Manager primary site** → **Next**
5. Kosongi product key (evaluation) → **Next**
6. Centang semua **I accept** → **Next**
7. Di **Prerequisite Downloads** → buat folder baru (misal `C:\SCCMDownload`) → **Next**
8. Isi **Site Code**: `LAB` (bebas, 3 huruf)
9. Isi **Site Name**: `LAB Primary Site`
10. **Installation folder** → biarkan default → **Next**
11. **Primary site** → pilih **Install as standalone** → **Next**
12. **Database Server**: ketik `SCCM-SERVER` → **Next**
13. Sisanya **Next** terus → **Begin Install**

⏳ Proses ini **30–60 menit**, jangan tutup!

Setelah selesai → buka **Configuration Manager Console** dari Start Menu → ✅ **SCCM siap dipakai!**

---

## 📋 Checklist Lengkap

| # | Langkah | Server | ✅ |
|---|---|---|---|
| 1 | Rename + IP statis | VM 1 (DC) | ⬜ |
| 2 | Install AD DS | VM 1 (DC) | ⬜ |
| 3 | Promote jadi DC / bikin domain | VM 1 (DC) | ⬜ |
| 4 | Rename + IP statis + join domain | VM 2 (SCCM) | ⬜ |
| 5 | Install SQL Server Developer | VM 2 (SCCM) | ⬜ |
| 6 | Install Windows ADK + WinPE | VM 2 (SCCM) | ⬜ |
| 7 | Aktifkan Windows Features | VM 2 (SCCM) | ⬜ |
| 8 | Download SCCM ISO | VM 2 (SCCM) | ⬜ |
| 9 | Extend AD Schema | VM 2 (SCCM) | ⬜ |
| 10 | Buat System Management container | VM 2 (SCCM) | ⬜ |
| 11 | Install SCCM | VM 2 (SCCM) | ⬜ |

---

Ikutin urutannya satu-satu ya! Kalau ada error di langkah mana pun, screenshot pesannya dan kasih tau gua, gua bantu debug 🙌
