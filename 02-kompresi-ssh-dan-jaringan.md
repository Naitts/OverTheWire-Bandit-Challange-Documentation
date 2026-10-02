# Level 12 sampai 21: Kompresi, SSH, dan Jaringan

Bagian ini memperkenalkan bekerja di folder sementara, autentikasi dengan private key, komunikasi jaringan, dan setuid. Detail command ada di [`../cheatsheet.md`](../cheatsheet.md).

| Level | Tema | Tools utama | Pelajaran |
|-------|------|-------------|-----------|
| 12 ke 13 | Kompresi berlapis dari hexdump | `mktemp -d`, `xxd -r`, `file`, `mv`, `gunzip`, `bunzip2`, `tar` | Siklus: cek tipe, beri ekstensi, ekstrak, cek lagi. Kerjakan di folder sementara |
| 13 ke 14 | Login dengan private key | `ssh -i`, `chmod 600` | SSH menolak key dengan permission terlalu longgar. SSH ke server yang sama dari dalam sesinya diblokir |
| 14 ke 15 | Berbicara dengan service lokal | `nc` | Netcat membuka koneksi TCP mentah ke port tertentu |
| 15 ke 16 | Koneksi terenkripsi | `openssl s_client` | Untuk service yang memakai SSL/TLS. Pesan `KEYUPDATE` bukan error |
| 16 ke 17 | Menemukan service di rentang port | `nmap -sV`, `openssl s_client -quiet`, `chmod 400` | Status `ssl/unknown` menandai service khusus yang layak diperiksa |
| 17 ke 18 | Membandingkan dua file | `diff` | Tanda `<` dari file pertama, `>` dari file kedua |
| 18 ke 19 | Login yang langsung ter-logout | `ssh ... -t "/bin/sh"` | Perintah bisa diberikan langsung lewat SSH sehingga melewati shell login default |
| 19 ke 20 | Setuid binary | `./binary` tanpa argumen | Prosesnya berjalan dengan hak akses pemilik file |
| 20 ke 21 | Menguji client dengan server buatan sendiri | `nc -lp`, `&`, `jobs` | Buat listener sendiri untuk memahami cara kerja client |

## Catatan per level

### Level 12 ke 13
Data awal berupa hexdump, bukan file biner asli. Langkah pertama mengembalikannya ke biner dengan `xxd -r`. Setelah itu tipe file diperiksa berulang karena format kompresinya berganti-ganti di tiap lapisan.

`mktemp -d` dipakai untuk membuat folder kerja dengan nama acak di `/tmp` agar home directory tetap bersih dan tidak bentrok dengan user lain.

### Level 13 ke 14
Autentikasi tidak selalu memakai password. Private key bisa menggantikannya, tetapi file key harus punya permission ketat. Pengalaman gagal pada level ini:

- Mencoba `chmod` pada file yang belum dibuat menghasilkan `No such file or directory`.
- Memakai key dengan permission `644` memunculkan peringatan `UNPROTECTED PRIVATE KEY FILE`.
- Mencoba SSH dari dalam sesi server ke server yang sama diblokir.

### Level 14 ke 15
Pengenalan `nc` sebagai client. Terminal menunggu input setelah koneksi terbentuk.

### Level 15 ke 16
Sama seperti level sebelumnya, tetapi koneksi wajib terenkripsi. `openssl s_client` menampilkan detail sertifikat sebelum siap menerima input.

### Level 16 ke 17
Pemindaian port memberi gambaran service apa saja yang berjalan. Dari semua hasil, service yang memakai SSL tetapi tidak dikenali `nmap` adalah kandidat paling menarik, sedangkan service yang hanya memantulkan input bisa dilewati.

### Level 17 ke 18
Membandingkan dua file panjang secara manual tidak efisien. `diff` langsung menampilkan baris yang berbeda.

### Level 18 ke 19
File konfigurasi shell (`.bashrc`) milik user target sudah dimodifikasi sehingga login normal langsung ter-logout. Solusinya memberi SSH perintah untuk dijalankan, bukan membiarkan proses login normal berjalan.

### Level 19 ke 20
Pertama kali bertemu setuid binary. Kebiasaan baik: jalankan dulu tanpa argumen untuk membaca petunjuk penggunaannya.

### Level 20 ke 21
Binary ini bertindak sebagai client yang membaca satu baris dari sebuah koneksi. Dengan menyiapkan listener buatan sendiri di background (`&`) dan memeriksanya lewat `jobs`, kita mengontrol apa yang dibaca client tersebut.
