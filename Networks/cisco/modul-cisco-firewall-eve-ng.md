# 🔥 Modul Lab Cisco Firewall di EVE-NG
### *Panduan Lengkap dari Nol sampai Firewall Berjalan*

---

## 📋 Daftar Isi

1. [Perkenalan EVE-NG](#1-perkenalan-eve-ng)
2. [Perangkat yang Dibutuhkan](#2-perangkat-yang-dibutuhkan)
3. [Topologi Lab](#3-topologi-lab)
4. [Rencana IP Address](#4-rencana-ip-address)
5. [Persiapan Canvas EVE-NG](#5-persiapan-canvas-eve-ng)
6. [Konfigurasi MikroTik Hotspot Bypass](#6-konfigurasi-mikrotik-hotspot-bypass)
7. [Konfigurasi Router Cisco IOL](#7-konfigurasi-router-cisco-iol)
8. [Konfigurasi Cisco ASAv Firewall](#8-konfigurasi-cisco-asav-firewall)
9. [Konfigurasi PC Inside (VPCS)](#9-konfigurasi-pc-inside-vpcs)
10. [Konfigurasi PC Outside (VPCS)](#10-konfigurasi-pc-outside-vpcs)
11. [Verifikasi dan Testing](#11-verifikasi-dan-testing)
12. [Konsep Penting yang Dipelajari](#12-konsep-penting-yang-dipelajari)
13. [Troubleshooting Umum](#13-troubleshooting-umum)
14. [Cheat Sheet Command](#14-cheat-sheet-command)
15. [Next Steps](#15-next-steps)

---

## 1. Perkenalan EVE-NG

**EVE-NG (Emulated Virtual Environment - Next Generation)** adalah platform emulasi jaringan berbasis web yang memungkinkan kita membangun lab jaringan virtual tanpa membutuhkan perangkat fisik.

### Kenapa EVE-NG?

| Keunggulan | Keterangan |
|---|---|
| **Berbasis Web** | Akses dari browser, tanpa install client tambahan |
| **Multi-vendor** | Support Cisco, Juniper, Fortinet, MikroTik, dll |
| **Hemat Biaya** | Tidak perlu beli perangkat fisik |
| **Fleksibel** | Bisa bangun topologi apapun |

### Arsitektur Setup Lab Ini

```
┌─────────────────────────────────────────┐
│              PROXMOX HOST               │
│           192.168.2.247                 │
│                                         │
│    ┌────────────────────────────┐        │
│    │        EVE-NG VM           │        │
│    │      192.168.2.43          │        │
│    │                            │        │
│    │   ┌──────────────────┐     │        │
│    │   │   LAB TOPOLOGY   │     │        │
│    │   └──────────────────┘     │        │
│    └────────────────────────────┘        │
└─────────────────────────────────────────┘
              │
         [MikroTik]
        192.168.2.254
              │
          INTERNET
```

---

## 2. Perangkat yang Dibutuhkan

### Image yang Harus Tersedia di EVE-NG

| Image | Tipe | Fungsi | Lokasi di EVE-NG |
|---|---|---|---|
| **Cisco ASAv** | QEMU | Firewall utama | `/opt/unetlab/addons/qemu/` |
| **L2 IOL** | IOL | Switch Layer 2 | `/opt/unetlab/addons/iol/bin/` |
| **L3 IOL** | IOL | Router | `/opt/unetlab/addons/iol/bin/` |
| **VPCS** | Built-in | Simulasi PC/Host | Sudah tersedia di EVE-NG |
| **Network Cloud** | Built-in | Koneksi ke luar | Sudah tersedia di EVE-NG |

### Cek dan Fix Permission setelah Upload Image

```bash
# Jalankan di terminal EVE-NG setelah upload image baru
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

### Versi ASAv yang Disarankan

> ⚠️ **Gunakan ASAv versi 9.x** — paling stabil di EVE-NG Community.  
> ASAv versi terbaru kadang memiliki isu kompatibilitas.

---

## 3. Topologi Lab

<img width="1055" height="615" alt="Image" src="https://github.com/user-attachments/assets/73ed634c-43d6-463d-99b3-f59374b1a26e" />

### Penjelasan Setiap Node

| Node | Role | Keterangan |
|---|---|---|
| **Internet (Cloud0)** | Network Object | Jembatan ke jaringan MikroTik (192.168.2.x) |
| **Router** | ISP Simulation | Menghubungkan lab ke internet, melakukan NAT |
| **ASAv Firewall** | Firewall | Filter traffic, NAT untuk inside network |
| **Switch L2** | LAN Switch | Forward traffic layer 2, plug & play |
| **PC Inside** | End User | Simulasi user di jaringan internal |
| **PC Outside** | Attacker/Internet | Simulasi serangan dari luar |

---

## 4. Rencana IP Address

### IP Plan Lengkap

| Device | Interface | IP Address | Subnet Mask | Gateway | Keterangan |
|---|---|---|---|---|---|
| **Router** | e0/0 | 192.168.2.233 | 255.255.255.0 | - | Ke internet/Cloud0 |
| **Router** | e0/1 | 192.168.100.1 | 255.255.255.0 | - | Ke ASAv outside |
| **ASAv** | Gi0/0 (outside) | 192.168.100.2 | 255.255.255.0 | 192.168.100.1 | Ke Router |
| **ASAv** | Gi0/1 (inside) | 10.10.10.1 | 255.255.255.0 | - | Ke LAN |
| **PC Inside** | eth0 | 10.10.10.10 | 255.255.255.0 | 10.10.10.1 | User LAN |
| **PC Outside** | eth0 | 192.168.2.90 | 255.255.255.0 | 192.168.2.254 | Attacker |
| **Gateway MikroTik** | - | 192.168.2.254 | - | - | Internet Gateway |

### Subnet yang Digunakan

```
192.168.2.0/24   → Jaringan MikroTik (existing network)
192.168.100.0/24 → Link antara Router dan ASAv
10.10.10.0/24    → Jaringan internal (LAN)
```

---

## 5. Persiapan Canvas EVE-NG

### Langkah Membuat Lab Baru

1. Login ke EVE-NG → `http://192.168.2.43`
2. Klik **"Add new lab"**
3. Beri nama lab → Klik **Save**

### Menambahkan Node

**Klik kanan di canvas → "Add Node"**

| Node | Template | Jumlah |
|---|---|---|
| Router | Cisco IOL L3 | 1 |
| Firewall | Cisco ASAv | 1 |
| Switch | Cisco IOL L2 | 1 |
| PC | Virtual PC (VPCS) | 2 |

**Klik kanan di canvas → "Add Network"**

| Network | Type | Name |
|---|---|---|
| Cloud | Management(Cloud0) | Internet |

### Menghubungkan Kabel

> Hover mouse ke node → muncul icon konektor → klik dan drag ke node tujuan

**Urutan koneksi:**

```
PC Outside  ──(eth0)──  Internet(Cloud0)
Internet    ──(e0/0)──  Router
Router      ──(e0/1)──  ASAv (Gi0/0)
ASAv        ──(Gi0/1)── Switch (e0/0)
Switch      ──(e0/1)──  PC Inside (eth0)
```

### Menjalankan Semua Node

```
Klik kanan canvas → "Start all nodes"
```

> ⏳ **ASAv membutuhkan 3–5 menit untuk boot penuh. Bersabar!**

### Cara Membuka Console

```
Double click node → terbuka tab browser baru (HTML5 Console)
```

> ⚠️ Pastikan browser **mengizinkan popup** dari EVE-NG!  
> Jika muncul notif "Pop-up blocked" → klik → Allow pop-ups

---

## 6. Konfigurasi MikroTik Hotspot Bypass

### Kenapa Perlu Bypass?

Jaringan ini menggunakan **MikroTik Hotspot** yang memfilter berdasarkan MAC address. Setiap device baru yang terhubung via Cloud0 perlu didaftarkan agar bisa akses internet.

### Cara Mendapatkan MAC Address

Setelah Router dikonfigurasi IP, jalankan perintah ini di console Router:

```
show interfaces e0/0
```

Output:
```
Ethernet0/0 is up
  Hardware is AmdP2, address is aabb.cc00.3000
                                ↑
                         Ini MAC address-nya!
```

### Konversi Format MAC

```
Cisco Format   : aabb.cc00.3000
MikroTik Format: AA:BB:CC:00:30:00
```

**Rumus konversi:** Pisahkan setiap 2 karakter dengan titik dua (`:`), jadikan huruf kapital.

### Daftarkan ke MikroTik Hotspot

**Buka Winbox → IP → Hotspot → IP Bindings → klik `+`**

| Field | Nilai |
|---|---|
| **MAC Address** | `AA:BB:CC:00:30:00` |
| **IP Address** | `192.168.2.233` |
| **To Address** | *(kosong)* |
| **Server** | `hotspot1` |
| **Type** | `bypassed` ← **WAJIB** |

Atau via Terminal MikroTik:
```
/ip hotspot ip-binding add \
  mac-address=AA:BB:CC:00:30:00 \
  address=192.168.2.233 \
  type=bypassed
```

### Penjelasan Type "bypassed"

```
Type: authenticated  → harus login dulu via halaman hotspot
Type: blocked        → diblokir total
Type: bypassed       → langsung diizinkan tanpa login ✅
```

---

## 7. Konfigurasi Router Cisco IOL

### Fungsi Router di Lab Ini

Router berperan sebagai **simulasi ISP** yang menghubungkan lab ke internet. Router juga melakukan **NAT (Network Address Translation)** agar traffic dari subnet lab (192.168.100.x) bisa keluar ke internet dengan IP yang dikenal MikroTik (192.168.2.233).

### Konfigurasi Lengkap

```
! ============================================
! ROUTER CISCO IOL - KONFIGURASI LENGKAP
! ============================================

enable
configure terminal

! --- Nama Perangkat ---
hostname Router

! --- Interface e0/0 (ke Internet/Cloud0) ---
interface Ethernet0/0
 description TO-INTERNET
 ip address 192.168.2.233 255.255.255.0
 ip nat outside           ! tandai sebagai interface "luar" untuk NAT
 no shutdown
 exit

! --- Interface e0/1 (ke ASAv) ---
interface Ethernet0/1
 description TO-FIREWALL
 ip address 192.168.100.1 255.255.255.0
 ip nat inside            ! tandai sebagai interface "dalam" untuk NAT
 no shutdown
 exit

! --- Default Route ke Internet ---
ip route 0.0.0.0 0.0.0.0 192.168.2.254

! --- DNS Server ---
ip domain-lookup
ip name-server 8.8.8.8
ip name-server 8.8.4.4

! --- NAT: semua subnet lab → IP 192.168.2.233 ---
ip access-list standard LAB-NET
 permit 192.168.100.0 0.0.0.255
 permit 10.10.10.0 0.0.0.255
 exit

ip nat inside source list LAB-NET interface Ethernet0/0 overload

! --- Simpan Konfigurasi ---
end
write memory
```

### Penjelasan Setiap Bagian

#### `ip nat outside` dan `ip nat inside`

```
ip nat outside → interface yang menghadap ke internet
ip nat inside  → interface yang menghadap ke jaringan lokal

Router perlu tahu mana "dalam" dan mana "luar"
supaya bisa melakukan translasi alamat IP dengan benar.
```

#### `ip access-list standard LAB-NET`

```
Mendefinisikan "siapa yang boleh di-NAT"
→ subnet 192.168.100.x (antara Router dan ASAv)
→ subnet 10.10.10.x (jaringan inside LAN)
```

#### `ip nat inside source list LAB-NET interface e0/0 overload`

```
Arti perintah ini:
- source list LAB-NET → NAT-kan semua IP yang ada di LAB-NET
- interface e0/0      → pakai IP e0/0 (192.168.2.233) sebagai IP pengganti
- overload            → banyak IP lokal bisa pakai 1 IP publik (PAT)
```

**Ilustrasi NAT di Router:**

```
PC Inside (10.10.10.10)
         ↓ NAT di ASAv
     192.168.100.2
         ↓ NAT di Router (overload)
     192.168.2.233
         ↓
    INTERNET ✅
```

### Verifikasi Router

```
! Cek interface status
show ip interface brief

! Cek routing table
show ip route

! Cek NAT translations
show ip nat translations

! Test koneksi ke gateway
ping 192.168.2.254

! Test koneksi ke internet
ping 8.8.8.8

! Test DNS
ping google.com
```

**Output `show ip interface brief` yang diharapkan:**

```
Interface    IP-Address      OK? Method Status   Protocol
Ethernet0/0  192.168.2.233   YES manual up       up
Ethernet0/1  192.168.100.1   YES manual up       up
```

---

## 8. Konfigurasi Cisco ASAv Firewall

### Konsep Security Level

Security Level adalah **tingkat kepercayaan** sebuah interface. Nilainya antara 0–100.

```
Security Level 100 = PALING DIPERCAYA (Inside/LAN)
Security Level 50  = SEMI DIPERCAYA (DMZ)
Security Level 0   = TIDAK DIPERCAYA (Outside/Internet)
```

**Aturan default traffic ASAv:**

| Arah Traffic | Security | Diizinkan? |
|---|---|---|
| Inside → Outside | 100 → 0 | ✅ **Otomatis boleh** |
| Outside → Inside | 0 → 100 | ❌ **Otomatis diblokir** |
| DMZ → Outside | 50 → 0 | ✅ **Otomatis boleh** |
| Outside → DMZ | 0 → 50 | ❌ **Otomatis diblokir** |
| Inside → DMZ | 100 → 50 | ✅ **Otomatis boleh** |
| DMZ → Inside | 50 → 100 | ❌ **Otomatis diblokir** |

**Analogi:**

```
🏠 Rumah Lo (Inside = 100)
   → Bisa keluar ke jalan bebas ✅

🌍 Orang asing (Outside = 0)
   → Gak bisa masuk rumah tanpa izin ❌

🏪 Toko (DMZ = 50)
   → Orang luar bisa masuk toko ✅
   → Tapi dari toko gak bisa masuk ke rumah ❌
```

### Konfigurasi Lengkap ASAv

```
! ============================================
! CISCO ASAv FIREWALL - KONFIGURASI LENGKAP
! ============================================

enable
! (masukkan password enable jika diminta)

configure terminal

! --- Nama Perangkat ---
hostname ASAv

! --- Interface Outside (ke Router) ---
interface GigabitEthernet0/0
 nameif outside              ! nama interface = outside
 security-level 0            ! level kepercayaan = 0 (tidak dipercaya)
 ip address 192.168.100.2 255.255.255.0
 no shutdown
 exit

! --- Interface Inside (ke Switch/LAN) ---
interface GigabitEthernet0/1
 nameif inside               ! nama interface = inside
 security-level 100          ! level kepercayaan = 100 (paling dipercaya)
 ip address 10.10.10.1 255.255.255.0
 no shutdown
 exit

! --- Default Route ke Router ---
route outside 0.0.0.0 0.0.0.0 192.168.100.1

! --- NAT untuk Inside Network (PAT) ---
object network inside-net
 subnet 10.10.10.0 255.255.255.0
 nat (inside,outside) dynamic interface
 exit

! --- DNS untuk ASAv sendiri ---
dns domain-lookup outside
dns server-group DefaultDNS
 name-server 8.8.8.8
 name-server 8.8.4.4
 exit

! --- Policy Inspection (izinkan DNS dan ICMP) ---
policy-map global_policy
 class inspection_default
  inspect dns
  inspect icmp
  exit
 exit

! --- Simpan Konfigurasi ---
end
write memory
```

### Penjelasan Setiap Bagian

#### `nameif outside` / `nameif inside`

```
Memberikan nama logis pada interface.
Nama ini digunakan di seluruh konfigurasi ASAv
sebagai pengganti nama interface fisik (Gi0/0, Gi0/1).

Contoh: nat (inside,outside) → pakai nama, bukan Gi0/1 → Gi0/0
```

#### NAT Object (PAT)

```
object network inside-net
 subnet 10.10.10.0 255.255.255.0
```
> Mendefinisikan kelompok IP yang akan di-NAT → seluruh subnet 10.10.10.x

```
 nat (inside,outside) dynamic interface
```
> - `(inside,outside)` → dari zone inside, keluar ke zone outside  
> - `dynamic` → IP berubah-ubah (bukan static mapping)  
> - `interface` → pakai IP interface outside (192.168.100.2) sebagai IP pengganti

**Alur NAT di ASAv:**

```
PC (10.10.10.10) → ping 8.8.8.8
                ↓
ASAv terima paket: src=10.10.10.10, dst=8.8.8.8
                ↓
ASAv NAT: src=192.168.100.2, dst=8.8.8.8
                ↓
Paket keluar ke Router → Internet
                ↓
Reply datang ke 192.168.100.2
                ↓
ASAv tahu ini balasan untuk 10.10.10.10
                ↓
PC menerima reply ✅
```

#### `inspect dns` dan `inspect icmp`

```
ASAv secara default MEMBLOKIR semua traffic masuk dari luar.
Termasuk reply dari internet!

"inspect" = ASAv "mengingat" koneksi yang keluar,
            lalu otomatis mengizinkan balasannya masuk.

inspect dns  → izinkan query DNS dan balasannya
inspect icmp → izinkan ping dan balasannya
```

**Tanpa inspect icmp:**
```
PC Inside ping 8.8.8.8
→ Request keluar ✅
→ Reply datang ke outside ASAv
→ ASAv blokir reply! (karena dari luar) ❌
→ Ping timeout!
```

**Dengan inspect icmp:**
```
PC Inside ping 8.8.8.8
→ Request keluar ✅
→ ASAv catat: "ada ping keluar ke 8.8.8.8 dari 10.10.10.10"
→ Reply datang ke outside ASAv
→ ASAv cek tabel: "ini balasan ping tadi, izinkan masuk!" ✅
→ Ping sukses!
```

### Verifikasi ASAv

```
! Cek interface status
show interface ip brief

! Cek routing table
show route

! Cek NAT translations
show xlate

! Cek NAT policy
show nat

! Test ping ke Router
ping 192.168.100.1

! Test ping ke Gateway
ping 192.168.2.254

! Test DNS
ping google.com
```

**Output `show interface ip brief` yang diharapkan:**

```
Interface          IP-Address   OK? Method Status   Protocol
GigabitEthernet0/0 192.168.100.2 YES manual up      up
GigabitEthernet0/1 10.10.10.1   YES manual up      up
```

---

## 9. Konfigurasi PC Inside (VPCS)

### Tentang VPCS

VPCS (Virtual PC Simulator) adalah emulator PC ringan yang sudah built-in di EVE-NG. Tidak membutuhkan image tambahan dan sangat hemat resource.

**Kemampuan VPCS:**
- Set IP address, subnet, gateway
- Ping & traceroute
- Resolve DNS
- Simulasi basic connectivity

### Konfigurasi

```
! Set IP address, subnet mask, dan gateway
ip 10.10.10.10 255.255.255.0 10.10.10.1

! Set DNS server
ip dns 8.8.8.8

! Simpan konfigurasi (tersimpan ke startup.vpc)
save
```

### Cek Konfigurasi

```
! Lihat detail IP configuration
show ip

! Output:
! NAME     : VPCS[1]
! IP/MASK  : 10.10.10.10/24
! GATEWAY  : 10.10.10.1
! DNS      : 8.8.8.8
! MAC      : 00:50:79:66:68:04
```

### Test Koneksi Bertahap

```
! Step 1: Ping ke gateway ASAv inside
ping 10.10.10.1

! Step 2: Ping ke Router
ping 192.168.100.1

! Step 3: Ping ke internet
ping 8.8.8.8

! Step 4: Test DNS + internet
ping google.com
```

---

## 10. Konfigurasi PC Outside (VPCS)

### Fungsi PC Outside

PC Outside berperan sebagai **simulasi attacker/user dari internet** yang mencoba mengakses jaringan internal. Digunakan untuk:
- Menguji apakah firewall memblokir akses dari luar
- Testing Access Control List (ACL)
- Simulasi serangan

### Konfigurasi

```
! Set IP di subnet 192.168.2.x (sama dengan Cloud0)
ip 192.168.2.90 255.255.255.0 192.168.2.254

! Set DNS
ip dns 8.8.8.8

! Simpan
save
```

### Test Firewall dari Outside

```
! Coba ping ke PC Inside → HARUS DIBLOKIR
ping 10.10.10.10

! Output yang diharapkan:
! 10.10.10.10 icmp_seq=1 timeout
! 10.10.10.10 icmp_seq=2 timeout
! → Firewall bekerja! ✅
```

---

## 11. Verifikasi dan Testing

### Checklist Koneksi Lengkap

```
[ ] Router e0/0 → ping 192.168.2.254  (ke gateway MikroTik)
[ ] Router e0/0 → ping 8.8.8.8        (ke internet)
[ ] Router e0/0 → ping google.com     (DNS + internet)
[ ] ASAv outside → ping 192.168.100.1 (ke Router)
[ ] ASAv outside → ping 192.168.2.254 (ke gateway)
[ ] PC Inside → ping 10.10.10.1       (ke ASAv inside)
[ ] PC Inside → ping 8.8.8.8          (ke internet)
[ ] PC Inside → ping google.com       (DNS + internet)
[ ] PC Outside → ping 192.168.2.233   (ke Router, harus bisa)
[ ] PC Outside → ping 10.10.10.10     (ke inside, HARUS DIBLOKIR)
[ ] PC Inside → ping 192.168.2.90     (ke outside, harus bisa)
```

### Hasil yang Diharapkan

| Test | Dari | Ke | Hasil |
|---|---|---|---|
| Ping gateway | Router | 192.168.2.254 | ✅ Reply |
| Ping internet | Router | 8.8.8.8 | ✅ Reply |
| Ping domain | Router | google.com | ✅ Reply |
| Ping router | ASAv | 192.168.100.1 | ✅ Reply |
| Ping internet | PC Inside | 8.8.8.8 | ✅ Reply |
| Ping domain | PC Inside | google.com | ✅ Reply |
| Ping inside | PC Outside | 10.10.10.10 | ❌ Timeout (diblokir) |
| Ping outside | PC Inside | 192.168.2.90 | ✅ Reply |

### Memahami Hasil `* * *` di Traceroute

```
VPCS> trace 8.8.8.8

1  * * *
2  * * *
3  * * *
```

> ⚠️ `* * *` di traceroute adalah **NORMAL** dan **bukan berarti koneksi mati!**

**Penjelasan:**

```
Traceroute bekerja dengan mengirim paket TTL kecil
dan menunggu balasan "ICMP TTL Exceeded" dari setiap router.

Masalah: Banyak router/firewall MEMBLOKIR ICMP TTL Exceeded:
- ASAv     → tidak forward ICMP TTL Exceeded by default
- MikroTik → memblokir traceroute
- ISP      → banyak yang memblokir traceroute

Solusi: Cukup gunakan PING untuk test koneksi!
```

---

## 12. Konsep Penting yang Dipelajari

### A. Security Level ASAv

```
┌─────────────────────────────────────────────┐
│              CISCO ASAv                     │
│                                             │
│  ┌─────────┐    ┌─────────┐   ┌─────────┐  │
│  │ Outside │    │   DMZ   │   │ Inside  │  │
│  │  Lv: 0  │    │ Lv: 50  │   │ Lv: 100 │  │
│  └─────────┘    └─────────┘   └─────────┘  │
│      │               │             │        │
│   Internet      Web Server       LAN        │
└─────────────────────────────────────────────┘

Traffic dari HIGH ke LOW  → ✅ Otomatis diizinkan
Traffic dari LOW ke HIGH  → ❌ Otomatis diblokir
```

### B. NAT vs PAT

```
NAT (Network Address Translation)
→ 1 IP private = 1 IP public
→ Butuh banyak IP public

PAT (Port Address Translation) / NAT Overload
→ Banyak IP private = 1 IP public (dibedakan via port number)
→ Hemat IP public ✅
→ Yang kita pakai di lab ini!
```

**Ilustrasi PAT:**

```
PC-1 (10.10.10.10:1234) ─┐
PC-2 (10.10.10.11:5678) ─┤→ 192.168.100.2:random_port → Internet
PC-3 (10.10.10.12:9012) ─┘
```

### C. Double NAT

Di lab ini terjadi **Double NAT** (NAT dua kali):

```
PC Inside (10.10.10.10)
         │
         ▼ NAT #1 di ASAv
    192.168.100.2
         │
         ▼ NAT #2 di Router
    192.168.2.233
         │
         ▼
      INTERNET
```

Double NAT diperlukan karena MikroTik Hotspot hanya mengenal IP 192.168.2.233 (Router).

### D. Stateful Inspection

```
ASAv menggunakan "Stateful Inspection":
→ Setiap koneksi keluar dicatat di Connection Table
→ Reply yang masuk dicocokkan dengan Connection Table
→ Jika cocok → diizinkan masuk
→ Jika tidak cocok → diblokir

Ini lebih aman dari packet filtering biasa
karena tidak perlu buka port untuk traffic balasan.
```

### E. Running Config vs Startup Config

```
running-config  = konfigurasi aktif di RAM
                → langsung berubah saat ketik command
                → HILANG jika device dimatikan

startup-config  = konfigurasi yang disimpan di NVRAM/Flash
                → diload saat device boot
                → TIDAK HILANG jika device dimatikan

Perintah untuk menyimpan:
  write memory
  copy running-config startup-config
  (keduanya identik)
```

---

## 13. Troubleshooting Umum

### Masalah 1: Ping gagal ke gateway setelah setting IP

**Gejala:** `Success rate is 0 percent (0/5)`

**Penyebab:** MAC address device belum terdaftar di MikroTik Hotspot

**Solusi:**
```
1. Jalankan: show interfaces e0/0
2. Catat MAC address
3. Buka Winbox → IP → Hotspot → IP Bindings
4. Tambah entry baru dengan type: bypassed
```

### Masalah 2: Konfigurasi hilang setelah restart

**Gejala:** Interface tidak ada IP setelah node restart

**Penyebab:** Lupa `write memory` atau node di-Wipe

**Solusi:**
```
1. Selalu ketik "write memory" setelah selesai config
2. Gunakan "Export CFG" di EVE-NG sebagai backup
3. JANGAN klik "Wipe" kecuali ingin reset total!
```

### Masalah 3: DNS tidak bekerja di ASAv

**Gejala:** `ping google.com` → `ERROR: Invalid Hostname`

**Solusi:**
```
configure terminal
dns domain-lookup outside
dns server-group DefaultDNS
 name-server 8.8.8.8
 name-server 8.8.4.4
exit
```

### Masalah 4: PC Inside tidak bisa ping internet

**Gejala:** Ping ke ASAv inside bisa, tapi ke 8.8.8.8 gagal

**Cek bertahap:**
```
! 1. Cek NAT di ASAv
show nat
show xlate

! 2. Cek route di ASAv
show route

! 3. Pastikan NAT object sudah benar
show run object network

! 4. Cek NAT di Router
show ip nat translations
```

### Masalah 5: Console tidak terbuka (pop-up blocked)

**Gejala:** Double click node tapi tidak ada yang terbuka

**Solusi:**
```
Browser Address Bar → klik icon pop-up blocked → Allow pop-ups for 192.168.2.43
```

### Masalah 6: ASAv menampilkan "Unlicensed"

**Gejala:**
```
Warning: ASAv platform license state is Unlicensed
```

**Penjelasan:** Ini normal di lab EVE-NG. ASAv akan tetap berfungsi untuk keperluan belajar meskipun tanpa lisensi. Hanya beberapa fitur advanced yang terbatas.

---

## 14. Cheat Sheet Command

### Cisco IOS (Router)

```bash
# Mode navigasi
enable                          # masuk privileged mode
configure terminal              # masuk config mode
end                             # keluar ke privileged mode
exit                            # keluar satu level

# Interface
show ip interface brief         # ringkasan semua interface
show interfaces e0/0            # detail interface e0/0

# Routing
show ip route                   # lihat routing table
show ip route 0.0.0.0           # lihat default route

# NAT
show ip nat translations        # lihat tabel NAT aktif
show ip nat statistics          # statistik NAT
clear ip nat translation *      # hapus semua NAT translations

# Save
write memory                    # simpan config
copy running-config startup-config  # sama dengan wr mem

# Debug
ping 8.8.8.8                   # test koneksi
traceroute 8.8.8.8             # traceroute
```

### Cisco ASAv

```bash
# Mode navigasi
enable                          # masuk privileged mode
configure terminal              # masuk config mode

# Interface
show interface ip brief         # ringkasan semua interface
show nameif                     # lihat nameif dan security level

# Route
show route                      # lihat routing table

# NAT
show nat                        # lihat NAT policy
show xlate                      # lihat tabel translasi aktif
clear xlate                     # reset NAT table

# Connection table
show conn                       # lihat koneksi aktif
show conn count                 # jumlah koneksi aktif

# Policy
show service-policy             # lihat inspection policy
show run object network         # lihat semua object network
show run nat                    # lihat semua rule NAT

# Log
show logging                    # lihat log ASA

# Save
write memory
copy running-config startup-config
```

### VPCS

```bash
ip 10.10.10.10 255.255.255.0 10.10.10.1  # set IP/mask/gateway
ip dns 8.8.8.8                            # set DNS
show ip                                   # lihat konfigurasi IP
show                                      # lihat semua VPCS
ping 10.10.10.1                           # ping
ping google.com                           # ping domain
trace 8.8.8.8                            # traceroute
save                                      # simpan konfigurasi
```

---

## 15. Next Steps

Setelah menyelesaikan modul ini, berikut materi lanjutan yang bisa dipelajari:

### Level Menengah

| Topik | Deskripsi |
|---|---|
| **ACL (Access Control List)** | Kontrol traffic lebih granular berdasarkan IP, port, protokol |
| **DMZ Configuration** | Tambah zona untuk server publik (web server, mail server) |
| **Static NAT** | Expose server internal ke internet dengan IP tetap |
| **Object Groups** | Kelompokkan IP/port untuk manajemen ACL lebih mudah |
| **Syslog** | Kirim log ASAv ke server monitoring |

### Level Lanjut

| Topik | Deskripsi |
|---|---|
| **VPN Site-to-Site** | Koneksi aman antar dua kantor lewat internet |
| **VPN Remote Access** | User dari rumah konek aman ke kantor |
| **IPS/IDS** | Deteksi dan cegah serangan |
| **High Availability (HA)** | ASAv aktif-standby untuk redundansi |
| **Cisco FTD** | Next-generation firewall (penerus ASA) |

### Contoh Lab ACL (Preview)

```
! Izinkan HTTP dari outside ke server di inside
access-list OUTSIDE-IN permit tcp any host 10.10.10.20 eq 80
access-list OUTSIDE-IN permit tcp any host 10.10.10.20 eq 443

! Terapkan ke interface outside (arah masuk)
access-group OUTSIDE-IN in interface outside
```

---

## 📌 Ringkasan Topologi Final

```
INTERNET
    │
[MikroTik 192.168.2.254]
    │
[Cloud0/Management]
    │         │
[PC Outside]  [Router-Cisco]
192.168.2.90   e0/0: 192.168.2.233
               e0/1: 192.168.100.1
               NAT: LAB-NET → e0/0
                    │
               [ASAv Firewall]
                Gi0/0: outside 192.168.100.2 (sec-level 0)
                Gi0/1: inside  10.10.10.1    (sec-level 100)
                NAT: 10.10.10.x → interface outside
                inspect: dns, icmp
                    │
               [Switch L2]
               (no config needed)
                    │
               [PC Inside]
               10.10.10.10
               gw: 10.10.10.1
               dns: 8.8.8.8
```

---

> 📝 **Catatan:** Modul ini dibuat berdasarkan sesi lab langsung.  
> Konfigurasi dapat berbeda tergantung versi software dan kebutuhan jaringan.

> 🔐 **Security Note:** Konfigurasi di modul ini adalah untuk **tujuan pembelajaran** di lab terisolasi.  
> Untuk production environment, diperlukan hardening dan konfigurasi keamanan tambahan.

---

*Selamat! Kamu telah berhasil membangun dan mengkonfigurasi Cisco Firewall Lab di EVE-NG dari nol!* 🎉
