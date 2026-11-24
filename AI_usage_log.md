# AI Usage Log

Wajib diisi jujur: alat, tujuan, prompt utama, bagian yang dihasilkan atau diubah. Setiap angka dan klaim harus dapat direproduksi dari kode tim.

| # | Tanggal | Alat | Tujuan | Prompt utama (ringkas) | Bagian yang dihasilkan/diubah | Diverifikasi oleh |
|---|---------|------|--------|------------------------|-------------------------------|-------------------|
| 1 | 2026-11-14 | Claude (Anthropic) | Menyatukan cabang per pertemuan ke `main` sesuai struktur panduan | "Pindahkan semua branch ke main agar sesuai panduan" | Struktur folder, README, `.gitignore`, `requirements.txt`, `data/raw/README.md`, `data_dictionary.md` (kolom tipe/satuan/sumber ditambahkan) | [nama anggota] |
| 2 | 2026-11-14 | Claude (Anthropic) | Meluruskan klasifikasi baris nasional "Indonesia" pada M2 (Keunikan → Konsistensi/Akurasi) setelah evaluasi dosen | "Bantu perbaiki secara mendalam: kolom agregat Indonesia vs 'tidak ada duplikat'" | `notebooks/01_quality`, `02_cleaning` (sel bukti baru), `cleaning_log.md`, `reports/M2_Data_Quality_Report.*` | [nama anggota] |
| 3 | 2026-11-24 | Claude (Anthropic) | Menyusun `notebooks/03_univariate.ipynb` (Pertemuan 6) di atas data olahan tim, diuji terhadap keluaran M1 | "Buatkan notebook 03 berdasarkan notebook tim, sesuaikan agar saling berkesinambungan; saya butuh hasil Python P6 dan P7 untuk KNIME" | `notebooks/03_univariate.ipynb`, `reports/p6_*.csv`, `reports/figures/p6_*.png` | [nama anggota — jalankan ulang dan cocokkan angka] |
| 4 | [isi] | [isi bila ada penggunaan AI lain] | | | | |
