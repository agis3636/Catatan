Bedanya perangkat/produk utama Fortinet dan “dll”-nya | Saya kelompokkan biar gampang kebayang arsitekturnya.

# Inti (paling sering dipakai)

* **FortiGate (FGT)** – Next-Gen Firewall/UTM: firewall, IPS/AV, web filter, SSL inspection, SD-WAN, IPsec/SSL VPN, routing. Ini “pintu gerbang” jaringan.
* **FortiManager (FMG)** – **Manajemen terpusat** untuk banyak FortiGate/produk Fortinet: policy packages, template, automation, approval workflow, mass-upgrade.
* **FortiAnalyzer (FAZ)** – **Log & analitik terpusat**: kumpulkan log dari FortiGate/produk lain, buat report, event/IOC, forensic, indikator serangan.
* **FortiGuard Services** – Paket **subscription** (AV/IPS/WebFilter/AppControl, sandbox db, URL rating, dll) yang menambah intelijen ke FortiGate & produk lain.

# LAN/Wi-Fi & WAN

* **FortiSwitch (FSW)** – Switch managed, terintegrasi dengan FortiGate (Security-Driven Networking, NAC policy dari FGT).
* **FortiAP (FAP)** – Access Point Wi-Fi, dikontrol dari FortiGate/FortiLAN Cloud.
* **FortiExtender (FEX)** – Modem/Router **LTE/5G** untuk WAN backup/primary; bisa dikelola dari FortiGate.
* **FortiGate SD-WAN** – Fitur di FortiGate untuk optimasi link (bukan perangkat terpisah). Ada **SD-WAN Orchestrator** via FMG/FG-Cloud untuk skala besar.

# Aplikasi & Web

* **FortiWeb (FWEB)** – **Web Application Firewall (WAF)**: lindungi aplikasi/web dari OWASP Top 10, bot mitigation, API protection.
* **FortiADC** – **Application Delivery Controller**: load balancing L4/L7, SSL offload, GSLB.
* **FortiProxy (FPX)** – **Secure Web Proxy**/SWG on-prem: policy web, caching, SSL inspect terpisah dari FGT bila mau arsitektur proxy dedicated.

# Email & Konten

* **FortiMail (FML)** – **Secure Email Gateway**: anti-spam/phishing, AV, DLP, impersonation protection, MTA.
* **FortiSandbox (FSB)** – Sandbox malware (detonasi file/URL) terintegrasi dengan FGT, FortiMail, FortiClient, dsb.

# Endpoint & Akses

* **FortiClient** – Agent endpoint (VPN/ZTNA, AV, web filter, EDR ketika dikombinasi).
* **FortiEDR** – **Endpoint Detection & Response** tingkat lanjut (real-time detection, response, hunting).
* **FortiEMS** – **Manajemen** FortiClient (deploy, posture, compliance, ZTNA tags).
* **FortiSASE** – **Security as a Service cloud** (SWG, CASB, ZTNA, FWaaS) untuk user/cabang ke cloud POP Fortinet.

# Identitas, NAC, & MFA

* **FortiAuthenticator (FAC)** – AAA/Identity: RADIUS/TACACS+, SSO, sertifikat, integrasi AD/LDAP, portal.
* **FortiToken (FTK)** – Token **MFA** (hardware/soft token) yang dipakai dengan FAC/FGT VPN/administrasi.
* **FortiNAC** – **Network Access Control**: profil perangkat (IoT/OT), kontrol akses, otomatisasi isolasi.

# SOC/SIEM & Otomasi

* **FortiSIEM** – **SIEM**: korelasi log lintas vendor, compliance, visibilitas aset.
* **FortiSOAR** – **Security Orchestration, Automation & Response**: playbook respons insiden, ticketing, integrasi alat SOC.
* **FortiInsight** (eks-ZoneFox) – **UEBA**/insider-threat (lebih niche, untuk visibilitas perilaku user/data).

# Cloud & Manajemen SaaS

* **FortiGate Cloud / FortiCloud** – Manajemen & logging berbasis cloud untuk FortiGate/FortiAP/FSW (alternatif ringan dibanding FMG/FAZ on-prem).
* **FortiCASB** – Visibilitas/policy untuk aplikasi SaaS (kini banyak fungsi disatukan dalam **FortiSASE**).
* **FortiCWP** (lebih lama, kini fungsi menyatu di CNAPP Fortinet) – Posture/security cloud (AWS/Azure/GCP).

# Spesialis/Hardware Security

* **FortiDDoS** – Mitigasi serangan DDoS berbasis hardware.
* **FortiWeb/FortiMail/FortiADC** sudah disebut; ada juga **FortiDeceptor** (honeypot/ deception) untuk menipu attacker (opsional, enterprise).

