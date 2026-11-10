# Distracted Driver Analytics Platform

## Overview

This project explores how driver monitoring data can be transformed into an end-to-end analytics and machine learning platform for identifying distracted driving behaviours.

The project combines:

- Python
- Databricks
- Delta Lake
- SQL
- MLflow
- PyTorch
- Power BI

Rather than focusing solely on image classification, the project demonstrates an end-to-end workflow including data quality validation, metadata engineering, machine learning, data modelling, and analytical reporting. The objective is to show how driver monitoring observations can be transformed into actionable safety insights.

The solution follows the complete journey from raw image data through validation, enrichment, classification, analytics, and reporting.

---

## Project Objective
The long-term goal is to simulate a driver monitoring system capable of identifying potentially unsafe driving behaviours before they contribute to road incidents.

The project combines:
1. Driver behaviour classification using computer vision.
2. Experiment tracking and model evaluation using MLflow.
3. Data engineering using a Medallion Architecture (Bronze, Silver, Gold).
4. Reporting and business-focused analytics.

---

## Architecture

The solution follows the workflow below:

Raw Images → Data Quality Validation → Metadata Generation → Bronze Layer → Silver Layer → Behaviour Classification Models → Prediction Analytics Layer → Reporting & Visualisation
---

## Dataset

### Multi-Class Driver Behavior Image Dataset
Behaviour categories:
- Safe Driving
- Talking on Phone
- Texting on Phone
- Turning
- Other Activities

The dataset was originally created for computer vision classification rather than estimating real-world distracted driving prevalence. Dataset distributions should therefore be interpreted as labelled training observations rather than representative population statistics.

### Data Quality Validation

A validation process was implemented prior to model training to verify image integrity across the dataset.

- **Images checked:** 7,286
- **Corrupted images identified:** 10
- **Corrupted images removed from the training dataset:** 10

Corrupted files were detected using Python and PIL image verification before model development. Invalid images were isolated from the training dataset to prevent data loading failures during PyTorch model training. This step helped establish a clean and reliable dataset for downstream machine learning workflows.

Metadata was generated using custom Python scripts prior to ingestion into Databricks. The resulting metadata dataset formed the foundation of the Bronze and Silver analytical layers used throughout the project and supported subsequent machine learning and analytics workflows.

---

## Data Model

### Bronze Layer
Raw image metadata extracted from the source dataset.
- `image_name`
- `behaviour`
- `extension`

### Silver Layer
Business enrichment layer that adds analytical classifications to behaviour observations.
- `behaviour_group`
- `risk_category`

> ⚠️ **Important Note:** Risk categories were developed as part of this project and are not provided by the original dataset. The `other_activities` class is assigned `unknown_risk` because it contains multiple behaviours with potentially different risk levels. The dataset does not provide sufficient detail to assign a reliable risk category.

---

## Machine Learning Layer
The machine learning component classifies driver behaviours directly from image data.

### Technology Stack
- **Framework:** PyTorch
- **Hardware:** Google Colab (Tesla T4 GPU)
- **Experiment Tracking:** MLflow
- **Data Platform:** Databricks

### Dataset Split

| Dataset | Images |
| :--- | :--- |
| **Training** | 5,093 |
| **Validation** | 1,091 |
| **Test** | 1,092 |

### Models Evaluated
Two transfer learning architectures were evaluated:
- MobileNetV2
- ResNet18

Both models were initialized using pretrained ImageNet weights and fine-tuned for driver behaviour classification.

### Model Comparison

| Model | Accuracy | Loss |
| :--- | :---: | :---: |
| **MobileNetV2** | 91.94% | 0.1972 |
| **ResNet18** | **93.22%** | **0.2592** |

#### Selected Model
**ResNet18** achieved the highest test accuracy and was selected as the best-performing model within the current experimental setup. Although MobileNetV2 is a lighter architecture with lower computational requirements, ResNet18 delivered stronger classification performance on the final evaluation dataset. MobileNetV2 remains a strong candidate for future deployment scenarios due to its suitability for edge devices and real-time monitoring applications.

