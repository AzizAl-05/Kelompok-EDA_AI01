# data/raw — data mentah (tidak di-commit)

Berkas di folder ini **tidak pernah diubah** dan **tidak ikut ter-commit** (lihat `.gitignore`). Letakkan berkas berikut di sini:

| Butir | Isi |
|---|---|
| Nama berkas | `covid_19_indonesia_time_series_all.csv` |
| Sumber | https://www.kaggle.com/datasets/hendratno/covid19-indonesia (Kaggle, Hendratno) |
| Lisensi | CC BY-NC-SA 4.0 (tertulis di Data Card Kaggle) |
| Ukuran | 31.822 baris × 38 kolom (± 7,5 MB) |
| SHA-256 | `c331a22d401351a3ee021cfbb671df9518c41f775c867aa0482ed0b8e1e7df7e` |

Cara memeriksa keutuhan setelah mengunduh:

```bash
sha256sum data/raw/covid_19_indonesia_time_series_all.csv     # macOS: shasum -a 256 ...
```

Bila hash berbeda, Kaggle mungkin menerbitkan versi baru; laporkan ke tim sebelum melanjutkan, karena angka di laporan didasarkan pada berkas dengan hash di atas.
