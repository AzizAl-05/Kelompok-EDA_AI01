# Cleaning Log

**Tim:** []
**Dataset:** `covid_19_indonesia_time_series_all.csv` (kasus harian COVID-19 per provinsi di Indonesia, 1 Maret 2020 – 16 September 2022)
**Baris mentah:** 31.822 → **baris bersih:** 30.893 (tingkat provinsi) + 929 baris nasional disimpan terpisah
**Terakhir diperbarui:** 19 Oktober 2026

---

## Urutan langkah

| # | Langkah | Kolom | Baris terdampak | Keputusan | Alasan |
|---|---|---|---|---|---|
| 1 | Perbaiki tipe data | Seluruh nama kolom; `case_fatality_rate`, `case_recovered_rate`; `date` | 38 kolom; 31.822; 31.822 | Nama kolom diubah ke snake_case. Dua kolom persentase diubah dari teks `'51.28%'` ke angka 0–100. `date` diubah ke datetime dengan format `%m/%d/%Y`. BOM di awal berkas dibuang saat membaca (`utf-8-sig`). | Selama tanggal dan persentase masih teks, tidak ada pemeriksaan angka yang bisa berjalan. Urutan tanggal dibuktikan, bukan ditebak: bagian pertama tidak pernah melebihi 12 dan bagian kedua mencapai 31, jadi formatnya bulan/hari/tahun. `dayfirst=True` ditolak karena menukar bulan dan hari (3/1/2020 menjadi 3 Januari) tanpa memunculkan galat. Titik pada persentase adalah desimal, jadi tidak boleh dihapus seperti pemisah ribuan pada Rupiah. |
| 2 | Buang spasi pada kolom teks | 9 kolom teks | 0 | Diperiksa pada seluruh kolom teks; tidak ada yang perlu diubah. | Spasi tersembunyi memecah `groupby` dan melambungkan `nunique`. Hasil nol tetap dicatat sebagai bukti pemeriksaan sudah dilakukan. |
| 3 | Placeholder menjadi kosong | Seluruh kolom teks; kolom hitungan (`new_cases`, `new_deaths`, `new_recovered`, `total_*`) | 0; 0 | Diperiksa teks `N/A`, `-`, `null`, dan kode `-999`/`-9999`; tidak ditemukan. | Kode seperti `-999` merusak rata-rata tanpa terdeteksi `isna()`. Kolom `new_active_cases` dan `total_active_cases` sengaja tidak diperiksa karena nilai negatif di sana sah, sehingga `-999` tidak otomatis bisa dianggap placeholder. |
| 4 | Seragamkan kategori | `location`, `island`, `time_zone`, `special_status`, `location_iso_code` | 0 | Tidak ada sinonim atau salah ketik; peta penyeragaman dibiarkan kosong. Huruf tidak diubah otomatis. | `.str.title()` ditolak karena merusak singkatan (`DKI Jakarta` menjadi `Dki Jakarta`, `UTC+07:00` menjadi `Utc+07:00`). Konsistensi diuji: setiap lokasi punya tepat satu kode ISO, satu pulau, satu zona waktu, dan satu status khusus. |
| 5 | Tangani duplikat | Seluruh kolom; kunci (`date`, `location`); baris `location_level = Country` | 0; 0; 0; 929 | Tidak ada duplikat persis, tidak ada kunci ganda, tidak ada kunci kosong, tidak ada hari bolong. 929 baris nasional (`Indonesia`) **dipisahkan** ke tabel sendiri, tidak dibuang. | Baris nasional adalah agregat dari 34 provinsi; bila dijumlahkan bersama baris provinsi, setiap kasus terhitung dua kali tanpa galat apa pun. Dipisah, bukan dibuang, karena masih berguna sebagai pembanding. Jumlah kasus baru provinsi 6.397.309 vs nasional 6.405.044, selisih 7.735 kasus yang tidak terpetakan ke provinsi. Aturan penyatuan kunci ganda (bila ada) adalah simpan `total_cases` terbesar, karena kumulatif tidak pernah turun. |
| 6 | Tangani nilai mustahil | `case_recovered_rate`; `case_fatality_rate`; kolom hitungan; `population`, `area_km2`; `total_recovered`, `total_deaths`, `total_active_cases` | 288; 43; 0; 0; 479 | Rasio sembuh > 100% (288 baris) dan rasio kematian > 100% (43 baris) dijadikan **kosong**. Hitungan negatif dan populasi/luas ≤ 0: tidak ada. Baris dengan sembuh > kasus, meninggal > kasus, atau aktif negatif **ditandai** di kolom `kumulatif_janggal` (479 baris, tersebar di 19 provinsi, terbanyak Jawa Timur 132 dan Banten 103), tidak ditimpa. | Rasio di atas 100% mustahil secara matematis, jadi dikosongkan; barisnya masih berguna untuk kolom lain. Identitas `aktif = kasus − meninggal − sembuh` selalu terpenuhi (0 selisih), artinya aktif negatif adalah **akibat** sembuh > kasus, bukan kesalahan hitung terpisah. Tidak diketahui kolom mana yang keliru (sembuh terlalu besar, atau kasus terlambat dilaporkan), jadi memaksa `total_recovered = total_cases` ditolak karena sama dengan memutuskan bahwa angka kasus pasti benar. Uji konsistensi: total tidak pernah turun dan selisih antar hari sama dengan kolom `new_*` (0 pelanggaran). |
| 7 | Tangani nilai kosong | 5 kolom dibuang; `growth_factor_of_new_cases`; `growth_factor_of_new_deaths`; `special_status`; `total_cities`; `total_urban_villages`; `total_rural_villages` | 5 kolom; 1.935; 3.442; 26.335; 901; 904; 929 | Dibuang: `city_or_regency` (100% kosong), `province` (identik dengan `location`, dibuktikan dengan `assert`), `country`, `continent`, `location_level` (konstan). `growth_factor_*` dan tiga kolom `total_*` **dibiarkan kosong**. `special_status` kosong diberi kategori **"Tidak ada status khusus"** plus kolom penanda `punya_status_khusus`. | Kolom yang tidak membawa informasi dibuang, bukan diisi. Kekosongan `growth_factor_*` seluruhnya dijelaskan oleh pembagian dengan nol (kasus kemarin 0) atau hari pertama lokasi, jadi tidak ada nilai benar yang bisa diisi. Kekosongan `total_cities`/`total_urban_villages`/`total_rural_villages` hanya terjadi pada satu provinsi (Sulawesi Barat, Aceh, DKI Jakarta) dan konstan sepanjang waktu: struktural, bukan acak. Tidak diisi 0 karena penyebabnya belum dipastikan. Mengisi dengan mean/median ditolak; simulasi pada notebook menunjukkan pengisian median meleset sekitar 13% dari rata-rata `new_cases` bahkan pada kasus acak, karena sebarannya sangat condong. |
| 8 | Buat kolom turunan | `bulan`, `tahun`, `hari`, `kasus_rata7`, `kelas_kasus` | 30.893 | Data diurutkan menurut `location` lalu `date`. Ditambahkan bulan, tahun, nama hari, rata-rata bergulir 7 hari `new_cases` per lokasi, dan `kelas_kasus` dari `new_cases_per_million` dengan batas 1, 10, 30 (Sangat rendah, Rendah, Sedang, Tinggi). Enam hari pertama tiap lokasi dibiarkan kosong pada `kasus_rata7`. | Rata-rata 7 hari meredam pola pelaporan mingguan dan hanya bermakna setelah data diurutkan dan baris nasional dipisahkan. Kasus per juta dipakai agar provinsi besar dan kecil dapat dibandingkan. Batas bin adalah keputusan, bukan perhitungan: dipilih dengan melihat sebaran (median sekitar 5,5, kuartil atas sekitar 22) agar tiap kelas cukup terisi. Mengubah batas akan mengubah kesimpulan analisis. |
| 9 | Verifikasi diagnosis ulang | Seluruh tabel | n/a | Diagnosis pertemuan 4 dijalankan ulang pada data sebelum dan sesudah. Rasio > 100% turun dari 288/43 menjadi 0/0; tipe data benar; nama "Indonesia" tidak lagi ada di tabel provinsi. `total_active_cases < 0` tetap 479 karena memang hanya ditandai. | Pembersihan belum selesai sampai masalahnya terbukti hilang dan tidak ada masalah baru yang diciptakan sendiri. |
| 10 | Simpan hasil | `data/processed/covid_provinsi_bersih.csv`, `data/processed/covid_nasional.csv` | 30.893; 929 | Hasil provinsi dan nasional disimpan terpisah. Data mentah di `data/raw/` tidak disentuh. | Aturan 1: data mentah tidak pernah ditimpa. Tabel nasional disimpan agar bisa dipakai sebagai pembanding tanpa menggandakan hitungan. |

