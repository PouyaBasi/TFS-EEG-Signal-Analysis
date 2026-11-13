# TFS Assignment 1 — EEG Signal Analysis

**Student:** Pouya Bastani  
**Student ID:** 40530262  
**Course:** TFS

This repository contains a Jupyter notebook analyzing multichannel EEG recordings from four cognitive-task conditions. The assignment combines time-resolved Hilbert analysis with windowed Fourier features and participant-held-out classification.

## Assignment objective

1. Examine the **30–45 Hz gamma-band** signal using the Hilbert transform; derive its analytic amplitude, phase, and instantaneous frequency.
2. Compute an illustrative **Pragmatic Information (PI)** trace and peak rate for subject 1, trial 1 in Arithmetic, Mirror-image, and Relax conditions.
3. Extract spectral features from **0.5-second windows** of the supplied cleaned EEG and compare four classifiers using five-fold validation grouped by participant.

## Repository contents

```text
TFS1/
├── Pouya_Bastani_TFS1.ipynb   # Explained analysis, figures, saved results, and conclusion
├── README.md
└── Dataset/                   # 480 MATLAB recordings (.mat)
```

The notebook expects to be run with the project folder as the current working directory. Each MATLAB file is read from `Dataset/` and the cleaned signal is stored under the `Clean_data` key. The saved notebook outputs describe **40 participants × 4 conditions × 3 trials = 480 recordings**. The analysis reports 32 channels, 3,200 samples per recording, and a 128 Hz sampling rate (25 s per recording).

## Methods

### Hilbert-transform branch

The signal is filtered with a fourth-order Butterworth band-pass filter from 30 to 45 Hz using forward–backward filtering. The analytic signal is

$$z(t)=x(t)+j\,\mathcal{H}\{x(t)\}=A(t)e^{j\phi(t)}.$$

The notebook derives amplitude $A(t)$, phase $\phi(t)$, and the discrete instantaneous-frequency estimate

$$f_{\mathrm{inst}}[n]=\frac{f_s}{2\pi}\left(\phi[n]-\phi[n-1]\right).$$

The PI example combines mean squared amplitude with adjacent-channel phase dispersion. Its interpretation depends on the channel ordering and the regularization term; it is an illustrative signal statistic, not a channel-order-independent physiological measure.

### Fourier-transform branch

Each recording is split into 50 non-overlapping 0.5-second windows (64 samples per window at 128 Hz). A Hann-windowed periodogram is used to obtain four features per channel and window:

| Feature | Summary |
|---|---|
| Spectral entropy | Spread of normalized power over frequency bins |
| Total power | Sum of the periodogram ordinates before normalization |
| Dominant frequency | Frequency bin with the maximum power |
| $PSK$ | Assignment-defined standardized offset between spectral centroid and dominant frequency |

The resulting feature representation has 32 channels × 4 features per window. Five-fold `GroupKFold` uses participant identifiers as groups, keeping every participant out of the training data for their test fold.

## Saved classification results

| Model | Accuracy | Macro-F1 | Weighted-F1 |
|---|---:|---:|---:|
| Logistic Regression | **0.3496** | **0.3421** | **0.3421** |
| SVM (RBF) | 0.3265 | 0.3205 | 0.3205 |
| Random Forest | 0.3185 | 0.3137 | 0.3137 |
| Decision Tree | 0.2727 | 0.2727 | 0.2727 |

The classes have equal support in the saved evaluation, so uniform guessing corresponds to 25% accuracy. Logistic Regression is the strongest model in this run, but its score remains modest. The confusion matrices show that Stroop is more readily identified than Mirror-image. These results are descriptive: the notebook does not report confidence intervals or a permutation test, and the windows from one participant are correlated even though cross-validation separates participants between folds.

The PI peak-rate output for the first 10 seconds of subject 1, trial 1 is:

| Condition | PI MAD | Peaks | Peak rate |
|---|---:|---:|---:|
| Arithmetic | 1.8090 | 51 | 5.10 peaks/s |
| Mirror-image | 1.5793 | 51 | 5.10 peaks/s |
| Relax | 3.4899 | 48 | 4.80 peaks/s |

## Run the notebook

A Python environment with Jupyter and the following packages is required:

```bash
pip install jupyter numpy pandas scipy matplotlib scikit-learn
jupyter lab
```

Open `Pouya_Bastani_TFS1.ipynb` and run the cells from top to bottom. Run it with `TFS1/` as the working directory so the relative `Dataset/` path resolves. The notebook contains a shell-style directory listing cell (`!ls Dataset`); if your local Jupyter shell does not provide `ls`, use a Linux/Colab-compatible environment for that cell or replace it with a Python directory listing.

## Notes and limitations

- The Hilbert/PI comparison uses one participant, one trial, and a 10-second segment; it should not be generalized to the full cohort.
- The PI denominator depends on channel ordering. The instantaneous-frequency estimate is sensitive to phase noise, low-amplitude intervals, and filtering edge effects.
- A 0.5-second Fourier window gives 2 Hz frequency spacing. The histogram plots are descriptive and do not test statistical significance.
- Participant-grouped folds reduce subject leakage, but the reported metrics do not quantify uncertainty across participants.
- Before publishing the `Dataset/` files in a public repository, confirm that their redistribution is permitted by the dataset's terms. The notebook can be versioned separately from the data if necessary.

## Academic integrity and scope

The notebook documents the analysis implemented in the assignment and interprets its saved results without claiming clinical significance or a complete EEG preprocessing pipeline. The reported classification results support limited task-label discrimination under the evaluated setup, not a strong physiological or diagnostic conclusion.
