# CMD & POWERSHELL WINDOWS 10/11

## 1. Membuka CMD dan PowerShell

CMD sebagai Administrator

1. Tekan `Win + R`.
2. Ketik `cmd`.
3. Tekan `Ctrl + Shift + Enter`.

PowerShell sebagai Administrator

1. Klik kanan Start.
2. Pilih Terminal (Admin) atau Windows PowerShell (Admin).
3. Jika Terminal terbuka dalam mode CMD, ketik `powershell`.

## 2. Cek OS Windows

GUI — tampilan grafis

Tekan `Win + R`, lalu ketik:

```
winver
```

Menampilkan versi dan build Windows.

CLI — Command Line Interface

```
systeminfo
```

Menampilkan informasi OS, tanggal instalasi, CPU, RAM, BIOS, dan sistem.

```
hostname
```

Menampilkan nama komputer.

## 3. Cek CPU / Prosesor

GUI: `Ctrl + Shift + Esc` → Task Manager → Performance → CPU

CLI — PowerShell:

```
Get-CimInstance Win32_Processor | Select Name, NumberOfCores, NumberOfLogicalProcessors, MaxClockSpeed
```

Keterangan:

- `Name`: nama prosesor.
- `NumberOfCores`: jumlah core fisik.
- `NumberOfLogicalProcessors`: jumlah logical processor.
- `MaxClockSpeed`: kecepatan maksimum yang dilaporkan, dalam MHz.

## 4. Cek RAM

GUI: `Ctrl + Shift + Esc` → Task Manager → Performance → Memory

Menampilkan kapasitas RAM, kecepatan, slot yang digunakan, dan informasi memori lainnya jika tersedia.

CLI — Cek kapasitas RAM total

```
Get-CimInstance Win32_ComputerSystem | Select @{N='RAM_GB';E={[math]::Round($_.TotalPhysicalMemory/1GB,2)}}
```

Cek detail setiap keping RAM

```
Get-CimInstance Win32_PhysicalMemory | Select DeviceLocator, @{N='Capacity_GB';E={[math]::Round($_.Capacity/1GB,2)}}, Speed, Manufacturer, PartNumber, SMBIOSMemoryType
```

Arti tipe RAM berdasarkan `SMBIOSMemoryType`:

| Angka | Jenis RAM |
| ----- | --------- |
| 24    | DDR3      |
| 26    | DDR4      |
| 34    | DDR5      |

Cek jumlah slot RAM menurut informasi firmware

```
Get-CimInstance Win32_PhysicalMemoryArray | Select MemoryDevices
```

Angka `2` berarti firmware melaporkan dua perangkat memori yang dapat dipasang. Angka ini tidak selalu sama dengan jumlah slot fisik yang benar-benar tersedia.

## 5. Cek GPU / VGA

GUI: `Ctrl + Shift + Esc` → Task Manager → Performance → GPU

CLI — PowerShell:

```
Get-CimInstance Win32_VideoController | Select Name
```

Menampilkan nama GPU yang terdeteksi.

## 6. Cek SSD / HDD, SATA, dan NVMe

GUI: `Ctrl + Shift + Esc` → Task Manager → Performance → Disk

CLI — PowerShell:

```
Get-PhysicalDisk | Select FriendlyName, MediaType, BusType, @{N='Size_GB';E={[math]::Round($_.Size/1GB,2)}}
```

Keterangan:

| Hasil            | Arti                        |
| ---------------- | --------------------------- |
| `MediaType: SSD` | Media penyimpanan SSD       |
| `MediaType: HDD` | Media penyimpanan hard disk |
| `BusType: NVMe`  | Antarmuka NVMe              |
| `BusType: SATA`  | Antarmuka SATA              |

Informasi `MediaType` atau `BusType` bisa saja tidak terdeteksi dengan benar pada perangkat atau driver tertentu.

Cek model disk melalui CMD

```
powershell -NoProfile -Command "Get-CimInstance Win32_DiskDrive | Select Model, InterfaceType, Size"
```

## 7. Cek Motherboard dan BIOS

GUI: Tekan `Win + R`, lalu ketik:

```
msinfo32
```

