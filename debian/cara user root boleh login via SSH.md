OpenSSH di Debian (terbaru), secara default disable root login via password (PermitRootLogin prohibit-password).
Container kamu memang cuma punya user root. Karena itu SSH gagal.
---
🔧 Solusi
1. Allow root login via SSH (kurang aman)

Edit /etc/ssh/sshd_config di dalam container:

nano /etc/ssh/sshd_config


Cari baris:

PermitRootLogin prohibit-password


Ubah jadi:

PermitRootLogin yes


Simpan, lalu restart SSH:

systemctl restart ssh


Sekarang bisa SSH root@container pakai password.
