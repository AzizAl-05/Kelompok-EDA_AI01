# Kelompok-EDA_AI01
Mata Kuliah Exploratory Data Analysis 2026-1 (A) - Hilmy Abidzar Tawakal, S.T., M.Kom.

# Analisis Beban COVID-19 Antarprovinsi di Indonesia (2022)

Proyek tim untuk menganalisis apakah kepadatan penduduk dan letak pulau berkaitan dengan perbedaan beban COVID-19 antarprovinsi di Indonesia pada tahun 2022.

## 1 AnggotaTim

| No | Nama | Inisial |
|----|------|---------|
| 1 | [Abdul Aziz Al Palembani] | [Aziz]
| 2 | [Fahri Mulyadi] | [Fahri] 
| 3 | [Lukman Maruf] | [Lukman] 
| 4 | [Nicha Amelia SRG] | [Nicha]

## 2. Catatan Dataset (Lima Butir)

| Butir | Isian |
|-------|-------|
| **Nama sumber** | Hendratno (kompilator, Kaggle). Data asli dari covid19.go.id, kemendagri.go.id, bps.go.id, dan bnpb-inacovid19.hub.arcgis.com |
| **Tautan** | https://www.kaggle.com/datasets/hendratno/covid19-indonesia |
| **Tanggal pengambilan** | [28 September 2026] |
| **Lisensi** | CC BY-NC-SA 4.0 (tertulis di Data Card Kaggle) |
| **Kalimat sitasi** | Lihat bagian 2.1 |

**Berkas yang dipakai:** `covid_19_indonesia_time_series_all.csv`
**Lokasi berkas:** berkas mentah **tidak disimpan di repositori** (`data/raw/` masuk `.gitignore`). Setiap anggota mengunduh sendiri dari tautan Kaggle di atas, lalu menaruhnya di `data/raw/`.
**Cakupan isi data:** 1 Maret 2020 sampai 16 September 2022. Untuk baris provinsi, data tahun 2022 berakhir 15 September 2022.
**Ukuran:** 31.822 baris, 38 kolom, 34 provinsi ditambah baris tingkat nasional.
**Catatan pembaruan:** halaman Kaggle menandai dataset ini terakhir diperbarui sekitar 4 tahun lalu, sehingga tidak ada data setelah September 2022.

### 2.1 Kalimat sitasi

> Hendratno. *COVID-19 Indonesia Dataset*. Kaggle. https://www.kaggle.com/datasets/hendratno/covid19-indonesia. Diakses [tanggal pengambilan]. Data dikompilasi dari covid19.go.id, kemendagri.go.id, bps.go.id, dan bnpb-inacovid19.hub.arcgis.com. Lisensi CC BY-NC-SA 4.0.

### 2.2 Dasar pemakaian dan konsekuensi lisensi

Lisensi **CC BY-NC-SA 4.0** berarti:

- **BY (Atribusi):** sumber wajib disebutkan. Kalimat sitasi di atas dipakai di laporan akhir dan di setiap grafik yang memakai data ini.
- **NC (Non-Komersial):** data tidak boleh dipakai untuk tujuan komersial. Proyek ini dipakai untuk keperluan akademik (tugas kuliah), sehingga sesuai.
- **SA (Berbagi Serupa):** jika tim membagikan hasil olahan data (misalnya berkas CSV yang sudah dibersihkan) ke publik, hasil olahan itu dibagikan dengan lisensi yang sama, CC BY-NC-SA 4.0.

---

## 3. Pertanyaan Penelitian

### 3.1 Pertanyaan payung

> Apakah kepadatan penduduk dan letak pulau berkaitan dengan perbedaan jumlah kasus baru per juta penduduk antarprovinsi selama 1 Januari sampai 15 September 2022?

### 3.2 Pertanyaan turunan

Setiap pertanyaan lolos tiga uji: **uji kolom** (kolom penjawab disebut namanya), **uji bentuk jawaban** (angka, peringkat, perbandingan, atau sebaran), dan **uji tindak lanjut** (apa yang bisa diputuskan dan oleh siapa, lihat 3.3).

| # | Pertanyaan | Kolom pendukung | Cara menjawab |
|---|-----------|-----------------|---------------|
| 1 | Provinsi mana yang punya 5 nilai kasus 2022 per juta tertinggi dan 5 terendah? | `Location`, `New Cases`, `Date`, `Population` | Jumlahkan `New Cases` tahun 2022 per provinsi, bagi dengan `Population`, urutkan |
| 2 | Apakah provinsi dengan `Population Density` lebih tinggi cenderung punya kasus 2022 per juta lebih tinggi? | `Population Density`, `New Cases`, `Population` | Korelasi pada 34 provinsi dan scatter plot, dijalankan dengan dan tanpa DKI Jakarta |
| 3 | Apakah rata-rata kasus 2022 per juta berbeda antara 7 kelompok `Island`? | `Island`, `New Cases`, `Population` | Rata-rata per pulau, bandingkan dengan bar chart |
| 4 | Apakah rata-rata kematian 2022 per juta berbeda antara 7 kelompok `Island`? | `Island`, `New Deaths`, `Population` | Rata-rata per pulau, bandingkan dengan bar chart |

### 3.3 Uji tindak lanjut

