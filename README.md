# Breast Cancer Survival Classification

Predicts death from any cause in SEER breast cancer patients using features known at diagnosis. A calibrated logistic regression on engineered features reaches a test ROC AUC of 0.75, with two operating points: max-F1 and 80 % recall.

## Dataset

SEER breast cancer data, 4,024 patients, 616 deceased (15 %). Source (save as `data.xlsx`): https://github.com/WEEDUENHEH/Breast-cancer-dataset/raw/9c577ad0fb9e3b5e269b7f12ba86d39a87e6180c/Breast_Cancer_dataset_.xlsx.

Survival Months is excluded because follow-up length depends on the outcome; all other features are known at diagnosis.

## Approach

- Features engineered: ordinal T, N, stage and grade; receptor flags; log tumour size; log positive nodes; node ratio.
- Pipelines compared with 5x5 repeated stratified CV: one-hot logistic regression as reference, engineered logistic regression, class weights, SMOTE, splines, interactions, boosting, ensemble.
- Thresholds chosen in nested CV.
- Held-out test set (805 patients) used once.

## Results

Cross-validation (25 folds):

| Candidate | ROC AUC | PR AUC | F1 |
|---|---|---|---|
| Reference: LR, one-hot | .740 | .400 | .405 |
| LR, engineered (chosen) | .744 | .400 | .406 |
| LR, engineered + splines | .747 | .401 | .404 |
| LightGBM (best of 2) | .741 | .389 | .402 |

Test set, 95 % bootstrap CIs:

| Model | ROC AUC | PR AUC | F1 | Recall |
|---|---|---|---|---|
| Reference, thr 0.5 | .749 [.70, .79] | .40 | .40 | .65 |
| Chosen, max-F1 (0.19) | .750 [.71, .79] | .42 [.34, .51] | .40 [.33, .47] | .56 |
| Chosen, 80 % recall (0.10) | .750 [.71, .79] | .42 [.34, .51] | .37 | .83 [.76, .89] |

The simplest model within one standard error of the best is chosen.

## Interpretation

Probabilities are calibrated (Brier 0.113). Grade, T stage, positive nodes, node ratio and age raise the odds of death; ER and PR positivity lower them.

## Limitations and next step

About a quarter of surviving patients have short follow-up, so the label is noisy; survival analysis is the next step. No external validation; not for clinical use.

## How to run

```
pip install numpy pandas scikit-learn imbalanced-learn lightgbm matplotlib openpyxl jupyter
jupyter nbconvert --execute --to notebook --inplace breast_cancer_survival.ipynb
```

Coursework project, Master of Data Science, University of Malaya (2024); updated 2026.
