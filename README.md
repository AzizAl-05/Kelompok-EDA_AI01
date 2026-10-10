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

> Hendratno. *COVID-19 Indonesia Dataset*. Kaggle. https://www.kaggle.com/datasets/hendratno/covid19-indonesia. Diakses [28 Spetember 2026]. Data dikompilasi dari covid19.go.id, kemendagri.go.id, bps.go.id, dan bnpb-inacovid19.hub.arcgis.com. Lisensi CC BY-NC-SA 4.0.

### 2.2 Dasar pemakaian dan konsekuensi lisensi

Lisensi **CC BY-NC-SA 4.0** berarti:

- **BY (Atribusi):** sumber wajib disebutkan. Kalimat sitasi di atas dipakai di laporan akhir dan di setiap grafik yang memakai data ini.
- **NC (Non-Komersial):** data tidak boleh dipakai untuk tujuan komersial. Proyek ini dipakai untuk keperluan akademik (tugas kuliah), sehingga sesuai.
- **SA (Berbagi Serupa):** jika tim membagikan hasil olahan data (misalnya berkas CSV yang sudah dibersihkan) ke publik, hasil olahan itu dibagikan dengan lisensi yang sama, CC BY-NC-SA 4.0.

---

## 3. Pertanyaan Penelitian

### 3.1 Pertanyaan payung

> Daerah seperti apa di Indonesia yang paling banyak terkena COVID-19 pada tahun 2022 (1 Januari sampai 15 September)?

### 3.2 Pertanyaan turunan

Setiap pertanyaan lolos tiga uji: **uji kolom** (kolom penjawab disebut namanya), **uji bentuk jawaban** (angka, peringkat, perbandingan, atau sebaran), dan **uji tindak lanjut** (apa yang bisa diputuskan dan oleh siapa, lihat 3.3).

| # | Pertanyaan | Kolom pendukung | jawaban |
|---|-----------|-----------------|---------------|
| 1 | Provinsi mana yang paling banyak dan paling sedikit terkena COVID-19? | `Location`, `New Cases`, `Population`, `Date` | Jumlahkan `New Cases` tahun 2022 per provinsi, bagi dengan `Population`, ambil 5 teratas dan 5 terbawah |
| 2 | Apakah provinsi yang lebih padat penduduknya juga lebih banyak terkena? | `Population Density`, `New Cases`, `Population` | Bagi 34 provinsi menjadi 3 kelompok kepadatan (jarang, sedang, padat), bandingkan nilai tengahnya |
| 3 | Pulau mana yang paling banyak terkena COVID-19? | `Island`, `New Cases`, `Population` | Jumlahkan kasus dan penduduk per pulau, bandingkan kasus per 1 juta penduduk |
| 4 | Dari setiap 1.000 orang yang tercatat terkena, berapa yang meninggal, dan apakah berbeda antarpulau? | `Island`, `New Cases`, `New Deaths` | Jumlahkan kematian dan kasus per pulau, hitung kematian per 1.000 kasus |

### 3.3 Uji tindak lanjut

| # | Keputusan yang bisa diinformasikan | Pengambil keputusan |
|---|------------------------------------|---------------------|
| 1 | Provinsi mana yang dicek lebih dulu kesiapan tes dan rumah sakitnya | Kemenkes bersama Dinas Kesehatan provinsi |
| 2 | Apakah kepadatan penduduk dijadikan salah satu pertimbangan awal memilih provinsi prioritas | Kemenkes dan pemerintah provinsi |
| 3 | Apakah persiapan dan pembagian logistik dibedakan per pulau | Kemenkes dan Dinas Kesehatan provinsi |
| 4 | Pulau mana yang layanan rujukan dan perawatan intensifnya perlu diperkuat | Kemenkes dan Dinas Kesehatan provinsi |

Hasil hanya menunjukkan pola, bukan sebab-akibat. Jumlah kasus yang tercatat juga dipengaruhi banyaknya orang yang dites di tiap daerah.

### 3.4 Pertanyaan yang dicoret

