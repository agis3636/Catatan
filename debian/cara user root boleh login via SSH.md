OpenSSH di Debian (terbaru), secara default disable root login via password (PermitRootLogin prohibit-password).
Container/OS kamu memang cuma punya user root. Karena itu SSH gagal.
---
# 🔧 Allow root login via SSH (kurang aman)
1. Edit /etc/ssh/sshd_config di dalam container:

        nano /etc/ssh/sshd_config
2. Cari baris:

        PermitRootLogin prohibit-password
3. Ubah jadi:

        PermitRootLogin yes
4. Simpan, lalu restart SSH:

        systemctl restart ssh
Sekarang bisa SSH root@container pakai password.

---
---
---

# 👍 mari simak penjelasan detail biar clear 👇
---
🔑 SSH & Root Login
- **SSH** (Secure Shell) = protokol untuk login remote ke server.
- root = superuser di Linux (punya hak penuh).
- Karena root punya akses penuh, kalau root bisa login langsung via SSH pakai password, itu target empuk buat hacker (brute-force attack).
Makanya OpenSSH secara default memblokir root login pakai password.
---
📂 Opsi PermitRootLogin di /etc/ssh/sshd_config
1. PermitRootLogin prohibit-password (default modern Debian/Ubuntu)
     - Artinya: root boleh login via SSH, tapi hanya dengan SSH key, bukan password.
     - Password root tidak diterima.
     - Lebih aman, karena hacker tidak bisa brute-force password.
2. PermitRootLogin yes
   - Artinya: root boleh login dengan password ATAU SSH key.
   - Ini yang kamu butuhkan kalau cuma punya user root dan belum set SSH key.
   - Kurang aman di server yang terbuka ke internet → brute-force gampang.
3. PermitRootLogin no
   - Root tidak boleh login via SSH sama sekali.
   - Biasanya dipakai kalau sysadmin hanya login pakai user biasa + sudo.
4. PermitRootLogin without-password (lama, sama dengan prohibit-password)
   - Alias lama, sekarang sudah diganti.
---
⚡ Jadi kenapa kamu harus ubah jadi PermitRootLogin yes?
Karena:
- Container kamu cuma punya user root.
- Kamu mau login pakai password.
- Default Debian: prohibit-password → login root pakai password ditolak.
Jadi supaya bisa SSH langsung pakai root + password → harus ubah ke yes.
---
📌 Best practice
- Kalau server hanya diakses via Tailscale (VPN private) → aman-aman saja pakai PermitRootLogin yes.
- Kalau server terbuka ke internet publik → sangat disarankan pakai SSH key saja dan tetap biarkan prohibit-password.
