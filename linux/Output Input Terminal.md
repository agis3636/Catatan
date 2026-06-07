# MODUL INPUT, OUTPUT, FILE DESCRIPTOR, REDIRECTION, DAN PIPELINE PADA TERMINAL LINUX

## A. Pendahuluan

Pada sistem operasi Linux, hampir semua aktivitas di terminal berhubungan dengan **input** dan **output**. Ketika pengguna mengetik perintah, sistem akan menerima input, memprosesnya, lalu menghasilkan output. Output tersebut bisa ditampilkan ke layar, disimpan ke file, dikirim ke perintah lain, atau bahkan dibuang.

Materi ini penting karena banyak perintah Linux bekerja dengan konsep aliran data. Dengan memahami input, output, file descriptor, redirection, dan pipeline, pengguna dapat mengelola hasil perintah dengan lebih efisien.

Contoh sederhana:

```bash
echo "Hello Linux"
```

Perintah tersebut akan menampilkan teks ke layar:

```bash
Hello Linux
```

Pada contoh tersebut, teks `"Hello Linux"` merupakan output dari perintah `echo`, dan layar terminal menjadi tempat keluarnya output.

---

# BAB 1

# FILE DESCRIPTOR

## 1.1 Pengertian File Descriptor

**File descriptor** adalah nomor identitas yang digunakan Linux untuk mengenali jalur input dan output dari suatu proses.

Secara default, setiap proses di Linux memiliki tiga file descriptor utama:

| Nomor File Descriptor | Nama                     | Fungsi                                        |
| --------------------- | ------------------------ | --------------------------------------------- |
| 0                     | Standard Input / stdin   | Sumber input, biasanya keyboard               |
| 1                     | Standard Output / stdout | Tempat output normal, biasanya layar terminal |
| 2                     | Standard Error / stderr  | Tempat pesan error, biasanya layar terminal   |

Jadi, ketika kita menjalankan perintah di terminal, Linux sebenarnya mengatur aliran datanya melalui tiga jalur tersebut.

---

## 1.2 Standard Input

**Standard input** adalah jalur masuknya data ke sebuah perintah. Biasanya input berasal dari keyboard.

Contoh:

```bash
cat
```

Setelah menjalankan perintah tersebut, terminal akan menunggu input dari keyboard.

Contoh praktik:

```bash
cat
halo, apa kabar
```

Output:

```bash
halo, apa kabar
```

Perintah `cat` akan menampilkan kembali apa yang diketik oleh pengguna. Untuk keluar dari input `cat`, tekan:

```bash
Ctrl + D
```

`Ctrl + D` berarti pengguna mengakhiri input dari keyboard.

---

## 1.3 Standard Output

**Standard output** adalah jalur keluarnya hasil normal dari sebuah perintah. Secara default, output akan ditampilkan ke layar terminal.

Contoh:

```bash
ps
```

Perintah `ps` digunakan untuk menampilkan proses yang sedang berjalan.

Contoh output:

```bash
PID TTY          TIME CMD
1234 pts/0    00:00:00 bash
1250 pts/0    00:00:00 ps
```

Pada perintah `ps`, input utamanya tidak berasal dari keyboard, melainkan dari informasi sistem Linux. Output-nya ditampilkan ke layar terminal.

---

## 1.4 Standard Error

**Standard error** adalah jalur khusus untuk menampilkan pesan kesalahan. Secara default, pesan error juga muncul di layar terminal, tetapi jalurnya berbeda dengan standard output.

Contoh:

```bash
mkdir
```

Karena perintah `mkdir` membutuhkan nama direktori, maka jika dijalankan tanpa argumen akan muncul pesan error.

Contoh output error:

```bash
mkdir: missing operand
Try 'mkdir --help' for more information.
```

Pesan tersebut bukan standard output biasa, melainkan standard error.

Contoh lain:

```bash
mkdir mydir
mkdir mydir
```

Jika direktori `mydir` sudah ada, maka perintah kedua akan menghasilkan error:

```bash
mkdir: cannot create directory ‘mydir’: File exists
```

---

## 1.5 Catatan Penting: Input Tidak Selalu dari Keyboard

Tidak semua perintah mengambil input dari keyboard.

Contoh:

```bash
mkdir mydir
```

Pada contoh tersebut, `mydir` bukan standard input, melainkan **argumen perintah**.

Perbedaan sederhananya:

