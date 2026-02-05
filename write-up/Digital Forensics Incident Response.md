# | Digital Forensics / Incident Response | **Hard** |

---

## 🚩 Deskripsi Soal

Sebuah perusahaan mengalami *security incident* di mana salah satu server internal terindikasi melakukan komunikasi mencurigakan ke jaringan eksternal. Tim SOC berhasil mengamankan:

1. **Memory Dump** dari server saat insiden: `server_mem.raw`
2. **Network Capture** hasil monitoring IDS: `exfiltration.pcapng`

Tugas peserta adalah mengidentifikasi:

* Teknik serangan yang digunakan
* Data apa yang dicuri
* Serta mendapatkan **flag tersembunyi**

---

## 🕵️ Metodologi & Langkah Pengerjaan

### 1. Analisis Awal Network Traffic (Wireshark)

Saya membuka file `exfiltration.pcapng` menggunakan **Wireshark** untuk mencari indikasi komunikasi keluar.

**Filter utama:**

```
tcp.port == 8080 || tcp.port == 4444
```

**Temuan:**

* Ditemukan komunikasi intens ke IP eksternal: `45.133.77.21`
* Menggunakan metode **HTTP POST** berulang
* User-Agent mencurigakan: `python-requests/2.28`

Ini mengindikasikan **data exfiltration via custom script**.

Saya lakukan **Follow TCP Stream**, dan menemukan payload:

```
U2Vuc2l0aXZlRGF0YTpM...
```

Payload terlihat dalam format **Base64**.

---

### 2. Dekode Payload Exfiltration

Saya copy payload ke **CyberChef**:

Recipe:

* From Base64

Hasil:

```
SensitiveData:Login=admin
Password=********
File=flag.enc
```

Artinya penyerang mengirim **file terenkripsi bernama `flag.enc`**.

---

### 3. Identifikasi Profil Sistem (Volatility 3)

Selanjutnya saya analisis memory dump menggunakan **Volatility 3**.

Command:

```bash
python3 vol.py -f server_mem.raw windows.info
```

Hasil:

* OS: Windows Server 2019 x64
* Arsitektur sesuai untuk plugin Windows.

---

### 4. Deteksi Proses Malware

Saya cek daftar proses aktif:

```bash
python3 vol.py -f server_mem.raw windows.pslist
```

Temuan mencurigakan:

```
PID 3124  python.exe
Parent: services.exe
```

Python berjalan sebagai service → **sangat tidak normal**.

---

### 5. Analisis Command Line

```bash
python3 vol.py -f server_mem.raw windows.cmdline
```

Hasil:

```
python.exe C:\Windows\Temp\exfil.py
```

Ini adalah script malware utama.

---

### 6. Dump Script Malware dari Memory

Saya cari lokasi file:

```bash
python3 vol.py -f server_mem.raw windows.filescan | grep exfil.py
```

Dump file:

```bash
python3 vol.py -f server_mem.raw windows.dumpfiles --virtaddr 0x9f3a21c0
```

Isi `exfil.py`:

```python
from Crypto.Cipher import AES

key = b'CYBERJAWARA2026!'
iv = b'0000000000000000'
```

Script menunjukkan **AES-CBC encryption dengan static key**.

---

### 7. Dekripsi File Flag

Saya ambil data `flag.enc` dari PCAP stream, lalu ke **CyberChef**:

Recipe:

* From Hex
* AES Decrypt

  * Mode: CBC
  * Key: `CYBERJAWARA2026!`
  * IV: `0000000000000000`

Hasil dekripsi:

```
TOP_SECRET_FLAG
LKS{CYB3R_J4W4R4_D1G1T4L_F0R3NS1C_2026}
```

---

## 🏆 Flag

```
LKS{CYB3R_J4W4R4_D1G1T4L_F0R3NS1C_2026}
```

---

## 🛡️ Kesimpulan Teknis

Challenge ini merepresentasikan **real-world data exfiltration attack** dengan alur:

1. Malware Python dijalankan sebagai service
2. Mengambil data sensitif
3. Mengenkripsi dengan AES
4. Mengirim via HTTP POST ke server C2
5. Kunci enkripsi ditemukan di memory dump

Keberhasilan solve membutuhkan **korelasi antara PCAP + RAM**, yang merupakan skill inti **Digital Forensics & Incident Response tingkat lanjut**.

---

## 🛠️ Tools Profesional yang Digunakan

| Tool             | Fungsi                   |
| ---------------- | ------------------------ |
| **Wireshark**    | Analisis network traffic |
| **Volatility 3** | Memory forensics         |
| **CyberChef**    | Dekripsi & decoding      |
| **Python**       | Analisis malware         |
| **Linux CLI**    | Filtering & parsing data |

---
