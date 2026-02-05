## Cryptography – XOR Encryption

### 1. Identitas Soal

**Kategori :** Cryptography

**Nama Soal :** XOR Message

**Deskripsi :** Dekripsi pesan XOR untuk menemukan flag

---

### 2. Deskripsi Soal

Peserta diberikan sebuah file `secret.txt` berisi string acak. Dari analisis awal, terlihat bahwa teks tidak sesuai dengan pola Base64 atau hex murni. Setelah dicoba XOR dengan beberapa key pendek, ditemukan bahwa pesan dapat dikembalikan ke plaintext yang bermakna.

---

### 3. Tahap Analisis

1. Mengecek format data → bukan Base64 / Hex
2. Mencoba XOR brute-force menggunakan Python
3. Ditemukan bahwa key = `key`
4. Setelah di-XOR, muncul plaintext flag

---

### 4. Tahap Penyelesaian

```python
data = open("secret.txt","rb").read()
key = b"key"
out = b""
for i in range(len(data)):
    out += bytes([data[i] ^ key[i % len(key)]])
print(out.decode())
```

---

### 5. Flag

```
SLKS{X0R_crypt0_br0k3n}
```

---

### 6. Dampak Keamanan

XOR tanpa random key sangat mudah dipecahkan dan tidak layak digunakan sebagai sistem pengamanan data.

---

### 7. Rekomendasi

Gunakan AES, kunci panjang, dan manajemen key yang baik.