| # | Keputusan yang bisa diinformasikan | Pengambil keputusan |
|---|------------------------------------|-------------------------------------------|
| 1 | Provinsi mana yang dicek lebih dulu kesiapan tes, pelacakan, dan fasilitas kesehatannya untuk gelombang berikutnya | Kemenkes bersama Dinas Kesehatan provinsi |
| 2 | Apakah kepadatan penduduk dipakai sebagai salah satu kriteria awal menentukan provinsi prioritas kesiapsiagaan | Kemenkes dan pemerintah provinsi |
| 3 | Apakah perencanaan logistik dan kesiapsiagaan dipisah per pulau | Kemenkes (perencanaan nasional) dan Dinas Kesehatan provinsi |
| 4 | Apakah pulau tertentu perlu penguatan layanan rujukan dan perawatan intensif | Kemenkes dan Dinas Kesehatan provinsi |

Semua hasil hanya menunjukkan keterkaitan, jadi dipakai untuk memilih prioritas pemeriksaan lebih lanjut, bukan untuk menyimpulkan penyebab. Angka kasus yang tinggi juga bisa mencerminkan banyaknya pengujian di provinsi itu.

### 3.4 Pertanyaan yang dicoret (langkah 3 latihan)

| Pertanyaan yang dicoret | Alasan |
|-------------------------|--------|
| Apakah vaksinasi menurunkan angka kematian antarprovinsi? | Gagal uji kolom: tidak ada kolom vaksinasi di dataset |

### 3.5 Definisi ukuran

| Ukuran | Rumus | Kolom sumber |
|--------|-------|--------------|
| Kasus 2022 per juta | jumlah `New Cases` (1 Jan 2022 s/d akhir data) ÷ `Population` × 1.000.000 | `New Cases`, `Population` |
| Kematian 2022 per juta | jumlah `New Deaths` (1 Jan 2022 s/d akhir data) ÷ `Population` × 1.000.000 | `New Deaths`, `Population` |

`Total Cases per Million` **tidak dipakai** karena menghitung kasus kumulatif sejak Maret 2020, bukan hanya 2022.

---

## 4. Aturan Pengolahan Bersama

1. Satuan analisis adalah 34 provinsi. Baris dengan `Location Level` = `Country` dibuang agar tidak terhitung ganda.
2. Periode analisis: 1 Januari sampai 15 September 2022. Sebut periode ini di setiap judul grafik dan tabel.
3. Kolom `Date` berformat bulan/hari/tahun. Ubah ke tipe tanggal sebelum diurutkan.
4. Pertanyaan 2 selalu dijalankan dua kali: dengan dan tanpa DKI Jakarta.
5. Berkas mentah tidak pernah diubah dan tidak pernah di-commit. Semua pembersihan dilakukan lewat kode.

---

## 5. Kondisi Data dan Keterbatasan

Temuan pemeriksaan awal pada berkas:

- 479 baris dengan `Total Active Cases` negatif.
- 288 baris dengan `Total Recovered` lebih besar dari `Total Cases`.
- 43 baris dengan `Case Fatality Rate` di atas 100%.
- `Case Fatality Rate` dan `Case Recovered Rate` tersimpan sebagai teks (contoh: "51.28%").
- Kolom `City or Regency` kosong seluruhnya, jadi tidak ada analisis tingkat kota atau kabupaten.
- Sekitar 18% baris tahun 2022 punya `New Cases` = 0. Bisa berarti tidak ada kasus atau belum dilaporkan.
- Gorontalo dan Sulawesi Barat kurang satu hari pada 2022 (berakhir 14 September).
- DKI Jakarta jauh di atas provinsi lain: kasus 2022 per juta sekitar 50.452, sedangkan median sekitar 4.233.

Keterbatasan analisis:

- Hanya menunjukkan **keterkaitan**, bukan sebab-akibat.
- Tidak ada data vaksinasi, usia, jenis kelamin, kebijakan, atau fasilitas kesehatan.
- Kelompok pulau berukuran tidak seimbang, sehingga rata-rata per pulau dibaca hati-hati.
- Periode 2022 hanya mewakili satu fase pandemi, jadi kesimpulan tidak digeneralisasi ke seluruh pandemi.
- Angka kasus bergantung pada kapasitas pengujian dan pelaporan tiap provinsi.

---

## 6. Kamus Data

Kamus data untuk seluruh 38 kolom dataset, termasuk kategori data pribadi dan tindakan penanganan tiap kolom, disimpan di `data_dictionary.csv`.

Seluruh kolom pada dataset ini bukan data pribadi. Data berupa hitungan agregat per wilayah dan atribut wilayah atau waktu, tidak memuat nama, NIK, alamat, usia, atau jenis kelamin.

---

## 7. Penandaan dan Nilai k

**Sebelum agregasi** (provinsi-hari, tahun 2022, 8.770 baris): k kasus harian = 1, k kematian harian = 1. Dari jumlah itu, 802 baris berisi tepat 1 kasus baru, dan 1.032 baris berisi tepat 1 kematian baru.

**Sesudah agregasi** (satu baris per provinsi untuk seluruh periode 2022): k kasus = 2.102 (Gorontalo), k kematian = 20 (Papua). Ambang yang dipakai K_MIN = 5. Status: **LULUS**.

---

## 8. Daftar Periksa Sebelum `git push`

- [ ] `data/raw/` masuk `.gitignore` dan tidak ikut ter-commit
- [ ] Tidak ada nama, NIK, nomor telepon, surel, atau alamat pada berkas mana pun
- [ ] Keluaran sel yang sempat menampilkan data pribadi sudah dibersihkan
- [ ] Garam hash tidak tertulis di dalam notebook
- [ ] Kamus data memuat kategori dan tindakan untuk setiap kolom
- [ ] Nilai k pada data yang diunggah sudah dihitung dan dicatat
- [ ] Bila masih ragu: repositori dibuat privat

Menghapus berkas lalu commit ulang tidak menghilangkan data dari riwayat Git. Periksa sebelum commit pertama, bukan sesudahnya.
