# Perjanjian Layanan Sistem Bisnis Terkelola
# Hutabyte Antigravity

**TEMPLATE — Isi bagian [DALAM KURUNG SIKU] sebelum presentasi kepada klien**

---

## PERJANJIAN LAYANAN

Perjanjian ini dibuat dan ditandatangani oleh:

**Pihak Pertama (Penyedia Layanan)**
Nama Perusahaan : PT/CV Hutabyte [*sesuaikan dengan badan hukum*]
Alamat           : [Alamat Hutabyte]
Diwakili oleh    : [Nama Penanggungjawab Hutabyte]
Jabatan          : [Jabatan]

**Pihak Kedua (Klien)**
Nama Organisasi  : [Nama Organisasi Klien]
Alamat           : [Alamat Klien]
Diwakili oleh    : [Nama Penanggungjawab Klien]
Jabatan          : [Jabatan]

Selanjutnya Pihak Pertama dan Pihak Kedua secara bersama-sama disebut "Para Pihak."

---

## Pasal 1 — Lingkup Layanan

Pihak Pertama menyediakan layanan pengelolaan sistem bisnis berbasis perangkat lunak
sumber terbuka (*open-source*) untuk Pihak Kedua, yang meliputi:

1. Penyediaan dan konfigurasi sistem [Odoo 17 Community / ERPNext] sesuai kebutuhan
   Pihak Kedua sebagaimana tercantum dalam Lampiran A.
2. Hosting server di Hetzner Cloud, region Singapura.
3. Pemeliharaan server: pembaruan keamanan (*security patching*), pemantauan uptime,
   dan pencadangan data harian.
4. Dukungan teknis sesuai tingkat layanan yang dipilih (Pasal 3).

Layanan **tidak mencakup**: pengembangan modul khusus (*custom module development*),
migrasi data dari sistem lama, atau pelatihan pengguna, kecuali disepakati secara
terpisah dan tertulis dengan biaya tambahan yang dikutip sebelum dimulai.

---

## Pasal 2 — Biaya Layanan dan Ketentuan Pembayaran

### 2.1 Biaya Bulanan

| Komponen | Biaya |
|---|---|
| Layanan dasar (hosting + maintenance + support bisnis) | Rp [JUMLAH]/bulan |
| [Tambahan: dukungan 24/7 dengan SLA 1 jam respons] | Rp [JUMLAH]/bulan |
| **Total** | **Rp [JUMLAH]/bulan** |

Harga di atas berlaku untuk jumlah pengguna tidak terbatas dalam satu organisasi
Pihak Kedua.

### 2.2 Penagihan

- Invoice diterbitkan setiap tanggal [TANGGAL] bulan berjalan.
- Pembayaran jatuh tempo 14 (empat belas) hari kalender setelah tanggal invoice.
- Metode pembayaran: transfer bank ke rekening Hutabyte yang tercantum di invoice.

### 2.3 Keterlambatan Pembayaran

Keterlambatan pembayaran lebih dari 14 hari setelah jatuh tempo dikenakan denda
keterlambatan sebesar 2% per bulan dari jumlah yang belum dibayar.

Hutabyte berhak menangguhkan layanan jika pembayaran terlambat lebih dari 30 hari,
dengan pemberitahuan tertulis 7 hari sebelumnya.

---

## Pasal 3 — Tingkat Layanan (Service Level Agreement / SLA)

### 3.1 Target Uptime

Hutabyte menjamin ketersediaan sistem (*uptime*) sebesar **99,5%** per bulan,
tidak termasuk:

- Jendela pemeliharaan terjadwal (maksimal 2 jam per bulan, pemberitahuan H-2)
- Gangguan di luar kendali Hutabyte (force majeure, gangguan Hetzner Cloud, gangguan
  jaringan internet pihak ketiga)

### 3.2 Waktu Respons Insiden

| Prioritas | Kondisi | Target Respons | Target Resolusi |
|---|---|---|---|
| P1 (Kritis) | Sistem tidak dapat diakses sama sekali | 1 jam | 4 jam |
| P2 (Tinggi) | Fitur utama tidak berfungsi, ada workaround | 4 jam | 8 jam |
| P3 (Normal) | Gangguan minor, tidak menghalangi operasi | 24 jam | 72 jam |

