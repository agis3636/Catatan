
Berikut adalah beberapa rekomendasi skenario *lab* yang sangat praktis dan relevan untuk dipelajari:

**1. Segmentasi Antar-Entitas (VLAN & VRF)**
Anda bisa mempraktikkan cara memisahkan trafik dua entitas bisnis yang berbeda (misalnya memisahkan trafik PT KUM dan PT IPA) di atas satu infrastruktur fisik yang sama.

* **Fokus Konfigurasi:** Pembuatan VLAN, *Trunking* (802.1Q), dan implementasi *Virtual Routing and Forwarding* (VRF). Dengan VRF, Anda bisa membuat tabel *routing* yang terisolasi untuk masing-masing entitas agar datanya tidak saling bocor, meskipun berada di *switch* yang sama.

**2. Topologi Core-Access & Link Aggregation (LACP)**
Mensimulasikan switch Aruba CX 6300 sebagai *Core/Aggregation Layer* (Layer 3) dan Aruba CX 6100 sebagai *Access Layer* (Layer 2).

* **Fokus Konfigurasi:** Menggabungkan dua atau lebih *interface* antar *switch* menjadi satu jalur logis menggunakan LACP (*Link Aggregation Control Protocol*). Ini sangat berguna untuk menyimulasikan penggabungan *bandwidth* (seperti koneksi *uplink* 10G) dan mengamankan jalur (*redundancy*) jika salah satu kabel putus.

**3. Dynamic Routing Internal (OSPF)**
Mengingat infrastruktur beroperasi murni di jaringan internal tanpa *routing* ke domain publik luar, penggunaan OSPF (*Open Shortest Path First*) sangat efisien untuk mendistribusikan rute secara otomatis.

* **Fokus Konfigurasi:** Mengaktifkan OSPFv2 di Aruba CX 6300. Anda bisa membuat beberapa *Loopback Interface* untuk menyimulasikan berbagai segmen jaringan lokal, lalu membagikan rute-rute tersebut antar *switch* (Layer 3) agar saling mengenali tanpa harus membuat *Static Route* satu per satu.

**4. First-Hop Redundancy Protocol (VRRP)**
Menyimulasikan skenario *High Availability* di level *gateway* agar pengguna (*user*) tidak mengalami putus koneksi jika salah satu *switch Core* mati.

* **Fokus Konfigurasi:** Memasang dua *switch* Aruba yang bertindak sebagai *router*, lalu mengonfigurasi VRRP (*Virtual Router Redundancy Protocol*). Anda bisa melakukan tes dengan mematikan salah satu *switch* di EVE-NG dan melihat apakah trafik ICMP (*ping*) dari PC klien (*node* virtual) tetap berjalan lancar ke *gateway* virtual.

**5. Command Line AOS-CX Familiarization**
Karena sistem operasi AOS-CX sedikit berbeda dari perangkat lain, Anda bisa membiasakan diri dengan perintah sehari-hari.

* **Fokus Konfigurasi:** Melatih perintah seperti `allow-unsupported-transceiver confirm` untuk modul optik pihak ketiga, mengonfigurasi fitur keamanan *port* dasar, serta menggunakan fitur `show lldp neighbors` untuk memetakan topologi secara otomatis.

