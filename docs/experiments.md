# Experiment Log

## Competition

**Kaggle Playground Series — Season 6 Episode 9**
**Predicting Electric Vehicle Purchases**

Evaluation metric: **ROC-AUC**

---

# EXP-000 — Project Setup

**Status:** Complete

### Changes

* Created the local project structure.
* Created a private GitHub repository.
* Downloaded the Kaggle competition data.
* Configured `.gitignore` to exclude:

  * Competition CSV files
  * Local virtual environment
  * Model artifacts
  * Submission CSV files
  * OOF/test prediction files
* Created an isolated Python 3.12 environment.
* Installed the machine-learning stack.
* Configured JupyterLab and a project-specific kernel.

### Notes

No model training was performed during this experiment.

---

# EXP-001 — Logistic Regression Baseline

### Model

`LogisticRegression`

### Purpose

Establish a simple interpretable baseline before testing nonlinear gradient-boosted models.

### Features

* 7 numerical features
* 6 categorical features
* `id` excluded

### Preprocessing

Numerical:

* `StandardScaler`

Categorical:

* `OneHotEncoder`
* `handle_unknown="ignore"`

### Single-Split Validation

* 80% training
* 20% validation
* Stratified target
* `random_state=42`

ROC-AUC:

**0.937958**

Constant prediction baseline:

**0.500000**

### 5-Fold Cross-Validation

* Fold 1: **0.936669**
* Fold 2: **0.938045**
* Fold 3: **0.939071**
* Fold 4: **0.938625**
* Fold 5: **0.938067**

Mean CV ROC-AUC:

**0.938096**

CV standard deviation:

**0.000809**

### Notes

This established a strong linear baseline and confirmed that the stratified validation framework was stable.

---

# EXP-002 — CatBoost Baseline

### Model

`CatBoostClassifier`

### Features

* 7 numerical features
* 6 categorical features
* `id` excluded
* Native categorical handling

### Main Parameters

* `learning_rate=0.05`
* `depth=7`
* Maximum iterations: **2000**
* `eval_metric="AUC"`
* Early stopping: **150 rounds**

### Single-Split Result

ROC-AUC:

**0.941434**

Best iteration:

**988**

### 5-Fold Results

* Fold 1: **0.940559**
* Fold 2: **0.941387**
* Fold 3: **0.942775**
* Fold 4: **0.942211**
* Fold 5: **0.941649**

Mean fold ROC-AUC:

**0.941716**

OOF ROC-AUC:

**0.941711**

CV standard deviation:

**0.000751**

Best iterations:

* 1353
* 980
* 1370
* 914
* 1243

### Kaggle Submission

**submission_001_catboost_cv.csv**

Public leaderboard ROC-AUC:

**0.941560**

### Notes

CatBoost substantially improved over Logistic Regression and established the first strong tree-based baseline.

---

# EXP-003 — LightGBM Baseline

### Model

`LGBMClassifier`

### Features

* Same 13 raw predictors
* `id` excluded
* Native categorical handling

### Parameters

* `n_estimators=3000`
* `learning_rate=0.03`
* `num_leaves=31`
* `min_child_samples=50`
* `subsample=0.9`
* `colsample_bytree=0.9`
* `reg_alpha=0.1`
* `reg_lambda=1.0`
* Early stopping: **150 rounds**

### Single-Split Result

ROC-AUC:

**0.941669**

Best iteration:

**979**

### 5-Fold Results

* Fold 1: **0.940614**
* Fold 2: **0.941525**
* Fold 3: **0.942845**
* Fold 4: **0.942252**
* Fold 5: **0.941670**

Mean fold ROC-AUC:

**0.941781**

OOF ROC-AUC:

**0.941770**

CV standard deviation:

**0.000748**

Best iterations:

* 1292
* 1109
* 1160
* 767
* 977

### Kaggle Submission

**submission_002_lightgbm_cv.csv**

Public leaderboard ROC-AUC:

**0.941650**

### Notes

LightGBM slightly outperformed CatBoost and became the strongest standalone raw-feature model at this stage.

---

# EXP-004 — CatBoost + LightGBM Ensemble

### Prediction Correlation

CatBoost ↔ LightGBM:

**0.997437**

### Best Probability Blend

* LightGBM: **55%**
* CatBoost: **45%**

OOF ROC-AUC:

**0.941907**

### Best Rank Blend

* LightGBM: **55%**
* CatBoost: **45%**

OOF ROC-AUC:

**0.941911**

### Kaggle Submission

**submission_003_cat_lgb_rank_blend.csv**

Public leaderboard ROC-AUC:

**0.941690**

### Notes

Despite extremely high prediction correlation, rank averaging produced a small but repeatable improvement.

---

# EXP-005 — XGBoost Baseline

### Model

`XGBClassifier`

### Features

* 13 raw predictors
* `id` excluded
* Native categorical handling

### Parameters

* `n_estimators=3000`
* `learning_rate=0.03`
* `max_depth=6`
* `min_child_weight=5`
* `subsample=0.9`
* `colsample_bytree=0.9`
* `reg_alpha=0.1`
* `reg_lambda=1.0`
* `tree_method="hist"`
* `enable_categorical=True`
* Early stopping: **150 rounds**

### Single-Split Result

ROC-AUC:

**0.941672**

Best iteration:

**879**

### 5-Fold Results

* Fold 1: **0.940641**
* Fold 2: **0.941580**
* Fold 3: **0.942841**
* Fold 4: **0.942434**
* Fold 5: **0.941832**

Mean fold ROC-AUC:

**0.941866**

OOF ROC-AUC:

**0.941857**

CV standard deviation:

**0.000756**

Best iterations:

* 1020
* 760
* 808
* 763
* 855

### Prediction Correlations

* CatBoost ↔ LightGBM: **0.997437**
* CatBoost ↔ XGBoost: **0.997973**
* LightGBM ↔ XGBoost: **0.997616**

### Notes

XGBoost became the strongest standalone raw-feature model, although all three tree models remained highly correlated.

---

# EXP-006 — Three-Model Rank Ensemble

### Models

* CatBoost
* LightGBM
* XGBoost

### Equal Rank Blend

OOF ROC-AUC:

**0.941982**

### Best Coarse Weight Search

* CatBoost: **20%**
* LightGBM: **35%**
* XGBoost: **45%**

OOF ROC-AUC:

**0.941988**

### Kaggle Submission

**submission_004_three_model_rank_blend.csv**

Public leaderboard ROC-AUC:

**0.941710**

### Notes

The third model improved OOF performance but added only a very small public leaderboard improvement.

The raw-feature boosting models appeared to be approaching a performance ceiling.

---

# EXP-007 — Manual Feature Engineering

### Purpose

Change the feature representation instead of continuing to add similar boosting models.

### Added Features

Numerical interactions:

* `Income_per_Car`
* `Total_Charging_Stations`
* `Charging_Station_Difference`
* `Income_x_Concern`
* `Commute_x_Concern`

Categorical interactions:

* `Subsidy_x_Concern`
* `Subsidy_x_Anxiety`
* `Concern_x_Anxiety`
* `Income_x_Subsidy`
* `HomeCharge_x_Subsidy`
* `HomeCharge_x_Commute`

### Single-Split Result

Raw LightGBM:

**0.941669**

Feature-engineered LightGBM:

**0.941785**

Gain:

**+0.000116**

Best iteration:

**972**

### 5-Fold Results

* Fold 1: **0.940593**
* Fold 2: **0.941624**
* Fold 3: **0.942960**
* Fold 4: **0.942345**
* Fold 5: **0.941878**

Mean fold ROC-AUC:

**0.941880**

OOF ROC-AUC:

**0.941871**

CV standard deviation:

**0.000788**

Best iterations:

* 1001
* 1005
* 1187
* 981
* 945

