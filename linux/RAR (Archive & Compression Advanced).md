# 📘 MODUL PRAKTIKUM LINUX ADMIN

## 📦 RAR (Archive & Compression Advanced)

---

# 🎯 Tujuan Pembelajaran

Setelah praktik ini, mahasiswa mampu:

1. Memahami konsep RAR dalam Linux
2. Menginstal tool `rar` dan `unrar`
3. Membuat file `.rar`
4. Membuka / extract file `.rar`
5. Membuat RAR dengan password
6. Membuat RAR multi-part (split file)
7. Memahami perbedaan RAR vs ZIP vs TAR.GZ

---

# 📌 1. Konsep Dasar RAR

## 🔹 Apa itu RAR?

RAR adalah format:

> 📦 Archive + Compression + Advanced Features

RAR bukan bawaan Linux, jadi harus diinstall dulu.

---

## 🔥 Fitur utama RAR:

* Kompresi lebih kecil dari ZIP (biasanya)
* Bisa pakai password
* Bisa recovery file rusak
* Bisa split file besar jadi beberapa bagian

---

# ⚙️ 2. Instalasi RAR di Linux

## 🔧 Install:

```bash
sudo apt update
sudo apt install rar unrar -y
```

---

## 🔍 Cek instalasi:

```bash
rar
unrar
```

Kalau muncul help → berarti berhasil.

---

# 📁 3. Persiapan Praktikum

## Buat folder latihan:

```bash
mkdir ~/praktikum-rar
cd ~/praktikum-rar
```

## Buat data:

```bash
mkdir data
mkdir backup
mkdir restore
mkdir hasil
```

---

## Isi file:

```bash
echo "File laporan 1" > data/laporan1.txt
echo "File laporan 2" > data/laporan2.txt
echo "Data mahasiswa Linux" > data/mahasiswa.txt
```

Tambahkan file besar:

```bash
yes "Praktikum RAR Linux Admin" | head -n 1000 > data/besar.txt
```

---

# 📦 4. Membuat File RAR

## 🔹 Perintah dasar:

```bash
rar a hasil/data.rar data/
```

---

## 📌 Penjelasan:

```bash
rar a hasil/data.rar data/
```

* `rar` = program
* `a` = add (membuat archive)
* `data.rar` = nama output
* `data/` = folder sumber

---

## 🔍 Cek hasil:

```bash
ls -lh hasil
```

---

# 📂 5. Melihat Isi File RAR

```bash
unrar l hasil/data.rar
```

### Penjelasan:

* `l` = list isi file tanpa extract

---

# 📤 6. Extract File RAR

## Buat folder extract:

```bash
mkdir restore/rar
```

## Extract:

```bash
unrar x hasil/data.rar restore/rar/
```

---

## 📌 Penjelasan:

* `x` = extract full path
* `restore/rar/` = lokasi hasil extract

---

## 🔍 Cek hasil:

```bash
find restore/rar
```

---

# 🔐 7. RAR dengan Password

## Buat file RAR pakai password:

```bash
rar a -p hasil/secure.rar data/
```

---

## Penjelasan:

* `-p` = password aktif
* nanti sistem akan minta password

---

## Extract file password:

```bash
unrar x hasil/secure.rar
```

akan diminta password

---

# 📦 8. RAR Split (File Besar Dipotong)

Ini fitur penting di dunia nyata.

## Contoh:

```bash
rar a -v10m hasil/split.rar data/
```

---

## Penjelasan:

* `-v10m` = split tiap 10MB
* hasil jadi:

```bash
split.part1.rar
split.part2.rar
split.part3.rar
```

---

## Kenapa dipakai?

* kirim file besar via email
* upload ke server terbatas
* backup ke flashdisk kecil

---

## 📌 Ibarat:

| Format | Analogi                                                 |
| ------ | ------------------------------------------------------- |
| tar    | kardus                                                  |
| gzip   | vacuum plastik                                          |
| zip    | kardus vacuum biasa                                     |
| rar    | vacuum + anti rusak + bisa dipotong jadi beberapa paket |

---

# 📥 9. Extract RAR Split

Cukup extract part pertama:

```bash
unrar x split.part1.rar
```

RAR otomatis gabungkan semua part
