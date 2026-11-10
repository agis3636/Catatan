
Berikut adalah settingan tambahan yang sangat direkomendasikan untuk melengkapi pengamanan trafik:

### 1. DNS Filter

* **Fungsi:** Menyaring dan memblokir akses berdasarkan *domain name resolution* (DNS) sebelum koneksi HTTP/HTTPS terbentuk.
* **Kelebihan:** Sangat efektif untuk menangkal situs *phishing*, malware, atau domain baru yang mencurigakan di level DNS.

### 2. File Filter

* **Fungsi:** Mencegah pengguna mendownload atau mengupload jenis file tertentu yang berbahaya atau tidak diizinkan oleh perusahaan.
* **Contoh Penerapan:** Anda bisa memblokir ekstensi file tertentu seperti `.exe`, `.bat`, `.scr`, atau file arsip seperti `.zip`/`.rar` agar tidak bisa di-download via web.

### 3. Antivirus

* **Fungsi:** Melakukan pemindaian (*scanning*) secara *real-time* terhadap lalu lintas web, FTP, atau email untuk mendeteksi virus, trojan, dan malware.
* **Catatan:** Pastikan *Feature set* menggunakan **Proxy-based** atau *Flow-based* dengan *Deep Inspection* agar file di dalam jalur terenkripsi bisa ikut dipindai.

### 4. Intrusion Prevention (IPS)

* **Fungsi:** Mendeteksi dan memblokir serangan jaringan, eksploitasi celah keamanan (*vulnerabilities*), atau trafik aneh yang mengarah ke server atau klien internal.

---







Berikut adalah 4 konfigurasi "Wajib" yang biasanya diterapkan di perusahaan:

### 1. VLAN & Segmentasi Jaringan (Pemisahan Entitas)

Perusahaan pantang menggabungkan semua komputer dalam satu jaringan *flat* (satu segmen IP).

* **Prakteknya:** Dibuat *VLAN Interface* di bawah *port* LAN FortiGate. Misalnya di satu FortiGate, Mas harus mengatur pemisahan trafik antara jaringan operasional PT KUM dan PT IPA agar data internalnya tidak saling bocor atau tercampur, meskipun mereka berada di infrastruktur *hardware* yang sama.
* **Firewall Policy:** Dibuat aturan ketat. Contoh: Klien PT KUM hanya boleh akses server internal mereka sendiri, dan dilarang *ping* atau akses ke segmen IP milik PT IPA, kecuali untuk *port* tertentu yang memang diizinkan secara spesifik.

### 2. Traffic Shaping (Manajemen Bandwidth)

Jangan sampai satu orang *download* file `.iso` Proxmox atau main *game*, lalu satu kantor internetnya *lag* semua. Di sinilah *Traffic Shaping* (QoS) bekerja.

* **Prakteknya:** Masuk ke menu **Policy & Objects > Traffic Shaping**.
* Kita bisa buat aturan *Per-IP Shaper* (membatasi maksimal *speed* tiap komputer, misal cuma 5 Mbps per orang) atau *Shared Shaper* (menggaransi *bandwidth* untuk trafik penting seperti *Zoom Meeting* atau aplikasi internal perusahaan agar tidak terganggu trafik lain).

### 3. Application Control (Blokir VPN Proxy & Torrent)

User jaman sekarang pada pintar. Kalau web diblokir pakai *Web Filter*, mereka pakai *ekstensi* VPN di Chrome atau pakai *Tor Browser* biar tembus.

* **Prakteknya:** Di menu **Security Profiles > Application Control**, admin wajib memblokir kategori **Proxy** dan **P2P** (Peer-to-Peer / Torrent).
* Ini memastikan tidak ada *user* yang bisa membuat "terowongan gelap" untuk membypass *firewall* atau menghabiskan kuota internet perusahaan untuk *download* film.

### 4. SD-WAN (Failover 2 ISP)

Perusahaan biasanya tidak mau ambil risiko internet mati total. Mereka pasang 2 ISP (misal Indihome dan Biznet).

* **Prakteknya:** Di menu **Network > SD-WAN**, kedua *port* ISP tersebut digabung jadi satu virtual interface.
* FortiGate diatur agar otomatis memindahkan jalur (*failover*) jika ISP 1 mati, atau membagi beban (*load balance*) agar trafik *browsing* lewat ISP 1, dan trafik *download/email* lewat ISP 2.

---

