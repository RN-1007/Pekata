# Pekata (Peka Kata) — SafePath AI On-Device Sentiment Engine

[![Model Architecture](https://img.shields.io/badge/Architecture-IndoBERT--Base-blue.svg)](https://huggingface.co/indobenchmark/indobert-base-p1)
[![Model Format](https://img.shields.io/badge/Format-ONNX%20INT8-green.svg)](https://github.com/microsoft/onnxruntime)
[![Target Runtime](https://img.shields.io/badge/Target-Android%20Mobile%20(ARM64)-orange.svg)]()
[![Model Size](https://img.shields.io/badge/Size-115%20MB%20(Target%20%3C180MB)-brightgreen.svg)]()
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)]()

**Pekata** (*Peka Kata*) adalah mesin inferensi sentimen *on-device* berbasis **IndoBERT-Base** yang dirancang khusus untuk berjalan secara lokal di dalam aplikasi keyboard Android **SafePath AI**. 

Model ini bertindak sebagai penjaga (*safeguard*) afektif yang mendeteksi indikasi stres, luapan emosi negatif, dan keletihan mental secara *real-time* saat pengguna mengetik, dengan jaminan **100% privasi data** (tanpa pengiriman teks ke server eksternal).

---

## 🚀 Ringkasan Kinerja & Spesifikasi Teknis

| Parameter / Metrik | Spesifikasi / Hasil |
| :--- | :--- |
| **Model Dasar** | `indobenchmark/indobert-base-p1` (124M Parameters) |
| **Dataset Fine-Tuning** | IndoNLU SmSA (Indonesian Sentiment Analysis) |
| **Format Model** | ONNX Runtime Mobile (`.onnx`) |
| **Metode Kuantisasi** | Dynamic Range INT8 (`ort.quantize_dynamic`) |
| **Ukuran File Model** | **~115.6 MB** (Terkompresi dari ~470 MB FP32) |
| **Batas Memori PRD** | Lolos batas ketat `< 180 MB` |
| **Akurasi Test Set** | **90.4%** (Weighted F1: **0.897**) |
| **Deteksi Negatif/Stres** | **> 99.5% confidence** pada teks emosional pendek |
| **Estimasi Latensi** | 15 – 30 ms per inferensi (CPU ARM64-v8a) |
| **Target Runtime** | `com.microsoft.onnxruntime:onnxruntime-android` |

---

## 🧠 Pipeline Optimasi Edge AI

1. **Fine-Tuning:** IndoBERT-Base dilatih dengan PyTorch untuk klasifikasi 3-kelas (*positive*, *neutral*, *negative*).
2. **Static Tracing:** Model dibungkus (*wrapper*) untuk mengekstraksi tensor logits murni dan diekspor menggunakan Opset 14 dengan *fixed sequence length* (`max_length=64`).
3. **Bypass TFLite Flex Ops Collapse:** Menghindari fragmentasi graf dan degradasi akurasi numerik akibat operator non-primitif (`tf.Erf` / FlexDelegate) dengan beralih langsung ke ekosistem **ONNX Runtime Mobile**.
4. **Dynamic INT8 Quantization:** Bobot perkalian matriks dikompresi ke 8-bit integer menggunakan tool resmi `onnxruntime.quantization`, menjaga kestabilan *attention matrix* sekaligus menghemat ~75% ukuran file.

---

## 📦 Struktur Repositori

```text
├── .gitignore
├── README.md
├── requirements.txt
├── notebooks/
│   └── safepath_ai_indobert_base_compact.ipynb
└── tokenizer/
    ├── vocab.txt                 # Kosakata WordPiece resmi untuk Android
    ├── tokenizer_config.json
    └── tokenizer.json