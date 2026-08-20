Intinya: Berikut adalah modul dokumentasi lengkap dari awal hingga akhir seluruh proses konfigurasi dan integrasi Proxmox Mail Gateway (PMG) dengan iRedMail beserta solusi kendala yang telah kita selesaikan.

---

# Modul Dokumentasi Implementasi: Proxmox Mail Gateway (PMG) & iRedMail

**Domain:** `archtech.consulting`

**Provider:** IDCloudHost

---

## 1. Arsitektur & Informasi Jaringan (Private Network)

* **Server Gateway (PMG):**
* Hostname: `pmg.archtech.consulting`
* IP Publik: `103.250.11.18`
* IP Privat: `10.110.59.252`


* **Server Backend (iRedMail):**
* Hostname: `mail.archtech.consulting`
* IP Publik: `103.250.10.38`
* IP Privat: `10.110.59.254`


* **Komunikasi Internal:** Menggunakan subnet jaringan privat IDCloudHost `10.110.59.0/24` untuk lalu lintas aman antar server tanpa melalui internet publik.

---

## 2. Tahap Instalasi & Konfigurasi PMG

### A. Instalasi Sistem Dasar (Debian 12 Bookworm)

1. **Re-install OS VPS** di panel IDCloudHost ke Debian 12 (karena PMG 8 membutuhkan pustaka Bookworm).
2. **Atur Hostname dan Host Files**:
```bash
hostnamectl set-hostname pmg.archtech.consulting

```


Sesuaikan `/etc/hosts` agar memuat IP privat dan hostname dengan benar.

### B. Menambahkan Repositori & Instalasi Proxmox Mail Gateway

```bash
echo "deb http://download.proxmox.com/debian/pmg bookworm pmg-no-subscription" > /etc/apt/sources.list.d/pmg-no-subscription.list
curl -fsSL https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -o /etc/apt/trusted.gpg.d/proxmox-release.gpg
apt update && apt dist-upgrade -y
apt install proxmox-mailgateway -y

```

### C. Penyesuaian Sistem & DNS PMG (Mengatasi Error Sinkronisasi)

* **Kendala:** `pmgconfig sync` gagal karena variabel `mydomain` kosong (`bad string length 0 < 1`).
* **Solusi:**
1. Masuk ke Web GUI PMG (`[https://103.250.11.18:8006](https://103.250.11.18:8006)`).
2. Masuk ke menu **Configuration -> System -> DNS**, klik **Edit**, lalu isi **Search domain** dengan `archtech.consulting`.
3. Daftarkan domain di **Configuration -> Mail Proxy -> Relay Domains**: tambahkan `archtech.consulting`.
4. Atur **Transports**: arahkan domain `archtech.consulting` ke host `10.110.59.254` (IP privat iRedMail) pada port `25`.
5. Masukkan jaringan internal ke **Networks**: `10.110.59.0/24`.



---

## 3. Tahap Konfigurasi iRedMail (Relay & Bypass)

### A. Outbound Relay (iRedMail ke PMG)

* Edit file konfigurasi Postfix di server iRedMail:
```bash
nano /etc/postfix/main.cf

```


* Ubah baris `relayhost` agar melempar email keluar melalui IP privat PMG di port 25:
```text
relayhost = [10.110.59.252]:25

```


* Terapkan perubahan:
```bash
systemctl reload postfix

```



### B. Bypass Greylisting & Keamanan Masuk (PMG ke iRedMail)

* Agar iRedMail tidak menolak email dari PMG dengan pesan *Intentional policy rejection* (Greylisting), daftarkan IP privat PMG ke dalam *whitelist* iRedAPD:
```bash
python3 /opt/iredapd/tools/wblist_admin.py --add --whitelist 10.110.59.252
systemctl restart iredapd postfix

```



---

## 4. Konfigurasi DNS & Keamanan (Cloudflare)

* **MX Record:** Diarahkan ke `pmg.archtech.consulting` (pintu gerbang masuk email).
* **A Record:**
* `archtech.consulting` $\rightarrow$ `103.250.10.38` (iRedMail)
* `mail.archtech.consulting` $\rightarrow$ `103.250.10.38`
* `pmg.archtech.consulting` $\rightarrow$ `103.250.11.18` (PMG)


* **SPF Record (TXT):**
```text
v=spf1 ip4:103.250.11.18 ~all

```


* **DKIM Record (TXT):**
* Di PMG (**Configuration -> Mail Proxy -> DKIM**), aktifkan *Sign All Outgoing Mail* (Yes) dengan selector `pmg`.
* Ambil *public key* dan buat TXT record di Cloudflare dengan nama `pmg._domainkey`.


* **DMARC Record (TXT):**
```text
v=DMARC1; p=none; rua=mailto:admin@archtech.consulting

```



---

## 5. Konfigurasi Reverse DNS (PTR Record)

* **Kendala:** Provider IDCloudHost menyembunyikan menu pengaturan PTR di panel mandiri.
* **Solusi:** Membuka tiket dukungan teknis ke IDCloudHost untuk memetakan IP Publik PMG (`103.250.11.18`) agar berkorespondensi balik ke `pmg.archtech.consulting`.