---

## Peta keputusan manual

| Nilai asal | Diubah menjadi | Dasar keputusan |
|---|---|---|
| *(tidak ada sinonim atau salah ketik pada nama lokasi, pulau, zona waktu)* | *(peta penyeragaman dibiarkan kosong)* | Diperiksa dengan membandingkan jumlah nilai unik sebelum dan sesudah `casefold` + rapikan spasi: 35 lokasi, 7 pulau, 3 zona waktu, tidak ada yang berubah. Kosongnya peta adalah hasil pemeriksaan, bukan kelalaian. |
| `special_status` kosong (26.335 baris) | "Tidak ada status khusus" | Kosong konstan per provinsi: 29 dari 34 provinsi tidak punya status khusus, sedangkan lima provinsi yang berstatus khusus (Aceh, DKI Jakarta, DI Yogyakarta, Papua, Papua Barat) selalu terisi. Maknanya "tidak berlaku", bukan "hilang". **Perlu dikonfirmasi ke pemilik data** (lihat pertanyaan di bawah). |
| Baris `location_level = Country` | Dipindah ke tabel nasional | Satu baris `Indonesia` per hari adalah agregat 34 provinsi; keputusan ini dibuktikan dengan membandingkan jumlah kasus (6.397.309 vs 6.405.044). |

