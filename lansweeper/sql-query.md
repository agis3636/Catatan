### 1. Alert: Aset Baru Ditemukan (7 Hari Terakhir)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.IPAddress,
  tblAssets.Mac,
  tblAssetCustom.Manufacturer,
  tblAssetCustom.Model,
  tblAssets.Firstseen As [Pertama Terdeteksi]
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
Where tblAssets.Firstseen > GetDate() - 7
Order By [Pertama Terdeteksi] Desc

```

---

### 2. Alert: BitLocker Tidak Aktif (Security Risk)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username,
  tblEncryptableVolume.DriveLetter,
  Case tblEncryptableVolume.ProtectionStatus
    When 0 Then 'OFF (Tidak Terenkripsi)'
    When 1 Then 'ON (Aman)'
    Else 'Unknown'
  End As [Status BitLocker]
From tblAssets
  Inner Join tblEncryptableVolume On
      tblAssets.AssetID = tblEncryptableVolume.AssetID
Where tblEncryptableVolume.DriveLetter = 'C:' And
  tblEncryptableVolume.ProtectionStatus = 0
Order By tblAssets.AssetName

```

---

### 3. Alert: Cek Apakah Aset Printer Ada? melihat semua aset yang dianggap Printer:

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.IPAddress,
  tblAssetCustom.Manufacturer,
  tblAssetCustom.Model
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
Where tblAssets.Assettype = 16
Order By tblAssets.AssetName

```

---

### 4. Alert: Deteksi Error Hardisk (Event Log)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.IPAddress,
  tblAssets.Username,
  tblNtlog.TimeGenerated As [Error Time],
  tblNtlog.Eventcode,
  Case tblNtlog.Eventcode
    When 7 Then 'Bad Block / Sector Rusak (Critical)'
    When 11 Then 'Controller Error (Critical)'
    When 55 Then 'File System Corrupt (NTFS Error)'
    When 98 Then 'Volume Dirty / Needs CheckDisk'
    Else 'Disk Error Lainnya'
  End As [Diagnosa Cepat]
From tblAssets
  Inner Join tblNtlog On tblAssets.AssetID = tblNtlog.AssetID
Where tblNtlog.TimeGenerated > GetDate() - 7 And tblNtlog.Eventcode In (7, 11,
  55, 98)
Order By [Error Time] Desc

```

---

### 5. Alert: Deteksi Perubahan RAM (Hardware Change). memberitahu Anda jika ada komputer yang kapasitas RAM-nya berubah (bertambah atau berkurang).

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username,
  tblPhysicalMemoryHist.Action As Status,
  Cast(tblPhysicalMemoryHist.Capacity / 1024 / 1024 / 1024 As Numeric(10,2)) As
  [Size (GB)],
  tblPhysicalMemoryHist.Lastchanged As [Waktu Kejadian]
From tblAssets
  Inner Join tblPhysicalMemoryHist On
      tblAssets.AssetID = tblPhysicalMemoryHist.AssetID
Where tblPhysicalMemoryHist.Lastchanged > GetDate() - 30
Order By [Waktu Kejadian] Desc

```

---

### 6. Alert: Deteksi USB Flashdisk / Hardisk Eksternal

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username,
  tblUsbDevices.Name As [Nama Device],
  tblUsbDevices.Deviceid
From tblAssets
  Inner Join tblUsbDevices On tblAssets.AssetID = tblUsbDevices.AssetID
Where (tblUsbDevices.Name Like '%Mass Storage%' Or
    tblUsbDevices.Name Like '%Flash%' Or tblUsbDevices.Name Like '%SanDisk%' Or
    tblUsbDevices.Name Like '%Kingston%')
Order By tblAssets.AssetName

```

---

### 7. Alert: Gagal Login (Potensi Serangan Brute Force)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username As [Last User],
  Count(tblNtlog.Eventcode) As [Gagal Login Count],
  Max(tblNtlog.TimeGenerated) As [Kejadian Terakhir]
From tblAssets
  Inner Join tblNtlog On tblAssets.AssetID = tblNtlog.AssetID
Where tblNtlog.Eventcode = 4625 And tblNtlog.TimeGenerated > GetDate() - 1
Group By tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username
Having Count(tblNtlog.Eventcode) > 5
Order By [Gagal Login Count] Desc

