Siap, kita langsung ke poin teknis untuk menghidupkan servicenya saja tanpa bongkar konfigurasi yang aneh-aneh.

Kalau sebelumnya jalan normal dan tiba-tiba mati semua (terutama LDAP), biasanya ini karena ada **file PID (lock file)** yang tersisa atau antrean proses yang nyangkut.

Ikuti urutan perintah ini sebagai user **root** dan **zimbra**:

### 1. Bersihkan Proses yang Nyangkut (Sebagai Root)

Kadang ada proses "hantu" yang membuat service tidak mau start karena merasa dirinya masih jalan.

```bash
# Pastikan tidak ada proses zimbra yang lari
killall -u zimbra -9
# Bersihkan file socket/lock yang tertinggal
rm -rf /opt/zimbra/zmstat/pid/*

```

### 2. Jalankan Ulang Service (Sebagai Zimbra)

Sekarang coba jalankan secara bertahap:

```bash
su - zimbra
# 1. Jalankan konfigurasi engine dulu
zmconfigd start
# 2. Jalankan LDAP (Wajib running sebelum yang lain)
ldap start

```

**Tunggu sekitar 30 detik.** Jika `ldap start` berhasil tanpa error, baru jalankan sisanya:

```bash
zmcontrol start

```

---

### Jika Masih Gagal di Bagian LDAP:

Cek kapasitas disk Anda (perintah paling aman, tidak merusak apa-apa):

```bash
df -h /opt/zimbra

```

Zimbra **otomatis mematikan seluruh service** jika sisa disk (khususnya di folder `/opt/zimbra`) kurang dari 5% atau penuh. Jika `Use%` menunjukkan 95% atau lebih, itu penyebab utamanya.

---

**Hasilnya bagaimana setelah `ldap start`?** * Kalau muncul **"OK"**, lanjut `zmcontrol start`.

* Kalau muncul **error**, tuliskan pesan errornya di sini, biar saya kasih solusi tepat sasaran tanpa coba-coba.

Setelah Web UI Zimbra hidup lagi, kita baru lanjut tes kirim spam ke PMG yang tadi. Mau saya siapkan perintah untuk pantau log kirim-terimanya nanti?
