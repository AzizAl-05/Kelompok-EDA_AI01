# Cleaning Log

Dataset: covid_19_indonesia_time_series_all.csv  
Baris mentah: 31822 -> baris bersih (provinsi): 30893; baris nasional dipisahkan: 929  

| # | Langkah | Baris terdampak | Alasan |
|---|---|---|---|
| 1 | Seragamkan nama kolom ke snake_case | 38 | Nama berspasi, berkurung, dan berkapital (mis. "Area (km2)") menyulitkan kode; yang dihitung di sini KOLOM, bukan baris |
| 2 | Konversi case_fatality_rate ke angka | 31822 | Buang "%" agar kolom dapat dihitung; satuan tetap persen (0-100), bukan pecahan |
| 3 | Konversi case_recovered_rate ke angka | 31822 | Buang "%" agar kolom dapat dihitung; satuan tetap persen (0-100), bukan pecahan |
| 4 | Konversi date dengan format %m/%d/%Y | 31822 | dayfirst=True akan menukar bulan dan hari (3/1/2020 menjadi 3 Januari) tanpa memunculkan galat |
| 5 | Buang spasi awal dan akhir kolom teks | 0 | Spasi tersembunyi memecah groupby dan melambungkan nunique |
| 6 | Placeholder teks menjadi kosong | 0 | N/A, -, null, dan teks kosong bukan nilai sungguhan |
| 7 | Kode sentinel -999/-9999 pada kolom hitungan menjadi kosong | 0 | Kode semacam ini merusak rata-rata tanpa pernah terdeteksi isna(); diperiksa dan tidak ditemukan |
| 8 | Seragamkan kategori | 0 | Tidak ada sinonim/salah ketik pada nama lokasi; peta kosong adalah temuan, bukan kelalaian. Huruf tidak diubah otomatis |
| 9 | Hapus duplikat persis | 0 | Seluruh kolom identik; satu baris disimpan (hasil pemeriksaan: tidak ada) |
| 10 | Satukan duplikat menurut (date, location) | 0 | Satu lokasi hanya boleh satu baris per hari; dipilih baris dengan total_cases terbesar (hasil pemeriksaan: tidak ada) |
| 11 | Buang baris tanpa date atau location | 0 | Kunci utama kosong tidak dapat ditelusuri kembali ke sumber (hasil pemeriksaan: tidak ada) |
| 12 | Periksa hari yang hilang di tengah deret tiap lokasi | 0 | Tidak ada hari bolong. Lokasi baru muncul saat kasus pertamanya, itu BUKAN data hilang melainkan belum ada, jadi tidak diisi nol |
| 13 | Pisahkan baris tingkat nasional ke tabel sendiri | 929 | Bukan duplikat (kunci (date, location) tetap unik), tetapi tingkat agregasi berbeda: baris Country mendekati jumlah provinsi, sehingga bila dijumlahkan bersama baris provinsi setiap kasus terhitung dua kali. Disimpan terpisah, tidak dihapus |
| 14 | case_recovered_rate > 100% menjadi kosong | 288 | Rasio mustahil; berasal dari total sembuh/meninggal yang melebihi total kasus. Barisnya masih berguna untuk kolom lain |
| 15 | case_fatality_rate > 100% menjadi kosong | 43 | Rasio mustahil; berasal dari total sembuh/meninggal yang melebihi total kasus. Barisnya masih berguna untuk kolom lain |
| 16 | Hitungan harian/kumulatif negatif menjadi kosong | 0 | Jumlah orang tidak bisa negatif; hasil pemeriksaan: tidak ada. Selisih negatif pada new_active_cases sengaja tidak disentuh |
| 17 | Periksa populasi dan luas wilayah <= 0 | 0 | Penyebut untuk kolom per juta penduduk; hasil pemeriksaan: tidak ada |
| 18 | Uji identitas total_active = cases - deaths - recovered | 0 | Identitas selalu terpenuhi, jadi aktif negatif adalah AKIBAT sembuh > kasus, bukan kesalahan hitung terpisah |
| 19 | Tandai baris kumulatif janggal | 479 | Sembuh/meninggal melebihi total kasus atau aktif negatif; ditandai, tidak ditimpa, karena belum diketahui kolom mana yang keliru |
| 20 | Uji konsistensi kolom kumulatif dan harian | 0 | Total tidak pernah turun dan selisihnya sama dengan new_*; hasil pemeriksaan: konsisten. Anomali ada di rasio sembuh/kasus, bukan di hitungan harian |
| 21 | Buang kolom kosong, konstan, atau redundan | 5 | 5 kolom tidak membawa informasi (city_or_regency, province, country, continent, location_level); yang dihitung KOLOM |
| 22 | growth_factor_of_new_cases dibiarkan kosong | 1935 | Kosong karena pembagi nol (kemarin 0 kasus) atau hari pertama; tidak ada nilai yang benar untuk diisi |
| 23 | growth_factor_of_new_deaths dibiarkan kosong | 3442 | Kosong karena pembagi nol (kemarin 0 kasus) atau hari pertama; tidak ada nilai yang benar untuk diisi |
| 24 | special_status kosong menjadi kategori "Tidak ada status khusus" | 26335 | Kosong bukan hilang: provinsi itu memang tidak punya status khusus; juga dibuat penanda biner |
| 25 | total_cities dibiarkan kosong | 901 | Kosong hanya pada satu provinsi (Sulawesi Barat) dan konstan sepanjang waktu: struktural, bukan acak. Tidak diisi 0 karena penyebabnya belum dipastikan |
| 26 | total_urban_villages dibiarkan kosong | 904 | Kosong hanya pada satu provinsi (Aceh) dan konstan sepanjang waktu: struktural, bukan acak. Tidak diisi 0 karena penyebabnya belum dipastikan |
| 27 | total_rural_villages dibiarkan kosong | 929 | Kosong hanya pada satu provinsi (DKI Jakarta) dan konstan sepanjang waktu: struktural, bukan acak. Tidak diisi 0 karena penyebabnya belum dipastikan |
| 28 | Urutkan menurut lokasi dan tanggal, buat bulan/tahun/hari/kasus_rata7 | 30893 | Rata-rata 7 hari meredam pola pelaporan mingguan; enam hari pertama tiap lokasi sengaja dibiarkan kosong, bukan diisi |
| 29 | Binning new_cases_per_million menjadi kelas_kasus | 30893 | Batas 1, 10, 30 per juta adalah keputusan, bukan perhitungan; dipilih agar tiap kelas cukup terisi |