---

## Yang sengaja TIDAK diubah

| Masalah | Mengapa dibiarkan | Akibat bagi analisis |
|---|---|---|
| `new_active_cases` bernilai negatif (12.696 baris) | Bukan kesalahan: aktif turun bila yang sembuh lebih banyak daripada kasus baru. Mengubahnya akan merusak data yang benar. | Aman dipakai apa adanya. Jangan dianggap anomali saat memeriksa pencilan. |
| `total_active_cases` negatif dan sembuh/meninggal > kasus (479 baris, `kumulatif_janggal`) | Tidak diketahui kolom mana yang keliru; menimpa berarti menebak. Hanya ditandai. | Analisis yang memakai angka sembuh, aktif, atau rasio kematian harus dijalankan **dengan dan tanpa** baris bertanda, lalu dibandingkan. Terkonsentrasi di 19 provinsi, terutama Jawa Timur dan Banten. |
| `growth_factor_of_new_cases` kosong 1.935 baris dan `growth_factor_of_new_deaths` kosong 3.442 baris | Kosong karena pembagian dengan nol (kemarin 0) atau hari pertama; tidak ada nilai benar untuk diisi. | Jangan diimputasi. Rata-rata faktor pertumbuhan hanya menggambarkan hari-hari yang punya pembanding. |
| `growth_factor_*` bernilai 1,0 saat kemarin 0 dan hari ini 0 (2.197 baris pada kasus baru) | Itu konvensi sumber data (0/0 dianggap 1), bukan hasil hitung yang bisa diperiksa; tidak ada dasar untuk mengubahnya. | Nilai 1,0 pada hari tanpa kasus bukan berarti "stabil". Hati-hati saat merata-ratakan. |
| `total_cities` (901 baris, Sulawesi Barat), `total_urban_villages` (904, Aceh), `total_rural_villages` (929, DKI Jakarta) kosong | Struktural dan konstan sepanjang waktu; penyebabnya belum dipastikan, jadi tidak diisi 0. | Kolom ini tidak bisa dipakai membandingkan seluruh provinsi tanpa memperlakukan tiga provinsi itu secara khusus. |
| Provinsi baru muncul pada tanggal kasus pertamanya (mulai 1 Maret sampai 10 April 2020) | Itu bukan data hilang melainkan belum ada kasus; mengisi dengan nol berarti mengarang. | Total dan rata-rata per provinsi memiliki jumlah hari berbeda; bandingkan dengan hati-hati. Tidak ada hari bolong di tengah deret. |
| Nilai `new_cases` yang sangat besar (maksimum 16.251 per provinsi per hari) | Belum diputuskan sebagai pencilan atau kejadian nyata; itu bahan analisis univariat (pertemuan 6). | Rata-rata `new_cases` sangat condong ke kanan (mean 207, median 24). Gunakan median atau skala log bila perlu. |

