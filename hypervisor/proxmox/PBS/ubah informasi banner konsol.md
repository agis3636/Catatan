IP jaringan sudah berubah menjadi `192.168.200.4`, tetapi tulisan URL pada layar login masih menampilkan IP lama.

Itu hanya informasi banner konsol, bukan berarti konfigurasi IP gagal.

URL yang benar sekarang:

```
https://192.168.200.4:8007/
```

1\. Login sebagai root, lalu cek hostname mapping:

```
cat /etc/hosts
```

2\. Edit file hosts:

```
nano /etc/hosts
```

Cari baris yang masih menggunakan IP lama `192.168.200.62`, lalu ganti menjadi `192.168.200.4`.

Contoh:

```
127.0.0.1 localhost.localdomain localhost
192.168.200.4 pbs
```

Apabila ada nama domain seperti `pbs.local`, pertahankan nama tersebut. Yang penting hostname `pbs` mengarah ke `192.168.200.4`.

3\. Refresh banner PBS:

```
systemctl restart proxmox-backup-banner.service
```

4\. Cek hasilnya:

```
cat /etc/issue
```

Seharusnya sekarang menampilkan:

```
https://192.168.200.4:8007/
```

PBS menggunakan hostname resolution untuk membuat banner login dan menyimpannya di `/etc/issue`.



Jadi, tidak perlu mengubah konfigurasi IP lagi. Cukup perbaiki mapping hostname agar banner ikut diperbarui.
