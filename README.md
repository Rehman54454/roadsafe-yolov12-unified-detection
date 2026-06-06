# RoadSafe-YOLOv12: Unified Real-Time Road Safety Detection

A unified **YOLOv12** framework for the **simultaneous real-time detection of potholes, pedestrians, and vehicles** in a single inference pass. This repository contains the training notebook, configuration, trained weights, full experimental results, and qualitative test images for the baseline model described in our paper.

> **Paper:** *A Unified YOLOv12 Framework for Real-Time Multi-Hazard Road Safety Detection: Potholes, Pedestrians, and Vehicles* (under review).

---

## Overview

Most road safety detection systems handle only one hazard type at a time, requiring separate models for potholes, pedestrians, and vehicles. This work trains a single YOLOv12s model to detect all three at once. Because no public dataset combines these classes, we built one by merging and relabeling three single-class sources into a unified 6,000-image dataset.

This repository establishes the **first YOLOv12 baseline** for unified road safety detection and openly reports both its strengths and its failure modes.

---

## Key Results

| Metric | Value |
|---|---|
| mAP@0.5 | **0.778** |
| mAP@0.5:0.95 | 0.529 |
| Precision | 0.823 |
| Recall | 0.702 |
| Inference speed | 10.2 ms/image (~98 FPS, Tesla T4) |

**Per-class AP@0.5:** Pedestrian 0.872 · Pothole 0.810 · Vehicle 0.652

Vehicle detection is weaker due to high intra-class scale diversity (cars, trucks, buses, motorcycles, bicycles under one label), and small/distant potholes are frequently missed — limitations that motivate ongoing architectural work (P2 detection head, AFPN, EMA attention, WIoU loss).

---

## Repository Structure

```
roadsafe-yolov12-unified-detection/
├── RoadSafe_YOLO12_Training.ipynb   # Training notebook (Google Colab)
├── exp1_baseline_v2/                # Full training run
│   ├── weights/                     # best.pt and last.pt
│   ├── results.csv / results.png    # Training metrics and curves
│   ├── confusion_matrix_normalized.png
│   ├── PR_curve.png / F1_curve.png / P_curve.png / R_curve.png
│   ├── train_batch*.jpg             # Training batch samples
│   └── val_batch*_labels/pred.jpg   # Validation predictions
└── Testing - Images/                # Qualitative detection results
    ├── qual_1.jpg / qual_2.jpg / qual_3.jpg   # Correct multi-class detections
    └── failure_case.jpg             # Missed-pothole failure example
```

---

## Dataset

The combined road safety dataset (6,000 images, 2,000 per class, 80/20 train/val split, 640×640) is released as a versioned asset in this repository.

➡️ **Release page:** https://github.com/Rehman54454/roadsafe-yolov12-unified-detection/releases/tag/v1.0

➡️ **Direct download:** https://github.com/Rehman54454/roadsafe-yolov12-unified-detection/releases/download/v1.0/Combined_Dataset.zip

To get the dataset, open the **Releases** section (right sidebar of the repo) → **Dataset v1.0** → download `Combined_Dataset.zip` (408 MB).

| Class | Label ID | Images |
|---|---|---|
| Pothole | 0 | 2,000 |
| Pedestrian | 1 | 2,000 |
| Vehicle | 2 | 2,000 |

> Note: each source image originally contained a single class. The merged set is a multi-source, single-class collection unified under one label scheme (pothole = 0, pedestrian = 1, vehicle = 2).

---

## Model

| Property | Value |
|---|---|
| Base model | YOLOv12s |
| Parameters | 9.07 M |
| GFLOPs | 19.3 |
| Detection scales | P3, P4, P5 |
| Pretrained weights | MS-COCO (yolov12s.pt) |

**Training:** 150 epochs · AdamW · lr 0.001 · batch 16 · image size 640 · early-stopping patience 20.

---

## Usage

### Environment

This model was trained with the original YOLOv12 implementation (uses `qk` attention). Install it from the official repo:

```bash
pip install git+https://github.com/sunsmarterjie/yolov12
pip install huggingface_hub opencv-python
```

### Inference

```python
from ultralytics import YOLO

model = YOLO('exp1_baseline_v2/weights/best.pt')
results = model.predict(source='path/to/images', conf=0.25, save=True)
```

Class colors in the provided inference script: **pothole = red**, **pedestrian = green**, **vehicle = blue**.

---

## Citation

If you use this work, please cite:

```bibtex
@article{zafar2026roadsafe,
  title   = {A Unified YOLOv12 Framework for Real-Time Multi-Hazard Road Safety Detection: Potholes, Pedestrians, and Vehicles},
  author  = {Zafar, Muhammad Abdul Rehman},
  year    = {2026}
}
```

---

## License

Released under the MIT License. The underlying YOLOv12 code is AGPL-3.0 (sunsmarterjie/yolov12).

---

## Contact

**Muhammad Abdul Rehman Zafar** — abdul.ai.developerr@gmail.com
