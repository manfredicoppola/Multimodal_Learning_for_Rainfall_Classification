# Multimodal Rain Intensity Classification

An automatic rain intensity classification system based on a multimodal approach that integrates a **piezoelectric sensor** and a **microphone**, developed as a Bachelor's thesis in Electronic Engineering at the University of Catania.

## Overview

The project addresses rain intensity estimation as a low-cost alternative to traditional measurement systems (rain gauges, weather radar), leveraging two physically distinct signals:

- **Piezoelectric sensor** — captures the mechanical impact of raindrops
- **Microphone** — captures the acoustic emission of precipitation

The two modalities are combined through **multimodal fusion** techniques (Early Fusion and Late Fusion) to improve classification performance compared to each sensor used individually.

The work explores two complementary experimental directions:

1. **Full-window approach** — original dataset, 10s buffers, physically motivated features
2. **Data augmentation approach** — buffers segmented into 3s sub-windows to increase sample size

## Intensity classes

| Code | Class | Description |
|---|---|---|
| NR | No Rain | Absence of precipitation |
| W | Weak | Light rain |
| M | Moderate | Moderate rain |
| H | Heavy | Heavy rain |
| S | Shower | Intermittent downpour |
| C | Cloudburst | Extreme downpour *(data augmentation approach only)* |

## Methodology

### Full-window approach

- **Dataset**: 1,467 piezo-audio pairs, 5 classes
- **Piezo features (18)**: impact intensity, distribution shape, temporal structure, spectral characteristics, burst structure
- **Audio features (35)**: MFCC (13 coefficients × mean/std), global spectral descriptors, temporal energy structure
- **Classifiers**: Random Forest, XGBoost, SVM (RBF), MLP
- **Fusion**: Early Fusion (feature concatenation) and Late Fusion (weighted probability averaging)
- **Validation**: 80/20 stratified split
- **Metric**: macro F1-score

### Data augmentation approach

- **Dataset**: 118,499 fused segments, traced back to 16,931 original sessions, 6 classes
- **Features (16+16, symmetric)**: MFCC, RMS, ZCR, ACR
- **Classifier**: Random Forest
- **Validation**: comparison between Stratified K-Fold (naive) and Stratified Group K-Fold (session-constrained, anti-leakage)
- **Metrics**: Accuracy and macro F1-score

## Key results

### Full-window — macro F1 by modality and strategy

| Model | Piezo | Audio | Early Fusion | Late Fusion |
|---|---|---|---|---|
| Random Forest | 0.518 | 0.752 | 0.804 | 0.765 |
| **XGBoost** | 0.516 | 0.771 | **0.808** | 0.805 |
| SVM (RBF) | 0.512 | 0.726 | 0.770 | 0.740 |
| MLP | 0.520 | 0.686 | 0.751 | 0.692 |

The best result (**macro F1 = 0.808**) is achieved by **Early Fusion + XGBoost**. Heavy and Shower are the most difficult classes to classify; fusion improves performance non-uniformly, with the largest gains precisely on these two classes.

### Data augmentation — the effect of data leakage

| | Stratified K-Fold (naive) | Stratified Group K-Fold (constrained) |
|---|---|---|
| Accuracy | 0.8429 | 0.8277 |
| Macro F1 | 0.8587 | 0.8420 |

Validation without a session-level constraint overestimates performance by about 1.5–1.7 percentage points: segments derived from the same original buffer, if split across both training and test sets, allow the model to recognize the recording session rather than generalize the underlying physical phenomenon.

## Key contributions

- Design of piezoelectric features derived from a systematic exploratory analysis of the signal, rather than the adoption of standard off-the-shelf descriptors
- Systematic comparison across four classifier families and two multimodal fusion strategies
- Identification and quantification of a data leakage phenomenon in the validation of segmented data, along with its methodological correction

## Known limitations

- Full-window approach validated on a single acquisition session (cross-session domain shift not explored systematically)
- Unresolved scale anomaly in the MFCC coefficients of the data augmentation approach
- Direct comparison between the two approaches is not fully homogeneous (number of classes, validation protocol, available metrics)

## Future work

- Explicit cross-session validation, to measure robustness to different acquisition conditions
- Homogeneous comparison between the two approaches (same classes, same validation protocol)
- Integration of the two pipelines into a single system

## Authors

- **Manfredi Coppola** — full-window approach, feature engineering, multimodal fusion
- Data augmentation approach developed in collaboration

Bachelor's Degree in Electronic Engineering, University of Catania
