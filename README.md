# Electrical Wiring Fault Detection using YOLOv8

This repository contains three versions of a YOLOv8-based model for detecting electrical wiring faults. The project aims to identify various fault types such as burnt wires, short circuits, overloaded components, and disconnected wires to facilitate predictive maintenance.

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

Three implementations were developed to optimize performance, resource utilization, and visualization. Below is a comparison of the key differences and their impact on the results.

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

## Comparative Analysis

| Feature | Version 1 | Version 2 | Version 3 |
| :--- | :--- | :--- | :--- |
| **Model** | YOLOv8l | YOLOv8l | YOLOv8l |
| **Image Size (Train)** | 896 | 640 | 640 |
| **Image Size (Val)** | 896 | 640 | 640 |
| **Batch Size** | 16 | 8 | 8 |
| **Visualizations** | None | None | Comprehensive EDA and Training/Eval plots |
| **Naming** | Generic | Descriptive | Descriptive |

### Key Improvements in Version 3

1. **Exploratory Data Analysis (EDA):** Added visual distribution of class categories and plotted sample images with their ground-truth bounding boxes.
2. **Metrics Visualization:** Consolidated training metric plots directly in the notebook to easily monitor losses and mAP curves over time.
3. **Inference Viewing:** Evaluates a sample of test images and displays the bounding box predictions directly in the output.
4. **Structured Results:** Displays a comprehensive Final Summary log at the end of the notebook.

## Usage

To run the detector:
1. Ensure `ultralytics` is installed.
2. Configure the paths in the notebook to point to your dataset location.
3. Run the "Build YOLO Dataset Structure" cell to organize files.
4. Execute the training cell.

## Results & Performance Metrics

Below is the quantitative comparison of the models trained across the three versions:

### Validation Set Performance

| Metric | Version 1 (YOLOv8s) | Version 2 (YOLOv8l) | Version 3 (YOLOv8l) |
| :--- | :--- | :--- | :--- |
| **mAP@0.5** | 0.4560 | 0.6992 | 0.6992 |
| **mAP@0.5:0.95** | 0.2364 | 0.4225 | 0.4225 |
| **Precision** | 0.6642 | 0.8369 | 0.8369 |
| **Recall** | 0.4421 | 0.6502 | 0.6502 |

### Test Set Performance

| Metric | Version 1 (YOLOv8s) | Version 2 (YOLOv8l) | Version 3 (YOLOv8l) |
| :--- | :--- | :--- | :--- |
| **mAP@0.5** | 0.4651 | 0.6596 | 0.6596 |
| **mAP@0.5:0.95** | 0.2436 | 0.3893 | 0.3893 |
| **Precision** | 0.7169 | 0.8628 | 0.8628 |
| **Recall** | 0.4469 | 0.6108 | 0.6108 |

### Best Accuracy Conclusion

**Versions 2 and 3** (both using the YOLOv8l architecture with standard 640x640 resolution) achieved the **best overall accuracy**. Switching from YOLOv8s (Version 1) to the larger YOLOv8l model drastically improved the average precision across all faults. 

On the test set, Version 2/3 achieved a **mAP@0.5 of 0.6596** (a nearly +0.20 improvement over Version 1) and a **Precision of 0.8628** (a +0.14 improvement), meaning it is significantly more reliable at precisely localizing and identifying specific electrical fault types. **Version 3** is highly recommended as the final reference due to its identical top-tier performance paired with comprehensive data and inference visualizations.
