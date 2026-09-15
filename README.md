# ECG Multilabel Classification

This repository contains the experimental code for the study:

**Evaluating the Performance of the Deep Learning-based ECG Multilabel Classifiers for the Choices of Lead Counts and Preprocessing Methods**  
Seong Hyeon Jee and Yoon-Chul Kim  
*The Journal of Korean Institute of Next Generation Computing*, 2025

## 1. Overview

Automatic electrocardiogram (ECG) analysis is important for supporting the diagnosis of various cardiovascular diseases and improving the efficiency of clinical interpretation.

This project implements a deep learning-based multilabel ECG classification framework using the PhysioNet/CinC Challenge 2021 dataset. The study evaluates how different ECG lead configurations, preprocessing methods, and thresholding strategies affect class-wise prediction performance.

The main goal of this repository is to provide reproducible code for comparing ECG multilabel classifiers under different input and preprocessing conditions.

## 2. Main Features

- Multilabel ECG classification using 1D ResNet-based CNN models
- Evaluation of different ECG lead configurations
- Comparison of ECG preprocessing strategies
- Class-wise performance analysis using F1-score
- Fixed and class-dependent threshold comparison
- ROC-based threshold optimization using Youden index
- Reproducible training and evaluation pipeline

## 3. Dataset

This project uses the **PhysioNet/Computing in Cardiology Challenge 2021** ECG dataset.

The original dataset contains **88,253 ECG recordings** with 12-lead ECG signals. Among the original diagnostic labels, this study used **23 classes** after excluding classes with very small sample sizes.

The ECG recordings were treated as multilabel data, meaning that one recording can contain more than one diagnostic label.

Raw ECG data are not included in this repository. Please download the dataset from the official PhysioNet/CinC Challenge 2021 website.

## 4. ECG Lead Configurations

The study compared the following ECG lead configurations:

| Number of leads | Lead names |
|---:|---|
| 12 | I, II, III, aVR, aVL, aVF, V1, V2, V3, V4, V5, V6 |
| 6 | I, II, III, aVR, aVL, aVF |
| 4 | I, II, III, V2 |
| 3 | I, II, V2 |
| 2 | I, II |

## 5. Preprocessing Methods

Three preprocessing strategies were evaluated:

**<img width="1038" height="820" alt="image" src="https://github.com/user-attachments/assets/2aae8137-d0e5-4643-968a-950ffec658a8" />**


### Raw ECG

The ECG signal was used after basic filtering, resampling, and standardization.

### Pan-Tompkins Filtering

The Pan-Tompkins-based preprocessing method was used to emphasize QRS-related information and R-R interval patterns.

### Discrete Wavelet Transform

Discrete wavelet transform (DWT) was used to reduce baseline drift while preserving the overall ECG waveform structure.

## 6. Model Architecture

The classification model is based on a **1D ResNet convolutional neural network**.

The model takes ECG signals as one-dimensional time-series inputs and predicts the probability of each diagnostic class using sigmoid outputs.

The overall architecture consists of:

```text
ECG input
    ↓
1D convolutional block
    ↓
Residual bottleneck blocks
    ↓
Global average pooling
    ↓
Fully connected layer
    ↓
Sigmoid multilabel output
```

The model was designed based on a ResNet-50-style architecture adapted for one-dimensional ECG signals.

## 7. Training Setup

The model was trained under the following experimental setup:

| Setting | Value |
|---|---|
| Framework | TensorFlow / Keras |
| Model | 1D ResNet-based CNN |
| Input length | 10 seconds |
| Sampling frequency | 500 Hz |
| Batch size | 32 |
| Epochs | 50 |
| Learning rate | 0.001 |
| Optimizer | Adam |
| Loss function | Binary cross-entropy |

The dataset was split into training, validation, and test sets using multilabel stratification to preserve class distribution across splits.

## 8. Thresholding Strategy

Two thresholding strategies were compared:

### Fixed Threshold

A single fixed threshold was applied to all classes.

### Class-dependent Threshold

