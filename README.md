# Klasifikasi Citra Limbah: BDC Satria Data 2026

Klasifikasi citra limbah ke dalam 3 kategori untuk mendukung sistem pemilahan
sampah otomatis:

| Kode | Kelas |
|------|-------|
| 0 | Recyclable |
| 1 | Electronic |
| 2 | Organic |

**Hasil:** skor **96,7%** pada platform lomba Big Data Competition (BDC) Satria Data 2026.
Metrik penilaian lomba: *macro-averaged F1-score*.

**Tim:** ThreeSrikandi

## Pendekatan

- **Transfer learning** dari 3 backbone pretrained (timm):
  ConvNeXt-Small, EfficientNet-B3, EfficientNetV2-S
- **Stratified 3-fold cross-validation** dengan **SWA** (Stochastic Weight Averaging)
- **Augmentasi** untuk citra objek: flip, rotasi ringan, color jitter, noise/blur,
  coarse dropout (hanya pada data latih)
- **Weighted sampling** untuk ketidakseimbangan kelas, **mixup/cutmix**, label smoothing 0,1
- **Mixed precision (AMP)**, cache citra ke disk lokal Colab, dan resume dari checkpoint
  untuk menghemat waktu GPU
- **Seleksi ensemble** berdasarkan macro F1 pada data validasi: EfficientNet-B3
  dikeluarkan dari ensemble karena menurunkan skor
- **Inference:** ensemble 6 checkpoint (ConvNeXt-S dan EfficientNetV2-S, masing-masing
  3 fold) dengan 3 TTA (original, horizontal flip, brightness ringan)

Pipeline dasar diadaptasi dari kompetisi anti-spoofing sebelumnya, dengan perubahan:
tanpa face-cropping, 3 kelas dengan mapping label sesuai ketentuan panitia,
dan penyesuaian untuk dataset yang lebih besar.

## Hasil Validasi

Macro F1 pada data validasi (1 fold per backbone):

| Model | Macro F1 |
|-------|----------|
| ConvNeXt-S | 0,9815 |
| EfficientNet-B3 | 0,9670 |
| EfficientNetV2-S | 0,9737 |
| Ensemble (3 backbone) | 0,9806 |
| **Ensemble tanpa EfficientNet-B3 (dipakai)** | **0,9818** |

Skor validasi lebih tinggi dari skor platform lomba karena data validasi diambil
dari data latih, sedangkan platform menilai data uji yang tidak pernah dilihat model.

## Konfigurasi Utama

| Parameter | Nilai |
|-----------|-------|
| Ukuran citra | 224 x 224 (cache 288 x 288) |
| Batch size | 32 |
| Epoch | 15 (SWA mulai epoch 10) |
| Learning rate | 1e-4 (SWA: 1e-5) |
| Early stopping patience | 4 |
| Fold | 3 |

## Isi Repositori

```
├── README.md
├── waste_classification_bdc2026.ipynb   # pipeline lengkap beserta output
└── data/
    └── README.md                        # cara menyiapkan dataset
```

Catatan: output notebook berasal dari proses training di Google Colab yang
berjalan lintas beberapa sesi, sehingga ada beberapa pesan error sisa dari sesi
yang terputus (`KeyboardInterrupt`, `NameError`). Hasil akhir tidak terpengaruh.

## Cara Menjalankan

Notebook dibuat untuk **Google Colab dengan GPU** (dijalankan pada T4).

1. Siapkan dataset di Google Drive (lihat `data/README.md`).
2. Buka notebook di Colab, pilih Runtime → Change runtime type → GPU.
3. Sesuaikan `BASE_DIR` dan `TEAM_NAME` di cell konfigurasi.
4. Jalankan semua cell secara berurutan.

Dependensi utama: `torch`, `timm`, `albumentations`, `scikit-learn`, `pandas`,
`numpy`, `Pillow`, `matplotlib`, `seaborn`.
(`timm` dan `albumentations` diinstal otomatis oleh cell pertama.)

## Referensi

- Izmailov et al., *Averaging Weights Leads to Wider Optima and Better Generalization*, UAI 2018
- Tan & Le, *EfficientNet* (ICML 2019) dan *EfficientNetV2* (ICML 2021)
- Liu et al., *A ConvNet for the 2020s* (ConvNeXt), CVPR 2022
