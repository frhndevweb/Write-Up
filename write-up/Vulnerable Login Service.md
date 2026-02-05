## Attack & Defense (Full Pwn) – SQL Injection Manual

### 1. Identitas Soal

**Kategori :** Full Pwn & Defense

**Nama Soal :** Vulnerable Login Service

**Target :** Web Server Tim Lawan

**Metode Serangan :** SQL Injection (Manual, tanpa scanning tools)

---

### 2. Deskripsi Soal

Pada modul Full Pwn & Defense, setiap tim diberikan sebuah server berisi aplikasi web.
Tugas peserta adalah menemukan celah, melakukan eksploitasi, mengambil flag,
kemudian melakukan **patching** agar tidak dapat dieksploitasi kembali.

Pada target ditemukan form login sederhana tanpa proteksi input.
Hal ini membuka peluang terjadinya **SQL Injection**.

---

### 3. Tahap Analisis (Tanpa Nmap)

Karena scanning tidak diperbolehkan, analisis dilakukan hanya melalui **browser**.

#### 3.1 Observasi Halaman

* Terdapat form:

  * username
  * password
* Setelah mencoba login salah, muncul pesan:

  > "Invalid username or password"

Ini menandakan backend memproses query SQL secara langsung.

---

### 4. Tahap Eksploitasi SQL Injection

#### 4.1 Payload Uji

Pada kolom **username**:

```
' OR '1'='1' --
```

Pada kolom **password**:

```
bebas
```

#### 4.2 Hasil

* Sistem menerima login tanpa password valid
* Berhasil masuk sebagai admin

---

### 5. Pengambilan Flag

Setelah masuk ke dashboard, ditemukan menu **Database / Config**.
Di dalam file config terdapat:

```
FLAG = SLKS{SQLi_FullPwn_Success}
```

Flag berhasil disubmit ke scoreboard.

---

### 6. Dampak Keamanan

Celah SQL Injection memungkinkan:

* Bypass login
* Akses data rahasia
* Penghapusan database
* Pengambilalihan sistem

Jika terjadi di sistem nyata, dapat menyebabkan kebocoran data besar.

---

### 7. Defense (Patching)

Langkah pengamanan yang dilakukan:

1. Mengganti query:

   ```php
   SELECT * FROM users WHERE username='$u' AND password='$p'
   ```

   menjadi **prepared statement**

2. Menambahkan:

   * Validasi input
   * Error handling
   * Escape string

3. Menonaktifkan debug error

---

### 8. Monitoring

Mengaktifkan logging pada web server dan database untuk mendeteksi percobaan SQL Injection.
