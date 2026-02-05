
| Cryptography | Easy |

---

## 🚩 Deskripsi Soal
Kami menemukan sebuah pesan rahasia yang terenkripsi beserta beberapa nilai kunci publik. Bisakah Anda memecahkannya?

**Data yang diberikan:**
* `c` (Ciphertext): `47382...` (angka besar)
* `n` (Modulus): `869341...` (angka besar)
* `e` (Public Exponent): `65537`

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Identifikasi Algoritma
Berdasarkan variabel yang diberikan (`c`, `n`, dan `e`), ini adalah karakteristik utama dari algoritma **RSA (Rivest–Shamir–Adleman)**. Untuk mendapatkan pesan asli (`m`), kita membutuhkan kunci privat `d`.

Rumus dasar RSA:
$m = c^d \pmod{n}$

Untuk mencari `d`, kita harus memfaktorkan `n` menjadi dua bilangan prima `p` dan `q`.

### 2. Faktorisasi Modulus (n)
Karena nilai `n` dalam soal ini tidak terlalu besar (tidak sampai ribuan bit), saya mencoba menggunakan database faktorisasi *online* seperti **factordb.com** atau menggunakan tool `msieve`.



**Hasil Faktorisasi:**
* `p` = `28537`
* `q` = `30463`

### 3. Menghitung Private Key (d)
Setelah mendapatkan `p` dan `q`, langkah selanjutnya adalah menghitung:
1. **Phi (n)**: $\phi(n) = (p-1) \times (q-1)$
2. **d**: Invers perkalian modular dari `e` terhadap $\phi(n)$.

Saya menggunakan script Python sederhana untuk menghitungnya:

```python
from Crypto.Util.number import inverse, long_to_bytes
```
p = 28537
q = 30463
n = p * q
e = 65537
c = 47382... # (ciphertext dari soal)

phi = (p - 1) * (q - 1)
d = inverse(e, phi)
m = pow(c, d, n)

print(long_to_bytes(m).decode())

### 4. Mendapatkan Flag
Setelah script dijalankan, nilai m (message) yang dalam bentuk angka dikonversi menjadi string (bytes).
Output Script:
LKS{RSA_D3CRYPT_1S_EASY_2026}

🏆 Kesimpulan
Tantangan ini menguji pemahaman dasar tentang cara kerja algoritma RSA. Keamanan RSA sangat bergantung pada besarnya nilai n. Jika nilai n terlalu kecil, maka ia dapat difaktorkan dengan cepat untuk mendapatkan kunci privat.
Flag: LKS{RSA_D3CRYPT_1S_EASY_2026}
🛠️ Tools yang Digunakan
 * FactorDB: Untuk faktorisasi bilangan prima.
 * Python (PyCryptodome): Untuk perhitungan modular inverse dan dekripsi RSA.
<!-- end list -->