---

## Pertanyaan yang belum terjawab

- [ ] Mengapa `total_recovered` melebihi `total_cases` pada 288 baris (dan `total_deaths` melebihi `total_cases` pada 43 baris)? Apakah karena keterlambatan pelaporan kasus atau pelaporan serentak data sembuh lama? Perlu konfirmasi ke pemilik data atau penjelasan metodologi sumber.
- [ ] Mengapa jumlah kasus di seluruh provinsi (6.397.309) kurang 7.735 dari angka nasional (6.405.044)? Apakah ada kasus yang tidak dipetakan ke provinsi mana pun?
- [ ] Apakah `special_status` kosong memang berarti "tidak punya status khusus"? Perlu dipastikan, karena kategori "Tidak ada status khusus" adalah tafsiran kami.
- [ ] Apakah `total_cities` kosong (Sulawesi Barat), `total_urban_villages` kosong (Aceh), dan `total_rural_villages` kosong (DKI Jakarta) berarti 0 atau data tidak tersedia?
- [ ] Mengapa dua provinsi berhenti pada 14 September 2022 sedangkan 32 provinsi lain berhenti pada 15 September 2022? Apakah ada keterlambatan pelaporan atau pencatatan terakhir yang belum masuk?
- [ ] Nilai `growth_factor_*` sebesar 1,0 saat 0/0: apakah ini konvensi resmi sumber data?

---

## Dampak keseluruhan

| Ukuran | Sebelum | Sesudah | Catatan |
|---|---|---|---|
| Jumlah baris | 31.822 | 30.893 | Turun 929 karena baris nasional dipisahkan ke berkas sendiri (tidak dibuang). Tidak ada baris yang dihapus karena rusak. |
| Jumlah kolom | 38 | 40 | 5 kolom dibuang (`city_or_regency`, `province`, `country`, `continent`, `location_level`) dan 7 ditambahkan (`kumulatif_janggal`, `punya_status_khusus`, `bulan`, `tahun`, `hari`, `kasus_rata7`, `kelas_kasus`). |
| Total sel kosong | 69.993 | 8.646 | **Turun**, tetapi bukan karena data ditemukan: 31.822 sel hilang karena `city_or_regency` (100% kosong) dibuang, 26.335 karena `special_status` kosong diberi arti sebagai kategori, dan sisanya karena baris nasional dipisahkan. Sebaliknya, **bertambah 331** kosong baru yang jujur: `case_recovered_rate` 0 menjadi 288 dan `case_fatality_rate` 0 menjadi 43, yaitu rasio mustahil yang tadinya menyamar sebagai angka. `kasus_rata7` juga menambah kosong pada enam hari pertama tiap lokasi. |
| Baris duplikat | 0 | 0 | Tidak ditemukan duplikat persis maupun kunci (`date`, `location`) ganda. Duplikat yang nyata adalah duplikat tingkat agregasi (baris nasional), yang sudah dipisahkan. |
| Kategori pada kolom kunci (`location`) | 35 | 34 | Selisih satu karena `Indonesia` bukan provinsi. Jumlah pulau tetap 7; `special_status` naik dari 3 menjadi 4 nilai karena kategori "Tidak ada status khusus". |

---

## Reproduktibilitas

- [x] Notebook berjalan dari awal sampai akhir setelah *Restart and run all* (diuji pada pandas 2.2 dan 3.0 tanpa galat)
- [x] Pembersihan dibungkus dalam satu fungsi, bukan tersebar di banyak sel (`bersihkan()`; dijalankan dua kali menghasilkan data dan log identik)
- [ ] `data/raw/` tidak berubah dan tidak ikut diunggah (tidak berubah: terbukti; **belum** diatur agar tidak ikut diunggah, tambahkan ke `.gitignore` saat push)
- [x] Hasil tersimpan di `data/processed/`
- [ ] `python cek_m2.py` berjalan tanpa status GAGAL (skrip belum tersedia pada kami; jalankan setelah `cek_m2.py` dimasukkan ke repositori)
