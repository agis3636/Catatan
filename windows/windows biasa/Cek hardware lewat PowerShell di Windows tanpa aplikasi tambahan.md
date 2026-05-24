## ✅ 1️⃣ Cek RAM DDR3 / DDR4 / DDR5 (PowerShell)

Buka **PowerShell** (Run as Administrator), lalu:

```powershell
Get-CimInstance Win32_PhysicalMemoryArray | Select SMBIOSMemoryType, Manufactuer, Capacity, Speed, PartNumber, MemoryDevices

```

### Arti hasil angka:

* `24` → **DDR3**
* `26` → **DDR4**
* `34` → **DDR5**

Kalau muncul lebih dari satu baris → RAM lebih dari satu keping.

---




Kalau mau **verifikasi slot RAM terpakai vs total slot**, pakai ini:

```powershell
Get-CimInstance Win32_PhysicalMemoryArray |
Select MemoryDevices
```

Contoh:

* `MemoryDevices : 2` → motherboard punya 2 slot
* tapi tadi cuma kebaca 1 baris → berarti **1 slot terisi, 1 kosong**

Atau mau cek **dual-channel aktif atau tidak**, bilang aja.







## ✅ 2️⃣ Cek Detail RAM (kapasitas, speed, merk)

```powershell
Get-CimInstance Win32_PhysicalMemory |
Select Manufacturer, Capacity, Speed, PartNumber
```

---

## ✅ 3️⃣ Cek SSD / HDD + NVMe / SATA (PowerShell – paling akurat)

```powershell
Get-PhysicalDisk | Select FriendlyName, MediaType, BusType, Size
```

### Contoh interpretasi:

* `MediaType : SSD` + `BusType : NVMe` → **SSD NVMe**
* `MediaType : SSD` + `BusType : SATA` → **SSD SATA**
* `MediaType : HDD` → **Harddisk**

---

## ✅ 4️⃣ Cek Motherboard (PowerShell)

```powershell
Get-CimInstance Win32_BaseBoard |
Select Manufacturer, Product, Version, SerialNumber
```

---

## ✅ 5️⃣ Alternatif Cepat (tanpa command ribet)

Kalau cuma mau **lihat cepat**:

```powershell
msinfo32
```

→ Components → Memory / Storage / BaseBoard

---

## 🔥 Ringkasannya

> ❌ **WMIC sudah tidak bisa dipakai**
> ✅ **PowerShell = cara resmi & future-proof**

Kalau mau, gue bisa:

* bikinin **script PowerShell 1x jalan semua**
* atau versi **buat materi SMK / modul**
* atau versi **Linux / dual boot**

tinggal bilang mau yang mana.
Normal 👍
Di **Windows 10/11 versi baru**, **WMIC memang sudah dihapus / deprecated**, makanya error.

Solusinya pakai **PowerShell** (resmi pengganti WMIC). Ikuti ini 👇

---

## ✅ 1️⃣ Cek RAM DDR3 / DDR4 / DDR5 (PowerShell)

Buka **PowerShell** (Run as Administrator), lalu:

```powershell
Get-CimInstance Win32_PhysicalMemory | Select SMBIOSMemoryType
```

### Arti hasil angka:

* `24` → **DDR3**
* `26` → **DDR4**
* `34` → **DDR5**

Kalau muncul lebih dari satu baris → RAM lebih dari satu keping.

---

## ✅ 2️⃣ Cek Detail RAM (kapasitas, speed, merk)

```powershell
Get-CimInstance Win32_PhysicalMemory |
Select Manufacturer, Capacity, Speed, PartNumber
```

---

## ✅ 3️⃣ Cek SSD / HDD + NVMe / SATA (PowerShell – paling akurat)

```powershell
Get-PhysicalDisk | Select FriendlyName, MediaType, BusType, Size
```

### Contoh interpretasi:

* `MediaType : SSD` + `BusType : NVMe` → **SSD NVMe**
* `MediaType : SSD` + `BusType : SATA` → **SSD SATA**
* `MediaType : HDD` → **Harddisk**

---

## ✅ 4️⃣ Cek Motherboard (PowerShell)

```powershell
Get-CimInstance Win32_BaseBoard |
Select Manufacturer, Product, Version, SerialNumber
```

---

## ✅ 5️⃣ Alternatif Cepat (tanpa command ribet)

Kalau cuma mau **lihat cepat**:

```powershell
msinfo32
```

→ Components → Memory / Storage / BaseBoard

---

## 🔥 Ringkasannya

> ❌ **WMIC sudah tidak bisa dipakai**
> ✅ **PowerShell = cara resmi & future-proof**

Kalau mau, gue bisa:

* bikinin **script PowerShell 1x jalan semua**
* atau versi **buat materi SMK / modul**
* atau versi **Linux / dual boot**

tinggal bilang mau yang mana.