A separate threshold was determined for each class using validation-set ROC analysis. The final threshold for each class was selected by maximizing the Youden index.

This class-dependent thresholding strategy was used to address class imbalance and improve multilabel prediction performance.

## 9. Evaluation

Model performance was evaluated using:

- Class-wise F1-score
- Macro F1-score
- Weighted F1-score
- Precision
- Recall
- ROC-based threshold analysis
- Analysis of variance (ANOVA)

The experiments compared the effects of:

- ECG lead count
- Preprocessing method
- Thresholding strategy

## 10. Key Findings

The study found that differences in class-wise F1-score across lead counts and preprocessing methods were not statistically significant.

Several frequent rhythm-related classes, such as normal sinus rhythm, sinus bradycardia, and sinus tachycardia, showed relatively stable performance even when the number of leads was reduced.

Pan-Tompkins preprocessing was helpful for some arrhythmia-related classes because it emphasized R-R interval patterns. DWT preprocessing was useful for reducing baseline drift.

Class-dependent thresholding improved multilabel classification performance compared with a fixed threshold. For example, in the 2-lead setting with Pan-Tompkins preprocessing, macro F1-score improved from 0.4448 to 0.4644.

## 11. Repository Structure

```text
.
├── README.md
├── train.ipynb
├── test.ipynb
├── team_code.py
├── helper_code.py
├── model_type/
│   └── ResNet/
└── requirements.txt
```

## 12. Main Files

### `team_code.py`

Contains the main training and inference pipeline.

Key components include:

- `train_model`, `train_model_2`: model training functions
- `run_model`: inference function for trained models
- `generate_X_rawecg`: raw ECG input generation
- `generate_X_dwt`: DWT-preprocessed ECG input generation
- `generate_X_pan_tompkins`: Pan-Tompkins-preprocessed ECG input generation
- Training logs, callbacks, and threshold calculation utilities

### `helper_code.py`

Contains utility functions for data loading, signal preprocessing, metrics, and callbacks.

Key components include:

- `find_challenge_files`
- `load_challenge_data`
- `bandpass_filter`
- `perform_discrete_wavelet_transform`
- `pan_tompkins`
- `CustomF1WithClassThresholds`
- `DynamicF1`
- `ROCThresholdExcelCallback`
- `NaNLossRecoveryCallback`

### `train.ipynb`

Notebook for model training.

It includes:

- Loading PhysioNet ECG data
- Applying preprocessing methods
- Training 1D ResNet models
- Saving training logs and trained models

### `test.ipynb`

Notebook for model evaluation.

It includes:

- Loading trained models
- Applying fixed or class-dependent thresholds
- Computing class-wise F1-score
- Visualizing results

## 13. Installation

Install the required Python packages:

```bash
pip install -r requirements.txt
```

A minimal set of required packages includes:

```bash
pip install numpy pandas scikit-learn seaborn matplotlib tensorflow tensorflow-addons scipy joblib tqdm PyWavelets
```

## 14. Data Preparation

Download the PhysioNet/CinC Challenge 2021 ECG dataset from the official source.

After downloading, place the `.mat` and `.hea` files in the project data directory.

Example structure:

```text
data/
├── records/
│   ├── example_001.mat
│   ├── example_001.hea
│   └── ...
```

## 15. Usage

### Training

Run the training notebook:

```bash
jupyter notebook train.ipynb
```

### Testing

Run the evaluation notebook:

```bash
jupyter notebook test.ipynb
```

## 16. Notes

This repository does not include the raw ECG dataset, trained model weights, or large experimental outputs.

Some file paths in the notebooks and scripts may need to be modified according to the local data directory structure.

## 17. Citation

If you use this repository or refer to this work, please cite:

```text
Jee, S. H., & Kim, Y.-C. (2025).
Evaluating the Performance of the Deep Learning-based ECG Multilabel Classifiers for the Choices of Lead Counts and Preprocessing Methods.
The Journal of Korean Institute of Next Generation Computing, 21(6), 60–69.
```