| Bentuk                   | Keterangan                                        |
| ------------------------ | ------------------------------------------------- |
| `cat` lalu mengetik teks | Input berasal dari keyboard                       |
| `mkdir mydir`            | `mydir` adalah argumen, bukan input dari keyboard |
| `cat file.txt`           | `file.txt` adalah argumen nama file               |
| `cat < file.txt`         | Input dibelokkan dari file                        |

---

# BAB 2

# REDIRECTION ATAU PEMBELOKAN INPUT DAN OUTPUT

## 2.1 Pengertian Redirection

**Redirection** adalah proses membelokkan input atau output dari satu tempat ke tempat lain.

Secara default:

* input berasal dari keyboard,
* output normal tampil di layar,
* pesan error tampil di layar.

Dengan redirection, kita bisa mengubah aliran tersebut.

Contohnya:

```bash
echo "halo" > file.txt
```

Output yang seharusnya tampil di layar akan dibelokkan dan disimpan ke file `file.txt`.

---

## 2.2 Membelokkan Standard Output dengan `>`

Operator `>` digunakan untuk membelokkan standard output ke file.

Contoh:

```bash
echo "hello" > output.txt
```

Perintah tersebut tidak menampilkan teks ke layar, tetapi menyimpan teks ke file `output.txt`.

Untuk melihat isi file:

```bash
cat output.txt
```

Output:

```bash
hello
```

Contoh lain:

```bash
cat > myfile.txt
ini adalah teks
ini baris kedua
```

Lalu tekan:

```bash
Ctrl + D
```

Untuk melihat isi file:

```bash
cat myfile.txt
```

Output:

```bash
ini adalah teks
ini baris kedua
```

Artinya, perintah `cat > myfile.txt` mengambil input dari keyboard dan membelokkan output-nya ke file `myfile.txt`.

---

## 2.3 Perbedaan `>` dan `1>`

Operator `>` sebenarnya sama dengan `1>`.

Contoh:

```bash
echo "halo" > file.txt
```

Sama dengan:

```bash
echo "halo" 1> file.txt
```

Angka `1` menunjukkan file descriptor standard output.

Jadi:

```bash
1>
```

berarti membelokkan standard output.

---

## 2.4 Membelokkan Standard Input dengan `<`

Operator `<` digunakan untuk membelokkan standard input dari file.

Contoh:

```bash
cat < myfile.txt
```

Artinya, perintah `cat` tidak mengambil input dari keyboard, tetapi dari file `myfile.txt`.

Output:

```bash
ini adalah teks
ini baris kedua
```

Contoh berikut:

```bash
cat myfile.txt
```

dan:

```bash
cat < myfile.txt
```

hasilnya terlihat sama, tetapi cara kerjanya berbeda.

Penjelasan:

```bash
cat myfile.txt
```

`myfile.txt` menjadi argumen perintah `cat`.

Sedangkan:

```bash
cat < myfile.txt
```

isi `myfile.txt` dijadikan standard input untuk perintah `cat`.

---

## 2.5 Membelokkan Standard Error dengan `2>`

Operator `2>` digunakan untuk menyimpan pesan error ke file.

Contoh:

```bash
mkdir mydir
mkdir mydir 2> myerror.txt
```

Jika direktori `mydir` sudah ada, maka pesan error tidak tampil di layar, tetapi disimpan ke file `myerror.txt`.

Untuk melihat isi file error:

```bash
cat myerror.txt
```

Output:

```bash
mkdir: cannot create directory ‘mydir’: File exists
```

Contoh lain:

```bash
ls filetidakada 2> error.txt
```

Lalu:

```bash
cat error.txt
```

Output:

```bash
ls: cannot access 'filetidakada': No such file or directory
```

---

## 2.6 Menambahkan Output ke File dengan `>>`

Operator `>` akan menimpa isi file lama. Sedangkan `>>` digunakan untuk menambahkan output ke bagian akhir file tanpa menghapus isi sebelumnya.

Contoh:

```bash
echo "kata pertama" > surat.txt
echo "kata kedua" >> surat.txt
echo "kata ketiga" >> surat.txt
cat surat.txt
```

Output:

```bash
kata pertama
kata kedua
kata ketiga
```

Jika menggunakan `>` lagi:

```bash
echo "kata keempat" > surat.txt
cat surat.txt
```

Output:

```bash
kata keempat
```

