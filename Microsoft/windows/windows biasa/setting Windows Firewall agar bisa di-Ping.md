Berikut adalah cara **murni setting Windows Firewall** agar bisa di-Ping dari IP beda segmen tanpa mematikan Firewall (Firewall tetap **ON**).

Lakukan langkah ini di komputer yang mau di-Ping:

### 1. Buka Windows Firewall with Advanced Security

1. Tekan tombol **Windows + R** di keyboard.
2. Ketik **`wf.msc`** lalu tekan **Enter**.

### 2. Buat Aturan Baru (New Rule)

1. Di panel kiri, klik **Inbound Rules**.
2. Di panel kanan (Actions), klik **New Rule...**

### 3. Setting Rule Langkah demi Langkah (Jangan Salah Pilih)

Akan muncul jendela wizard. Ikuti urutan ini:

* **Rule Type:** Pilih **Custom**. (Klik Next)
* **Program:** Pilih **All programs**. (Klik Next)
* **Protocol and Ports:**
* Pada **Protocol type**, ganti dari TCP menjadi **ICMPv4**.
* *(Opsional)* Klik tombol **Customize** di sebelahnya -> Pilih **Specific ICMP types** -> Centang **Echo Request**. (Ini supaya hanya Ping yang lewat, bukan yang lain). Kalau bingung, biarkan default ICMPv4 saja.
* (Klik Next)


* **Scope (PENTING UNTUK BEDA SEGMEN):**
* Pada bagian **"Which remote IP addresses does this rule apply to?"**:
* Pilih **Any IP address** (Agar bisa di-ping dari segmen manapun).
* *ATAU* Pilih **These IP addresses** -> Add -> Masukkan IP subnet lawan (misal `192.168.100.0/24`) jika ingin spesifik.
* (Klik Next)


* **Action:** Pilih **Allow the connection**. (Klik Next)
* **Profile:** Centang semua (**Domain, Private, Public**) agar aturan ini berlaku di semua kondisi jaringan. (Klik Next)
* **Name:** Beri nama, misal **`Buka Ping Semua Segmen`**.
* Klik **Finish**.

### Selesai

Sekarang coba Ping lagi dari komputer beda segmen. Seharusnya statusnya **Reply**, meskipun Windows Firewall di komputer target statusnya **ON (Hijau)**.
