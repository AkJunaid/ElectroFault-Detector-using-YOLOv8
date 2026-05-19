# Electrical Wiring Fault Detection using YOLOv8

This repository contains two versions of a YOLOv8-based model for detecting electrical wiring faults. The project aims to identify various fault types such as burnt wires, short circuits, overloaded components, and disconnected wires to facilitate predictive maintenance.

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

Two implementations were developed to optimize performance and resource utilization. Below is a comparison of the key differences and their impact on the results.

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

## Comparative Analysis

| Feature | Version 1 | Version 2 |
| :--- | :--- | :--- |
| **Model** | YOLOv8l | YOLOv8l |
| **Image Size (Train)** | 896 | 640 |
| **Image Size (Val)** | 896 | 640 |
| **Batch Size** | 16 | 8 |
| **Resilience** | Basic | High (Handles case-sensitive extensions) |
| **Naming** | Generic | Descriptive |

### Key Improvements in Version 2

1. **Resolution Consistency:** Version 1 used 896px for training but 640px for test inference. Version 2 standardizes at 640px across all splits, leading to more reliable and interpretable metrics.
2. **Resource Management:** Version 1 used a higher batch size (16) which often triggers VRAM warnings or OOM errors on standard cloud environments. Version 2 reduces this to 8 for stability.
3. **Data Handling:** Version 2 includes a more comprehensive globbing function that captures both lower-case and upper-case image extensions, ensuring no data is skipped during the structured copy process.
4. **Metadata Accuracy:** The labeling in Version 2 was refined to reflect the actual electrical fault types discovered in the dataset annotations.

## Usage

To run the detector:
1. Ensure `ultralytics` is installed.
2. Configure the paths in the notebook to point to your dataset location.
3. Run the "Build YOLO Dataset Structure" cell to organize files.
4. Execute the training cell.

## Results

Both models demonstrate high precision in detecting electrical faults. Version 2 is recommended for deployment due to its standardized resolution and better resource efficiency.
