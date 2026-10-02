# OverTheWire Bandit Notes

Catatan belajar saya setelah menyelesaikan wargame **OverTheWire Bandit** dari Level 0 sampai Level 33. Repository ini berisi ringkasan konsep dan kumpulan command Linux yang saya pelajari di setiap tahap.

> **Catatan penting:** password dan jawaban akhir setiap level **sengaja tidak dicantumkan**. OverTheWire meminta peserta tidak menyebarkan solusi agar orang lain tetap bisa merasakan proses belajarnya. Fokus catatan ini adalah konsep, tools, dan pelajaran yang didapat.

## Dokumentasi Video

Seluruh proses pengerjaan saya rekam dalam bentuk live stream:

- YouTube: https://www.youtube.com/live/GLiz1WRN_Bc

## Isi Repository

| File | Isi |
|------|-----|
| [`cheatsheet.md`](cheatsheet.md) | Kumpulan command Linux yang dikelompokkan per topik, lengkap dengan penjelasan opsi |
| [`levels/01-file-dan-pencarian.md`](levels/01-file-dan-pencarian.md) | Level 0 sampai 12: navigasi file, pencarian, pengolahan teks, encoding |
| [`levels/02-kompresi-ssh-dan-jaringan.md`](levels/02-kompresi-ssh-dan-jaringan.md) | Level 12 sampai 21: kompresi berlapis, SSH key, netcat, OpenSSL, nmap, setuid |
| [`levels/03-cron-privilege-dan-git.md`](levels/03-cron-privilege-dan-git.md) | Level 21 sampai 34: cron job, brute force, pager breakout, Git |

## Skill yang Dilatih

| Area | Topik |
|------|-------|
| Linux command line | `ls`, `cat`, `find`, `grep`, `sort`, `uniq`, `strings`, `diff`, `tr`, pipe dan redirect |
| File dan permission | hidden file, `chmod`, owner dan group, setuid binary |
| Encoding dan kompresi | base64, ROT13, hexdump (`xxd`), `gzip`, `bzip2`, `tar` |
| Jaringan | `nc` (netcat), `openssl s_client`, `nmap`, SSH dengan private key |
| Otomasi dan scripting | bash loop, brace expansion, cron job, background job |
| Git | `clone`, `log -p`, `branch`, `tag`, `add -f`, `push` |
| Pola pikir keamanan | membaca script milik sistem, memahami privilege, mencari informasi tersembunyi |

## Cara Terhubung ke Bandit

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

Level berikutnya diakses dengan user `bandit1`, `bandit2`, dan seterusnya, memakai password yang didapat dari level sebelumnya.

## Referensi

- Situs resmi: https://overthewire.org/wargames/bandit/
- Tutorial pembanding yang membantu saya: david-varghese.medium.com

## Penulis

Yohanes Christian Wibowo
Network Engineering, Cyber Security, System Analyst, dan Project Manager
LinkedIn: [isi dengan link profil LinkedIn kamu]
