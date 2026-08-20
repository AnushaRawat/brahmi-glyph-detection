# Brahmi Glyph Detection

Detection and extraction of individual Brahmi glyphs from historical manuscript images using YOLOv8.

This repository implements the **detection stage** of a larger Brahmi OCR pipeline. A fine-tuned YOLOv8n model locates and crops individual glyphs from manuscript scans; the extracted images are then passed downstream to a CNN character classifier.

---

## Pipeline Overview

```
Brahmi Manuscript Image
        │
        ▼
YOLOv8 Glyph Detection        ◄─── this repository
        │
        ▼
Individual Glyph Extraction   ◄─── this repository
        │
        ▼
CNN Character Classification
        │
        ▼
Brahmi Character Recognition
```

---

## Results

Evaluated on a validation set of **3 manuscript images** containing **545 annotated glyph instances**.

| Metric | Score |
|---|---|
| Precision | 89.59% |
| Recall | 91.63% |
| mAP@50 | 95.51% |
| mAP@50–95 | 52.10% |

Training curves, PR curves, confusion matrix, and F1 plots are in [`results/`](results/). A CSV summary of all metrics is in [`training_summary.csv`](training_summary.csv).

---

## Repository Structure

```
.
├── BrahmiGlyphData/
│   ├── data.yaml
│   ├── images/
│   │   ├── train/
│   │   └── val/
│   └── labels/
│       ├── train/
│       └── val/
│
├── model/
│   └── yolov8n.pt            # base pretrained weights( not uploaded)
│
├── trained_model/
│   └── best.pt               # fine-tuned weights (best epoch)
│
├── results/
│   ├── results.png
│   ├── confusion_matrix.png
│   ├── PR_curve.png
│   ├── P_curve.png
│   ├── R_curve.png
│   └── F1_curve.png
│
├── training_summary.csv
├── yolo.ipynb
├── requirements.txt
└── README.md
```

---

## Getting Started

### Prerequisites

```bash
pip install -r requirements.txt
```

### Dataset

The dataset uses YOLO-format bounding-box annotations with a single class: `brahmi_glyph`.

Place your data under `BrahmiGlyphData/` following the structure above, or update the paths in `data.yaml` to match your layout.

### Running the Notebook

Open [`yolo.ipynb`](yolo.ipynb) — it walks through the full workflow in order:

1. Environment setup
2. Dataset configuration
3. YOLOv8 training
4. Model validation
5. Qualitative bounding-box inspection
6. Individual glyph extraction

---

## Model

**Architecture:** YOLOv8n, fine-tuned from pretrained weights

| Hyperparameter | Value |
|---|---|
| Image size | 640 × 640 |
| Epochs | 300 |
| Optimizer | AdamW |
| Learning rate | 0.001 |
| Batch size | 8 |
| Data augmentation | enabled |
| Multi-scale training | disabled |

The best checkpoint is saved to `trained_model/best.pt`.

---

## Glyph Extraction

After inference, predicted bounding boxes are used to crop individual glyphs from the source images. Crops are saved with their detection confidence in the filename:

```
extracted_glyphs/
├── manuscript_glyph_000_conf0.91.jpg
├── manuscript_glyph_001_conf0.87.jpg
├── manuscript_glyph_002_conf0.94.jpg
└── ...
```

These images form the input to the downstream CNN classifier.

---

## Future Work

The next stage is training a CNN to classify each extracted glyph into its corresponding Brahmi character. The full pipeline, once complete, will go from raw manuscript scan to character-level transcription.

---

## Acknowledgements

Annotations were created manually from Brahmi manuscript images. The base detector uses [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics).