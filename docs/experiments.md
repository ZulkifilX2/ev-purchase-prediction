# Experiment Log

## Competition

**Kaggle Playground Series — Season 6 Episode 9**
**Predicting Electric Vehicle Purchases**

Evaluation Metric: **ROC-AUC**

---

## EXP-000 — Project Setup

**Status:** Complete

### Changes

* Created the local project structure.
* Created a private GitHub repository.
* Downloaded the Kaggle competition data.
* Configured `.gitignore` to exclude:

  * Competition CSV files
  * Python virtual environment
  * Model files
  * Submission files
  * Out-of-fold prediction files
* Created an isolated Python 3.12 environment.
* Installed the initial machine-learning stack.
* Configured JupyterLab and the project kernel.
* Created the initial project documentation.

### Notes

No models were trained during this experiment.

---

## EXP-001 — Logistic Regression Baseline

### Model

`LogisticRegression`

### Purpose

Establish a simple, interpretable linear baseline before moving to nonlinear gradient-boosting models.

### Features

* 7 numerical features
* 6 categorical features
* `id` excluded

Numerical features:

* `Age`
* `Annual_Income_USD`
* `Daily_Commute_km`
* `Number_of_Cars_Owned`
* `Charging_Stations_Near_Home`
* `Charging_Stations_Near_Work`
* `Environmental_Concern_Level`

Categorical features:

* `Gender`
* `City_Type`
* `Current_Car_Type`
* `Home_Charging_Possible`
* `Subsidy_Available`
* `Range_Anxiety_Level`

### Preprocessing

Numerical features:

* `StandardScaler`

Categorical features:

* `OneHotEncoder`
* `handle_unknown="ignore"`

### Initial Validation Split

* 80% training
* 20% validation
* Stratified by target
* `random_state=42`

Single-split ROC-AUC:

**0.937958**

Constant-prediction ROC-AUC:

**0.500000**

### Cross-Validation

Validation method:

**Stratified 5-Fold Cross-Validation**

Fold scores:

* Fold 1: **0.936669**
* Fold 2: **0.938045**
* Fold 3: **0.939071**
* Fold 4: **0.938625**
* Fold 5: **0.938067**

Mean CV ROC-AUC:

**0.938096**

CV standard deviation:

**0.000809**

### Interpretation

The strongest positive signals in the fitted Logistic Regression model were:

* Environmental concern
* Subsidy availability
* Low range anxiety
* Annual income

Features with weak linear effects included:

* Age
* Number of cars owned
* Charging-station counts

### Notes

The Logistic Regression model established a strong linear baseline.

Its stable 5-fold performance showed that the validation framework was working consistently.

This experiment became the reference point for evaluating more powerful nonlinear models.

---

## EXP-002 — CatBoost Baseline

### Model

`CatBoostClassifier`

### Purpose

Test whether a nonlinear gradient-boosting model with native categorical-feature handling could improve on the Logistic Regression baseline.

### Features

* 7 numerical features
* 6 categorical features
* `id` excluded
* Native CatBoost categorical-feature handling

### Initial Single-Split Parameters

* `iterations=1000`
* `learning_rate=0.05`
* `depth=7`
* `loss_function="Logloss"`
* `eval_metric="AUC"`
* `random_seed=42`
* `early_stopping_rounds=100`

### Initial Single-Split Result

Best iteration:

**988**

Validation ROC-AUC:

**0.941434**

This improved substantially over the Logistic Regression single-split score of **0.937958**.

### Initial Feature Importance

Most important CatBoost features:

1. `Subsidy_Available`
2. `Environmental_Concern_Level`
3. `Annual_Income_USD`
4. `Range_Anxiety_Level`
5. `Age`

Approximate feature importances from the single-split model:

* Subsidy availability: **54.17**
* Environmental concern: **19.00**
* Annual income: **10.31**
* Range anxiety: **4.10**
* Age: **3.16**
* Daily commute: **2.92**
* Charging stations near home: **1.80**
* Charging stations near work: **1.75**
* Home charging possible: **0.95**
* Number of cars owned: **0.72**
* Current car type: **0.55**
* City type: **0.37**
* Gender: **0.18**

### Cross-Validation Configuration

Validation method:

**Stratified 5-Fold Cross-Validation**

Parameters:

* Maximum iterations: **2000**
* `learning_rate=0.05`
* `depth=7`
* `loss_function="Logloss"`
* `eval_metric="AUC"`
* `early_stopping_rounds=150`
* Fold-specific random seeds

### Fold Results

* Fold 1: **0.940559**
* Fold 2: **0.941387**
* Fold 3: **0.942775**
* Fold 4: **0.942211**
* Fold 5: **0.941649**

Mean Fold ROC-AUC:

**0.941716**

OOF ROC-AUC:

**0.941711**

CV standard deviation:

**0.000751**

### Best Iterations

