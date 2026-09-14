Tekan Shift + F10

Akan muncul Command Prompt (hitam)

Ketik:
    
    start ms-cxh:localonly

atau

    OOBE\BYPASSNRO

---

Perbedaan utamanya terletak pada **fase kerja** dan **cara mengelabui sistem** saat melewati keharusan koneksi internet:

* **`OOBE\BYPASSNRO`**: Perintah sistem yang mengeksekusi program bawaan Windows untuk memodifikasi *Registry* OOBE agar opsi *"I don't have internet"* dimunculkan kembali. Karena ini mengubah alur OOBE secara permanen untuk sesi tersebut, komputer **wajib melakukan *restart*** terlebih dahulu sebelum Anda bisa mengeklik tombol *offline* dan membuat akun lokal.
* **`start ms-cxh:localonly`**: Perintah instan untuk memanggil *Cloud Experience Host* secara langsung tanpa *restart*. Perintah ini memaksa jendela pop-up *setup* akun lokal (*username, password, hint*) langsung muncul di layar detik itu juga, memotong langsung halaman "Let's connect you to a network" tanpa peduli apakah internet menyala atau mati.
