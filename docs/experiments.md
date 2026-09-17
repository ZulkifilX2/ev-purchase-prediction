# Experiment Log

## Competition

Kaggle Playground Series S6E9  
Predicting Electric Vehicle Purchases

Metric: ROC-AUC

---

## EXP-000 — Project Setup

Status: Complete

Changes:
- Created project structure
- Downloaded competition data
- Configured Git exclusions
- Initialized project documentation

Notes:
No models trained yet.

## EXP-001 — Logistic Regression Baseline

Model:
Logistic Regression

Features:
- 7 numerical features
- 6 categorical features
- `id` excluded

Preprocessing:
- StandardScaler for numerical features
- OneHotEncoder for categorical features

Validation:
Stratified 5-Fold Cross-Validation

Single Split AUC:
0.937958

Fold Scores:
- Fold 1: 0.936669
- Fold 2: 0.938045
- Fold 3: 0.939071
- Fold 4: 0.938625
- Fold 5: 0.938067

Mean CV AUC:
0.938096

CV Standard Deviation:
0.000809

Notes:
Established an interpretable linear baseline before gradient boosting.
Strongest signals were environmental concern, subsidy availability,
range anxiety, and annual income.
