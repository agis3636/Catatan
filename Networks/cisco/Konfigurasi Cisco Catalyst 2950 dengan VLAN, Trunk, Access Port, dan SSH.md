# **Konfigurasi Cisco Catalyst 2950 dengan VLAN, Trunk, Access Port, dan SSH**

Perangkat: Cisco Catalyst 2950 · IOS 12.1(22)EA14 · Router: UCG Ultra

## **1. Topologi jaringan**

![WS-C2950-24-CISCO Cisco Network Switch](https://images.openai.com/static-rsc-4/vxL4dI_CHP6Lz7uQyxb00pDtNT5jHf6E1wY2nharihgocA7oT_DAkLizH74NURcm6ojwGclZniXb15S2tEvsOfw4oq6PkSN52L0qMfcRm0h0Hf1zlRGiIK5U4H7VIk-nSoj5Ke4RVaSHfPyhfAa5_us6mADwGqVKqY5nG3gFqp0?purpose=inline)

<img width="483" height="234" alt="image" src="https://github.com/user-attachments/assets/350b2ddb-001b-43a7-82f2-d9c6ef95ead8" />

### **IP Addressing**

| VLAN | Network          | Gateway       |
| ---- | ---------------- | ------------- |
| 50   | 192.168.50.0/24  | 192.168.50.1  |
| 80   | 192.168.80.0/24  | 192.168.80.1  |
| 150  | 192.168.150.0/24 | 192.168.150.1 |

IP management Cisco: `192.168.50.13/24`.

## **2. Konfigurasi UCG Ultra**

![Configuring VLANs on UniFi Switches](https://images.openai.com/static-rsc-4/4af36C4EV2yXZKhV4sIAc9VtIOtFxC8hyJHgAKMmX-DM4SxGo23B_to8y3etE3o9P5TClBkw3Go0IxQQ8ttpTQNg3auJYm9HobWlY0DZq-LOB-iWJxHD4AFyrktarTvI-njHHJGdZvcMPzoOZMyQq6IGurqqcPyre7E4eBoYqQg?purpose=inline)

Bagian ini dilakukan dari UniFi Network.

### **A. Membuat VLAN Network**

Masuk ke Settings → Networks, buat tiga network:

| Network Name | VLAN ID | Gateway          |
| ------------ | ------- | ---------------- |
| KUM-JKT      | 50      | 192.168.50.1/24  |
| Vlan-Testing | 80      | 192.168.80.1/24  |
| vlan-port2   | 150     | 192.168.150.1/24 |

Pengaturan masing-masing:

- Gateway/Subnet: sesuai tabel.
- DHCP Mode: DHCP Server.
- DHCP Range: tentukan sesuai kebutuhan, pastikan IP management `.13` tidak dibagikan kepada client.

**Fungsi:** UCG menjadi gateway untuk ketiga VLAN sekaligus menyediakan DHCP dan akses internet.

### **B. Konfigurasi port UCG**

Masuk ke Devices → UCG Ultra → Ports → Port 2.

Atur:

| Pengaturan             | Nilai                               |
| ---------------------- | ----------------------------------- |
| Native VLAN / Network  | KUM-JKT (50)                        |
| Tagged VLAN Management | Custom                              |
| Tagged VLAN            | Vlan-Testing (80), vlan-port2 (150) |

**Fungsi:** Port 2 menjadi jalur trunk yang membawa ketiga VLAN menuju Cisco.

## **3. Persiapan Cisco Catalyst 2950**

Hubungkan laptop ke Cisco menggunakan kabel console.

Buka aplikasi terminal, gunakan pengaturan serial:

| Parameter    | Nilai |
| ------------ | ----- |
| Baud Rate    | 9600  |
| Data Bits    | 8     |
| Parity       | None  |
| Stop Bits    | 1     |
| Flow Control | None  |

Masuk ke privileged EXEC:

```
Switch> enable
```

Masuk ke global configuration:

```
Switch# configure terminal
```

Atur hostname:

```
Switch(config)# hostname SW-C2950
```

**Fungsi:** Mengubah nama switch agar mudah dikenali saat konfigurasi.

Nonaktifkan DNS lookup:

```
SW-C2950(config)# no ip domain-lookup
```

**Fungsi:** Mencegah Cisco mencoba mencari DNS ketika ada kesalahan pengetikan perintah.

## **4. Membuat VLAN**

Buat tiga VLAN sesuai konfigurasi UCG.

**VLAN 50**

```
vlan 50
name KUM-JKT
exit
```

**VLAN 80**

```
vlan 80
name Vlan-Testing
exit
```

**VLAN 150**

```
vlan 150
name vlan-port2
exit
```

Keterangan:

| Sintaks        | Fungsi                       |
| -------------- | ---------------------------- |
| `vlan 50`      | Membuat VLAN ID 50           |
| `name KUM-JKT` | Memberikan nama VLAN         |
| `exit`         | Keluar dari konfigurasi VLAN |

Cek hasil:

```
show vlan brief
```

## **5. Konfigurasi Trunk**

Port Fa0/1 digunakan untuk menghubungkan Cisco ke UCG Ultra.

```
interface fa0/1
switchport mode trunk
switchport trunk native vlan 50
switchport trunk allowed vlan 50,80,150
no shutdown
exit
```

**Fungsi setiap perintah:**

| Sintaks                                   | Fungsi                                 |
| ----------------------------------------- | -------------------------------------- |
| `interface fa0/1`                         | Memilih port Fa0/1                     |
| `switchport mode trunk`                   | Mengubah port menjadi trunk            |
| `switchport trunk native vlan 50`         | Menentukan VLAN native 50              |
| `switchport trunk allowed vlan 50,80,150` | Mengizinkan ketiga VLAN melewati trunk |
| `no shutdown`                             | Mengaktifkan interface                 |

Cisco 2950 menggunakan 802.1Q untuk trunk. Tidak perlu mengetik `switchport trunk encapsulation dot1q`.

**Hasil yang diharapkan:**

- VLAN 50: Untagged / Native.
- VLAN 80: Tagged.
- VLAN 150: Tagged.

Native VLAN di Cisco harus sama dengan Native VLAN di UCG.

## **6. Konfigurasi Access Port**

Sekarang kita menentukan pembagian port untuk perangkat akhir.

### **A. Port Fa0/2–Fa0/8 → VLAN 50**

![VLAN Riassunto Maturità: Segmentazione Reti | Maturando](https://images.openai.com/static-rsc-4/iVRVFwHNm2Idmnnmpg1XQsM2uIF2Z7T4RfmqwkW1DbXLNWQXBsaIMPhIHfEQJgZghpqDwjscxi_vsVRozx9PgVcd-wVVRa2t5jMKzqAliRjbO6MCM3xI6v1fM-JFGPCWgkw4PHgUx0VHZ1nGIxnNCNpGHHRokyiKA_1Qappv-LQ?purpose=inline)

```
interface range fa0/2 - 8
switchport mode access
switchport access vlan 50
spanning-tree portfast
exit
```

### **B. Port Fa0/9–Fa0/16 → VLAN 80**

```
interface range fa0/9 - 16
switchport mode access
switchport access vlan 80
spanning-tree portfast
exit
```

### **C. Port Fa0/17–Fa0/24 → VLAN 150**

```
interface range fa0/17 - 24
switchport mode access
switchport access vlan 150
spanning-tree portfast
exit
```

**Fungsi sintaks:**

| Sintaks                     | Fungsi                                              |
| --------------------------- | --------------------------------------------------- |
| `interface range`           | Memilih beberapa port sekaligus                     |
| `switchport mode access`    | Menjadikan port access                              |
| `switchport access vlan 50` | Menetapkan VLAN untuk port                          |
| `spanning-tree portfast`    | Mempercepat port menuju forwarding untuk end-device |

Catatan: PortFast hanya digunakan pada port yang terhubung ke perangkat akhir seperti PC, printer, atau server. Jangan gunakan pada koneksi antarswitch.

## **7. Konfigurasi IP Management Static**

Cisco Catalyst 2950 merupakan Layer 2 switch. IP management digunakan untuk mengakses switch melalui jaringan, bukan untuk memberikan internet kepada client.

Kita gunakan VLAN 50.

Masuk ke interface VLAN 1:

```
interface vlan 1
no ip address
shutdown
exit
```

Masuk ke interface VLAN 50:

```
interface vlan 50
ip address 192.168.50.13 255.255.255.0
no shutdown
exit
```

Atur default gateway:

```
ip default-gateway 192.168.50.1
```

**Penjelasan:**

| Sintaks              | Fungsi                               |
| -------------------- | ------------------------------------ |
| `interface vlan 1`   | Memilih interface virtual VLAN 1     |
| `no ip address`      | Menghapus IP management VLAN 1       |
| `shutdown`           | Menonaktifkan SVI VLAN 1             |
| `interface vlan 50`  | Memilih SVI VLAN 50                  |
| `ip address`         | Memasang IP management static        |
| `no shutdown`        | Mengaktifkan SVI VLAN 50             |
| `ip default-gateway` | Menentukan gateway management switch |

Ingat: IP management cukup satu. VLAN 50 dipilih karena menjadi jaringan administrasi switch.

**VLAN 1 tetap ada sebagai VLAN default**, tetapi tidak digunakan untuk management.

## **8. Simpan Konfigurasi**

```
end
write memory
```

Fungsi:

- `end`: keluar dari configuration mode.
- `write memory`: menyimpan running-config ke startup-config agar konfigurasi tidak hilang setelah reboot.

## **9. Verifikasi VLAN dan IP**

Jalankan:

```
show vlan brief
```

Melihat pembagian VLAN dan port access.

```
show interfaces trunk
```

Melihat status trunk, native VLAN, dan VLAN yang diizinkan.

```
show ip interface brief
```

Melihat IP management dan status interface.

```
ping 192.168.50.1
```

Memastikan Cisco dapat menjangkau gateway UCG.

Target:

- Fa0/1 = trunking.
- Fa0/2–8 = VLAN 50.
- Fa0/9–16 = VLAN 80.
- Fa0/17–24 = VLAN 150.
- VLAN 50 = `192.168.50.13`, status up/up.

## **10. Konfigurasi SSH**

SSH digunakan agar switch dapat dikelola melalui jaringan tanpa kabel console.

### **A. Atur domain dan username**

```
configure terminal
hostname SW-C2950
ip domain-name kum.local
username admin privilege 15 password 0 PASSWORD_KAMU
```

Penjelasan:

- `ip domain-name`: menetapkan domain untuk kebutuhan pembentukan RSA key.
- `username admin`: membuat akun login.
- `privilege 15`: memberikan hak akses administrator.
- `password 0`: memasukkan password dalam bentuk teks saat konfigurasi.

### **B. Generate RSA Key**

```
crypto key generate rsa
```

Ketika muncul:

```
How many bits in the modulus [512]:
```

Masukkan:

```
1024
```

RSA key digunakan untuk identitas dan proses keamanan koneksi SSH.

### **C. Aktifkan SSH Version 2**

```
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 3
```

| Sintaks                           | Fungsi                           |
| --------------------------------- | -------------------------------- |
| `ip ssh version 2`                | Mengaktifkan SSH versi 2         |
| `ip ssh time-out 60`              | Batas waktu autentikasi 60 detik |
| `ip ssh authentication-retries 3` | Maksimal 3 percobaan login       |

### **D. Atur akses remote**

```
line vty 0 15
login local
transport input ssh
exit
```

Penjelasan:

- `line vty 0 15`: memilih jalur akses remote.
- `login local`: memakai username dan password lokal Cisco.
- `transport input ssh`: mengizinkan SSH dan menolak Telnet.

Matikan web server HTTP yang tidak digunakan:

```
no ip http server
```

Simpan:

```
end
write memory
```

### **E. Verifikasi SSH**

```
show ip ssh
```

Perintah ini menampilkan status SSH.

## **11. Konfigurasi SSH di Windows** / **Koneksi SSH dari Windows**

**Tes pertama — CMD**

Gunakan perintah lengkap berikut karena Cisco 2950 menawarkan algoritma SSH generasi lama:

```
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa -oCiphers=+3des-cbc -oMACs=+hmac-sha1 admin@192.168.50.13
```

Masukkan password admin saat diminta.

**Supaya tidak perlu mengetik perintah panjang setiap kali:**

Buat konfigurasi SSH di Windows. Jalankan CMD:

```
if not exist "%USERPROFILE%\.ssh" mkdir "%USERPROFILE%\.ssh"
notepad "%USERPROFILE%\.ssh\config"
```

Masukkan konfigurasi berikut ke Notepad:

```
Host cisco2950
    HostName 192.168.50.13
    User admin
    KexAlgorithms +diffie-hellman-group1-sha1
    HostKeyAlgorithms +ssh-rsa
    Ciphers +3des-cbc
    MACs +hmac-sha1
```

**Fungsi konfigurasi:**

| Parameter           | Fungsi                               |
| ------------------- | ------------------------------------ |
| `Host cisco2950`    | Membuat nama alias koneksi           |
| `HostName`          | Menentukan IP tujuan                 |
| `User`              | Menentukan username                  |
| `KexAlgorithms`     | Mengatur algoritma pertukaran kunci  |
| `HostKeyAlgorithms` | Mengatur algoritma identitas server  |
| `Ciphers`           | Mengatur algoritma enkripsi          |
| `MACs`              | Mengatur autentikasi integritas data |

Simpan dengan nama `config` (tanpa `.txt`).

Mulai sekarang cukup satu perintah

```
ssh cisco2950
```

Selesai. Tidak perlu parameter panjang lagi.

Catatan keamanan: algoritma SSH lama ini lemah dan hanya layak digunakan pada jaringan management internal tepercaya. Jangan membuka akses SSH switch ke internet.

## **12. Ringkasan konfigurasi akhir**

| Komponen           | Konfigurasi   |
| ------------------ | ------------- |
| Uplink             | Fa0/1         |
| Mode uplink        | Trunk         |
| Native VLAN        | 50            |
| Tagged VLAN        | 80, 150       |
| Access VLAN 50     | Fa0/2–Fa0/8   |
| Access VLAN 80     | Fa0/9–Fa0/16  |
| Access VLAN 150    | Fa0/17–Fa0/24 |
| Management IP      | 192.168.50.13 |
| Management Gateway | 192.168.50.1  |
| Remote Access      | SSH v2        |
| Username           | admin         |
| SSH Port           | TCP 22        |

### **Perintah yang sering digunakan**

| Perintah                  | Kegunaan                        |
| ------------------------- | ------------------------------- |
| `show vlan brief`         | Cek VLAN dan access port        |
| `show interfaces trunk`   | Cek trunk                       |
| `show interfaces status`  | Cek kondisi port                |
| `show mac-address-table`  | Melihat MAC Address             |
| `show ip interface brief` | Melihat IP dan status interface |
| `show running-config`     | Melihat konfigurasi aktif       |
| `show version`            | Melihat versi IOS               |
| `show ip ssh`             | Melihat status SSH              |
| `write memory`            | Menyimpan konfigurasi           |

**Catatan keamanan:** SSH pada Catalyst 2950 ini menggunakan algoritma lama seperti `diffie-hellman-group1-sha1` dan `3des-cbc`. Batasi akses management pada jaringan internal tepercaya dan jangan membuka port SSH switch ke internet. Dukungan SSH dan 3DES merupakan bagian dari kemampuan image kriptografi Catalyst 2950 generasi tersebut.

**Selesai.** Ini mencakup alur dari switch kosong sampai remote SSH, dengan topologi dan pembagian VLAN yang sudah kita gunakan.

## **Dokumentasi resmi**

1. [Cisco Catalyst 2950/2955 — Command Reference](https://www.cisco.com/c/dam/en/us/td/docs/switches/lan/catalyst2955/software/release/12_1_12c_ea1/command/reference/cr2955.pdf?utm_source=chatgpt.com) — referensi perintah IOS dan VLAN default.
2. [Cisco Catalyst 2950 — System Message Guide](https://www.cisco.com/c/dam/en/us/td/docs/switches/lan/catalyst2950/software/release/12-1_22_ea2/system/messages/smg2950.pdf?utm_source=chatgpt.com) — dokumentasi sistem dan referensi seri 2950.
3. [Ubiquiti — Switch Port VLAN Assignment](https://help.ui.com/hc/en-us/articles/26136855808919-Switch-Port-VLAN-Assignment-Trunk-Access-Ports?utm_source=chatgpt.com) — penjelasan trunk, access, native dan tagged VLAN.
4. [Ubiquiti — UniFi Switch Settings](https://help.ui.com/hc/en-us/articles/33402927617047-UniFi-Switch-Settings?utm_source=chatgpt.com) — pengaturan port dan tagged VLAN.
5. [Cisco — Configuring Secure Shell](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960x/software/15-2_5_e/configuration_guide/b_1525e_consolidated_2960x_cg/b_1525e_consolidated_2960x_cg_chapter_0101101.pdf?utm_source=chatgpt.com) — referensi konfigurasi SSH Cisco.

**Target akhir:** Cisco Catalyst 2950 berfungsi sebagai switch Layer 2 dengan tiga VLAN, trunk ke UCG Ultra, pembagian access port, IP management static, dan remote administration melalui SSH.
