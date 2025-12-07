# Automated Microscopic Cell Quantification 🔬

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-1.10%2B-orange)
![LIVECell](https://img.shields.io/badge/Dataset-LIVECell-green)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

> **A comparative analysis of Instance Segmentation (Mask R-CNN) and Density Estimation (CSRNet) architectures for high-throughput biomedical image analysis.**

---

## 📋 Project Overview
Quantifying cellular proliferation is a bottleneck in pharmaceutical research and quality control. Traditional manual counting is slow and subjective. This project establishes an automated computer vision pipeline using the **LIVECell dataset** (1.6M+ annotations) to solve the challenge of counting transparent, overlapping cells in phase-contrast microscopy.

We investigated two core hypotheses:
1.  **Resolution vs. Context:** Does splitting high-res images into tiles improve detection of small cells?
2.  **Detection vs. Regression:** Can Density Estimation (CSRNet) handle crowds better than Instance Segmentation (Mask R-CNN)?

### 🏆 Key Results
| Model Architecture | Input Strategy | Accuracy | MAE | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Mask R-CNN** | Naive Resize (512x512) | 59.6% | 104.3 | Baseline |
| **Mask R-CNN** | **Tiled Grid (2x2)** | **62.2%** | **78.1** | **Best Performer** |
| **CSRNet** | Density Regression | N/A | High | Experimental |

**Key Finding:** Preserving local resolution via **Tiled Processing** is more critical than global context for small object detection, yielding a **2.6% accuracy gain** over the baseline.

---

## 🧠 Model Architectures

### 1. Instance Segmentation (Mask R-CNN)
We utilized a **ResNet-50 + FPN** backbone initialized on COCO weights.
- **Why:** Provides both *Count* and *Morphology* (Size/Shape).
- **Innovation:** Implemented a **"Smart Crop"** data loader that filters out empty background tiles during training to stabilize loss convergence.

### 2. Density Estimation (CSRNet)
We implemented a dilated convolutional network to regress a pixel-wise density map.
- **Why:** To address the "Crowd Counting" problem where overlapping cells are suppressed by NMS.
- **Outcome:** While the model successfully learned texture features (Loss 125 -> 90), it requires significantly higher compute resources for calibration.

---

## 📊 Performance Analysis

### Loss Convergence (Naive vs. Tiled)
The Tiled model (Green) converged to a lower final loss than the Naive model (Red), proving that higher-resolution inputs allow the Feature Pyramid Network (FPN) to extract sharper boundaries.

### Accuracy by Cell Type
The Tiled approach dominated in cell lines with small, clustered morphologies (e.g., *SH-SY5Y*), resolving individual instances that were blurred in the Naive approach.

| Cell Type | Naive Acc | Tiled Acc | Winner |
| :--- | :--- | :--- | :--- |
| **SH-SY5Y** (Neuroblastoma) | 48.2% | **53.5%** | 🟢 Tiled (+5.3%) |
| **A172** (Glioblastoma) | 61.0% | **62.8%** | 🟢 Tiled (+1.8%) |


👥 Contributors
• Yatharth Vardan - Lead Developer, Model Architecture, Data Pipeline
--- 

📚 References
1. LIVECell Dataset: Edlund et al., Nature Methods (2021).
2. Mask R-CNN: He et al., ICCV (2017).
3. CSRNet: Li et al., CVPR (2018).

