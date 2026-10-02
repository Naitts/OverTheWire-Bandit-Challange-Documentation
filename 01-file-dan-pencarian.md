# Level 0 sampai 12: File, Pencarian, dan Pengolahan Teks

Bagian ini membangun fondasi command line Linux. Detail command ada di [`../cheatsheet.md`](../cheatsheet.md).

| Level | Tema | Tools utama | Pelajaran |
|-------|------|-------------|-----------|
| 0 ke 1 | Membaca file dasar | `cat` | Membaca file di home directory. Nama file tanpa spasi atau karakter khusus tidak butuh tanda kutip |
| 1 ke 2 | Nama file `-` | `cat ./-` | Satu tanda strip dianggap simbol stdin, jadi perlu `./` agar dibaca sebagai nama file |
| 2 ke 3 | Spasi dan awalan `--` | `cat --` dan tanda kutip | `--` menandai akhir opsi, tanda kutip menyatukan nama file yang berspasi |
| 3 ke 4 | File tersembunyi | `ls -la` | File yang diawali titik tidak muncul di `ls` biasa |
| 4 ke 5 | Memilih file yang bisa dibaca manusia | `file ./*` | `file` menentukan tipe dari isi, bukan dari ekstensi. Cari yang bertipe `ASCII text` |
| 5 ke 6 | Pencarian berdasarkan kriteria | `find` | Filter ukuran, tipe, dan sifat executable sekaligus. `find` tidak memeriksa isi file |
| 6 ke 7 | Pencarian di seluruh server | `find /` | Cari berdasarkan owner, group, dan ukuran. Gunakan `2>/dev/null` untuk membuang error `Permission denied` |
| 7 ke 8 | Mencari kata dalam file besar | `grep` | Tidak perlu membaca file satu per satu |
| 8 ke 9 | Mencari baris unik | `sort`, `uniq -u`, pipe | `uniq` hanya mendeteksi duplikat yang berdekatan, jadi data harus diurutkan dulu |
| 9 ke 10 | File campuran teks dan biner | `strings`, `grep` | `strings` mengambil teks yang terbaca dari data biner |
| 10 ke 11 | Data ter-encode | `base64 -d` | Base64 adalah encoding, bukan enkripsi |
| 11 ke 12 | ROT13 | `tr` | ROT13 simetris: proses yang sama dipakai untuk encode dan decode |

## Catatan per level

### Level 0 ke 1
Pintu masuk ke wargame. Latihan login SSH dan membaca file.

### Level 1 ke 2
Contoh bahwa shell dan program menafsirkan karakter tertentu secara khusus. `./` memaksa nama itu dibaca sebagai file di folder saat ini.

### Level 2 ke 3
Dua masalah sekaligus pada satu nama file (spasi dan awalan `--`), sehingga dua solusi dipakai bersamaan.

### Level 3 ke 4
Opsi `-a` hanya menampilkan semua file, sedangkan `-la` menambahkan detail permission, ukuran, dan tanggal ubah.

### Level 4 ke 5
Kombinasi `./*` berarti "semua file di folder ini", sehingga `file` memeriksa semuanya dalam sekali jalan.

### Level 5 ke 6
Latihan menerjemahkan deskripsi (ukuran, bisa dibaca, bukan executable) menjadi opsi `find`. Satuan `c` pada `-size` berarti byte.

### Level 6 ke 7
Lokasi file tidak diketahui, jadi pencarian dimulai dari root dengan filter kepemilikan. Perbedaan `find /` dan `find /*` dijelaskan di cheatsheet.

### Level 7 ke 8
Pengenalan `grep` untuk menyaring baris berdasarkan kata kunci.

### Level 8 ke 9
Pengenalan pipe sebagai cara menyambung beberapa command kecil menjadi satu alur kerja.

### Level 9 ke 10
Saat `cat` menghasilkan karakter rusak, `strings` memisahkan bagian teks dari bagian biner. Hasilnya disaring lagi dengan `grep`.

### Level 10 ke 11
Mengenali bahwa teks yang tampak acak bisa jadi hanya base64 dan dapat di-decode dengan mudah.

### Level 11 ke 12
`tr` memetakan satu set karakter ke set lain. Rentang `A-Za-z` dipetakan ke versi yang digeser 13 posisi.