Waktu respons dihitung dari saat Pihak Kedua melaporkan insiden melalui saluran resmi
yang disepakati. SLA P1 hanya berlaku untuk paket dukungan 24/7.

### 3.3 Kompensasi SLA

Jika uptime di bawah 99,5% dalam satu bulan karena kelalaian Hutabyte, Pihak Kedua
berhak mendapat kredit layanan sebesar:

- Uptime 99,0%–99,5%: kredit 10% dari biaya bulan berjalan
- Uptime 95,0%–99,0%: kredit 25% dari biaya bulan berjalan
- Uptime di bawah 95,0%: kredit 50% dari biaya bulan berjalan

Kredit dipotong dari invoice bulan berikutnya. Kredit adalah satu-satunya kompensasi
yang tersedia atas pelanggaran SLA.

---

## Pasal 4 — Kepemilikan dan Privasi Data

### 4.1 Kepemilikan Data

Seluruh data yang dimasukkan dan dikelola oleh Pihak Kedua dalam sistem adalah
**milik sepenuhnya Pihak Kedua**. Hutabyte tidak memiliki hak untuk mengakses,
menggunakan, menjual, atau membagikan data Pihak Kedua kepada pihak ketiga manapun,
kecuali sebagaimana diatur dalam Pasal 4.3.

### 4.2 Akses Data oleh Hutabyte

Hutabyte hanya mengakses data Pihak Kedua untuk keperluan:
- Pemeliharaan teknis dan pemecahan masalah (*troubleshooting*)
- Pencadangan data (*backup*)
- Pemantauan performa sistem (metrik teknis, bukan isi data bisnis)

Setiap akses data oleh tim Hutabyte dicatat dalam log yang dapat diminta oleh Pihak
Kedua kapan saja.

### 4.3 Penggunaan AI dalam Layanan

Hutabyte menggunakan teknologi kecerdasan buatan (AI) untuk meningkatkan kualitas
layanan. Ketentuan penggunaannya:

**Data yang boleh diproses oleh AI Hutabyte:**
- Konfigurasi teknis sistem (daftar modul yang terpasang, versi, status)
- Log error teknis (tanpa isi transaksi atau data personal)
- Metrik performa server (CPU, RAM, waktu respons)

**Data yang TIDAK PERNAH diproses oleh AI:**
- Data karyawan (nama, NIK, gaji, data pribadi lainnya)
- Data pelanggan atau donor
- Transaksi keuangan (jumlah, rekening, pembayaran)
- Catatan proyek yang mengandung informasi personal

Pemrosesan AI menggunakan layanan Claude API (Anthropic, Amerika Serikat) terbatas
pada data operasional/teknis non-personal sebagaimana disebutkan di atas. Tidak ada
data personal yang dikirimkan ke layanan AI pihak ketiga.

### 4.4 Kepatuhan UU PDP 2022

Hutabyte berkomitmen mematuhi Undang-Undang No. 27 Tahun 2022 tentang Pelindungan
Data Pribadi dalam seluruh aspek pengelolaan data Pihak Kedua.

---

## Pasal 5 — Kebijakan Pencadangan Data

### 5.1 Jadwal Backup

- Pencadangan otomatis dilakukan setiap hari pukul 02.00 WIB
- Data dicadangkan ke penyimpanan objek yang terenkripsi (S3-compatible)
- Retensi: 30 (tiga puluh) hari terakhir

### 5.2 Verifikasi Backup

Hutabyte melakukan uji pemulihan (*restore test*) setiap bulan untuk memverifikasi
integritas backup. Hasil pengujian tersedia atas permintaan Pihak Kedua.

### 5.3 Ekspor Data

Pihak Kedua dapat meminta ekspor lengkap data mereka kapan saja. Hutabyte akan
menyediakan file ekspor dalam format standar ([CSV/JSON/SQL dump]) dalam waktu
7 (tujuh) hari kerja setelah permintaan diterima, tanpa biaya tambahan.

---

## Pasal 6 — Kebijakan Kustomisasi

### 6.1 Modul Standar

Layanan mencakup modul-modul standar yang tercantum dalam Lampiran A. Seluruh
konfigurasi modul standar termasuk dalam biaya bulanan.

### 6.2 Modul Khusus