### Ensemble Test

Replacing raw LightGBM with feature-engineered LightGBM produced:

**0.942046 OOF ROC-AUC**

Best composition:

* CatBoost: **20%**
* Feature-engineered LightGBM: **35%**
* XGBoost: **45%**

### Notes

Manual feature engineering produced a real but relatively small improvement.

---

# EXP-008 — Leak-Free Target Encoding

### Purpose

Test whether target-derived statistics for low-cardinality categorical combinations could improve the feature-engineered LightGBM.

### Method

Target encoding used:

* Inner 5-fold OOF encoding for training rows
* Training-fold-only mappings for validation rows
* Smoothed group means
* No validation target leakage

### Encoded Groups

Included:

* Subsidy
* Environmental concern
* Range anxiety
* Home charging
* City
* Car type
* Subsidy × concern
* Subsidy × anxiety
* Concern × anxiety
* Income band × subsidy
* Income band × concern
* Home charging × subsidy

### Result

Raw LightGBM:

**0.941669**

Feature Engineering:

**0.941785**

Feature Engineering + Target Encoding:

**0.941771**

Gain versus FE:

**-0.000014**

Best iteration:

**998**

### Decision

**Rejected**

### Notes

Low-cardinality categorical target encoding did not improve over feature engineering alone.

LightGBM was already able to learn most of this structure directly.

---

# EXP-009 — Original Dataset Investigation

### Dataset

Original EV Adoption Behavior and Range Anxiety dataset.

Rows:

**10,000**

Predictor schema:

**Exact match with the 13 competition predictors**

### Target Distribution

Original positive rate:

**0.175000**

Competition positive rate:

**0.174645**

### Distribution Differences

The original dataset was related to, but not identical to, the competition distribution.

Examples:

* Competition mean commute: approximately **32.16 km**

* Original mean commute: approximately **41.11 km**

* Competition cars owned mean: approximately **1.71**

* Original mean: approximately **1.86**

The original dataset also contained missing numerical values.

### EXP-009A — Direct Row Augmentation

Competition training fold:

**534,932 rows**

Original data added:

**10,000 rows**

Augmented training size:

**544,932 rows**

Validation remained entirely competition data.

Result:

**0.941698**

Feature-engineered baseline:

**0.941785**

Difference:

**-0.000087**

### Decision

Direct augmentation rejected.

---

### EXP-009B — Original-Data Source Score

A Logistic Regression model was trained only on the original dataset using:

* Annual income
* Environmental concern
* Subsidy availability
* Range anxiety

Original-only model evaluated on competition validation:

**0.937515**

The source model's decision score was then added to the feature-engineered LightGBM.

Result:

**0.941722**

Difference versus FE baseline:

**-0.000063**

### Decision

Source-score transfer rejected.

### Notes

The source dataset is related to the competition generator, but direct domain transfer did not improve the competition model.

---

# EXP-010 — Synthetic Income Artifacts

### Purpose

Investigate whether repeated numerical values in the synthetic competition dataset contain generator-specific predictive signal.

Annual income was selected because the training set contains approximately 668k rows but only around 13k unique income values.

### Income Artifact Features

Target-encoded:

* Exact income
* Income floored to $50
* Income floored to $250
* Income floored to $500

Frequency-encoded:

* Exact income
* $50 income bin
* $250 income bin
* $500 income bin

Target encodings were produced leak-free using inner OOF folds.

### Single-Split Result

Feature Engineering only:

**0.941785**

Feature Engineering + Income Artifacts:

**0.945266**

Gain:

**+0.003481**

Best iteration:

**847**

### Artifact Feature Importance

Strong synthetic-artifact features included:

* `TE_Income_Exact`
* `TE_Income_Floor_50`
* `FREQ_Income_Exact`
* `FREQ_Income_Floor_50`
* `TE_Income_Floor_500`
* `TE_Income_Floor_250`
* `FREQ_Income_Floor_500`
* `FREQ_Income_Floor_250`

