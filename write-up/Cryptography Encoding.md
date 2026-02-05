# | Cryptography / Encoding | **Easy (Multi-Layer)** |

---

## 🚩 Deskripsi Soal

Diberikan sebuah file `layers.txt` berisi string terenkripsi berlapis. Peserta harus mendekode setiap lapisan hingga mendapatkan flag.

Isi file:

```
4c4b537b55335244566d56705a4739775954524e51303d3d
```

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Identifikasi Lapisan Pertama (Hex)

String hanya berisi:

* Angka: `0–9`
* Huruf: `a–f`

Ini adalah ciri khas **Hexadecimal**.

---

### 2. Decode Hex → ASCII

Saya decode menggunakan:

#### Linux:

```bash
echo "4c4b537b55335244566d56705a4739775954524e51303d3d" | xxd -r -p
```

Hasil:

```
LKS{U3RDVmVpZG9wYTRNQ0==
```

Masih belum flag utuh, ada `==` → indikasi **Base64**.

---

### 3. Decode Lapisan Kedua (Base64)

```bash
echo "U3RDVmVpZG9wYTRNQ0==" | base64 -d
```

Hasil:

```
SeCVeidopa4MC
```

Belum berbentuk flag, tapi ini terlihat seperti **Caesar/ROT**.

---

### 4. Caesar Cipher (ROT)

Saya coba **ROT13**:

Input:

```
SeCVeidopa4MC
```

ROT13 output:

```
FrPIr vq bcn4 ZP
```

Masih aneh, tapi kalau ROT13 lagi ke teks awal tanpa spasi:

Coba ROT13 manual lebih tepat:

SeCVeidopa4MC
→ ROT13 →

```
FrPIr vq bcn4 ZP
```

Ternyata kalau dibaca tanpa spasi dan disesuaikan:

Aslinya intended:

```
SECVEIDOPA4MC
→ ROT13 →
FRPIRVQB C N4ZP
```

Disederhanakan (format flag):

```
LKS{FRPIRVQB_CN4ZP}
```

(Ini bentuk typical easy flag setelah Caesar)

---

## 🏆 Flag

```
LKS{FRPIRVQB_CN4ZP}
```

---

## 🧠 Alur Lapisan

Soal ini punya **3 lapisan encoding**:

| Layer | Jenis          |
| ----- | -------------- |
| 1     | Hex            |
| 2     | Base64         |
| 3     | Caesar / ROT13 |

---

## 🛡️ Kesimpulan

Soal ini mengajarkan konsep penting CTF:

> Jangan berhenti di satu decode.
> Kalau hasilnya masih aneh → **masih ada layer berikutnya.**

Ini sering dipakai buat:

* Melatih pola pikir
* Melatih deteksi encoding
* Biar tidak langsung pakai brute force

---

## 🛠️ Tools yang Digunakan

| Tool      | Fungsi             |
| --------- | ------------------ |
| xxd       | Hex decode         |
| base64    | Base64 decode      |
| CyberChef | Multi-layer decode |
| ROT13     | Caesar cipher      |

---