Isi sebelumnya hilang karena operator `>` menimpa isi file.

---

## 2.7 Menambahkan Error ke File dengan `2>>`

Selain output biasa, pesan error juga bisa ditambahkan ke file dengan `2>>`.

Contoh:

```bash
ls file1 2>> error.log
ls file2 2>> error.log
cat error.log
```

Pesan error dari kedua perintah akan ditambahkan ke file `error.log`.

---

# BAB 3

# MENGGABUNGKAN OUTPUT DAN ERROR

## 3.1 Membelokkan Output dan Error ke File Berbeda

Contoh:

```bash
ls fileada filetidakada > output.txt 2> error.txt
```

Penjelasan:

* output normal masuk ke `output.txt`,
* pesan error masuk ke `error.txt`.

Untuk melihat output normal:

```bash
cat output.txt
```

Untuk melihat error:

```bash
cat error.txt
```

---

## 3.2 Membelokkan Output dan Error ke File yang Sama

Untuk menyimpan standard output dan standard error ke file yang sama, gunakan:

```bash
perintah > file.txt 2>&1
```

Contoh:

```bash
ls fileada filetidakada > hasil.txt 2>&1
```

Penjelasan:

* `> hasil.txt` membelokkan standard output ke file `hasil.txt`,
* `2>&1` membelokkan standard error ke tempat yang sama dengan standard output.

Jadi, output normal dan error akan masuk ke file yang sama.

---

## 3.3 Kesalahan Umum pada `2>&1`

Urutan penulisan sangat penting.

Benar:

```bash
ls fileada filetidakada > hasil.txt 2>&1
```

Artinya stdout masuk ke `hasil.txt`, lalu stderr ikut masuk ke tempat stdout.

Kurang tepat:

```bash
ls fileada filetidakada 2>&1 > hasil.txt
```

Pada bentuk ini, stderr diarahkan dulu ke layar, lalu stdout baru diarahkan ke file. Akibatnya, error tetap muncul di layar.

Jadi, untuk menggabungkan output dan error ke satu file, gunakan bentuk ini:

```bash
perintah > file.txt 2>&1
```

Atau pada Bash bisa juga menggunakan:

```bash
perintah &> file.txt
```

Contoh:

```bash
ls fileada filetidakada &> hasil.txt
```

---

## 3.4 Membelokkan Standard Output ke Standard Error

Notasi:

```bash
1>&2
```

atau:

```bash
>&2
```

digunakan untuk mengirim standard output ke standard error.

Contoh sederhana:

```bash
echo "Ini pesan error" >&2
```

Teks tersebut akan dikirim ke jalur standard error, bukan standard output.

Biasanya ini digunakan dalam shell script, misalnya untuk menampilkan pesan kesalahan.

Contoh script:

```bash
echo "Terjadi kesalahan" >&2
```

---

# BAB 4

# HERE DOCUMENT

## 4.1 Pengertian Here Document

**Here document** adalah cara memberikan input langsung ke sebuah perintah menggunakan pembatas tertentu.

Format umum:

```bash
perintah <<BATAS
isi input
isi input
BATAS
```

Kata pembatas bisa diganti dengan apa saja, tetapi pembuka dan penutup harus sama.

Contoh:

```bash
cat <<++
hallo, apa kabar?
Baik-baik saja?
Ok!
++
```

Output:

```bash
hallo, apa kabar?
Baik-baik saja?
Ok!
```

Contoh lain:

```bash
cat <<SELESAI
Ini baris pertama
Ini baris kedua
Ini baris ketiga
SELESAI
```

Output:

```bash
Ini baris pertama
Ini baris kedua
Ini baris ketiga
```

---

## 4.2 Aturan Penting Here Document

Penutup here document harus ditulis di awal baris tanpa spasi.

Benar:

```bash
cat <<EOF
halo
EOF
```

Salah:

```bash
cat <<EOF
halo
   EOF
```

Jika penutup diberi spasi di depannya, shell tidak menganggapnya sebagai penutup.

---

## 4.3 Here Document untuk Membuat File

Here document juga bisa digunakan untuk membuat file.

Contoh:

```bash
cat > biodata.txt <<EOF
Nama: Agis
Kelas: Linux Dasar
Materi: Input dan Output
EOF
```

Lalu cek isi file:

```bash
cat biodata.txt
```

Output:

