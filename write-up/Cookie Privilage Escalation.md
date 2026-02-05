## Web Exploitation – Cookie Manipulation & Privilege Escalation

---

## 1. Identitas Soal

**Kategori :** Web Exploitation

**Nama Soal :** Cookie Privilege Escalation

**Target :** Web Service CTF

**Metode :** Manipulasi Cookie & Session Trust Abuse

---

## 2. Deskripsi Soal

Pada soal ini peserta diberikan sebuah web application dengan sistem autentikasi berbasis session cookie. Aplikasi tersebut memiliki dua level akses, yaitu **user** dan **admin**.

Secara normal, user hanya dapat mengakses halaman profil dan daftar produk, sedangkan halaman admin seharusnya hanya dapat diakses oleh pengguna dengan hak akses tinggi.

Namun setelah dilakukan observasi lebih dalam, ditemukan bahwa sistem hanya memeriksa **nilai cookie di sisi client**, tanpa melakukan validasi ulang di sisi server. Hal ini menimbulkan celah **Broken Access Control**, yang termasuk dalam kategori kritis menurut OWASP Top 10.

Dengan memanfaatkan celah ini, peserta dapat memanipulasi cookie untuk mengubah hak akses dari user menjadi admin dan memperoleh flag.

---

## 3. Cara Pengerjaan (Tahap Teknis Lengkap)

### 3.1 Observasi Sistem

1. Peserta melakukan login menggunakan akun biasa.
2. Sistem mengarahkan ke halaman `/dashboard`.
3. Pada menu tidak terdapat fitur admin.

Peserta kemudian mencoba mengakses langsung `/admin`, namun ditolak.

---

### 3.2 Analisis Session

1. Membuka **Developer Tools (F12)**
2. Masuk ke tab **Application → Cookies**
3. Ditemukan cookie berikut:

```
session=eyJ1c2VyIjoiaGFuIiwicm9sZSI6InVzZXIifQ==
```

Nilai tersebut tampak seperti **Base64**.

---

### 3.3 Decode Cookie

Menggunakan CyberChef → **From Base64**

Hasil:

```json
{"user":"han","role":"user"}
```

Ini menandakan role user disimpan langsung di cookie.

---

### 3.4 Manipulasi Cookie

Ubah nilai menjadi:

```json
{"user":"han","role":"admin"}
```

Lalu encode kembali ke Base64:

```
eyJ1c2VyIjoiaGFuIiwicm9sZSI6ImFkbWluIn0=
```

Ganti cookie lama dengan nilai ini.

---

### 3.5 Akses Admin Panel

Setelah refresh, menu **Admin Panel** muncul.
Masuk ke `/admin/config`, ditemukan flag.

---

## 4. Flag

```
SLKS{C00k13_Pr1v1l3g3_3sc}
```

---

## 5. Dampak Keamanan

Celah ini memungkinkan:

* Privilege escalation
* Pengambilalihan sistem
* Akses data sensitif
* Bypass autentikasi

Dalam sistem nyata, celah ini dapat menyebabkan kebocoran data besar.

---

## 6. Rekomendasi Perbaikan

1. Jangan menyimpan role di cookie
2. Validasi session di server
3. Gunakan JWT dengan signature
4. HttpOnly & Secure cookie
5. Implementasi access control server-side
