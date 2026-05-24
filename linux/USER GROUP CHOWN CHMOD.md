# MODUL PRAKTIK LINUX

# USER, GROUP, DAN HAK AKSES (PERMISSION)

## Tujuan Pembelajaran

Setelah praktik ini siswa mampu:

* memahami user di Linux
* memahami group di Linux
* memahami permission/hak akses
* memahami owner, group, dan others
* memahami perbedaan permission file dan folder
* memahami fungsi `chmod`, `chown`, dan `usermod`
* memahami keamanan antar user di Linux
* melakukan praktik multiuser Linux

---

# A. PENGENALAN DASAR

Linux adalah sistem operasi multiuser.

Artinya:

* banyak user bisa memakai satu sistem
* setiap user punya hak akses berbeda
* keamanan antar user dipisahkan

---

# 1. USER

User adalah akun pengguna Linux.

Contoh:

* agis
* andi
* caca

---

# 2. GROUP

Group adalah kumpulan user.

Tujuan:

* mempermudah pengaturan hak akses

Contoh:

* multimedia
* programmer

---

# 3. PERMISSION

Permission adalah hak akses terhadap:

* file
* folder

Linux menggunakan:

```text id="5ix70x"
rwx
```

| Permission | Arti    |
| ---------- | ------- |
| r          | read    |
| w          | write   |
| x          | execute |

---

# B. PERBEDAAN FILE DAN FOLDER

# 1. PERMISSION PADA FILE

| Permission | Fungsi           |
| ---------- | ---------------- |
| r          | membaca isi file |
| w          | mengedit file    |
| x          | menjalankan file |

---

# 2. PERMISSION PADA FOLDER

| Permission | Fungsi                       |
| ---------- | ---------------------------- |
| r          | melihat isi folder           |
| w          | membuat/menghapus isi folder |
| x          | masuk ke folder              |

---

# C. STRUKTUR HAK AKSES LINUX

Contoh:

```text id="13vvv8"
rwx rwx r-x
```

dibagi menjadi:

| Bagian      | Fungsi |
| ----------- | ------ |
| rwx pertama | owner  |
| rwx kedua   | group  |
| r-x ketiga  | others |

---

# PENJELASAN

## OWNER

Pemilik file/folder.

---

## GROUP

Anggota group file/folder.

---

## OTHERS

User selain owner dan group.

---

# D. PENJELASAN CHOWN DAN CHMOD

# 1. CHOWN

Digunakan untuk:

* mengubah owner
* mengubah group owner

Format:

```bash id="0rzgfe"
chown USER:GROUP
```

Contoh:

```bash id="htn3ea"
sudo chown andi:multimedia /multimedia-room
```

Artinya:

* owner folder = andi
* group folder = multimedia

---

# 2. CHMOD

Digunakan untuk:

* mengatur permission

Contoh:

```bash id="c0nuxn"
chmod 770 folder
```

Artinya:

```text id="aefp6l"
rwx rwx ---
```

| Siapa  | Hak               |
| ------ | ----------------- |
| owner  | full akses        |
| group  | full akses        |
| others | tidak punya akses |

---

# E. PRAKTIK USER, GROUP, DAN PERMISSION

# STUDI KASUS

Terdapat 2 divisi:

| Divisi     | Group      |
| ---------- | ---------- |
| Multimedia | multimedia |
| Programmer | programmer |

---

# USER

| User | Group      |
| ---- | ---------- |
| andi | multimedia |
| budi | multimedia |
| caca | programmer |
| dodi | programmer |

---

# TUJUAN PRAKTIK

## KONDISI 1

User multimedia:

* hanya bisa masuk folder multimedia

---

## KONDISI 2

User multimedia:

* bisa masuk folder programmer melalui permission others

---

# F. LANGKAH PRAKTIK

# 1. MEMBUAT GROUP

```bash id="wgf3wq"
sudo groupadd multimedia
sudo groupadd programmer
```

---

# 2. MEMBUAT USER

```bash id="6ezr7y"
sudo useradd -m andi
sudo useradd -m budi
sudo useradd -m caca
sudo useradd -m dodi
```

---

# 3. MEMBERIKAN PASSWORD

```bash id="jlwm8b"
sudo passwd andi
sudo passwd budi
sudo passwd caca
sudo passwd dodi
```

---

# 4. MEMASUKKAN USER KE GROUP

## Group multimedia

```bash id="4t30h8"
sudo usermod -aG multimedia andi
sudo usermod -aG multimedia budi
```

---

## Group programmer

```bash id="z7u0kt"
sudo usermod -aG programmer caca
sudo usermod -aG programmer dodi
```

---

# 5. CEK GROUP USER

```bash id="jlwm6m"
groups andi
groups caca
```

---

# HASIL