```bash
Nama: Agis
Kelas: Linux Dasar
Materi: Input dan Output
```

---

# BAB 5

# TANDA MINUS `-` SEBAGAI INPUT DARI KEYBOARD

## 5.1 Pengertian Tanda `-`

Pada beberapa perintah Linux, tanda minus `-` digunakan untuk mewakili standard input, yaitu input dari keyboard.

Contoh:

```bash
cat file1.txt - file2.txt
```

Artinya:

1. tampilkan isi `file1.txt`,
2. lalu ambil input dari keyboard,
3. lalu tampilkan isi `file2.txt`.

Contoh praktik:

```bash
cat myfile.txt - surat.txt
```

Setelah isi `myfile.txt` tampil, terminal akan menunggu input dari keyboard. Setelah selesai mengetik, tekan:

```bash
Ctrl + D
```

Kemudian isi `surat.txt` akan ditampilkan.

---

# BAB 6

# KOMBINASI STANDARD INPUT DAN STANDARD OUTPUT

## 6.1 Membelokkan Input dan Output Sekaligus

Contoh:

```bash
cat < output.txt > out.txt
```

Penjelasan:

* input berasal dari `output.txt`,
* output disimpan ke `out.txt`.

Untuk melihat hasil:

```bash
cat out.txt
```

Jika isi `output.txt` adalah:

```bash
hello
bye
```

Maka isi `out.txt` juga menjadi:

```bash
hello
bye
```

---

## 6.2 Menggunakan Append pada Output

Contoh:

```bash
cat < output.txt >> out.txt
```

Artinya isi `output.txt` ditambahkan ke akhir file `out.txt`.

Jika sebelumnya `out.txt` berisi:

```bash
hello
bye
```

Maka setelah perintah dijalankan, hasilnya bisa menjadi:

```bash
hello
bye
hello
bye
```

---

## 6.3 Kesalahan: Input dan Output Menggunakan File yang Sama

Contoh berbahaya:

```bash
cat < output.txt > output.txt
```

Perintah ini tidak disarankan.

Mengapa?

Karena shell akan mempersiapkan file output terlebih dahulu. Saat menggunakan `> output.txt`, isi file akan dikosongkan sebelum `cat` membaca input. Akibatnya, file `output.txt` bisa menjadi kosong.

Contoh lain yang juga berbahaya:

```bash
cat < out.txt >> out.txt
```

Perintah ini dapat menyebabkan proses berjalan terus-menerus. Hal ini terjadi karena file yang sedang dibaca juga terus ditambahkan isinya. Untuk menghentikannya, tekan:

```bash
Ctrl + C
```

Kesimpulan:

Jangan gunakan file yang sama sebagai input dan output dalam satu perintah redirection.

---

# BAB 7

# PIPELINE ATAU PIPA

## 7.1 Pengertian Pipeline

**Pipeline** adalah mekanisme untuk menghubungkan output dari satu perintah menjadi input untuk perintah berikutnya.

Operator pipeline adalah:

```bash
|
```

Format umum:

```bash
perintah1 | perintah2
```

Artinya output dari `perintah1` tidak ditampilkan langsung ke layar, tetapi dikirim sebagai input untuk `perintah2`.

---

## 7.2 Contoh Dasar Pipeline

Contoh:

```bash
who
```

Perintah `who` menampilkan user yang sedang login.

Untuk mengurutkan hasilnya:

```bash
who | sort
```

Penjelasan:

* `who` menghasilkan daftar user login,
* output dari `who` dikirim ke `sort`,
* `sort` mengurutkan hasilnya.

Untuk mengurutkan secara terbalik:

```bash
who | sort -r
```

---

## 7.3 Pipeline sebagai Pengganti File Sementara

Tanpa pipeline:

```bash
who > tmp
sort tmp
rm tmp
```

Dengan pipeline:

```bash
who | sort
```

Pipeline membuat perintah lebih singkat karena tidak perlu membuat file sementara.

---

## 7.4 Pipeline dengan `more`

Contoh:

```bash
ls -l /etc | more
```

Penjelasan:

* `ls -l /etc` menampilkan isi direktori `/etc` secara detail,
* karena hasilnya panjang, output dikirim ke `more`,
* `more` menampilkan hasil per halaman.

Contoh lain:

```bash
ls -l /etc | sort | more
```

Penjelasan:

* daftar file dari `/etc` ditampilkan,
* hasilnya diurutkan dengan `sort`,
* lalu ditampilkan per halaman dengan `more`.

Selain `more`, sering juga digunakan:

```bash
less
```

Contoh:

```bash
ls -l /etc | less
```

---

# BAB 8

# PIPELINE DENGAN PERINTAH FILTER

## 8.1 Menggunakan `grep`

Perintah `grep` digunakan untuk mencari teks tertentu.

Contoh:

```bash
grep agis /etc/passwd
```

Perintah tersebut mencari teks `agis` di file `/etc/passwd`.

Contoh menggunakan pipeline:

```bash
cat /etc/passwd | grep agis
```

Hasilnya sama, tetapi bentuk ini kurang efisien karena `grep` sebenarnya bisa langsung membaca file.

Lebih baik:

```bash
grep agis /etc/passwd
```

Namun pipeline tetap berguna jika data berasal dari output perintah lain.

Contoh:

```bash
ps aux | grep firefox
```

Artinya mencari proses yang mengandung kata `firefox`.

---

## 8.2 Menggunakan `wc`

Perintah `wc` digunakan untuk menghitung jumlah baris, kata, dan karakter.

Contoh:

```bash
ls /etc | wc
```

Output biasanya berisi tiga angka:

```bash
jumlah_baris jumlah_kata jumlah_karakter
```

Jika hanya ingin menghitung jumlah baris:

```bash
ls /etc | wc -l
```

Contoh:

```bash
cat /etc/passwd | wc -l
```

Perintah tersebut menghitung jumlah baris dalam file `/etc/passwd`.

---

## 8.3 Menggunakan `sort`

Perintah `sort` digunakan untuk mengurutkan teks.

Contoh membuat file:

```bash
cat > kelas1.txt
badu
zulkifli
yudi
ade
```

Tekan:

```bash
Ctrl + D
```

Buat file kedua:

```bash
cat > kelas2.txt
budi
gama
asep
muchlis
```

Tekan:

```bash
Ctrl + D
```

Gabungkan dan urutkan:

```bash
cat kelas1.txt kelas2.txt | sort
```

Output:

```bash
ade
asep
badu
budi
gama
muchlis
yudi
zulkifli
```

---

## 8.4 Menggunakan `uniq`

Perintah `uniq` digunakan untuk menghapus baris duplikat yang berurutan.

Contoh:

```bash
cat kelas1.txt kelas2.txt > kelas.txt
cat kelas.txt | sort | uniq
```

Penjelasan:

* `cat kelas.txt` menampilkan isi file,
* `sort` mengurutkan data,
* `uniq` menghapus data yang duplikat.

Catatan penting:

`uniq` hanya menghapus duplikat yang bersebelahan. Karena itu biasanya digunakan setelah `sort`.

Contoh lebih ringkas:

```bash
sort kelas.txt | uniq
```

Atau:

```bash
sort -u kelas.txt
```

---

# BAB 9

# PERINTAH TAMBAHAN YANG BERHUBUNGAN DENGAN INPUT DAN OUTPUT

## 9.1 Perintah `tee`

Perintah `tee` digunakan untuk menampilkan output ke layar sekaligus menyimpannya ke file.

Contoh:

```bash
echo "belajar linux" | tee catatan.txt
```

Output tampil di layar:

```bash
belajar linux
```

Dan juga tersimpan di file `catatan.txt`.

Untuk melihat isi file:

```bash
cat catatan.txt
```

Jika ingin menambahkan ke file tanpa menimpa:

```bash
echo "materi redirection" | tee -a catatan.txt
```

Opsi `-a` berarti append atau menambahkan ke akhir file.

---

## 9.2 Perintah `/dev/null`

`/dev/null` adalah tempat pembuangan output. Apa pun yang diarahkan ke `/dev/null` akan dibuang.

Contoh membuang output normal:

```bash
ls /etc > /dev/null
```

Output tidak tampil di layar.

Contoh membuang pesan error:

```bash
ls filetidakada 2> /dev/null
```

Pesan error tidak tampil.

Contoh membuang output normal dan error:

```bash
ls filetidakada > /dev/null 2>&1
```

Atau di Bash:

```bash
ls filetidakada &> /dev/null
```

---

## 9.3 Perintah `head` dan `tail`

Perintah `head` digunakan untuk menampilkan bagian awal file.

Contoh:

```bash
head /etc/passwd
```

Menampilkan 10 baris pertama.

Jika ingin 5 baris pertama:

```bash
head -n 5 /etc/passwd
```

Perintah `tail` digunakan untuk menampilkan bagian akhir file.

Contoh:

```bash
tail /etc/passwd
```

Jika ingin 5 baris terakhir:

```bash
tail -n 5 /etc/passwd
```

Contoh pipeline:

```bash
cat /etc/passwd | head -n 5
```

Atau lebih baik:

```bash
head -n 5 /etc/passwd
```

---

## 9.4 Perintah `cut`

Perintah `cut` digunakan untuk mengambil bagian tertentu dari setiap baris.

Contoh:

```bash
cut -d: -f1 /etc/passwd
```

Penjelasan:

* `-d:` berarti pemisah datanya adalah tanda titik dua `:`,
* `-f1` berarti mengambil kolom pertama.

Perintah tersebut akan menampilkan daftar username pada file `/etc/passwd`.

Contoh dengan pipeline:

```bash
cut -d: -f1 /etc/passwd | sort
```

---

## 9.5 Perintah `tr`

Perintah `tr` digunakan untuk mengubah atau menghapus karakter.

Contoh mengubah huruf kecil menjadi huruf besar:

```bash
echo "linux" | tr 'a-z' 'A-Z'
```

Output:

```bash
LINUX
```

Contoh mengganti spasi menjadi baris baru:

```bash
echo "apel jeruk mangga" | tr ' ' '\n'
```

Output:

```bash
apel
jeruk
mangga
```

---

# BAB 10

# CONTOH PRAKTIK LENGKAP

## 10.1 Membuat File dari Keyboard

```bash
cat > nama.txt
budi
andi
zaki
andi
rara
```

Tekan:

```bash
Ctrl + D
```

Cek isi file:

```bash
cat nama.txt
```

---

## 10.2 Mengurutkan Isi File

```bash
sort nama.txt
```

Output:

```bash
andi
andi
budi
rara
zaki
```

---

## 10.3 Menghapus Duplikat

```bash
sort nama.txt | uniq
```

Output:

```bash
andi
budi
rara
zaki
```

---

## 10.4 Menyimpan Hasil Sort ke File Baru

```bash
sort nama.txt | uniq > nama_urut.txt
```

Cek hasil:

```bash
cat nama_urut.txt
```

---

## 10.5 Menghitung Jumlah Nama Unik

```bash
sort nama.txt | uniq | wc -l
```

Output:

```bash
4
```

Penjelasan:

* `sort nama.txt` mengurutkan nama,
* `uniq` menghapus duplikat,
* `wc -l` menghitung jumlah baris.

---

# BAB 11

# KESALAHAN YANG SERING TERJADI

## 11.1 Menggunakan `>` Padahal Ingin Menambahkan Data

Salah jika ingin menambahkan:

```bash
echo "baris baru" > data.txt
```

Perintah tersebut akan menimpa isi lama.

Benar jika ingin menambahkan:

```bash
echo "baris baru" >> data.txt
```

---

## 11.2 Lupa Spasi pada Perintah

Salah:

```bash
catfile.txt
```

Benar:

```bash
cat file.txt
```

Linux sangat memperhatikan spasi dalam perintah.

---

## 11.3 Salah Menulis Nama File yang Mengandung Spasi

Contoh:

```bash
ls file baru
```

Perintah tersebut dibaca sebagai dua nama file:

* `file`
* `baru`

Jika nama file sebenarnya adalah `file baru`, gunakan tanda kutip:

```bash
ls "file baru"
```

Atau gunakan backslash:

```bash
ls file\ baru
```

---

## 11.4 Salah Menggabungkan Output dan Error

Kurang tepat:

```bash
ls filetidakada 2>&1 > hasil.txt
```

Lebih tepat:

```bash
ls filetidakada > hasil.txt 2>&1
```

Urutan redirection sangat berpengaruh.

---

# BAB 12

# RINGKASAN OPERATOR