```

---

### 8. Alert: Komputer Tanpa Antivirus (atau AV Mati)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblAssets.Username,
  tblOperatingsystem.Caption As OS,
  tblAssets.Lastseen
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
  Inner Join tblOperatingsystem On
      tblAssets.AssetID = tblOperatingsystem.AssetID
  Left Join tblAntivirus On tblAssets.AssetID = tblAntivirus.AssetID
Where tblAntivirus.AssetID Is Null And tblAssetCustom.State = 1 And
  tblAssets.Assettype = -1
Order By tblAssets.AssetName

```

---

### 9. Alert: mencari drive (C:, D:, dsb.) yang sisa kapasitasnya di bawah 10 GB

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblAssets.Username,
  tblDiskdrives.Caption As Drive,
  Cast(tblDiskdrives.Freespace / 1024 / 1024 / 1024 As Numeric(10,2)) As
  [Free Space (GB)],
  Cast(tblDiskdrives.Size / 1024 / 1024 / 1024 As Numeric(10,2)) As
  [Total Size (GB)],
  tblAssets.Lastseen
From tblAssets
  Inner Join tblDiskdrives On tblAssets.AssetID = tblDiskdrives.AssetID
Where tblDiskdrives.DriveType = 3 And tblDiskdrives.Freespace < 10737418240
Order By [Free Space (GB)]

```

---

### 10. Alert: mencari komputer yang belum menginstall update Windows apa pun dalam 90 hari terakhir. Belum Patch Windows (Outdated)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblOperatingsystem.Caption As OS,
  Max(tblQuickFixEngineering.InstalledOn) As [Last Update Install]
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
  Inner Join tblOperatingsystem On
      tblAssets.AssetID = tblOperatingsystem.AssetID
  Left Join tblQuickFixEngineering On
      tblAssets.AssetID = tblQuickFixEngineering.AssetID
Where tblAssets.Assettype = -1 And tblAssetCustom.State = 1
Group By tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblOperatingsystem.Caption
Having Max(tblQuickFixEngineering.InstalledOn) < GetDate() - 90
Order By [Last Update Install]

```

---

### 11. Alert: mengetes apakah Lansweeper Anda sebenarnya sedang menarik data Event Log atau tidak:

```sql
Select Top 10 tblAssets.AssetName,
  tblNtlog.TimeGenerated,
  tblNtlog.Eventcode
From tblNtlog
  Inner Join tblAssets On tblNtlog.AssetID = tblAssets.AssetID
Order By tblNtlog.TimeGenerated Desc

```

---

### 12. Alert: meranking komputer berdasarkan seberapa sering mereka mati paksa (tanpa shutdown proper) dalam 30 hari terakhir.

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.IPAddress,
  Count(tblNtlog.Eventcode) As [Total Crash Count],
  Max(tblNtlog.TimeGenerated) As [Last Crash Date]
From tblAssets
  Inner Join tblNtlog On tblAssets.AssetID = tblNtlog.AssetID
Where tblNtlog.Eventcode In (41, 6008) And tblNtlog.TimeGenerated > GetDate() -
  30
Group By tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.IPAddress
Order By [Total Crash Count] Desc

```

---

### 13. Alert: Server/PC Tidak Reboot 1 jam

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblAssets.IPAddress,
  tblOperatingsystem.Caption As OS,
  Floor(tblAssets.Uptime / 3600) As [Uptime (Hours)],
  tblAssets.Lastseen
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
  Inner Join tblOperatingsystem On
      tblAssets.AssetID = tblOperatingsystem.AssetID
Where tblAssetCustom.State = 1 And tblAssets.Uptime > 3600
Order By tblAssets.Uptime Desc

```

---

### 14. Alert: Software Baru Diinstall (7 Hari Terakhir)

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Username,
  tblSoftwareUni.softwareName As [Software Name],
  tblSoftware.softwareVersion As Version,
  tblSoftwareUni.SoftwarePublisher As Publisher,
  tblSoftware.Lastchanged As [Install Date]
From tblAssets
  Inner Join tblSoftware On tblAssets.AssetID = tblSoftware.AssetID
  Inner Join tblSoftwareUni On tblSoftware.softID = tblSoftwareUni.SoftID
Where tblSoftware.Lastchanged > GetDate() - 7
Order By [Install Date] Desc

```

---

### 15. Alert: User Masuk Grup "Local Administrator" (Security Risk) Report ini menangkap siapa saja admin lokal yang bukan user domain admin standar.

```sql
Select Top 1000000 tblAssets.AssetName,
  tblUsersInGroup.Domainname,
  tblUsersInGroup.Username,
  tblUsersInGroup.Groupname
From tblAssets
  Inner Join tblUsersInGroup On tblAssets.AssetID = tblUsersInGroup.AssetID
