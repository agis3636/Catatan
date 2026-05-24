**LDPlayer, BlueStacks, dan emulator lain TIDAK cocok kalau Hyper-V aktif.**
Begitu kamu aktifkan:

* **WSL2**
* **Virtual Machine Platform**
* **Windows Hypervisor Platform**
* **Hyper-V** (walau tidak sengaja aktif)

Maka emulator Android akan baca *“Hyper-V aktif → performa turun → muncul popup kaya di gambar itu.”*

---

# ✔ Cara Mematikan Hyper-V Total (Biar LDPlayer Lancar)

Lakuin semuanya ya, biar bersih.

## **1. Disable fitur terkait Hyper-V**

Buka **Windows Search → Turn Windows features on or off**
Matikan semuanya di bawah:

* ☐ **Hyper-V**
* ☐ **Virtual Machine Platform**
* ☐ **Windows Hypervisor Platform**
* ☐ **Windows Sandbox** (kalau ada)
* ☐ **Windows Subsystem for Linux** (kalau kamu tidak butuh dulu)

Restart.



---
---
---


Oke, berarti **fitur Hyper-V di Windows Features memang sudah OFF**, tapi LDPlayer **masih mendeteksi Hyper-V aktif**.
Biasanya ini terjadi karena **masih ada servis Hyper-V yang hidup di background** atau **WSL2/Virtual Machine Platform masih mengaktifkan hypervisor Windows meski centangnya sudah kamu matikan.**

Kita bereskan satu-satu.

---

# ✔ 1. Cek apakah hypervisor masih aktif (penyebab terbesar)

Jalankan **CMD (Run as Administrator)**:

```
bcdedit
```

Lihat bagian:

```
hypervisorlaunchtype
```

Kalau keluar:

```
hypervisorlaunchtype    Auto
```

➡ **Itu artinya Hyper-V tetap aktif walaupun centang di Windows Features sudah mati.**

### 🔧 Solusi:

Jalankan:

```
bcdedit /set hypervisorlaunchtype off
```

Restart.



---
---
---



Kalau kamu mau WSL **versi 2** berjalan lagi, kamu harus **mengaktifkan hypervisor**.

Caranya cukup ubah balik:

```cmd
bcdedit /set {current} hypervisorlaunchtype auto
```

Lalu **restart**.

---

# ✔ Hasilnya setelah di-AUTO:

### 🔥 WSL2 → bisa jalan lagi

### ❌ LDPlayer → akan baca “Hyper-V aktif” dan performa turun / error

### ✔ Ini normal, karena WSL2 memang wajib Hyper-V
