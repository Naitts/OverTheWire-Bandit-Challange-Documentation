# Level 21 sampai 34: Cron, Privilege, Brute Force, dan Git

Bagian ini berfokus pada membaca script milik sistem, memahami privilege, otomasi dengan bash, dan Git. Detail command ada di [`../cheatsheet.md`](../cheatsheet.md).

| Level | Tema | Tools utama | Pelajaran |
|-------|------|-------------|-----------|
| 21 ke 22 | Cron job sederhana | `/etc/cron.d/`, `cat` | Membaca konfigurasi dan script yang dijalankan terjadwal |
| 22 ke 23 | Cron dengan nama file dihitung | `md5sum`, `bash script.sh` | Menjalankan script secara manual membantu memahami alurnya |
| 23 ke 24 | Cron mengeksekusi script dari folder spool | `stat`, `chmod`, `cp` | Path absolut, izin folder tujuan, dan syarat owner file |
| 24 ke 25 | Brute force PIN 4 digit | loop bash, brace expansion, `nc` | Kirim semua percobaan dalam satu koneksi agar jauh lebih cepat |
| 25 ke 26 | Shell login yang bukan bash | `/etc/passwd`, `more`, `vi` | Pager dan editor bisa dipakai untuk memanggil shell |
| 26 ke 27 | Setuid binary (lanjutan) | `./binary` | Pola yang sama dengan Level 19 ke 20 |
| 27 ke 28 | Clone repo lewat SSH | `git clone` | Port khusus ditulis di dalam URL |
| 28 ke 29 | Riwayat commit | `git log -p` | Data yang "dihapus" bisa tetap ada di history |
| 29 ke 30 | Branch lain | `git branch -a`, `git checkout` | Informasi tidak harus ada di branch utama |
| 30 ke 31 | Tag | `git tag`, `git show` | Tag bisa menyimpan informasi tersembunyi |
| 31 ke 32 | Push ke remote | `.gitignore`, `git add -f`, `git config`, `git push` | File yang diblokir `.gitignore` bisa ditambahkan paksa. Identitas commit wajib diset |
| 32 ke 33 | Shell yang mengubah input jadi huruf besar | `$0` | Variabel tidak ikut difilter oleh shell tersebut |
| 33 ke 34 | Level terakhir | - | Tidak ada tantangan tambahan, Bandit selesai |

## Catatan per level

### Level 21 ke 22
Cron menjalankan tugas otomatis. Kebiasaan yang dilatih: cek `/etc/cron.d/` untuk tahu apa yang dijalankan, lalu baca script-nya.

### Level 22 ke 23
Nama file hasil ditentukan lewat perhitungan, bukan path tetap. Saat logika script membingungkan, jalankan manual untuk melihat output-nya, lalu tiru perhitungan yang sama.

### Level 23 ke 24
Level dengan pelajaran paling banyak soal praktik script:

- Cron mengeksekusi file di folder spool dengan hak akses user lain, tetapi hanya jika owner file sesuai syarat, lalu file dihapus.
- Folder spool bisa ditulis tetapi tidak bisa dibaca, jadi hasil harus ditulis ke lokasi lain yang bisa diakses.
- Gunakan path absolut karena working directory saat dieksekusi cron bisa berbeda.
- Folder tujuan harus bisa dimasuki oleh user yang menjalankan script, bukan hanya file hasilnya yang dibuka izinnya.
- Di lab ini `chmod 777` dipakai demi kemudahan. Di sistem nyata permission sebaiknya jauh lebih ketat.

### Level 24 ke 25
Ada 10.000 kemungkinan PIN (0000 sampai 9999). Alih-alih membuka koneksi baru untuk setiap percobaan, semua kombinasi dihasilkan lewat loop dan dialirkan ke satu koneksi `nc`. Ini jauh lebih efisien.

### Level 25 ke 26
Shell login user target ternyata bukan `/bin/bash`, melainkan script yang menampilkan teks lalu logout. Pemeriksaan `/etc/passwd` dan isi script tersebut mengungkap cara kerjanya. Triknya memanfaatkan perilaku pager dan fitur editor, dengan syarat jendela terminal cukup kecil agar pager berhenti menunggu input. Teknik umumnya dirangkum di bagian terakhir [`cheatsheet.md`](../cheatsheet.md).

### Level 26 ke 27
Mengulang konsep setuid. Pola kerjanya identik dengan Level 19 ke 20.

### Level 27 ke 28
Pertama kali memakai Git dari lokal terhadap repository di server. Lakukan di folder kerja kosong dan ingat bahwa port harus ditulis di dalam URL.

### Level 28 ke 29
Konten sensitif yang dihapus dari versi terbaru file tetap tersimpan di riwayat commit. Pelajaran nyata: jangan pernah commit secret ke repository, karena menghapusnya di commit berikutnya tidak cukup.

### Level 29 ke 30
Branch utama tidak selalu berisi semua informasi. Periksa semua branch, termasuk branch remote.

### Level 30 ke 31
Selain branch dan commit, Git punya tag sebagai referensi tambahan. `git show` menampilkan isi objek yang ditunjuk tag.

### Level 31 ke 32
Alur lengkap dari file baru sampai push: cek `.gitignore`, tambahkan file (paksa bila diblokir), set identitas, commit, lalu push. Respons server setelah push kadang membawa informasi tambahan.

### Level 32 ke 33
Shell yang mengubah semua input menjadi huruf besar membuat command biasa tidak bisa dijalankan. Variabel `$0` lolos dari filter karena berupa variabel, bukan teks yang diketik, sehingga memanggil ulang shell yang normal.

### Level 33 ke 34
Level terakhir hanya berisi pesan penutup. Bandit selesai di sini.