Where tblUsersInGroup.Username Not Like '%Domain Admins%' And
  tblUsersInGroup.Username Not Like '%Administrator%' And
  tblUsersInGroup.Admingroup = 1
Order By tblAssets.AssetName

```

---

### 16. Alert: Windows Patch Level & Uptime Overview

```sql
Select Top 1000000 tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblAssets.Username,
  tblAssets.Userdomain,
  Coalesce(tsysOS.Image, tsysAssetTypes.AssetTypeIcon10) As icon,
  tblAssets.IPAddress,
  tsysIPLocations.IPLocation,
  tblAssetCustom.Manufacturer,
  tblAssetCustom.Model,
  tsysOS.OSname As OS,
  tblAssets.Version,
  tblAssets.BuildNumber,
  Max(tblQuickFixEngineeringUni.HotFixID) As lastPatch,
  Max(Convert(date,tblQuickFixEngineering.InstalledOn)) As lastPatchDate,
  Convert(nvarchar(10),Ceiling(Floor(Convert(integer,tblAssets.Uptime) / 3600 /
  24))) + ' days ' +
  Convert(nvarchar(10),Ceiling(Floor(Convert(integer,tblAssets.Uptime) / 3600 %
  24))) + ' hours ' +
  Convert(nvarchar(10),Ceiling(Floor(Convert(integer,tblAssets.Uptime) % 3600 /
  60))) + ' minutes' As UptimeSinceLastReboot,
  Case
    When tblErrors.ErrorText Is Not Null Or
      tblErrors.ErrorText != '' Then
      'Scanning Error: ' + tsysasseterrortypes.ErrorMsg
    Else ''
  End As ScanningErrors,
  tblAssets.Lastseen,
  tblAssets.Lasttried
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
  Inner Join tsysAssetTypes On tsysAssetTypes.AssetType = tblAssets.Assettype
  Inner Join tsysIPLocations On tsysIPLocations.LocationID =
      tblAssets.LocationID
  Inner Join tblState On tblState.State = tblAssetCustom.State
  Left Join tsysOS On tsysOS.OScode = tblAssets.OScode
  Inner Join tblQuickFixEngineering On tblQuickFixEngineering.AssetID =
      tblAssets.AssetID
  Inner Join tblQuickFixEngineeringUni On tblQuickFixEngineeringUni.QFEID =
      tblQuickFixEngineering.QFEID
  Left Join (Select Distinct Top 1000000 tblErrors.AssetID As ID,
      Max(tblErrors.Teller) As ErrorID
    From tblErrors
    Group By tblErrors.AssetID) As ScanningError On tblAssets.AssetID =
      ScanningError.ID
  Left Join tblErrors On ScanningError.ErrorID = tblErrors.Teller
  Left Join tsysasseterrortypes On tsysasseterrortypes.Errortype =
      tblErrors.ErrorType
Where tblState.Statename = 'Active'
Group By tblAssets.AssetID,
  tblAssets.AssetName,
  tblAssets.Domain,
  tblAssets.Username,
  tblAssets.Userdomain,
  tblAssets.IPAddress,
  tsysIPLocations.IPLocation,
  tblAssetCustom.Manufacturer,
  tblAssetCustom.Model,
  tsysOS.OSname,
  tblAssets.Version,
  tblAssets.BuildNumber,
  tsysOS.Image,
  tsysAssetTypes.AssetTypeIcon10,
  tblAssets.Uptime,
  tblErrors.ErrorText,
  tsysasseterrortypes.ErrorMsg,
  tblAssets.Lastseen,
  tblAssets.Lasttried
Order By tblAssets.Domain,
  tblAssets.AssetName

```

---

### 17. Alert: Windows Updates Chart

```sql
Select Top 1000000 sub1.lastPatch,
  Count(tblAssets.AssetID) As Total
From tblAssets
  Inner Join tblAssetCustom On tblAssets.AssetID = tblAssetCustom.AssetID
  Left Join (Select Top 1000000 Max(tblQuickFixEngineeringUni.HotFixID) As
      lastPatch,
      tblQuickFixEngineering.AssetID
    From tblQuickFixEngineering
      Inner Join tblQuickFixEngineeringUni On tblQuickFixEngineeringUni.QFEID =
          tblQuickFixEngineering.QFEID
    Group By tblQuickFixEngineering.AssetID) As sub1 On sub1.AssetID =
      tblAssets.AssetID
Where sub1.lastPatch Is Not Null And tblAssetCustom.State = 1 And
  tblAssets.Assettype = -1
Group By sub1.lastPatch
Order By Total Desc

```
