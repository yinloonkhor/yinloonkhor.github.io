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

> 🏆 *This project was published in **IEEE EAIS 2024** and won the **Gold Prize** in UTAR’s LKC FES FYP Poster Competition 2024.*

## ⚙️ Methodology
We extended YOLOv8 by adding **three parallel detection heads**, each tailored for one task. All heads share a common backbone (CSPDarknet53), promoting **feature sharing** while preserving task-specific accuracy. The model uses anchor-free detection and optimized loss functions.

- **Multi-task loss weighting**: `0.6 (OCR) : 0.3 (LP) : 0.1 (VCR)`  
- **Synthetic data generation** was used to augment the training set, especially for OCR, by generating five synthetic variations for each real image to improve model robustness.

## 🧪 Dataset
- Custom-labeled dataset of **1,555 images** from residential areas.  
- Each image is annotated for characters (OCR), license plates (LP), and car colours (VCR).  
- Data split: 70% training, 20% validation, 10% test.
- **Synthetic data generation and augmentation** expanded the dataset by creating 5× more samples for LP and OCR tasks using synthetic variations, and an additional 3× increase through data augmentation techniques.

## 📊 Results

| Model Configuration                                              | mAP50 (OCR) | mAP50 (LP) | mAP50 (VCR) | Average | Epochs   |
|-----------------------------------------------------------------|-------------|------------|-------------|---------|----------|
| **1 Head, 1 Task**                                               |             |            |             |         |          |
| A: OCR (Pretrained)                                              | 0.819       | -          | -           | 0.828   | 20+80    |
| B: LP                                                            | -           | 0.956      | -           |         | 80       |
| C: VCR                                                           | -           | -          | 0.708       |         | 80       |
| **1 Head, 3 Tasks (Pretrained)**                                 |             |            |             |         |          |
| D                                                                | 0.791       | 0.874      | 0.891       | 0.852   | 80+80    |
| **3 Head, 3 Tasks (Pretrained)**                                 |             |            |             |         |          |
| E                                                                | 0.735       | 0.965      | 0.862       | 0.854   | 20+40    |
| E1: E with disabled mosaic                                       | 0.755       | 0.966      | 0.817       | 0.846   | 20+40    |
| E2: E with frozen VCR head, 5:4:1 loss weightage and mosaic off | 0.755       | 0.958      | 0.849       | 0.854   | 20+40    |
| **E3: E with frozen VCR head, 6:3:1 loss weightage and mosaic off** | **0.778**   | **0.963**  | **0.881**   | **0.874** | **20+40** |
| E4: E with frozen VCR head, 7:2:1 loss weightage and mosaic off | 0.764       | 0.953      | 0.870       | 0.862   | 20+40    |

Ablation study of YOLOv8 variants. Model E3 achieved the best overall performance with an average mAP of 0.874.
{:.figcaption}

- **Model E3** (multi-head with tuned loss weights) achieved the highest average mAP, comparable to single-head, single-task and multi-task models.
- **36 FPS on GPU**, 9.5 FPS on CPU — enabling near real-time performance.
- **~3× faster** than sequential models, with fewer parameters than three separate models.
- **Multithreaded** deployment of Model E3 improved inference speed by 45.7% on GPU and 69.1% on CPU for single-stream video processing.

## 💡 Key Contributions
- First unified, single-stage YOLOv8 model combining OCR, LP detection, and VCR.  
- Demonstrated competitive accuracy and **significant speed-up** over sequential models.  
- Introduced a **multi-labeled dataset** and **loss balancing strategy** for multi-task training.  
- Model is deployable in **law enforcement, surveillance, and smart city** scenarios.