* Fold 1: **1353**
* Fold 2: **980**
* Fold 3: **1370**
* Fold 4: **914**
* Fold 5: **1243**

Average best iteration:

**1172**

### Saved Predictions

Local-only files:

* `outputs/predictions/catboost_oof.csv`
* `outputs/predictions/catboost_test.csv`

Submission file:

* `outputs/submissions/submission_001_catboost_cv.csv`

These files are excluded from GitHub.

### Kaggle Submission

Submission:

**submission_001_catboost_cv.csv**

Public leaderboard ROC-AUC:

**0.941560**

Local OOF ROC-AUC:

**0.941711**

CV-to-public-LB difference:

**-0.000151**

### Notes

CatBoost improved substantially over the Logistic Regression baseline.

The small difference between local OOF performance and the public leaderboard suggests that the current cross-validation strategy is well aligned with the competition test distribution.

CatBoost also demonstrated that nonlinear relationships and feature interactions provide useful predictive signal beyond the linear baseline.

---

## EXP-003 — LightGBM Baseline

### Model

`LGBMClassifier`

### Purpose

Evaluate a second gradient-boosted decision-tree model and determine whether it could improve on CatBoost or provide complementary predictions for later ensembling.

### Features

* 7 numerical features
* 6 categorical features
* `id` excluded
* Native LightGBM categorical-feature handling using pandas `category` dtype

### Initial Single-Split Parameters

* `objective="binary"`
* `n_estimators=3000`
* `learning_rate=0.03`
* `num_leaves=31`
* `max_depth=-1`
* `min_child_samples=50`
* `subsample=0.9`
* `colsample_bytree=0.9`
* `reg_alpha=0.1`
* `reg_lambda=1.0`
* `random_state=42`
* `n_jobs=-1`
* Early stopping after 150 non-improving rounds

### Initial Single-Split Result

Best iteration:

**979**

Validation ROC-AUC:

**0.941669**

Single-split comparison:

* Logistic Regression: **0.937958**
* CatBoost: **0.941434**
* LightGBM: **0.941669**

LightGBM slightly outperformed CatBoost on the same validation split.

### Cross-Validation Configuration

Validation method:

**Stratified 5-Fold Cross-Validation**

Same base parameters as the single-split model, with fold-specific random seeds.

### Fold Results

* Fold 1: **0.940614**
* Fold 2: **0.941525**
* Fold 3: **0.942845**
* Fold 4: **0.942252**
* Fold 5: **0.941670**

Mean Fold ROC-AUC:

**0.941781**

OOF ROC-AUC:

**0.941770**

CV standard deviation:

**0.000748**

### Best Iterations

* Fold 1: **1292**
* Fold 2: **1109**
* Fold 3: **1160**
* Fold 4: **767**
* Fold 5: **977**

### Comparison With CatBoost

CatBoost OOF ROC-AUC:

**0.941711**

LightGBM OOF ROC-AUC:

**0.941770**

Local improvement over CatBoost:

**+0.000059**

The standalone difference is very small, but the two models may make different prediction errors and therefore remain useful for ensembling.

### Saved Predictions

Local-only files:

* `outputs/predictions/lightgbm_oof.csv`
* `outputs/predictions/lightgbm_test.csv`

Submission file:

* `outputs/submissions/submission_002_lightgbm_cv.csv`

These files are excluded from GitHub.

### Kaggle Submission

Submission:

**submission_002_lightgbm_cv.csv**

Public leaderboard ROC-AUC:

**0.941650**

Local OOF ROC-AUC:

**0.941770**

CV-to-public-LB difference:

**-0.000120**

### Comparison With CatBoost on Kaggle

CatBoost public leaderboard:

**0.941560**

LightGBM public leaderboard:

**0.941650**

Public leaderboard improvement:

**+0.000090**

### Notes

LightGBM produced the strongest standalone result so far.

Its local OOF result and public leaderboard result were again extremely close, reinforcing confidence in the current validation framework.

Because CatBoost and LightGBM have nearly identical standalone performance but different model structures, their prediction diversity should be tested through probability and rank-based ensembling.

---

# Current Model Leaderboard

| Model               | Local OOF / CV ROC-AUC | Public Kaggle ROC-AUC |
| ------------------- | ---------------------: | --------------------: |
| Constant baseline   |               0.500000 |                     — |
| Logistic Regression |               0.938096 |                     — |
| CatBoost            |               0.941711 |              0.941560 |
| LightGBM            |           **0.941770** |          **0.941650** |

Current best standalone model:

**LightGBM**

Current best public leaderboard score:

**0.941650**

---

# Next Experiment

## EXP-004 — CatBoost + LightGBM Ensemble

Planned tests:

* Probability averaging
* Weighted probability blending
* Rank averaging
* OOF-based blend-weight comparison

The ensemble will be evaluated locally using saved out-of-fold predictions before any Kaggle submission is created.


