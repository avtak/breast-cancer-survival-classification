# Breast Cancer Survival Classification

Alive/dead prediction for breast cancer patients with Random Forest and XGBoost, SMOTE and tuning. The focus is recall on the minority class (dead), where a miss is costly.

## Dataset

- Breast cancer patient dataset (Kaggle origin); the notebook loads a public GitHub copy.
- 4,024 rows, 16 columns.
- Target: `Status`, alive = 0, dead = 1. About 15% dead (616 patients).

## Approach

- Cleaning: dropped `Marital Status`, mapped `Grade`. No missing values.
- Features actually used: the 5 numeric columns, standardised with `StandardScaler`. One-hot encoded columns are created but not passed to the models.
- 80/20 split (`random_state=42`, 805 test rows: 682 alive, 123 dead).
- SMOTE on the training split only.
- Tuning: GridSearchCV and RandomizedSearchCV (Random Forest), stratified 5-fold GridSearchCV (XGBoost).

## Results (test set, class "dead")

| Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| Random Forest, default | 0.89 | 0.71 | 0.46 | 0.56 |
| Random Forest, GridSearchCV | 0.87 | 0.58 | **0.61** | 0.60 |
| Random Forest, RandomizedSearchCV | 0.88 | 0.62 | 0.60 | **0.61** |
| XGBoost, default | 0.89 | 0.71 | 0.50 | 0.58 |
| XGBoost, tuned (SMOTE pipeline) | 0.85 | 0.51 | 0.60 | 0.55 |

XGBoost tuned confusion matrix: [[611, 71], [49, 74]]. AUC 0.83 is reported for an XGBoost SMOTE pipeline with default parameters. Tuned-model differences are small on 123 positive cases.

## Caveats

- The notebook's SVM section is trained on a synthetic `make_classification` dataset, not this data. Its recall of 0.83 and its "SVM is best" conclusion are invalid and are not in the table above.
- `Survival Months` is a feature and may leak outcome information.
- Scaling was fitted before the split.
- Single split, no confidence intervals; some notebook commentary does not match its printed outputs (63% versus 61% recall).
- Not a clinical tool.

## How to run

Run the notebook in Colab or Jupyter. Needs pandas, scikit-learn, imbalanced-learn, xgboost, seaborn, openpyxl.

## Context

Coursework project, Master of Data Science, University of Malaya, 2024.
