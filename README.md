# Electrical Wiring Fault Detection using YOLOv8

This repository contains four versions of a YOLOv8-based model for detecting electrical wiring faults. The project aims to identify various fault types such as burnt wires, short circuits, overloaded components, and disconnected wires to facilitate predictive maintenance.

## Project Overview

The dataset consists of images and YOLO-formatted labels for electrical wiring components. The models are trained to classify and localize 5 distinct categories:
- **Normal**
- **Burnt**
- **Short Circuit**
- **Overloaded**
- **Disconnected**

## Dataset Information

The dataset used in this project is sourced from Kaggle: [Predictive Maintenance for Electrical Wiring Faults](https://www.kaggle.com/datasets/abulkashemjunaid/electrical-wiring-faults). 
- **Usability Score:** 6.88
- **Format:** YOLO v8
- **Content:** Images of electrical wiring components with corresponding bounding box annotations in text files.

### Dataset Structure

The original dataset is structured into images and labels folders, which are further divided into subfolders representing different data splits:

```text
Predictive Maintenance for Electrical Wiring Faults/
├── images/
│   ├── train01/    # Training images
│   ├── val01/      # Validation images
│   └── test01/     # Test images
└── labels/
    ├── train01/    # YOLO format labels for training
    ├── val01/      # YOLO format labels for validation
    └── test01/     # YOLO format labels for testing
```

During the execution of the notebooks, this structure is reorganized into a standard YOLOv8 directory format within the working directory for compatibility with the Ultralytics training pipeline.

## Implementation Comparison

Four implementations were developed to optimize performance, resource utilization, visualization, and data balance. Below is a comparison of the key differences and their impact on the results.

### Version 1: Initial Implementation
**File:** `electrofault-detector-using-yolov8.ipynb`

- **Input Resolution:** Trained at 896px.
- **Batch Size:** 16.
- **Validation Resolution:** Evaluated at 896px.
- **Test Resolution:** Evaluated at 640px (Inconsistent with training).
- **Class Mapping:** Manual mapping with generic labels (e.g., "Fault_Type_1").

### Version 2: Optimized Implementation
**File:** `electrofault-detector-using-yolov8-v2.ipynb`

- **Input Resolution:** Standardized at 640px for both training and evaluation.
- **Batch Size:** 8 (Adjusted to avoid Out-Of-Memory errors on Kaggle GPU).
- **Reliability:** Uses explicit path checking and robust globbing for image extensions (.jpg and .JPG).
- **Class Mapping:** Improved class naming based on confirmed fault categories.
- **Performance:** Optimized for VRAM constraints while maintaining high detection accuracy.

### Version 3: Final Implementation with Visualizations
**File:** `electrofault-detector-using-yolov8-v3.ipynb`

- **Input Resolution:** Standardized at 640px.
- **Batch Size:** 8.
- **Visualizations:** Extensive EDA including class distribution bar charts, and visualization of sample images with ground-truth bounding boxes.
- **Evaluation:** Evaluated on validation and test sets and plots training learning curves on a single 2x3 grid. Plots predictions for test images directly in the notebook.
- **Summary:** Outputs a heavily formatted final summary table summarizing dataset structure, parameters, and final metrics for the model.

### Version 4: Advanced Tuning & Augmentation
**File:** `electrofault-detector-using-yolov8-v4.ipynb`

- **Data Augmentation:** Implements offline data augmentation (horizontal flipping) to explicitly oversample minority classes (e.g., "Disconnected" fault types) before training. Integrates `albumentations` package.
- **Hyperparameter Tuning:** Increased training duration to 150 epochs (patience 40) using Cosine LR annealing (`cos_lr=True`). Added Label Smoothing (`0.1`) to prevent overconfidence and adjusted class loss multiplier (`cls=1.5`) and `copy_paste=0.5` to further heavily penalize misses and synthetically boost rare classes.
- **Confidence Sweep:** Features an extensive post-training confidence threshold sweep to identify the exact threshold parameter yielding the highest theoretical F1-Score.
- **Visualizations:** Includes new plots mapping Precision, Recall, and F1 Score against validation confidence thresholds.

## Comparative Analysis

| Feature | Version 1 | Version 2 | Version 3 | Version 4 |
| :--- | :--- | :--- | :--- | :--- |
| **Model** | YOLOv8l | YOLOv8l | YOLOv8l | YOLOv8l |
| **Image Size (Train)** | 896 | 640 | 640 | 640 |
| **Batch Size** | 16 | 8 | 8 | 8 |
| **Epochs** | 100 | 100 | 100 | 150 |
| **Visualizations** | None | None | Comprehensive | Advanced (w/ Conf Sweep) |
| **Minority Class Handling**| None | None | None | Offline Augmentation & `copy_paste` |

### Key Improvements in Version 4

1. **Strategic Minority Class Oversampling:** Fixes previously unaddressed data imbalance by writing an offline script that horizontally flips images for fault classes containing less than 40 examples.
2. **Confidence Threshold Optimization:** Programmatically calculates and plots the threshold yielding the best F1 Score, ensuring the deployed model strikes the truest balance between Precision and Recall.
3. **Hyperparameter Hardening:** Adjusts label smoothing, learning rate schedules, and mosaic/copy-paste operations pushing localization accuracy higher.

## Version 4 — Final Run (v4) Summary

This section summarizes the final training and evaluation run from `electrofault-detector-using-yolov8-v4.ipynb` (model name: `wiring_fault_yolov8l_v3`). Key configuration and results from the run are listed below.

- **Model:** YOLOv8l (`wiring_fault_yolov8l_v3`)
- **Checkpoint (best weights):** `/kaggle/working/wiring_fault_yolov8l_v3/weights/best.pt`
- **Classes (nc = 5):** Normal, Burnt, Short_Circuit, Overloaded, Disconnected
- **Image size:** 640x640
- **Batch size:** 8
- **Max epochs:** 150 (patience=40)
- **label_smoothing:** 0.1
- **cls loss weight:** 1.5
- **copy_paste:** 0.5
- **cos_lr:** True
- **Best confidence threshold (best F1):** 0.40

### Validation (conf=0.25)

- mAP@0.5: **0.7026**
- mAP@0.5:0.95: **0.4072**
- Precision: **0.8906**
- Recall: **0.6890**

### Test (conf=0.25)

- mAP@0.5: **0.7009**
- mAP@0.5:0.95: **0.3873**
- Precision: **0.8700**
- Recall: **0.6691**

### Best F1 @ conf=0.40

- Precision: **0.8756**
- Recall: **0.6691**
- F1 Score: **0.7585**
- mAP@0.5 (at conf=0.40): **0.6610**

**Notes:**

- Results and artifacts saved to: `/kaggle/working/wiring_fault_yolov8l_v3`
- Sample predictions saved to: `/kaggle/working/predictions_v3`
- Reproduce the run using `electrofault-detector-using-yolov8-v4.ipynb` (follow the notebook cells in order).

## Usage

To run the detector:
1. Ensure `ultralytics` is installed.
2. Configure the paths in the notebook to point to your dataset location.
3. Run the "Build YOLO Dataset Structure" cell to organize files.
4. Execute the training cell.

## Results & Performance Metrics

Below is the quantitative comparison of the models trained across the versions. (*Note: Version 4 metrics are heavily dependent on threshold optimization and data randomization, exact output variables vary per run.*)

### Validation Set Performance

| Metric | Version 1 (YOLO8l) | Version 2 (YOLOv8l) | Version 3 (YOLOv8l) | Version 4 (YOLOv8l) |
| :--- | :--- | :--- | :--- | :--- |
| **mAP@0.5** | 0.4560 | 0.6992 | 0.6992 | 0.7026 |
| **mAP@0.5:0.95** | 0.2364 | 0.4225 | 0.4225 | 0.4072 |
| **Precision** | 0.6642 | 0.8369 | 0.8369 | 0.8906 |
| **Recall** | 0.4421 | 0.6502 | 0.6502 | 0.6890 |

### Test Set Performance

| Metric | Version 1 (YOLO8l) | Version 2 (YOLOv8l) | Version 3 (YOLOv8l) | Version 4 (YOLOv8l) |
| :--- | :--- | :--- | :--- | :--- |
| **mAP@0.5** | 0.4651 | 0.6596 | 0.6596 | 0.7009 |
| **mAP@0.5:0.95** | 0.2436 | 0.3893 | 0.3893 | 0.3873 |
| **Precision** | 0.7169 | 0.8628 | 0.8628 | 0.8700 |
| **Recall** | 0.4469 | 0.6108 | 0.6108 | 0.6691 |

### Best Accuracy Conclusion

**Versions 2 and 3** (both using the YOLOv8l architecture with standard 640x640 resolution) significantly outperformed Version 1. 

On the test set, Version 2/3 achieved a **mAP@0.5 of 0.6596** (a nearly +0.20 improvement over Version 1) and a **Precision of 0.8628** (a +0.14 improvement), meaning it is significantly more reliable at precisely localizing and identifying specific electrical fault types. 

**Version 4** introduces sweeping minority fault class balancing using offline augments alongside label smoothing and threshold sweeps to chase theoretical perfection across datasets prone to class imbalance. It is positioned as the most robust experiment iteration for adapting YOLO configurations toward high `Recall` objectives.
