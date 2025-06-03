---
layout: post
description: > 
  Multi-task YOLOv8 model architecture
image: 
  path: https://github.com/user-attachments/assets/5096e151-46e0-4de4-ad1e-93a655e11dbd
---

# Multi-Task YOLO for Vehicle Colour Recognition and Automatic License Plate Recognition
💻 [Source Code](https://github.com/yinloonkhor/YOLOv8-multi-detection-head/tree/main) | 📝 [Paper](https://ieeexplore.ieee.org/document/10570013)

## 🧠 Overview
This project presents a unified deep learning model for **real-time vehicle identification**, combining **Automatic License Plate Recognition (ALPR)** and **Vehicle Colour Recognition (VCR)** into a single framework. Traditional systems follow a two-stage pipeline — first detecting the license plate, then performing OCR. However, these systems are prone to failure when plates are damaged, occluded, or tampered with. Our solution introduces a **multi-task YOLOv8 model** that performs:
- License Plate (LP) Detection  
- Optical Character Recognition (OCR)  
- Vehicle Colour Recognition (VCR)  

## ⚙️ Methodology
We extended YOLOv8 by adding **three parallel detection heads**, each tailored for one task. All heads share a common backbone (CSPDarknet53), promoting **feature sharing** while preserving task-specific accuracy. The model uses anchor-free detection and optimized loss functions.

- **Multi-task loss weighting**: `0.6 (OCR) : 0.3 (LP) : 0.1 (VCR)`  
- **Synthetic data generation** was used to augment training data, particularly for OCR.

## 🧪 Dataset
- Custom-labeled dataset of **1,555 images** from residential areas.  
- Each image is annotated for characters (OCR), license plates (LP), and car colours (VCR).  
- Data split: 70% training, 20% validation, 10% test.  
- **Synthetic augmentation** created 5x more samples for LP and OCR tasks.

## 📊 Results

| Task | mAP (best config) |
|------|-------------------|
| OCR | 0.778 |
| LP Detection | 0.963 |
| VCR | 0.881 |

- **Model E3** (multi-head with tuned loss weights) achieved the highest average mAP.
- **36 FPS on GPU**, 9.5 FPS on CPU — enabling near real-time performance.
- **~3× faster** than sequential models, with fewer parameters than three separate models.

## 💡 Key Contributions
- First unified, single-stage YOLOv8 model combining OCR, LP detection, and VCR.  
- Demonstrated competitive accuracy and **significant speed-up** over sequential models.  
- Introduced a **multi-labeled dataset** and **loss balancing strategy** for multi-task training.  
- Model is deployable in **law enforcement, surveillance, and smart city** scenarios.