### 5-Fold Nested Validation

Each outer fold generated artifact target encodings using only that fold's training portion.

Fold results:

* Fold 1: **0.944590**
* Fold 2: **0.945256**
* Fold 3: **0.946335**
* Fold 4: **0.945939**
* Fold 5: **0.945468**

Mean fold ROC-AUC:

**0.945518**

OOF ROC-AUC:

**0.945511**

CV standard deviation:

**0.000596**

Best iterations:

* 842
* 858
* 938
* 649
* 783

### Prediction Correlation

Artifact LightGBM correlations:

* With CatBoost: **0.979795**
* With XGBoost: **0.980184**
* With FE-LightGBM: **0.981238**

This was substantially lower than the approximately 0.997 correlations among the raw-feature models.

### Ensemble Tests

Artifact model alone:

**0.945511**

90% Artifact + 10% previous ensemble:

**0.945539**

90% Artifact + 10% XGBoost:

**0.945544**

The additional ensemble gain was very small.

### Kaggle Submission

**submission_005_income_artifact_lgbm.csv**

Description:

`Income artifact LightGBM | exact + 50/250/500 income TE/frequency | 5-fold OOF 0.945511`

Public leaderboard ROC-AUC:

**0.945800**

Previous best public leaderboard:

**0.941710**

Public leaderboard improvement:

**+0.004090**

### Notes

This was the largest improvement of the project so far.

Synthetic numerical repetition contained significantly more useful predictive structure than ordinary feature interactions, model tuning, low-cardinality target encoding, or source-data augmentation.

The artifact model became the new primary modeling approach.

---

# EXP-011 — Income + Commute Artifact Probe

**Status:** Single-split probe only — full 5-fold validation pending.

### Added Commute Features

Target encodings:

* Exact 0.1 km commute value
* Commute floored to 1 km
* Commute floored to 5 km
* Commute floored to 10 km

Frequency encodings:

* Exact commute
* 1 km bin
* 5 km bin
* 10 km bin

### Single-Split Comparison

Income artifacts V1:

**0.945266**

Income + commute V2:

**0.945390**

Gain:

**+0.000124**

Best iteration:

**881**

### Commute Feature Usage

Strong commute-related features included:

* `TE_Commute_Exact`
* `TE_Commute_Floor_1`
* `FREQ_Commute_Exact`
* `TE_Commute_Floor_5`
* `FREQ_Commute_Floor_1`

### Decision

Promising enough for full 5-fold validation.

No Kaggle submission has been created from V2 yet.

---

# Current Model Leaderboard

| Model                     | Local OOF / CV ROC-AUC | Public Kaggle ROC-AUC |
| ------------------------- | ---------------------: | --------------------: |
| Constant baseline         |               0.500000 |                     — |
| Logistic Regression       |               0.938096 |                     — |
| CatBoost                  |               0.941711 |              0.941560 |
| LightGBM                  |               0.941770 |              0.941650 |
| XGBoost                   |               0.941857 |                     — |
| CatBoost + LightGBM Rank  |               0.941911 |              0.941690 |
| Three-Model Rank Ensemble |               0.941988 |              0.941710 |
| FE Three-Model Ensemble   |               0.942046 |                     — |
| Income-Artifact LightGBM  |           **0.945511** |          **0.945800** |

## Current Best Local Result

**0.945511 OOF ROC-AUC**

## Current Best Public Leaderboard Result

**0.945800 ROC-AUC**

---

# Next Experiment

## Income + Commute Artifact V2 — Full 5-Fold Validation

The next session will validate the promising single-split improvement from:

**0.945266 → 0.945390**

using the same nested 5-fold methodology used for the income-artifact model.

If V2 improves full OOF performance, additional synthetic artifacts may then be tested individually for:

* Age
* Charging-station counts
* Environmental concern
* Number of cars owned

Only features that improve leak-free cross-validation will be retained.
