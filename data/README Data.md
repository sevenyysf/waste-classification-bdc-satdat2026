# Data

Dataset berasal dari **BDC Satria Data 2026** dan **tidak disertakan** dalam
repositori ini. Dataset didapat dari panitia melalui platform lomba.

## Struktur yang diharapkan

Simpan di Google Drive dengan struktur berikut:

```
MyDrive/<nama-folder>/          # sesuaikan BASE_DIR di notebook
├── train/
│   ├── 0_Recyclable/
│   ├── 1_Electronic/
│   └── 2_Organic/
├── test/
├── submission.csv              # template dari panitia (kolom: id, predicted)
└── saved_models/               # dibuat otomatis oleh notebook
```

## Ringkasan

- Data latih: 26.527 citra, dalam 3 kelas
- Data uji: 1.458 citra (id 1 sampai 1458)
- Kode label (wajib sesuai ketentuan panitia): `0 = Recyclable`, `1 = Electronic`, `2 = Organic`

| Kelas | Jumlah | Persentase |
|-------|--------|------------|
| Recyclable | 9.999 | 37,7% |
| Electronic | 3.961 | 14,9% |
| Organic | 12.567 | 47,4% |

Jika lokasi folder berbeda, ubah `BASE_DIR` di notebook.
