
| Forensics | Easy |

---

## 🚩 Deskripsi Soal
Tantangan ini memberikan sebuah file gambar bernama `scenery.jpg`. Deskripsi soal berbunyi:
> "Seorang informan mengirimkan foto pemandangan ini, tapi kami yakin ada pesan rahasia di dalamnya. Temukan pesan tersebut!"

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Analisis Metadata
Langkah pertama adalah memeriksa metadata file menggunakan tool `exiftool` untuk melihat apakah ada informasi tersembunyi di dalam tag gambar.

**Command:**
```bash
exiftool scenery.jpg
```

Hasil:
Tidak ditemukan informasi yang mencurigakan pada metadata, hanya informasi standar kamera dan resolusi.

2. Analisis String
Saya mencoba mencari teks yang dapat dibaca manusia di dalam file biner tersebut menggunakan perintah strings.
Command:
```
strings scenery.jpg | grep "LKS"
```
Hasil:
Kosong. Flag tidak disimpan dalam bentuk teks mentah.

3. Deteksi Steganografi
Karena ini adalah file gambar, ada kemungkinan file lain disisipkan di dalamnya (Steganografi). Saya menggunakan tool binwalk untuk memeriksa struktur file.
Command:
```
binwalk scenery.jpg
```

Hasil:
```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
15420         0x3C3C          Zip archive data, at least v2.0 to extract, compressed size: 162, uncompressed size: 210, name: secret.txt
```
Ditemukan adanya Zip archive data yang tersembunyi di dalam file JPEG tersebut.

4. Ekstraksi File Tersembunyi
Saya melakukan ekstraksi file tersebut menggunakan parameter -e pada binwalk.
Command:
```
binwalk -e scenery.jpg
```
Setelah diekstrak, muncul sebuah folder bernama _scenery.jpg.extracted. Di dalamnya terdapat file secret.txt. Namun, saat mencoba membukanya, file tersebut ternyata diproteksi oleh password.

5. Brute Force Password Zip
Untuk memecahkan password file zip tersebut, saya menggunakan fcrackzip dengan wordlist rockyou.txt.
Command:
```
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt _scenery.jpg.extracted/3C3C.zip
```

Hasil:
PASSWORD FOUND!!!!: password: chocolate

6. Mendapatkan Flag
Setelah membuka file secret.txt dengan password chocolate, isi filenya adalah:
Selamat! Anda berhasil menemukan pesan rahasia ini.
Flag: LKS{ST3G0_1S_FUN_2026_JKT}

🏆 Kesimpulan
File gambar tersebut menggunakan teknik Steganografi dengan menyisipkan file ZIP di akhir struktur file JPEG. File ZIP tersebut juga diproteksi dengan password sederhana yang dapat dipecahkan melalui metode dictionary attack.
Flag: LKS{ST3G0_1S_FUN_2026_JKT}

🛠️ Tools yang Digunakan

 * exiftool: Analisis metadata.
   
 * binwalk: Analisis dan ekstraksi file tersembunyi.
   
 * fcrackzip: Brute force password file ZIP.
<!-- end list -->
