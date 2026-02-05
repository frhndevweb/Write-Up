| Reverse Engineering | Medium | 

---

## 🚩 Deskripsi Soal
Diberikan sebuah file binary Linux bernama `gatekeeper`. Program ini meminta sebuah "Master Key". Jika kunci benar, program akan memberikan flag.
> "Hanya mereka yang bisa membaca pikiran mesin yang boleh lewat."

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Identifikasi File
Pertama, saya memeriksa jenis file menggunakan perintah `file`.

**Command:**
```bash
file gatekeeper
```

Hasil:
gatekeeper: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked...
Ini adalah file executable 64-bit untuk sistem operasi Linux.

2. Pengujian Awal (Dynamic Analysis)
Saya mencoba menjalankan program untuk melihat perilakunya.
Command:
```
chmod +x gatekeeper
./gatekeeper
```

Hasil:
Enter Master Key: hello
Wrong Key! Try harder.

3. Static Analysis dengan strings
Sebelum menggunakan alat berat, saya mencoba melihat string yang tertanam di dalam binary.
Command:
```
strings gatekeeper
```

Hasil:
Terlihat beberapa string seperti Enter Master Key:, Wrong Key!, dan sebuah string aneh M1nd_Th3_G4p. Saya mencoba menggunakannya sebagai kunci, tetapi tetap gagal.
4. Decompilation dengan Ghidra
Saya memasukkan binary tersebut ke dalam Ghidra untuk melihat kode C tiruannya (pseudo-code). Di dalam fungsi main, ditemukan logika perbandingan kunci sebagai berikut:
```
undefined8 main(void) {
  char input[32];
  printf("Enter Master Key: ");
  scanf("%s", input);

  // Logika pengecekan kunci
  if (check_key(input) == 1) {
    print_flag();
  } else {
    puts("Wrong Key!");
  }
  return 0;
}
```

Saya memeriksa fungsi check_key:
```
int check_key(char *input) {
  int i;
  char local_key[] = "M1nd_Th3_G4p";
  for (i = 0; i < 12; i++) {
    if ((input[i] ^ 0x05) != local_key[i]) {
      return 0;
    }
  }
  return 1;
}
```

5. Memecahkan Algoritma (Decoding)
Berdasarkan kode di atas, program membandingkan input kita yang sudah di-XOR dengan nilai 0x05 terhadap string M1nd_Th3_G4p.
Maka, untuk mendapatkan kunci yang benar, saya harus melakukan operasi XOR balik:
Key = "M1nd_Th3_G4p" ^ 0x05
Saya menggunakan Python untuk menghitungnya:
cipher = "M1nd_Th3_G4p"
key = "".join([chr(ord(c) ^ 0x05) for c in cipher])
print(key)

Hasil Script:
H4ig_Qe6_B4u

6. Mendapatkan Flag
Saya menjalankan kembali program dan memasukkan kunci hasil dekoripsi tadi.
Command:
./gatekeeper
Enter Master Key: H4ig_Qe6_B4u

Hasil:
Access Granted! Flag: LKS{R3v3rs3_EnG_1s_Just_Log1c}

🏆 Kesimpulan
Program ini menggunakan teknik pengamanan sederhana dengan operasi gerbang logika XOR terhadap string statis. Dengan melakukan decompiling menggunakan Ghidra, kita dapat melihat algoritma aslinya dan membalikkan prosesnya untuk mendapatkan input yang valid.

Flag: LKS{R3v3rs3_EnG_1s_Just_Log1c}
🛠️ Tools yang Digunakan

 * file & strings: Analisis awal.
   
 * Ghidra: Decompiler untuk membaca kode C dari binary.
   
 * Python: Untuk menghitung balik operasi XOR.
<!-- end list -->
