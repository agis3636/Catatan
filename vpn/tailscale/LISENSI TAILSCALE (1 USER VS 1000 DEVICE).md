# BEDAH KASUS: LISENSI TAILSCALE (1 USER VS 1000 DEVICE)

Di kantor sekarang 1 akun (misal `admin@kum.co.id`) dipakai login ke 20 PC/Server dan *ternyata bisa jalan normal*. Secara teknis, lu memang bisa memaksa 1 akun login ke puluhan atau ratusan device. 

Tapi, makna kata **"Unlimited"** di halaman *Pricing* Tailscale itu adalah **Unlimited Personal Devices** (perangkat pribadi si karyawan). Tailscale membebaskan 1 karyawan (misal: Agis) untuk menginstal Tailscale di Laptop Windows, MacBook, iPhone, iPad, dan Mini PC di rumahnya, tanpa dikenakan biaya per-device.

Jika lu memproyeksikan trik "1 Akun untuk 1.000 Device" ke klien level *Enterprise*, ini adalah **Bencana Arsitektur**. Berikut alasannya untuk lu jelaskan ke klien:

### 1. Keamanan & Audit (Zero Trust Hancur)
Inti dari Tailscale adalah *Identity-aware* (berbasis identitas). Kalau 20 orang atau 1.000 server menggunakan 1 akun email yang sama, saat terjadi kebocoran data atau *hack*, **lu tidak akan bisa melacak dari log siapa pelakunya atau mesin mana yang bermasalah**. Semua tercatat melakukan aksi atas nama 1 akun tersebut.

### 2. Mimpi Buruk Karyawan Resign (Single Point of Failure)
Katakanlah akun yang dipakai di 1.000 device itu menggunakan email si Bos IT. Suatu hari si Bos resign atau akun Google/Microsoft-nya di-*suspend* oleh HRD. Akibatnya? **Seluruh 1.000 device dan server di cabang tersebut akan TERPUTUS koneksinya detik itu juga.** Jaringan lumpuh total.

### 3. Masalah "Key Expiry"
Tailscale punya fitur keamanan *Node Key Expiry* (sesi login kadaluarsa tiap 90 atau 180 hari). Bayangkan tim IT harus keliling meremote atau datang fisik ke 1.000 PC cabang hanya untuk "Re-authenticate" (Login ulang SSO) setiap 6 bulan karena menggunakan akun manusia.

---

### BAGAIMANA CARA BENARNYA DI ENTERPRISE?

Sebagai vendor yang kompeten, arahkan klien menggunakan arsitektur yang benar dan justru jauh lebih murah & elegan:

*   **Tim IT / Manusia (Karyawan Remote):** 
    Gunakan lisensi Standard/Premium ($8 atau $18). 1 Karyawan = 1 Lisensi = Unlimited *Personal Device*.
*   **Server / Mesin Headless (Tanpa Manusia):**
    Gunakan fitur **Tagged Resources** (misal: `tag:server-database`). Tailscale memberi gratis 50 *Tagged Resources*, dan sisanya cuma bayar **$1/bulan per mesin**. Mesin tidak pakai email manusia, jadi kalau karyawan resign, server tetap hidup. Mesin dengan *Tag* juga tidak terkena *Key Expiry*.
*   **Cabang (Ribuan CCTV, Printer, PC Kasir):**
    Gunakan fitur **Subnet Router**. Lu **TIDAK PERLU** menginstal Tailscale di 8.000 PC cabang. Cukup instal Tailscale di **1 Mini PC Linux atau OPNsense** di tiap cabang. 1 alat ini akan menjembatani seluruh LAN cabang tersebut ke pusat. Lu hemat ribuan lisensi!
```eof

