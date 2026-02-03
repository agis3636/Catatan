

### Analisis Masalah

Pesan error yang muncul adalah:

> *"This kernel requires an x86-64 CPU, but only detected an i686 CPU. Unable to boot..."*

Artinya, installer OS yang Anda gunakan adalah versi **64-bit**, namun mesin virtual (VM) yang Anda buat di VirtualBox terbaca sebagai sistem **32-bit (i686)**.

Ada dua kemungkinan penyebab utama:

1. **Pengaturan VM salah:** Anda mungkin memilih profil sistem "32-bit" saat membuat VM di VirtualBox.
2. **Virtualisasi Hardware (VT-x/AMD-V) belum aktif:** Jika fitur virtualisasi di BIOS/UEFI komputer asli Anda belum aktif, VirtualBox tidak bisa menjalankan sistem 64-bit di dalam VM.

---



---

### Cara Memperbaiki Error Tersebut

Jika Anda tetap ingin mencobanya, silakan ikuti langkah-langkah berikut untuk memperbaiki error di gambar:

1. **Cek BIOS/UEFI:** Pastikan opsi **Intel VT-x** atau **AMD-V** di BIOS komputer Anda sudah dalam posisi **Enabled**.
2. **Ubah Tipe Versi di VirtualBox:**
* Matikan VM Nutanix Anda.
* Klik kanan pada VM > **Settings** > **General** > Tab **Basic**.
* Pada bagian **Version**, pastikan Anda memilih opsi yang berakhiran **(64-bit)**. Misalnya: `Other Linux (64-bit)` atau `Ubuntu (64-bit)`.


3. **Aktifkan Nested VT-x (Penting):**
Agar hypervisor Nutanix bisa jalan di dalam VirtualBox, Anda harus mengaktifkan passthrough CPU:
* Buka Command Prompt (CMD) di Windows.
* Masuk ke folder VirtualBox (biasanya `cd "C:\Program Files\Oracle\VirtualBox"`).
* Ketik perintah:
`VBoxManage modifyvm "NamaVMAnda" --nested-hw-virt on`


4. **Alokasi Processor:**
Di Settings VM > **System** > **Processor**, berikan minimal 4 CPU dan centang **Enable PAE/NX**.

---

