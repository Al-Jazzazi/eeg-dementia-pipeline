# EEG Alzheimer’s Diagnosis from EEG

End‑to‑end pipeline for EEG‑based multi‑class dementia classification. Includes preprocessing, training‑only augmentation, baselines, and three deep learning models (EEGNet, EEGConvNeXt‑1D, and an adapted CNN).

---

## Repository structure

```
.
├── data-stas/    
│   ├── labels\_epochs.json   #  labels by subject (kept)
│   └── participants.tsv      # stats about the subjects' ages & genders
├── preprocessing.ipynb            # Filtering, ICA, bad-channel interpolation, subject-wise split, 15 s chunk export
├── model_eegnet.ipynb             # EEGNet implementation
├── model_eegconvnext1d.ipynb      # ConvNeXt-style 1D CNN
├── model_amini_adapted_cnn.ipynb  # Adapted CNN per paper
├── baseline.ipynb                 # SVM / RandomForest / GradientBoost
└── README.md

```

### Data setup
Please clone the data from this repository: [eeg-alzheimers-detection](https://github.com/Leofierus/eeg-alzheimers-detection)
---

## Data assumptions

* Input format: EEGLAB `.set` files chunked into 15‑second windows per subject.
* Channels: 19 EEG channels.
* Sampling rate: 95 Hz.


The dataset used in this project is the [A dataset of EEG recordings from: Alzheimer's disease, Frontotemporal dementia and Healthy subjects](https://openneuro.org/datasets/ds004504/versions/1.0.7) from OpenNeuro.

The original data was forked from [eeg-alzheimers-detection](https://github.com/Leofierus/eeg-alzheimers-detection)


### Dataset labels

- **F** — Frontotemporal Dementia (FTD)
- **A** — Alzheimer’s Disease (AD)
- **C** — Control (healthy)

## Preprocessing pipeline

1. **Concatenation per subject** to preserve long‑range temporal patterns.
2. **Band‑pass filtering**: 1–45 Hz, zero‑phase FIR.
3. **Artifact removal via ICA**: detect ocular/muscle components (e.g., with frontal channels) and exclude.
4. **Bad‑channel detection and interpolation** based on abnormal variance.
5. **Epoching/export**: 15‑s windows written as `.set` for consistency.

### Augmentation policy

* **Applied only during training** to prevent evaluation leakage.
* Techniques: Gaussian noise (σ ≈ 0.1 × signal STD), small time shifts (±5 samples), and amplitude scaling (0.9–1.1×).

---

## Models

### Baselines

* **SVM**, **Random Forest**, **Gradient Boosting** for reference and sanity checks.

### Deep learning

* **EEGNet (2018)**: compact CNN tailored to EEG with temporal + depthwise separable convolutions; tunable `F1` and depth multiplier `D`.
* **EEGConvNeXt‑1D (2021)**: 1D ConvNeXt‑style backbone; uses LeakyReLU and careful weight init to stabilize gradients; strong dropout and weight decay.
* **Adapted CNN**: CNN adapted from literature with L2 regularization to reduce overfitting.

---

## Results (subject‑wise split, held‑out test)

| Model          | Accuracy | Macro‑F1 | Weighted‑F1 |
| -------------- | -------: | -------: | ----------: |
| Adapted CNN    |   40.78% |    19.20 |       23.30 |
| EEGConvNeXt‑1D |   40.48% |    36.60 |       38.90 |
| EEGNet         |   41.76% |    41.57 |       42.18 |

Notes:

* Class‑wise performance is uneven. FTD is the hardest class. Prior leakage issues were removed by using training‑only augmentation and strict subject splits.

---

## Reproducibility and evaluation

* **Subject‑wise** splits: no subject appears in more than one split.
* **Single source of truth** for train/val/test indices saved to disk.
* **Determinism**: set seeds for NumPy and PyTorch; log versions and hyperparameters.

---

## Roadmap

* Class‑balanced or class‑conditional augmentation.
* FTD‑specific feature extraction and spectral representations.
* Ensembling across architectures; feature reuse.

---

## References

* Acharya et al., 2025. EEGConvNeXt.
* Amini et al., 2021. Time‑dependent spectrum descriptors + CNN.
* Lawhern et al., 2018. EEGNet.

---

## Contributors

* Yousef Al‑Jazzazi · Terezia Juras · Fengcheng Jiang