| Pertanyaan yang dicoret | Alasan |
|-------------------------|--------|
| Apakah vaksinasi menurunkan angka kematian antarprovinsi? | Gagal uji kolom: tidak ada kolom vaksinasi di dataset |

### 3.5 Arti istilah

| Istilah | Arti |
|---------|------|
| Terkena | Kasus COVID-19 yang tercatat di data (kolom `New Cases`) |
| Per 1 juta penduduk | Jumlah kasus ÷ `Population` × 1.000.000, supaya provinsi besar dan kecil bisa dibandingkan adil |
| Kelompok kepadatan | 34 provinsi diurutkan dari paling jarang sampai paling padat, lalu dibagi 3 kelompok hampir sama besar: Jarang (12), Sedang (11), Padat (11) |
| Nilai tengah (median) | Nilai yang berada di tengah kalau provinsi diurutkan. Dipakai karena DKI Jakarta sangat ekstrem, sehingga rata-rata biasa bisa menyesatkan |
| Kematian per 1.000 kasus | Jumlah kematian ÷ jumlah kasus tercatat × 1.000 |

`Total Cases per Million` tidak dipakai karena menghitung kasus kumulatif sejak Maret 2020, bukan hanya 2022.

---

## 4. Aturan Pengolahan

1. Satuan analisis adalah 34 provinsi. Baris dengan `Location Level` = `Country` dibuang agar tidak terhitung ganda.
2. Periode analisis: 1 Januari sampai 15 September 2022. Periode ini disebut di setiap judul grafik dan tabel.
3. Kolom `Date` berformat bulan/hari/tahun, diubah ke tipe tanggal sebelum diproses.
4. Angka per pulau dihitung dari total kasus dan total penduduk seluruh provinsi di pulau itu, bukan rata-rata angka tiap provinsi.
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

Keterbatasan analisis:

- Hanya menunjukkan pola, bukan sebab-akibat.
- Tidak ada data vaksinasi, usia, jenis kelamin, kebijakan, atau fasilitas kesehatan.
- DKI Jakarta jauh di atas provinsi lain (sekitar 50.452 kasus per 1 juta penduduk, sedangkan nilai tengah seluruh provinsi sekitar 4.233), sehingga hasil per pulau untuk Jawa sebagian besar dipengaruhi Jakarta.
- Pulau Maluku dan Papua masing-masing hanya terdiri dari 2 provinsi, sehingga angkanya mudah berubah oleh satu provinsi saja.
- Jumlah kasus yang tercatat bergantung pada banyaknya tes dan pelaporan di tiap provinsi.
- Periode 2022 hanya mewakili satu fase pandemi, jadi kesimpulan tidak digeneralisasi ke seluruh pandemi.

---

## 6. Kamus Data

Kamus data untuk seluruh 38 kolom dataset, termasuk kategori data pribadi dan tindakan penanganan tiap kolom, disimpan di `data_dictionary.csv`.

Seluruh kolom pada dataset ini bukan data pribadi. Data berupa hitungan agregat per wilayah dan atribut wilayah atau waktu, tidak memuat nama, NIK, alamat, usia, atau jenis kelamin.

---

## 7. Penandaan dan Nilai k

**Sebelum agregasi** (provinsi-hari, tahun 2022, 8.770 baris): k kasus harian = 1, k kematian harian = 1. Dari jumlah itu, 802 baris berisi tepat 1 kasus baru, dan 1.032 baris berisi tepat 1 kematian baru.

**Sesudah agregasi** (satu baris per provinsi untuk seluruh periode 2022): k kasus = 2.102 (Gorontalo), k kematian = 20 (Papua). Ambang yang dipakai K_MIN = 5. Status: **LULUS**.

**Tabel yang ditampilkan** (per pulau dan per kelompok kepadatan): k kasus terkecil = 6.630 (Maluku), k kematian terkecil = 48 (Papua). Semuanya di atas K_MIN = 5. Status: **LULUS**. Angka k ini tercatat di `data/processed/catatan_k.csv`.

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
