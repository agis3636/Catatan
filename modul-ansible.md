# 📘 Modul Ansible — Dari Dasar Sampai Deploy

> Modul ini dibuat berdasarkan sesi belajar praktik langsung menggunakan Proxmox + Raspberry Pi sebagai Control Node.

---

## Daftar Isi

1. [Apa itu Ansible?](#1-apa-itu-ansible)
2. [Cara Kerja Ansible](#2-cara-kerja-ansible)
3. [Konsep Utama](#3-konsep-utama)
4. [Persiapan Infrastruktur (Proxmox)](#4-persiapan-infrastruktur-proxmox)
5. [Full Clone vs Linked Clone](#5-full-clone-vs-linked-clone)
6. [Instalasi Ansible](#6-instalasi-ansible)
7. [Konfigurasi IP VM](#7-konfigurasi-ip-vm)
8. [Setup SSH Key](#8-setup-ssh-key)
9. [Membuat Inventory](#9-membuat-inventory)
10. [Membuat & Menjalankan Playbook](#10-membuat--menjalankan-playbook)
11. [Variable per Host](#11-variable-per-host)
12. [Deploy File Berbeda ke VM Berbeda](#12-deploy-file-berbeda-ke-vm-berbeda)
13. [Idempotency](#13-idempotency)
14. [Ansible Vault](#14-ansible-vault)
15. [Kemampuan Lain Ansible](#15-kemampuan-lain-ansible)

---

## 1. Apa itu Ansible?

Ansible adalah tool otomasi IT **open-source** yang digunakan untuk mengelola konfigurasi server, deploy aplikasi, dan otomasi berbagai task IT secara otomatis dan konsisten.

Bayangkan kamu punya 50 server yang harus dikonfigurasi dengan cara yang sama — tanpa Ansible, kamu harus masuk satu-satu dan mengetik perintah yang sama berulang-ulang. Dengan Ansible, cukup tulis instruksi sekali, jalankan sekali, semua server selesai sekaligus.

### Kegunaan Utama

| Kegunaan | Penjelasan |
|---|---|
| Configuration Management | Mengatur konfigurasi server agar tetap konsisten |
| Application Deployment | Deploy aplikasi ke banyak server sekaligus |
| Provisioning | Menyiapkan infrastruktur baru secara otomatis |
| Orchestration | Mengoordinasikan task kompleks di banyak sistem |

### Keunggulan Ansible

| Keunggulan | Penjelasan |
|---|---|
| **Agentless** | Tidak perlu install software tambahan di server target, cukup SSH |
| **Simple** | Menggunakan YAML yang mudah dibaca manusia |
| **Idempotent** | Aman dijalankan berkali-kali, hasilnya selalu sama |
| **Banyak Modul** | Ribuan modul siap pakai (AWS, Docker, Kubernetes, dll.) |

---

## 2. Cara Kerja Ansible

Ansible bekerja dengan prinsip **push-based** — dari satu komputer (Control Node), Ansible masuk ke semua server target lewat SSH dan menjalankan instruksi yang sudah ditulis.

```
Control Node (Laptop/PC/VM Ansible)
        |
        |--- SSH ---> Server/VM 1
        |--- SSH ---> Server/VM 2
        |--- SSH ---> Server/VM 3
```

> **Penting:** Hanya Control Node yang perlu install Ansible. Server target tidak perlu install apa-apa — cukup bisa diakses via SSH.

---

## 3. Konsep Utama

| Istilah | Penjelasan |
|---|---|
| **Control Node** | Komputer/VM tempat Ansible diinstall. Dari sinilah semua perintah dijalankan. |
| **Managed Node** | Server target yang akan dikontrol oleh Ansible. |
| **Inventory** | File berisi daftar IP/hostname server yang akan dikelola. |
| **Playbook** | File YAML berisi daftar instruksi (task) yang akan dijalankan. |
| **Task** | Satu perintah/aksi dalam playbook (contoh: install nginx). |
| **Module** | Unit kerja bawaan Ansible (contoh: `apt`, `copy`, `service`). |
| **Role** | Kumpulan playbook yang terorganisir dan bisa dipakai ulang. |
| **Handler** | Task yang hanya jalan jika ada perubahan (contoh: restart nginx hanya jika config berubah). |

---

## 4. Persiapan Infrastruktur (Proxmox)

Dalam studi kasus ini, infrastruktur yang digunakan adalah:

```
Proxmox
├── [VM] raspberry-ansible  → Control Node (Ansible diinstall di sini)
├── [VM] hasil-ansible-1    → Target VM 1
└── [VM] hasil-ansible-2    → Target VM 2

Template: Debian (sumber clone untuk VM target)
```

VM target dibuat dari **clone template Debian** yang sudah disiapkan sebelumnya di Proxmox.

---

## 5. Full Clone vs Linked Clone

Saat membuat VM dari template di Proxmox, ada dua pilihan metode clone:

| | Full Clone | Linked Clone |
|---|---|---|
| **Cara kerja** | Copy 100% data template | Hanya simpan perubahan dari template |
| **Kemandirian VM** | VM berdiri sendiri, template bisa dihapus | Bergantung pada template |
| **Storage** | Lebih besar | Lebih hemat |
| **Performa** | Lebih baik | Sedikit lebih lambat |
| **Rekomendasi** | Production | Testing / Lab |

> **Rekomendasi:** Untuk lingkungan production gunakan **Full Clone**. Untuk lab/testing, Linked Clone lebih hemat storage.

---

## 6. Instalasi Ansible

Ansible hanya diinstall di **Control Node**. Jalankan perintah berikut:

```bash
# Update package list dulu
sudo apt update

# Install Ansible
sudo apt install ansible -y

# Verifikasi instalasi berhasil
ansible --version
```

Hasil yang diharapkan:

```
ansible 2.10.17
  python version = 3.9.2
```

---

## 7. Konfigurasi IP VM

Masalah umum setelah clone dari template: **semua VM punya IP yang sama** (IP ter-copy dari template). Harus diubah manual via Proxmox console.

Masuk ke masing-masing VM via console Proxmox, lalu edit konfigurasi network:

```bash
sudo nano /etc/network/interfaces
```

Isi untuk **hasil-ansible-1:**

```
auto eth0
iface eth0 inet static
    address 192.168.200.21
    netmask 255.255.255.0
    gateway 192.168.200.1
```

Isi untuk **hasil-ansible-2:**

```
auto eth0
iface eth0 inet static
    address 192.168.200.22
    netmask 255.255.255.0
    gateway 192.168.200.1
```

Restart network setelah disimpan:

```bash
sudo systemctl restart networking
```

> **Perhatian:** Nama interface belum tentu `eth0`, bisa `ens18` atau `enp0s18`. Cek dulu dengan perintah `ip a`.

---

## 8. Setup SSH Key

Agar Ansible bisa masuk ke VM target **tanpa input password setiap kali**, kita perlu setup SSH key. Semua perintah dijalankan di **Control Node (raspberry-ansible)**.

### Konsep SSH Key

```
raspberry-ansible punya KUNCI 🔑
hasil-ansible-1 & 2 punya GEMBOK 🔒

Kita pasang gembok di VM target,
sehingga raspberry bisa masuk bebas pakai kuncinya.
```

### Langkah 1 — Buat SSH Key

```bash
# Di Control Node (raspberry-ansible)
ssh-keygen
# Tekan Enter terus sampai selesai, tidak perlu isi passphrase
```

### Langkah 2 — Kirim Key ke VM Target

Karena VM target menggunakan port SSH 456 (bukan default 22):

```bash
# Kirim ke VM 1
ssh-copy-id -p 456 debian@192.168.200.21

# Kirim ke VM 2
ssh-copy-id -p 456 debian@192.168.200.22

# Masukkan password user debian di VM target (hanya sekali)
```

Hasil sukses:

```
Number of key(s) added: 1
```

### Langkah 3 — Test SSH

```bash
# Harusnya langsung masuk tanpa diminta password
ssh -p 456 debian@192.168.200.21
```

---

## 9. Membuat Inventory

Inventory adalah file yang berisi **daftar server target** yang akan dikelola Ansible.

```bash
# Buat file inventory di home directory
nano ~/inventory.ini
```

Isi file:

```ini
# inventory.ini

[debian_vms]
192.168.200.21 ansible_port=456 ansible_user=debian
192.168.200.22 ansible_port=456 ansible_user=debian
```

| Parameter | Penjelasan |
|---|---|
| `[debian_vms]` | Nama grup, bisa dipakai di playbook untuk menentukan target |
| `ansible_port=456` | Port SSH yang digunakan (default 22) |
| `ansible_user=debian` | Username untuk login ke VM target |
| `ansible_become_pass=xxx` | Password sudo (opsional, jika tiap VM beda password) |

### Test Koneksi

```bash
# Ping semua VM di inventory
ansible -i ~/inventory.ini all -m ping
```

Hasil sukses:

```
192.168.200.21 | SUCCESS => {"ping": "pong"}
192.168.200.22 | SUCCESS => {"ping": "pong"}
```

> **Tanda ~ (tilde):** Dalam Linux, `~` adalah shortcut untuk home directory. `~/inventory.ini` sama dengan `/home/agis/inventory.ini`.

---

## 10. Membuat & Menjalankan Playbook

Playbook adalah file YAML berisi instruksi yang akan Ansible jalankan ke server target.

```bash
nano ~/playbook.yml
```

Isi playbook:

```yaml
---
- name: Setup Semua VM
  hosts: debian_vms
  become: yes

  tasks:

    - name: Update & upgrade
      apt:
        update_cache: true
        upgrade: "dist"

    - name: Install package
      apt:
        name:
          - nginx
          - curl
          - git
        state: present

    - name: Buat user baru
      user:
        name: ansibleuser
        password: "{{ 'password123' | password_hash('sha512') }}"
        shell: /bin/bash
        state: present

    - name: Pastikan nginx jalan
      service:
        name: nginx
        state: started
        enabled: true

    - name: Copy halaman web
      copy:
        src: "files/{{ web_file }}"
        dest: /var/www/html/index.html
```

### Penjelasan Baris per Baris

| Baris | Penjelasan |
|---|---|
| `hosts: debian_vms` | Jalankan ke grup debian_vms di inventory |
| `become: yes` | Gunakan sudo untuk semua task |
| `state: present` | Pastikan ada — jika sudah ada, skip (tidak install ulang) |
| `update_cache: true` | Sama seperti `apt update` |
| `upgrade: "dist"` | Upgrade semua package termasuk dependencies |
| `enabled: true` | Otomatis nyala saat VM restart |
| `{{ web_file }}` | Variable yang nilainya diambil dari inventory per host |

### Menjalankan Playbook

```bash
# Tanpa password sudo (jika sudah passwordless)
ansible-playbook -i ~/inventory.ini ~/playbook.yml

# Dengan input password sudo manual
ansible-playbook -i ~/inventory.ini ~/playbook.yml --ask-become-pass

# Jika password tiap VM beda, tulis di inventory:
# ansible_become_pass=passwordVM
# lalu jalankan tanpa --ask-become-pass
```

### Membaca Output Playbook

| Status | Warna | Artinya |
|---|---|---|
| `ok` | Hijau | Task berhasil, tidak ada perubahan |
| `changed` | Kuning/Oranye | Task berhasil dan ada perubahan |
| `failed` | Merah | Task gagal |
| `skipped` | Biru | Task dilewati (kondisi tidak terpenuhi) |

Contoh PLAY RECAP sukses:

```
192.168.200.21 : ok=4  changed=3  failed=0  skipped=0
192.168.200.22 : ok=4  changed=3  failed=0  skipped=0
```

---

## 11. Variable per Host

Ansible memungkinkan setiap host di inventory punya variable yang berbeda. Berguna saat kita ingin tiap VM melakukan hal yang sedikit berbeda.

```ini
[debian_vms]
192.168.200.21 ansible_port=456 ansible_user=debian web_file="vm1.html"
192.168.200.22 ansible_port=456 ansible_user=debian web_file="vm2.html"
```

Di playbook, variabel dipanggil dengan sintaks `{{ nama_variable }}`.

> Untuk konten pendek, variable bisa langsung ditulis di inventory. Untuk konten panjang (seperti file HTML), lebih baik buat file terpisah.

---

## 12. Deploy File Berbeda ke VM Berbeda

Studi kasus: VM1 menampilkan "Halo Badak" dan VM2 menampilkan "Halo Singa" di halaman web nginx.

### Struktur Folder

```
/home/agis/
├── inventory.ini
├── playbook.yml
└── files/
    ├── vm1.html    ← konten untuk VM1
    └── vm2.html    ← konten untuk VM2
```

### Langkah 1 — Buat folder files

```bash
mkdir ~/files
```

### Langkah 2 — Buat file HTML

```bash
nano ~/files/vm1.html
```

```html
<!DOCTYPE html>
<html>
<body>
    <h1>Halo Badak</h1>
</body>
</html>
```

```bash
nano ~/files/vm2.html
```

```html
<!DOCTYPE html>
<html>
<body>
    <h1>Halo Singa</h1>
</body>
</html>
```

### Langkah 3 — Update inventory

```ini
[debian_vms]
192.168.200.21 ansible_port=456 ansible_user=debian web_file="vm1.html"
192.168.200.22 ansible_port=456 ansible_user=debian web_file="vm2.html"
```

### Langkah 4 — Task di playbook

```yaml
- name: Copy halaman web
  copy:
    src: "files/{{ web_file }}"
    dest: /var/www/html/index.html
```

Ansible otomatis memilih file yang sesuai: VM1 mendapat `vm1.html`, VM2 mendapat `vm2.html`.

---

## 13. Idempotency

Idempotency adalah salah satu keunggulan terbesar Ansible. Artinya: **jalankan playbook 1x atau 100x, hasilnya selalu sama.**

Jika sebuah task sudah selesai sebelumnya (misalnya package sudah terinstall), Ansible tidak akan mengeksekusi ulang — cukup menampilkan status `ok` dan lanjut ke task berikutnya.

| | Run Pertama | Run Kedua |
|---|---|---|
| Update & upgrade | changed | ok (sudah up-to-date) |
| Install package | changed | ok (sudah terinstall) |
| Buat user | changed | ok (user sudah ada) |
| **Total changed** | **3** | **0** |

> **Manfaat praktis:** Kamu bisa jalankan ulang playbook kapan saja tanpa khawatir sesuatu "dobel" terjadi — package tidak diinstall dua kali, user tidak dibuat duplikat, dsb.

---

## 14. Ansible Vault

Menyimpan password langsung di inventory (plain text) tidak aman untuk production. Ansible Vault adalah fitur bawaan untuk **mengenkripsi password dan data sensitif**.

```bash
# Buat file vault yang terenkripsi
ansible-vault create secrets.yml
```

Isi file vault:

```yaml
vault_become_pass_vm1: "passwordVM1"
vault_become_pass_vm2: "passwordVM2"
```

Menjalankan playbook dengan vault:

```bash
ansible-playbook -i ~/inventory.ini ~/playbook.yml --ask-vault-pass
```

> **Untuk lab/belajar:** Menyimpan password plain text di inventory masih oke. Tapi untuk server production, selalu gunakan Ansible Vault.

---

## 15. Kemampuan Lain Ansible

Ansible tidak hanya untuk konfigurasi dasar. Berikut kemampuan lain yang bisa dieksplorasi:

| Kemampuan | Contoh Penggunaan |
|---|---|
| **Manajemen Firewall** | Buka/tutup port di semua server sekaligus |
| **Deploy Aplikasi** | Pull code dari Git, deploy ke banyak server, rolling update tanpa downtime |
| **Docker & Container** | Install Docker, pull image, jalankan/stop container |
| **Provisioning Cloud** | Buat VM baru di AWS, GCP, Azure, DigitalOcean secara otomatis |
| **Backup Otomatis** | Backup database, compress, kirim ke storage, jadwalkan via cron |
| **Monitoring Setup** | Install & konfigurasi Prometheus, Grafana, alert otomatis |
| **Integrasi Proxmox** | Buat/clone/start/stop VM di Proxmox lewat playbook |
| **Ansible Role** | Rapikan playbook menjadi struktur yang modular & bisa dipakai ulang |

---

## ✅ Recap — Yang Sudah Dikuasai

- ✅ Memahami konsep dan cara kerja Ansible
- ✅ Menyiapkan infrastruktur di Proxmox (template, clone, IP)
- ✅ Menginstall Ansible di Control Node
- ✅ Setup SSH key untuk akses tanpa password
- ✅ Membuat Inventory dengan parameter custom
- ✅ Membuat dan menjalankan Playbook
- ✅ Menggunakan variable per host
- ✅ Deploy file HTML berbeda ke VM berbeda
- ✅ Memahami konsep Idempotency

---

*Modul ini dibuat berdasarkan sesi belajar praktik langsung.*
