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
        │
        ▼
Future Transliteration / Translation
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

The complete epoch-by-epoch training results are available in trained_model/full_results.csvand the corresponding plots are available in trained_model/results.png

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
│   ├── args.yaml 
│   ├── best.pt               # best epoch weights
│   ├── full_results.csv 
│   └── results.png
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

The collected resources include:

Brahmi manuscript images
Individual Brahmi glyph images
Corresponding glyph data used for preparing the object-detection dataset

The individual glyph images are intended to support the next stage of the project: CNN-based character identification/classification after glyph detection.

Please refer to the original source for the dataset and associated materials.

### Dataset Annotation



The manuscript images and individual Brahmi glyph resources used for this project were obtained from:
Indoskript — http://indoskript.org

The training dataset was manually annotated using CVAT (Computer Vision Annotation Tool). Each glyph was annotated with a bounding box corresponding to its location within the manuscript image.

The resulting images and YOLO-format bounding-box annotations (.txt files) are included in the BrahmiGlyphData/ folder in this repository.

The dataset can also be accessed through Kaggle:
Kaggle Dataset: Kaggle link — coming soon

 
| Split | Images |
|---|---|
| Training | 32 |
| Validation | 3 |
| **Total** | **35** |

### Running the Notebook

Open [`yolo2.ipynb`](yolo2.ipynb) — it walks through the full workflow in order:

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