| Operator          | Fungsi                                          |                                                               |
| ----------------- | ----------------------------------------------- | ------------------------------------------------------------- |
| `>`               | Membelokkan stdout ke file dan menimpa isi file |                                                               |
| `1>`              | Sama seperti `>`                                |                                                               |
| `>>`              | Menambahkan stdout ke akhir file                |                                                               |
| `<`               | Membelokkan stdin dari file                     |                                                               |
| `2>`              | Membelokkan stderr ke file                      |                                                               |
| `2>>`             | Menambahkan stderr ke akhir file                |                                                               |
| `2>&1`            | Menggabungkan stderr ke stdout                  |                                                               |
| `1>&2` atau `>&2` | Mengirim stdout ke stderr                       |                                                               |
| `                 | `                                               | Mengirim output perintah pertama ke input perintah berikutnya |
| `<<`              | Here document                                   |                                                               |
| `-`               | Mewakili stdin pada beberapa perintah           |                                                               |
| `/dev/null`       | Tempat pembuangan output                        |                                                               |

---

# BAB 13

# LATIHAN SOAL

## A. Latihan Praktik

1. Buat file bernama `latihan.txt` menggunakan perintah `cat`.
2. Isi file tersebut dengan tiga baris teks.
3. Tampilkan isi file `latihan.txt`.
4. Tambahkan satu baris baru ke file tersebut menggunakan `>>`.
5. Tampilkan kembali isi file.
6. Buat perintah yang menghasilkan error, lalu simpan error-nya ke file `error.txt`.
7. Tampilkan isi file `error.txt`.
8. Buat file berisi daftar nama yang tidak berurutan.
9. Urutkan daftar nama tersebut menggunakan `sort`.
10. Urutkan daftar nama dan simpan hasilnya ke file baru.
11. Hitung jumlah baris pada file daftar nama.
12. Gunakan pipeline untuk mengurutkan dan menghapus data duplikat.
13. Gunakan `tee` untuk menampilkan output sekaligus menyimpannya ke file.
14. Buang pesan error ke `/dev/null`.
15. Gunakan here document untuk membuat file biodata.

---

## B. Soal Pemahaman

1. Apa yang dimaksud dengan standard input?
2. Apa perbedaan standard output dan standard error?
3. Apa fungsi file descriptor nomor 0, 1, dan 2?
4. Apa perbedaan operator `>` dan `>>`?
5. Apa fungsi operator `<`?
6. Apa fungsi operator `2>`?
7. Jelaskan fungsi `2>&1`.
8. Mengapa urutan redirection penting?
9. Apa fungsi pipeline?
10. Mengapa `sort | uniq` sering digunakan bersama?
11. Apa fungsi `/dev/null`?
12. Apa fungsi perintah `tee`?
13. Apa yang terjadi jika input dan output menggunakan nama file yang sama?
14. Apa fungsi here document?
15. Bagaimana cara keluar dari input `cat`?

---

## C. Kunci Jawaban Singkat

1. Standard input adalah sumber input perintah, biasanya keyboard.
2. Standard output adalah hasil normal, sedangkan standard error adalah pesan kesalahan.
3. FD 0 adalah stdin, FD 1 adalah stdout, FD 2 adalah stderr.
4. `>` menimpa isi file, sedangkan `>>` menambahkan ke akhir file.
5. `<` membelokkan input dari file.
6. `2>` membelokkan pesan error ke file.
7. `2>&1` mengarahkan stderr ke tempat yang sama dengan stdout.
8. Karena shell memproses redirection dari kiri ke kanan.
9. Pipeline menghubungkan output satu perintah menjadi input perintah lain.
10. Karena `uniq` hanya menghapus duplikat yang bersebelahan, sehingga data perlu diurutkan dulu.
11. `/dev/null` digunakan untuk membuang output.
12. `tee` menampilkan output ke layar sekaligus menyimpannya ke file.
13. File bisa kosong, rusak, atau proses berjalan terus tergantung bentuk perintah.
14. Here document memberi input langsung ke perintah dengan pembatas tertentu.
15. Tekan `Ctrl + D`.

---

# PENUTUP

Input dan output merupakan konsep dasar yang sangat penting dalam penggunaan terminal Linux. Dengan memahami file descriptor, redirection, dan pipeline, pengguna dapat mengatur aliran data dengan lebih baik.

Materi ini juga menjadi dasar untuk mempelajari shell script, administrasi sistem, pengolahan file log, pencarian data, dan otomatisasi perintah Linux.

Kunci utama dalam memahami materi ini adalah sering praktik. Semakin sering mencoba perintah seperti `cat`, `echo`, `grep`, `sort`, `uniq`, `wc`, `tee`, dan redirection, maka konsep input-output di Linux akan semakin mudah dipahami.