Permintaan modul di luar daftar standar (Lampiran A) tidak termasuk dalam lingkup
layanan ini. Setiap permintaan kustomisasi harus:

1. Diajukan secara tertulis kepada Hutabyte
2. Dievaluasi dan dikutip harganya oleh Hutabyte dalam 5 hari kerja
3. Disetujui secara tertulis oleh Pihak Kedua sebelum pengerjaan dimulai

Hutabyte berhak menolak permintaan kustomisasi yang dianggap berisiko tinggi terhadap
stabilitas sistem atau yang bertentangan dengan prinsip standarisasi layanan Hutabyte.

---

## Pasal 7 — Pengakhiran Perjanjian

### 7.1 Pengakhiran oleh Salah Satu Pihak

Setiap pihak dapat mengakhiri perjanjian ini dengan memberikan pemberitahuan tertulis
minimal **30 (tiga puluh) hari kalender** sebelum tanggal pengakhiran yang diinginkan.

### 7.2 Kewajiban Hutabyte setelah Pengakhiran

Dalam waktu 7 (tujuh) hari kerja setelah tanggal pengakhiran efektif, Hutabyte akan:

1. Menyediakan file ekspor lengkap seluruh data Pihak Kedua
2. Memastikan data dapat diakses dan diimpor ke sistem lain
3. Menghapus secara permanen semua data Pihak Kedua dari server Hutabyte dan backup
   setelah konfirmasi penerimaan ekspor dari Pihak Kedua

### 7.3 Pengakhiran karena Pelanggaran

Hutabyte dapat mengakhiri perjanjian secara sepihak tanpa pemberitahuan 30 hari jika
Pihak Kedua:
- Melakukan penyalahgunaan sistem yang membahayakan klien Hutabyte lainnya
- Tidak membayar tagihan lebih dari 60 hari setelah jatuh tempo

---

## Pasal 8 — Lokasi Hosting dan Implikasi Data

### 8.1 Lokasi Server

Data Pihak Kedua disimpan di server Hetzner Cloud, **region Singapura**. Singapura
memiliki regulasi perlindungan data yang sebanding dengan standar internasional
(Personal Data Protection Act / PDPA Singapura).

### 8.2 Pertimbangan Data Lintas Batas

Pihak Kedua yang mengelola data donor internasional, data penerima hibah asing, atau
data yang tunduk pada regulasi yurisdiksi lain, bertanggung jawab untuk memastikan
bahwa penyimpanan data di Singapura sesuai dengan kewajiban hukum mereka.

Hutabyte dapat mendiskusikan pilihan hosting alternatif atas permintaan tertulis
Pihak Kedua.

---

## Pasal 9 — Ketentuan Umum

### 9.1 Hukum yang Berlaku

Perjanjian ini tunduk pada hukum Negara Republik Indonesia.

### 9.2 Penyelesaian Sengketa

Sengketa diselesaikan terlebih dahulu melalui musyawarah. Jika tidak tercapai
kesepakatan dalam 30 hari, sengketa diselesaikan melalui [BANI / Pengadilan Negeri
yang disepakati].

### 9.3 Perubahan Perjanjian

Setiap perubahan perjanjian ini harus dilakukan secara tertulis dan ditandatangani
oleh kedua pihak.

---

## Tanda Tangan

Perjanjian ini dibuat dalam rangkap dua, masing-masing bermaterai cukup dan memiliki
kekuatan hukum yang sama.

**Pihak Pertama — Hutabyte**

Tanda Tangan : ___________________________

Nama          : ___________________________

Jabatan       : ___________________________

Tanggal       : ___________________________

---

**Pihak Kedua — [Nama Organisasi Klien]**

Tanda Tangan : ___________________________

Nama          : ___________________________

Jabatan       : ___________________________

Tanggal       : ___________________________

---

## Lampiran A — Modul Sistem yang Disediakan

*[Lampirkan dokumen odoo-config-spec.md atau erpnext-config-spec.md yang sesuai]*

| No. | Nama Modul | Fungsi |
|---|---|---|
| 1 | [sesuaikan] | [sesuaikan] |

---

*Template ini dibuat oleh Hutabyte untuk keperluan internal. Konsultasikan dengan
konsultan hukum sebelum digunakan untuk klien dengan nilai kontrak signifikan.*
