# Cheatsheet Command Linux (dari OverTheWire Bandit)

Command dikelompokkan per topik supaya mudah dicari. Semua contoh bersifat umum dan bisa dipakai di luar Bandit.

## Daftar Isi

1. [Navigasi dan melihat file](#1-navigasi-dan-melihat-file)
2. [Mencari file dan teks](#2-mencari-file-dan-teks)
3. [Mengolah teks](#3-mengolah-teks)
4. [Encoding dan hexdump](#4-encoding-dan-hexdump)
5. [Kompresi dan arsip](#5-kompresi-dan-arsip)
6. [Permission, owner, dan setuid](#6-permission-owner-dan-setuid)
7. [Jaringan dan SSH](#7-jaringan-dan-ssh)
8. [Proses background dan cron](#8-proses-background-dan-cron)
9. [Git](#9-git)
10. [Trik bash dan shell](#10-trik-bash-dan-shell)
11. [Teknik keluar dari shell terbatas](#11-teknik-keluar-dari-shell-terbatas)

---

## 1. Navigasi dan melihat file

| Command | Fungsi |
|---------|--------|
| `ls -a` | Tampilkan semua isi folder, termasuk file dan folder tersembunyi (diawali titik) |
| `ls -la` | Sama seperti `-a`, tetapi dalam format list: permission, owner, ukuran, tanggal ubah |
| `cat namafile` | Tampilkan isi file |
| `file namafile` | Cek tipe file berdasarkan **isi**, bukan ekstensi |
| `stat --format "%U" namafile` | Cek siapa owner file |

**Menangani nama file yang "aneh"**

| Kasus | Solusi | Alasan |
|-------|--------|--------|
| Nama file hanya `-` | `cat ./-` | `./` menandakan file di folder saat ini, bukan simbol stdin |
| Nama file diawali `--` | `cat -- "--namafile"` | `--` menandakan akhir opsi, teks sesudahnya dianggap argumen |
| Nama file mengandung spasi | `cat "nama file"` | Tanda kutip menyatukan jadi satu argumen |

**Simbol path**

| Simbol | Arti |
|--------|------|
| `./` | Direktori saat ini |
| `*` | Wildcard, cocok dengan apa saja |
| `/` | Root direktori |
| `./*` | Semua file di direktori saat ini |

---

## 2. Mencari file dan teks

### `find`

```bash
find <lokasi> <kriteria>
```

| Opsi | Fungsi |
|------|--------|
| `-type f` | Hanya file biasa |
| `-size 1033c` | Ukuran tepat 1033 byte (`c` = byte) |
| `-not -executable` | Kecualikan file executable |
| `-user nama` | Dimiliki oleh user tertentu |
| `-group nama` | Dimiliki oleh group tertentu |

Contoh gabungan:

```bash
find / -user user1 -group group1 -size 33c 2>/dev/null
```

Menjelaskan `find /` dan `find /*`:

- `find /` mulai dari root dan menelusuri semua subfolder secara rekursif. Ini cara yang benar dan umum.
- `find /*` membuat shell mengubah `*` menjadi daftar isi root dulu, sehingga `find` menerima banyak argumen sekaligus. Hasilnya kurang konsisten, jadi tidak disarankan.

`2>/dev/null` membuang pesan error (misalnya `Permission denied`) supaya hasil pencarian bersih.

> `find` tidak memeriksa apakah isi file bisa dibaca manusia. Setelah dapat kandidat, cek tipenya dengan `file`.

### `grep`

```bash
grep kata data.txt          # baris yang mengandung "kata"
strings data.txt | grep '=' # baris yang mengandung tanda =
```

---

## 3. Mengolah teks

| Command | Fungsi |
|---------|--------|
| `sort` | Mengurutkan baris |
| `uniq -u` | Hanya tampilkan baris yang muncul **tepat satu kali** |
| `strings` | Ambil bagian teks yang bisa dibaca dari file biner |
| `tr` | Translate: ganti sekumpulan karakter menjadi karakter lain |
| `diff file1 file2` | Bandingkan dua file. `<` dari file pertama, `>` dari file kedua |

**Pipe (`|`)** mengalirkan output satu command menjadi input command berikutnya.

```bash
sort data.txt | uniq -u
```

`sort` wajib dipakai sebelum `uniq`, karena `uniq` hanya mendeteksi duplikat pada baris yang **berdekatan**.

Perbedaan perilaku `uniq`:

- `uniq` biasa: baris duplikat berurutan digabung jadi satu, tetapi tetap ditampilkan satu kali.
- `uniq -u`: baris yang punya duplikat dibuang seluruhnya, hanya baris unik yang tersisa.

---

## 4. Encoding dan hexdump

| Kebutuhan | Command |
|-----------|---------|
| Decode base64 | `base64 -d data.txt` |
| Encode base64 | `base64 data.txt` |
| ROT13 (encode dan decode sama) | `tr 'A-Za-z' 'N-ZA-Mn-za-m'` |
| Hexdump kembali ke biner | `xxd -r data.txt > data` atau `xxd -r data.txt data` |

Pada `xxd -r`, opsi `-r` berarti *reverse*. Dua bentuk penulisan di atas hasilnya sama:

- Bentuk pertama: output dikirim ke stdout, lalu `>` (redirect shell) menyimpannya ke file.
- Bentuk kedua: `xxd` sendiri menerima argumen kedua sebagai nama file output.

---

## 5. Kompresi dan arsip

| Format | Compress | Decompress |
|--------|----------|------------|
| `.gz` | `gzip` | `gunzip` |
| `.bz2` | `bzip2` | `bunzip2` |
| `.tar` | `tar -cf` | `tar -xf` |
| `.Z` (format lama) | `compress` | `uncompress` |

**Pola untuk file yang dikompres berlapis-lapis:**

1. Cek tipe dengan `file`
2. Beri ekstensi yang sesuai dengan `mv` (misalnya `mv data data.gz`)
3. Ekstrak dengan tool yang cocok
4. Cek lagi dengan `file`, ulangi sampai muncul `ASCII text`

**Folder kerja sementara yang aman**

```bash
mktemp -d
```

Membuat folder dengan nama acak di `/tmp`. Lebih aman daripada `mkdir` manual karena namanya sulit ditebak dan tidak bentrok dengan user lain.

---

## 6. Permission, owner, dan setuid

### `chmod` dengan angka

Tiga digit: **owner, group, others**. Nilai tiap digit adalah penjumlahan:

| Hak | Nilai |
|-----|-------|
| Read (r) | 4 |
| Write (w) | 2 |
| Execute (x) | 1 |
| Tidak ada akses | 0 |

| Angka | Arti |
|-------|------|
| `400` | Owner hanya bisa baca |
| `600` | Owner bisa baca dan tulis (4+2) |
| `644` | Owner baca-tulis, group dan others hanya baca |
| `777` | Semua pihak bisa baca, tulis, dan eksekusi |

> **Peringatan:** `chmod 777` sangat longgar. Di lab seperti Bandit dipakai karena proses lain (user berbeda) harus bisa menulis ke folder, tetapi di sistem nyata hindari kebiasaan ini.

Private key SSH wajib `chmod 600` atau `400`. Dengan permission default `644`, SSH menolak key tersebut dengan peringatan `UNPROTECTED PRIVATE KEY FILE`.

### Setuid binary

File executable yang, saat dijalankan, prosesnya memakai hak akses **pemilik file**, bukan hak akses user yang menjalankan. Cara pakai yang aman untuk dipelajari: jalankan tanpa argumen dulu untuk melihat petunjuk penggunaan.

### Hash untuk nama file

```bash
echo "teks" | md5sum
```

Berguna untuk memahami script yang membentuk nama file berdasarkan hash.

---

## 7. Jaringan dan SSH

### `nc` (netcat)

| Command | Fungsi |
|---------|--------|
| `nc localhost PORT` | Menjadi client, terhubung ke port lokal lalu bisa mengetik input |
| `nc -lp PORT` | Menjadi listener (server) di port tertentu. `-l` listen, `-p` port |
| `echo "teks" \| nc -lp PORT &` | Listener yang otomatis mengirim teks ke siapa pun yang terhubung, berjalan di background |

### `openssl s_client`

```bash
openssl s_client -connect host:PORT
openssl s_client -connect host:PORT -quiet
```

Seperti `nc` tetapi untuk service yang memakai SSL/TLS. Opsi `-quiet` menyembunyikan detail sertifikat supaya output lebih bersih.

Pesan `KEYUPDATE`, `RENEGOTIATING`, atau `READ R BLOCK` bukan error. Itu bagian normal dari TLS 1.3. Jika sesi terasa menggantung, ketik `Q` lalu Enter untuk keluar.

### `nmap`

```bash
nmap -sV -T4 -p 31000-32000 localhost
```

| Opsi | Fungsi |
|------|--------|
| `-sV` | Deteksi versi service |
| `-T4` | Scan lebih cepat |
| `-p A-B` | Batasi ke rentang port tertentu |

Status seperti `ssl/echo` (hanya memantulkan input) dan `ssl/unknown` (service khusus yang tidak dikenali) membantu memilih port mana yang layak diperiksa.

### SSH

| Command | Fungsi |
|---------|--------|
| `ssh -p PORT user@host` | Login dengan port khusus |
| `ssh -i key -p PORT user@host` | Login memakai private key |
| `ssh -p PORT user@host -t "/bin/sh"` | Paksa menjalankan shell tertentu dengan pseudo-terminal, melewati shell login default |

**Catatan:** SSH ke server yang sama dari dalam sesi di server itu sering diblokir (`Connecting from/to localhost is blocked`). Keluar dulu dengan `exit`, lalu jalankan SSH dari terminal lokal.

---

## 8. Proses background dan cron

| Command | Fungsi |
|---------|--------|
| `perintah &` | Jalankan di background |
| `jobs` | Daftar proses background di sesi terminal ini |

### Cron

Cron adalah penjadwal tugas otomatis. Konfigurasi biasanya ada di `/etc/cron.d/`.

```bash
ls /etc/cron.d/            # lihat file konfigurasi
cat /etc/cron.d/namafile   # baca jadwal dan perintahnya
bash namascript.sh         # jalankan manual untuk melihat output debug
```

Pelajaran penting saat membuat script yang dijalankan proses lain (cron dan sejenisnya):

- Gunakan **path absolut** (`/tmp/folder/hasil`), karena working directory saat dieksekusi bisa berbeda.
- Folder tujuan harus bisa **dimasuki** oleh user yang menjalankan proses, bukan hanya file hasilnya yang perlu dibuka izinnya.
- Folder yang writable tetapi tidak readable berarti kamu bisa memasukkan file, tetapi tidak bisa melihat isinya dengan `ls`.

---

## 9. Git

| Command | Fungsi |
|---------|--------|
| `git clone ssh://user@host:PORT/path/repo` | Clone lewat SSH. Port ditulis di dalam URL, bukan lewat opsi `-p` |
| `git log` | Riwayat commit |
| `git log -p` | Riwayat beserta isi perubahan. Baris `-` dihapus, baris `+` ditambah |
| `git branch -a` | Semua branch, termasuk branch remote |
| `git checkout nama-branch` | Pindah branch |
| `git tag` | Daftar tag |
| `git show nama-tag` | Detail isi tag |
| `git add -f namafile` | Tambahkan file secara paksa walau ada di `.gitignore` |
| `git commit -m "pesan"` | Simpan perubahan sebagai commit |
| `git push origin master` | Kirim commit ke branch master di remote |

Identitas commit harus diset sekali di tiap mesin, jika tidak akan muncul error `Author identity unknown`:

```bash
git config --global user.email "email@contoh.com"
git config --global user.name "Nama Kamu"
```

**Pelajaran:** informasi bisa tersembunyi di riwayat commit, branch lain, atau tag. Tidak semuanya terlihat di file kerja saat ini. Cek juga `.gitignore` untuk tahu file apa yang sengaja diblokir.

---

## 10. Trik bash dan shell

**Loop dan brace expansion**

```bash
for n in {0000..9999}; do
  echo "$n"
done | nc localhost PORT
```

`{0000..9999}` menghasilkan seluruh angka dari 0000 sampai 9999. Mengirim semua percobaan lewat **satu koneksi** jauh lebih cepat daripada membuka koneksi baru untuk setiap percobaan.

**Variabel `$0`**

`$0` berisi nama atau path shell yang sedang berjalan. Karena berupa variabel (bukan teks yang diketik), isinya tidak ikut difilter oleh shell yang mengubah semua input menjadi huruf besar. Menjalankan `$0` memanggil ulang shell secara normal.

---

## 11. Teknik keluar dari shell terbatas

Jika shell login suatu user diganti dengan script yang menampilkan file memakai `more` lalu langsung logout:

1. Kecilkan ukuran jendela terminal sebelum login, supaya `more` berhenti dan menunggu input (muncul indikator `--More--`).
2. Tekan `v` untuk membuka file yang sedang ditampilkan di editor (vi/vim).
3. Dari dalam vim, panggil shell:

```
:set shell=/bin/bash
:shell
```

**Keluar dari vim jika terjebak**

| Perintah | Hasil |
|----------|-------|
| `Esc` lalu `:q!` | Keluar tanpa menyimpan |
| `Esc` lalu `:wq` | Simpan lalu keluar |