Buka System Summary untuk melihat informasi sistem, produsen perangkat, model, BIOS, dan mode BIOS.

CLI — PowerShell, cek motherboard

```
Get-CimInstance Win32_BaseBoard | Select Manufacturer, Product, Version
```

Cek BIOS

```
Get-CimInstance Win32_BIOS | Select Manufacturer, SMBIOSBIOSVersion, ReleaseDate
```

## 8. Cek Lisensi Windows dan Product Key

CLI — Cek status aktivasi

```
slmgr /xpr
```

Menampilkan status masa berlaku aktivasi Windows.

Cek detail lisensi

```
slmgr /dlv
```

Menampilkan edisi Windows, kanal lisensi, status aktivasi, dan lima karakter terakhir product key.

Cek OEM product key dari BIOS/UEFI

Jalankan di CMD atau PowerShell:

```
(Get-CimInstance -ClassName SoftwareLicensingService).OA3xOriginalProductKey
```

Jika tersedia, perintah ini menampilkan OEM key yang tertanam di firmware.

| Informasi      | Keterangan                                                      |
| -------------- | --------------------------------------------------------------- |
| `Licensed`     | Windows berstatus aktif                                         |
| `Notification` | Windows belum berstatus aktif atau memerlukan tindakan aktivasi |
| `OEM`          | Kanal lisensi OEM                                               |
| `RETAIL`       | Kanal lisensi Retail                                            |
| `VOLUME`       | Kanal lisensi Volume                                            |

Pada laptop yang diperiksa sebelumnya, status `Notification` dan kode `0xC004F034` menunjukkan Windows belum aktif. Kanal OEM tidak otomatis membuktikan lisensi valid.

GUI — Cek status aktivasi

Buka Settings → System → Activation untuk melihat status aktivasi Windows.

## 9. Cek Partisi dan Disk

GUI: Tekan `Win + R`, ketik:

```
diskmgmt.msc
```

Membuka Disk Management untuk melihat disk, partisi, kapasitas, dan ruang yang tersedia.

CLI — PowerShell

```
Get-Volume | Select DriveLetter, FileSystem, @{N='Size_GB';E={[math]::Round($_.Size/1GB,2)}}, @{N='Free_GB';E={[math]::Round($_.SizeRemaining/1GB,2)}}
```

Menampilkan drive, filesystem, kapasitas, dan ruang kosong.

## 10. Shutdown dan Restart

CLI — Shutdown langsung

```
shutdown /s /t 0
```

Shutdown setelah 60 detik

```
shutdown /s /t 60
```

Batalkan shutdown terjadwal

```
shutdown /a
```

Restart langsung

```
shutdown /r /t 0
```

GUI — Shutdown

Klik Start → Power → Shut down.

GUI — Restart

Klik Start → Power → Restart.

## 11. Ringkasan GUI vs CLI

| Pemeriksaan     | GUI                           | CLI                                     |
| --------------- | ----------------------------- | --------------------------------------- |
| Versi Windows   | `winver`                      | `systeminfo`                            |
| CPU             | Task Manager → Performance    | `Get-CimInstance Win32_Processor`       |
| RAM             | Task Manager → Memory         | `Get-CimInstance Win32_PhysicalMemory`  |
| GPU             | Task Manager → GPU            | `Get-CimInstance Win32_VideoController` |
| SSD/HDD         | Task Manager → Disk           | `Get-PhysicalDisk`                      |
| Motherboard     | `msinfo32`                    | `Get-CimInstance Win32_BaseBoard`       |
| BIOS            | `msinfo32`                    | `Get-CimInstance Win32_BIOS`            |
| Lisensi Windows | Settings → Activation         | `slmgr /dlv`                            |
| Product key OEM | Tidak ditampilkan di Settings | `OA3xOriginalProductKey`                |
| Partisi disk    | `diskmgmt.msc`                | `Get-Volume`                            |
| Shutdown        | Start → Power                 | `shutdown /s /t 0`                      |

Catatan: gunakan PowerShell untuk pemeriksaan hardware modern. `WMIC` sudah deprecated dan mungkin tidak tersedia pada Windows terbaru.
