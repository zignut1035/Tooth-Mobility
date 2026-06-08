# 🦷 Deep Learning-Based ROI Hybrid Classification of Periodontal Disease

> A decision-support application that classifies tooth mobility and periodontal bone loss from dental radiographs using a Hybrid ROI-Guided Attention Model.

![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat-square&logo=python)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras-orange?style=flat-square&logo=tensorflow)
![DenseNet121](https://img.shields.io/badge/Transfer%20Learning-DenseNet121-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Research%20Prototype-yellow?style=flat-square)

---

## Overview

Periodontal disease is a leading cause of tooth loss, yet diagnosing the extent of alveolar bone loss from radiographs can be highly subjective. This project provides a **reliable secondary analysis system** to assist dental professionals in identifying bone loss caused by periodontal disease — offering clinical evidence to support treatment decisions, particularly when evaluating whether a tooth requires extraction or alternative care.

The system performs **binary classification** of periapical X-rays into:
- ✅ Healthy teeth (no significant bone loss)
- ⚠️ Teeth with periodontal disease indicators

> Developed as a Bachelor's Thesis in Data Engineering & AI at **Turku University of Applied Sciences** by Treenut Yusufee.
<img width="886" height="425" alt="image" src="https://github.com/user-attachments/assets/60651c41-c3ab-41f1-87c6-e0f0e14c577d" />

---

## Dataset

| Property | Details |
|---|---|
| Total images | 290 periapical X-rays (Maxillary & Mandibular anterior-posterior views) |
| Source | Real clinical examinations |
| Train / Val / Test split | 77% / 13% / 10% |
| Annotations | XML bounding boxes defining the Region of Interest (ROI) per tooth |

---

## Technical Architecture

### The Challenge
Dental X-rays contain complex background structures that introduce noise, making it difficult for standard CNNs to focus on the clinically relevant tooth region.
<img width="735" height="445" alt="image" src="https://github.com/user-attachments/assets/a8d07c68-094a-457e-b30b-d0c6745f5045" />

### Solution: Hybrid ROI-Guided Attention Model
Rather than relying on standard CNN feature extraction or hard-cropping alone, the production model uses a **dual-input pipeline** built on a pretrained **DenseNet121** backbone:

```
Full X-ray ──────────► [Learned Attention Branch]  ──┐
                                                       ├─► Fused Features ──► Classification
ROI-Masked Image ────► [Guided Attention Branch]   ──┘
                              (5 : 5 weight fusion)
```

| Branch | Weight | Description |
|---|---|---|
| **Learned Attention** | 50% | Processes the full image; the model discovers discriminative features autonomously |
| **Guided Attention** | 50% | Processes a masked image where everything outside the annotated ROI is blacked out |

The 5:5 fusion combines **human-guided focal points** with **machine-learned contextual features** for robust clinical inference.

### Preprocessing Pipeline
<img width="886" height="338" alt="image" src="https://github.com/user-attachments/assets/66d170bd-c523-4f42-aa92-65959115399a" />

1. **Image Rotation** — anatomical orientation consistency
2. **CLAHE** (Contrast Limited Adaptive Histogram Equalization) — enhanced contrast for subtle bone loss features
3. **Data Augmentation** — rescaling, rotation, shifting, flipping, zooming via TensorFlow/Keras

---

## Tech Stack

| Tool | Role |
|---|---|
| Python | Core language |
| TensorFlow / Keras | Model training & augmentation |
| DenseNet121 | Transfer learning backbone |
| Scikit-learn | Evaluation metrics & cross-validation |
| Google Colaboratory | Training environment |

---

## Results
<img width="757" height="317" alt="image" src="https://github.com/user-attachments/assets/0e343ba9-8406-4f08-81c6-a01c69c1c25c" />

<img width="771" height="474" alt="image" src="https://github.com/user-attachments/assets/ef3fde0b-2009-4f43-9885-50f62087bb69" />
<img width="886" height="278" alt="image" src="https://github.com/user-attachments/assets/d2b09409-f1ba-46dd-8a73-d1f0d5190329" />

All models were evaluated with **5-fold cross-validation**.

| Model | Accuracy | Stability | Notes |
|---|---|---|---|
| Simple Neural Network | ~60% | ❌ Highly unstable | Baseline only |
| DenseNet121 (Full Image) | ~78% ± 3.5% | ✅ Stable | No ROI guidance |
| **Hybrid ROI-Guided Attention** | **~82% ± 5%** | ✅ Stable | ⭐ **Production model** |
| ROI Cropped Image | ~86% ± 3% | ✅ Stable | Highest raw accuracy |

### Why the Hybrid Model over the Cropped Model?

While strict ROI cropping achieves the highest raw accuracy (86%) by eliminating background noise entirely, it **discards broader anatomical context** that may carry clinically relevant information.

The Hybrid Model (82%) is preferred for clinical deployment because it:
- Preserves full X-ray context alongside targeted ROI focus
- Combines human-guided annotations with autonomous machine learning
- Offers a more interpretable and generalizable diagnostic signal

---

## Future Work

- **Continuous Bone Loss Quantification** — Implement a regression stage to output the exact percentage of alveolar bone loss, rather than binary classification.
- **Autonomous ROI Detection** — Replace manual XML bounding box annotations with a YOLO-based object detection model to create a fully automated, end-to-end diagnostic pipeline.

---

## License

This project is for academic and research purposes. See `LICENSE` for details.