---

## Model Evaluation Results

### 1. MobileNetV2 Results

#### Artifacts Generated
- Confusion Matrix (`mobilenet_confusion_matrix.png`)
- Classification Report
- Prediction Table
- MLflow Experiment Run

#### Classification Report (Accuracy: 91.94%)

| Class | Precision | Recall | F1 Score |
| :--- | :---: | :---: | :---: |
| `other_activities` | 0.866 | 0.875 | 0.870 |
| `safe_driving` | 0.936 | 0.898 | 0.916 |
| `talking_phone` | 0.985 | 0.923 | 0.953 |
| `texting_phone` | 0.991 | 0.920 | 0.954 |
| `turning` | 0.824 | 0.981 | 0.896 |

#### Key Findings
- Phone-related behaviours achieved the strongest results.
- Texting and phone conversations were detected with high precision and recall.
- The most challenging behaviours involved activity overlap with turning and other non-phone activities.
- MobileNetV2 delivered strong performance while maintaining a lightweight architecture.

---

### 2. ResNet18 Results

#### Artifacts Generated
- Confusion Matrix (`resnet_confusion_matrix.png`)
- Classification Report
- Prediction Table
- MLflow Experiment Run

#### Classification Report (Accuracy: 93.22%)

| Class | Precision | Recall | F1 Score |
| :--- | :---: | :---: | :---: |
| `other_activities` | 0.746 | 0.962 | 0.840 |
| `safe_driving` | 0.972 | 0.910 | 0.940 |
| `talking_phone` | 0.985 | 0.925 | 0.954 |
| `texting_phone` | 0.987 | 0.957 | 0.972 |
| `turning` | 1.000 | 0.913 | 0.954 |

#### Key Findings
- ResNet18 achieved the strongest overall performance.
- Phone-related distraction behaviours were classified with high accuracy.
- The model delivered the highest F1 scores for texting, talking on the phone and turning.
- Most remaining classification errors involved the visually diverse `other_activities` category.

---

## Business Insights
The project demonstrates that driver behaviour detection can be integrated into a broader analytics solution rather than existing as a standalone machine learning model.

- **Phone-related distraction** behaviours are easier to classify than general activity categories.
- The `other_activities` class introduces significant visual variability and remains the most difficult category.
- Model evaluation should be based on **empirical testing** rather than assumptions about model complexity.
- Higher-capacity architectures may provide measurable improvements when distinguishing visually similar driving behaviours.

---

## Project Status

**Current phase:** `Analytics Layer Complete`
**Next phase:** `Semantic Model & Power BI Reporting`

### Current Progress

#### Data Engineering
- [x] Dataset selected
- [x] Metadata extraction process created
- [x] 7,286 image records validated
- [x] 10 corrupted images identified and removed
- [x] Bronze layer created
- [x] Silver layer created
- [x] Behaviour groups implemented
- [x] Risk categories implemented
- [x] Silver layer documented and validated

#### Machine Learning
- [x] MLflow configured
- [x] PyTorch environment configured
- [x] Google Colab GPU environment configured
- [x] Dataset ingestion completed
- [x] MobileNetV2 trained and evaluated
- [x] ResNet18 trained and evaluated
- [x] Classification reports generated
- [x] Confusion matrices generated
- [x] Prediction datasets generated
- [x] Model comparison completed
- [x] Best-performing model identified

### Analytics Layer

- [x] Prediction datasets loaded into Databricks
- [x] Unified prediction view created
- [x] Enriched prediction view created
- [x] Accuracy by risk category analysis completed
- [x] Accuracy by behaviour analysis completed
- [x] Confidence analysis completed
- [x] Misclassification analysis completed

☐ Semantic model
☐ Power BI dashboard
☐ Final reporting layer

### Next Steps

1. Create Power BI semantic model.
2. Build Driver Attention Monitoring dashboards.
3. Develop analytical KPIs and performance measures.
4. Deliver business-focused distracted driving insights.
