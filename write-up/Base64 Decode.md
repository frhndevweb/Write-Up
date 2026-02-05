## 1. Identitas Soal

**Kategori :** Cryptography

**Nama Soal :** Base64 Decode

**Deskripsi Singkat :** Decode pesan terenkripsi Base64 untuk mendapatkan flag

---

## 2. Deskripsi Soal

Pada soal ini peserta diberikan sebuah string terenkripsi dengan format yang tidak dapat langsung dibaca. Berdasarkan pola karakter yang terdiri dari huruf besar, huruf kecil, angka, dan tanda `=` di akhir string, dapat disimpulkan bahwa pesan tersebut menggunakan **encoding Base64**.

Tujuan dari soal ini adalah melakukan proses decoding terhadap string Base64 tersebut hingga menghasilkan plaintext berupa **flag** yang valid dan dapat di-submit ke scoreboard.

---

## 3. Tahap Analisis

Langkah awal adalah melakukan identifikasi terhadap jenis encoding yang digunakan.

### 3.1 Identifikasi Encoding

Ciri-ciri string yang diberikan:

* Mengandung huruf A–Z, a–z, angka 0–9
* Memiliki karakter `=` di akhir string
* Tidak mengandung simbol khusus lain

Berdasarkan ciri tersebut, encoding yang digunakan adalah **Base64**.

---

## 4. Tahap Penyelesaian

Proses decoding dilakukan menggunakan beberapa metode untuk memastikan hasil yang valid.

### 4.1 Menggunakan CyberChef

1. Membuka CyberChef
2. Memasukkan string Base64 ke input
3. Menambahkan operasi **From Base64**
4. Hasil decoding langsung menampilkan plaintext

### 4.2 Menggunakan Command Line (Alternatif)

```bash
echo "U0xLU3tCYXNlNjRfRkxBR30=" | base64 -d
```

Hasil decoding menghasilkan sebuah string yang sesuai dengan format flag.

---

## 5. Hasil (Flag)

```
SLKS{Base64_FLAG}
```

Flag berhasil diperoleh dan di-submit ke scoreboard tanpa error.

---

## 6. Dampak Keamanan

Penggunaan Base64 sebagai metode pengamanan data **tidak aman**, karena Base64 hanya merupakan encoding, bukan enkripsi. Data yang di-encode dapat dengan mudah dikembalikan ke bentuk asli oleh pihak yang tidak berwenang.

Jika digunakan pada sistem nyata, hal ini dapat menyebabkan kebocoran informasi sensitif seperti token, password, atau data rahasia lainnya.

---

## 7. Rekomendasi Perbaikan

1. Jangan menggunakan Base64 sebagai pengaman data
2. Gunakan algoritma enkripsi yang kuat seperti AES atau RSA
3. Kombinasikan dengan hashing dan salting untuk data sensitif
4. Terapkan kontrol akses dan logging