---

## Ringkas beda fungsi utamanya

* **FortiGate** = firewall/VPN/SD-WAN (kontrol lalu lintas & proteksi jaringan).
* **FortiManager** = **mengelola konfigurasi** banyak perangkat (templat & orkestrasi).
* **FortiAnalyzer** = **mengelola log/insight/report** dari banyak perangkat.
* **Produk “dll”** = komponen pelengkap sesuai kebutuhan: LAN/Wi-Fi (FSW/FAP), endpoint (FortiClient/EMS/EDR), aplikasi/web (FortiWeb/ADC/Proxy), email (FortiMail), identitas/MFA (FAC/Token), SOC (SIEM/SOAR), WAN 5G (Extender), layanan cloud (FortiCloud/SASE), dll.

---
---
---

# Kapan butuh yang mana?

* **Kantor kecil/single site**: FortiGate (+ FortiGuard). Tambah FortiSwitch/FortiAP kalau mau LAN/Wi-Fi dikelola dari FGT. Logging bisa cukup di disk/cloud.
* **Multi-cabang/menengah**: FortiGate di tiap site + **FortiManager** (pusat) + **FortiAnalyzer** (log/report pusat) + SD-WAN.
* **Aplikasi/web/email kritikal**: tambah **FortiWeb** (WAF) / **FortiMail**.
* **SOC/enterprise**: **FortiSIEM + FortiSOAR + FortiEDR**, **FortiNAC**, **Authenticator/Token**.
* **Remote user & cloud-first**: **FortiSASE** (SWG/CASB/ZTNA) + FortiClient/EMS.


---
---
---

# TOPOLOGY

<img width="1536" height="1024" alt="ChatGPT Image Sep 4, 2025, 08_26_37 AM" src="https://github.com/user-attachments/assets/8f60b84f-5eb5-413c-957f-ce697504302d" />

---

## 📌 Skenario

* 1 kantor pusat
* 2 cabang (terhubung via VPN/SD-WAN)
* Email server on-prem di kantor pusat
* Pakai Wi-Fi untuk karyawan & tamu
* Ada user WFH (work from home)

---

## 🏗️ Arsitektur & Produk

### **1. Kantor Pusat**

* **FortiGate** (NGFW + SD-WAN + VPN) → kontrol internet, IPS/AV/WebFilter, VPN IPsec/SSL.
* **FortiSwitch** → LAN switch terintegrasi policy security dari FortiGate.
* **FortiAP** → Wi-Fi access point (employee SSID + guest SSID).
* **FortiMail** → proteksi email server on-prem dari spam/phishing/virus.
* **FortiWeb** → proteksi web app internal (misalnya HR portal, ERP).
* **FortiAnalyzer** → kumpulkan log FortiGate, FortiMail, FortiWeb → laporan compliance.
* **FortiManager** → kelola policy/config semua FortiGate (pusat + cabang).

### **2. Cabang**

* **FortiGate kecil (entry-level model)** → WAN + SD-WAN ke HQ/cloud.
* **FortiSwitch** + **FortiAP** (opsional sesuai kebutuhan).
* Semua config policy di-push dari **FortiManager**.
* Semua log dikirim ke **FortiAnalyzer** pusat.

### **3. Remote User (WFH)**

* **FortiClient** → SSL VPN atau ZTNA Agent.
* **FortiAuthenticator + FortiToken** → MFA untuk login VPN/ZTNA.
* Opsional: kalau user banyak & akses cloud dominan → bisa pakai **FortiSASE** (POPs Fortinet di cloud).

---

## 📦 Lisensi minimum

* **FortiGuard Security Subscription** di tiap FortiGate (IPS, AV, WebFilter, AppControl).
* **FortiAnalyzer** → butuh license sesuai device/log storage.
* **FortiManager** → biasanya base license sudah cukup, tambah kalau device >100.
* **FortiMail** → butuh AV/Antispam subscription.
* **FortiWeb** → butuh WAF subscription (signature update).
* **FortiClient EMS** (jika ingin manage banyak endpoint).
* **FortiToken** (MFA user remote).

---

⚡ Jadi alurnya kira-kira begini:

* Internet → FortiGate HQ → LAN (Switch/AP), Email Server (dilindungi FortiMail), Web App (dilindungi FortiWeb).
* Cabang → FortiGate kecil → SD-WAN VPN ke HQ/cloud.
* User WFH → FortiClient + FortiAuthenticator (MFA) → FortiGate HQ.
* Semua log → FortiAnalyzer, semua config → FortiManager.

---