```text id="jlwm0n"
andi : andi multimedia
caca : caca programmer
```

---

# 6. MEMBUAT FOLDER DIVISI

Folder dibuat di root directory `/`

```bash id="jlwmm1"
sudo mkdir /multimedia-room
sudo mkdir /programmer-room
```

---

# 7. MENGATUR OWNER DAN GROUP FOLDER

```bash id="jlwmc2"
sudo chown andi:multimedia /multimedia-room
sudo chown caca:programmer /programmer-room
```

---

# CEK

```bash id="jlwm1x"
ls -ld /multimedia-room
ls -ld /programmer-room
```

---

# HASIL

```text id="jlwms2"
drwxr-xr-x andi multimedia
drwxr-xr-x caca programmer
```

---

# 8. MENGATUR PERMISSION

# Folder multimedia

```bash id="jlwm3g"
sudo chmod 770 /multimedia-room
```

---

# Folder programmer

```bash id="jlwm7d"
sudo chmod 775 /programmer-room
```

---

# PENJELASAN

# 770

```text id="jlwm4r"
rwx rwx ---
```

Artinya:

* owner full akses
* group full akses
* others tidak boleh akses

---

# 775

```text id="jlwm2j"
rwx rwx r-x
```

Artinya:

* owner full akses
* group full akses
* others boleh melihat dan masuk folder

---

# G. PRAKTIK TEST USER

# 1. LOGIN SEBAGAI ANDI

```bash id="jlwmy9"
su - andi
```

---

# 2. MASUK FOLDER MULTIMEDIA

```bash id="jlwmb2"
cd /multimedia-room
```

HASIL:

* berhasil

Karena:

* andi owner folder
* andi anggota multimedia

---

# 3. MASUK FOLDER PROGRAMMER

```bash id="jlwmv5"
cd /programmer-room
```

HASIL:

* berhasil

Karena:

* permission others pada folder programmer adalah `r-x`

---

# 4. COBA MEMBUAT FILE

```bash id="jlwmg7"
touch test.txt
```

HASIL:

* gagal

Karena:

* others tidak punya write

---

# PENJELASAN

Permission others pada folder programmer:

```text id="jlwmp6"
r-x
```

Artinya:

* boleh masuk
* boleh melihat
* tidak boleh membuat file

---

# H. CEK PERMISSION FILE DAN FOLDER

Gunakan:

```bash id="jlwm1m"
ls -l
```

---

# CONTOH FILE

```text id="jlwmm9"
-rw-r--r--
```

Awalan:

```text id="jlwm9q"
-
```

Artinya:

* file biasa

---

# CONTOH FOLDER

```text id="jlwmd8"
drwxrwxr-x
```

Awalan:

```text id="jlwm7k"
d
```

Artinya:

* directory/folder

---

# I. PERCOBAAN PERMISSION

# TEST 1

Ubah permission:

```bash id="jlwm4v"
sudo chmod 770 /programmer-room
```

Lalu test:

* andi tidak bisa masuk folder programmer

---

# TEST 2

Ubah permission:

```bash id="jlwme2"
sudo chmod 775 /programmer-room
```

Lalu test:

* andi bisa masuk folder programmer

---

# TEST 3

Ubah permission:

```bash id="jlwmu6"
sudo chmod 777 /programmer-room
```

Lalu test:

* semua user bisa membuat file

---

# J. KESIMPULAN

# USER

akun pengguna Linux

---

# GROUP

kumpulan user

---

# CHOWN

mengubah owner dan group file/folder

---

# CHMOD

mengubah hak akses

---

# OWNER

pemilik file/folder

---

# GROUP

anggota group file/folder

---

# OTHERS

selain owner dan group

---

# PERMISSION FILE

| Permission | Fungsi        |
| ---------- | ------------- |
| r          | baca file     |
| w          | edit file     |
| x          | jalankan file |

---

# PERMISSION FOLDER

| Permission | Fungsi                  |
| ---------- | ----------------------- |
| r          | lihat isi folder        |
| w          | tambah/hapus isi folder |
| x          | masuk folder            |

---

# K. LATIHAN MANDIRI

## Tugas 1

Buat:

* group accounting
* user rina dan joko

---

## Tugas 2

Buat folder:

```text id="jlwmn8"
/accounting-room
```

---

## Tugas 3

Atur agar:

* hanya group accounting yang bisa masuk

---

## Tugas 4

Ubah permission agar:

* others bisa masuk
* tapi tidak bisa membuat file

---

## Tugas 5

Jelaskan perbedaan:

* chmod 770
* chmod 775
* chmod 777

Modul ini cocok dipraktikkan langsung menggunakan VM Ubuntu/Debian agar siswa dapat memahami konsep multiuser Linux dan permission secara real-time.
