
| General Skills / Incident Response | Hard |

---

## 🚩 Deskripsi Soal
Seorang karyawan melaporkan aktivitas mencurigakan pada workstation-nya. Tim IT berhasil mengambil *memory dump* (RAM) dan *network capture* (PCAP) saat kejadian berlangsung. Temukan apa yang dicuri dan dapatkan flag-nya!

**File yang diberikan:**
1. `memdump.raw` (1GB Memory Dump)
2. `traffic.pcapng` (Network Traffic)

---

## 🕵️ Langkah-Langkah Pengerjaan

### 1. Analisis Lalu Lintas Jaringan (Wireshark)
Saya membuka file `traffic.pcapng` menggunakan **Wireshark** untuk melihat aktivitas koneksi keluar.

* **Filter:** `http.request.method == "POST"`
* **Temuan:** Terdapat banyak request ke IP eksternal `192.168.1.50` pada port `4444`. Ini adalah indikasi kuat adanya *Reverse Shell* atau *C2 (Command & Control) Traffic*.
* **Analisis Lanjutan:** Saya mengikuti *TCP Stream* pada salah satu paket tersebut. Ditemukan transmisi file terenkripsi bernama `secret_data.enc`.



### 2. Identifikasi Profil Memori (Volatility 3)
Karena file yang dicuri terenkripsi, saya harus mencari kunci enkripsinya di dalam memori workstation menggunakan **Volatility 3**.

**Command:**
```bash
python3 vol.py -f memdump.raw windows.info
```

Hasil: Sistem operasi teridentifikasi sebagai Windows 10 x64.

3. Analisis Proses Mencurigakan
Saya memeriksa daftar proses yang berjalan saat kejadian untuk menemukan malware atau script yang digunakan penyerang.
Command:
```
python3 vol.py -f memdump.raw windows.pslist
```

Temuan: Ditemukan proses powershell.exe dengan PID 4512 yang memiliki parent process explorer.exe. Ini mencurigakan karena dijalankan secara manual atau via script.

4. Ekstraksi Command Line & Script
Saya ingin melihat perintah apa yang dijalankan di PowerShell tersebut.
Command:
```
python3 vol.py -f memdump.raw windows.cmdline
```

Hasil:
powershell.exe -ExecutionPolicy Bypass -File C:\Users\Admin\Documents\encryptor.ps1

5. Memulihkan File dari Memori
Saya mencoba mengambil (dump) file encryptor.ps1 yang ada di dalam RAM untuk melihat algoritma enkripsinya.
Command:
```
python3 vol.py -f memdump.raw windows.filescan | grep "encryptor.ps1"
python3 vol.py -f memdump.raw windows.dumpfiles --virtaddr 0xdb819a30
```

7. Dekripsi Flag
Isi dari encryptor.ps1 menunjukkan penggunaan AES dengan kunci statis yang juga tersimpan di script tersebut:
 * Key: S3cur3_K3y_LKS_2026
 * IV: 0000000000000000
Saya menggunakan CyberChef (Tool profesional untuk decoding) dengan input data dari PCAP (Step 1) dan kunci dari RAM (Step 5).
Recipe CyberChef:
 * From Hex / Base64 (sesuaikan format data)
 * AES Decrypt (Mode: CBC, Key: UTF8, IV: UTF8)
 * 
🏆 Mendapatkan Flag
Setelah didekripsi melalui CyberChef, isi dari secret_data.enc adalah:
> "Exfiltration Successful. System Compromised."
> Flag: LKS{M3M_F0r3ns1cs_&_PC4P_An4lys1s_2026}

🛡️ Kesimpulan
Tantangan ini mensimulasikan skenario Real-world Intrusion. Kunci penyelesaiannya adalah menghubungkan data yang ditemukan di jaringan (PCAP) dengan artefak yang tertinggal di memori sistem (RAM).
Flag: LKS{M3M_F0r3ns1cs_&_PC4P_An4lys1s_2026}
🛠️ Tools Profesional yang Digunakan
 * Wireshark: Untuk analisis protokol jaringan.
 * Volatility 3: Framework standar industri untuk Memory Forensics.
 * CyberChef: "Swiss Army Knife" untuk operasi enkripsi dan encoding.
 * Python: Untuk otomasi ekstraksi data.
<!-- end list -->
