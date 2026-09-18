# Konfigurasi: Batas Toleransi Error HA (Max Restart & Max Relocate)

**Tujuan:** Mencegah mesin Proxmox kehabisan tenaga (CPU/RAM) akibat terus-menerus mencoba menghidupkan VM yang sistem operasinya sudah rusak total (mencegah *looping error*).

### Langkah Praktik Konfigurasi

Pengaturan ini murni hanya dilakukan di satu tempat. Tidak perlu menyentuh menu *Affinity Rules*.

1. Buka menu **Datacenter** > klik **HA**.
2. Fokus ke tabel bagian bawah (**Resources**).
3. Klik tombol **Add** (untuk mendaftarkan VM baru) atau pilih VM yang sudah ada di tabel lalu klik **Edit**.
4. Sesuaikan parameter pada jendela yang muncul, lalu klik **Add** / **OK**.

    <img width="480" height="179" alt="Image" src="https://github.com/user-attachments/assets/5a4378a1-504b-4bc2-9440-141955a076fe" />

### Penjelasan Parameter HA Resources

| Parameter / Tombol | Input / Status | Efek pada Sistem (Cara Kerja) |
| --- | --- | --- |
| **VM** | Pilih ID (misal `100`) | Menentukan target mesin yang akan diatur batas toleransi error-nya. |
| **Max. Restart** | Angka `1` *(Rekomendasi)* | Memberi jatah Proxmox **1 kali** mencoba menghidupkan ulang VM mati di *node* yang sama. Jika gagal, sistem langsung beralih ke fase pemindahan (*Relocate*). |
|  | Angka `> 1` (misal 2) | Sistem akan terus memaksa *restart* di tempat yang sama berkali-kali sampai jatah habis sebelum mau memindahkan VM tersebut. |
| **Max. Relocate** | Angka `1` *(Rekomendasi)* | Memberi jatah Proxmox **1 kali** memindahkan VM ke *node* lain jika *restart* di *node* awal gagal. Jika di *node* baru masih gagal hidup, Proxmox menyerah dan membiarkan VM mati (*error*). |
|  | Angka `> 1` (misal 2) | Sistem akan terus memindahkan VM ke *node-node* lain secara estafet jika gagal *booting*, menghabiskan *resource* klaster secara sia-sia. |
| **Failback** | **Dicentang (True)** | VM otomatis ditarik pulang ke *node* asalnya saat *node* tersebut normal kembali. |
|  | **Kosong (False)** | VM menetap permanen di *node* pengungsian, tidak akan ditarik pulang. |
| **Auto-Rebalance** | **Dicentang (True)** | Sistem aktif memantau dan membagi beban RAM/CPU antar-node secara otomatis saat kondisi normal. |
|  | **Kosong (False)** | Sistem pasif. Pemindahan VM murni hanya terjadi saat darurat (*node* mati/rusak). |

---
---
---

# Skenario Lapangan: Efek Domino "Max Restart 2" & "Max Relocate 2"

Setiap kali VM melakukan *Relocate* (pindah ke *node* baru), **jatah *Max Restart* akan di-reset (diulang dari awal)**.

Jika lu salah memasukkan nilai menjadi **Max Restart: 2** dan **Max Relocate: 2**, inilah alur penderitaan klaster lu jika OS VM 100 rusak saat berada di `node2`:

1. VM mati di `node2`.
2. **Restart `**1**` di node2:** Gagal.
3. **Restart `**2**` di node2:** Gagal. Jatah *restart* `node2` habis.
4. **Relocate `**1**` (ke node3):** Jatah *restart* direset jadi `**2**` lagi.
5. **Restart `**1**` di node3:** Gagal.
6. **Restart `**2**` di node3:** Gagal. Jatah *restart* `node3` habis.
7. **Relocate `**2**` (ke node4):** Jatah *restart* direset jadi `**2**` lagi.
8. **Restart `**1**` di node4:** Gagal.
9. **Restart `**2**` di node4:** Gagal. Seluruh jatah habis.
10. **Menyerah:** Proxmox membiarkan VM mati (*error*).

**Hasil Akhir:** Proxmox melakukan 6 kali percobaan *booting* yang sia-sia, membuat beban CPU/RAM klaster melonjak tanpa hasil. Dengan mengatur parameter ke angka **1 dan 1**, administrator bisa langsung mendeteksi kerusakan OS lebih cepat dan server tidak kelelahan.
