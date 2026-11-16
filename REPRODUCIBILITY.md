# Reproducibility Guide

## Project

**AML-2026-P02-Neural_Nomads — Next-Day Air Quality for an Indian City**

This guide describes how to reproduce the work completed in **Phase A, Phase B (Checkpoint 1), and Phase C**.

---

## 1. Environment Setup

Python 3.9 or newer is recommended.

From the repository root, install the required packages:

```bash
pip install -r requirements.txt
```

The project uses:

- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- Jupyter Notebook

> Note: Phase A was developed in Google Colab and contains some Colab-specific setup/download cells. When running locally, use the repository data paths instead of the Colab-specific paths where necessary.

---

## 2. Data

The original dataset used by the project is:

```text
city_day.csv
```

It contains daily air-quality observations for multiple Indian cities.

Phase A selects **Delhi** and creates the processed daily dataset:

```text
delhi_daily_reindexed.csv
```

The recommended data structure is:

```text
DATA/
├── raw/
│   └── city_day.csv
└── processed/
    └── delhi_daily_reindexed.csv
```

The processed Delhi dataset is used by the later phases of the project.

---

## 3. Phase A — EDA and Data Preparation

Run:

```text
notebooks/01-eda.ipynb
```

Phase A performs the following work:

1. Loads the original `city_day.csv` dataset.
2. Parses and checks the date column.
3. Selects Delhi as the target city.
4. Checks invalid dates, missing city values, and duplicate city-date records.
5. Examines overall and year-wise missingness.
6. Examines PM2.5 availability.
7. Produces exploratory plots, including:
   - Missingness heatmap
   - PM2.5 over time
   - Monthly PM2.5 distribution
8. Creates the processed Delhi daily dataset used by later phases.

The processed Delhi dataset covers:

```text
2015-01-01 to 2020-07-01
```

with 2009 daily rows.

Important Phase A outputs include:

```text
results/figures/missingness_heatmap.png
results/figures/pm25_over_time.png
results/figures/pm25_monthly_distribution.png

results/phase-a/eda_summary.csv
results/phase-a/missingness_by_year.csv
results/phase-a/missingness_observed_rows.csv
results/phase-a/missing_data_policy.md
```

---

## 4. Phase B — Checkpoint 1

After Phase A has produced the processed Delhi dataset, run:

```text
notebooks/02-preprocessing.ipynb
```

Phase B Checkpoint 1 performs:

1. Loading of the processed Delhi dataset.
2. Creation of the next-day PM2.5 target: `PM2.5_target_t_plus_1`.
3. A chronological train/test split.
4. A persistence baseline where today's PM2.5 is used as the prediction for tomorrow.
5. Initial lag-based feature engineering.
6. A manual lag/leakage sanity check.
7. Median imputation inside the scikit-learn pipeline.
8. Linear Regression training.
9. Evaluation using MAE, RMSE, and R².
10. Comparison of Linear Regression against the persistence baseline.
11. Export of metrics and test-period predictions.

### Chronological Split

The model uses observations before 2020 for training and 2020 as the held-out test period.

The evaluated test period is:

```text
2020-01-01 to 2020-06-30
```

The same test dates are used when comparing the persistence baseline and Linear Regression.

---

## 5. Phase B Outputs

The Checkpoint 1 results are:

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Persistence | 19.158 | 28.002 | 0.705 |
| Linear Regression | 18.444 | 25.451 | 0.756 |

The generated result files are stored under:

```text
results/phase-b/
```

The Phase B results provide the baseline and initial Linear Regression performance used for comparison with later modelling work.

---

## 6. Phase C — Feature Engineering and Regression Models

Phase C extends the next-day PM2.5 prediction pipeline with additional feature engineering and regression modelling.

Run the Phase C notebooks in the following order:

```text
1. notebooks/phase_c/02-feature-engineering.ipynb
2. notebooks/phase_c/03_regression_models.ipynb
```

### Phase C Feature Engineering

Run:

```text
notebooks/phase_c/02-feature-engineering.ipynb
```

This notebook prepares the regression-ready feature dataset used by the Phase C modelling experiments.

The generated feature dataset is:

```text
results/phase-c/delhi_regression_features.csv
```

### Phase C Regression Modelling

After feature engineering, run:

```text
notebooks/phase_c/03_regression_models.ipynb
```

This notebook performs the Phase C regression modelling experiments and generates model comparisons, predictions, coefficients, baseline comparisons, and evaluation figures.

---

## 7. Phase C Outputs

The Phase C outputs are stored under:

```text
results/phase-c/
```

The current Phase C result structure is:

```text
results/phase-c/
├── figures/
├── PHASE_C_SUMMARY.md
├── delhi_regression_features.csv
├── regression_baseline_improvement.csv
├── regression_coefficients.csv
├── regression_model_comparison.csv
└── regression_predictions.csv
```

### Output Description

**`PHASE_C_SUMMARY.md`**

Contains a summary of the Phase C modelling work and results.

**`delhi_regression_features.csv`**

Contains the regression-ready features generated during Phase C feature engineering.

**`regression_baseline_improvement.csv`**

Contains the comparison of regression performance against the baseline.

**`regression_coefficients.csv`**

Contains regression-model coefficient information.

**`regression_model_comparison.csv`**

Contains the evaluation results used to compare the regression models.

**`regression_predictions.csv`**

Contains actual and predicted next-day PM2.5 values used for model evaluation.

**`figures/`**

Contains the visual evaluation outputs generated during Phase C.

---

## 8. Leakage Prevention

The project uses a **time-ordered split** rather than a random train/test split.

For missing predictor values, the Phase B Linear Regression pipeline uses:

```text
SimpleImputer(strategy="median")
```

inside the scikit-learn pipeline.

This ensures that imputation statistics are learned from the training data during model fitting rather than from the complete dataset.

Lag features are created using previous/current observations only.

Future values must never be used as predictors for a next-day target.

The same time-series principle is maintained during Phase C feature engineering and regression modelling.

---

## 9. Recommended Execution Order

Run the complete project in the following order:

```text
1. Install requirements
        ↓
2. notebooks/01-eda.ipynb
        ↓
3. Verify the processed Delhi dataset
        ↓
4. notebooks/02-preprocessing.ipynb
        ↓
5. Verify Phase B results
        ↓
6. notebooks/phase_c/02-feature-engineering.ipynb
        ↓
7. Verify results/phase-c/delhi_regression_features.csv
        ↓
8. notebooks/phase_c/03_regression_models.ipynb
        ↓
9. Verify Phase C model comparison and prediction files
        ↓
10. Verify results/phase-c/figures/
```

---

## 10. Expected Phase B Checkpoint 1 Results

A successful reproduction of Phase B should produce results close to:

```text
Persistence
MAE  ≈ 19.158
RMSE ≈ 28.002
R²   ≈ 0.705

Linear Regression
MAE  ≈ 18.444
RMSE ≈ 25.451
R²   ≈ 0.756
```

Small differences may occur if the dataset, preprocessing steps, package versions, or notebook code are changed.

---

## 11. Phase C Output Verification

After running the Phase C notebooks, verify that the following files have been generated successfully:

```text
results/phase-c/
├── PHASE_C_SUMMARY.md
├── delhi_regression_features.csv
├── regression_baseline_improvement.csv
├── regression_coefficients.csv
├── regression_model_comparison.csv
├── regression_predictions.csv
└── figures/
```

These outputs provide:

- Regression-ready feature data
- Regression model comparisons
- Test-period predictions
- Regression coefficients
- Baseline-improvement analysis
- Visual evaluation of the regression models

---

## 12. Repository Structure

After completing Phase C, the relevant repository structure is:

```text
AML-2026-P02-Neural_Nomads/
│
├── DATA/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01-eda.ipynb
│   ├── 02-preprocessing.ipynb
│   ├── NOTEBOOK DESCR
│   │
│   └── phase_c/
│       ├── 02-feature-engineering.ipynb
│       └── 03_regression_models.ipynb
│
├── results/
│   ├── figures/
│   ├── metrics/
│   ├── phase-a/
│   ├── phase-b/
│   │
│   └── phase-c/
│       ├── figures/
│       ├── PHASE_C_SUMMARY.md
│       ├── delhi_regression_features.csv
│       ├── regression_baseline_improvement.csv
│       ├── regression_coefficients.csv
│       ├── regression_model_comparison.csv
│       └── regression_predictions.csv
│
├── src/
│
├── .gitignore
├── README.md
├── REPRODUCIBILITY.md
└── requirements.txt
```

---

## 13. Requirements

The current `requirements.txt` contains:

```text
pandas
numpy
matplotlib
seaborn
scikit-learn
jupyter
```

Install all dependencies using:

```bash
pip install -r requirements.txt
```

---

## 14. Current Scope

This reproducibility guide covers the work currently completed through:

- **Phase A — EDA and Data Preparation**
- **Phase B — Checkpoint 1**
- **Phase C — Feature Engineering and Regression Models**

Phase A establishes the cleaned and processed Delhi air-quality dataset.

Phase B establishes the next-day PM2.5 prediction target, persistence baseline, chronological evaluation procedure, and initial Linear Regression model.

Phase C extends this work with additional feature engineering and regression modelling, including model comparisons, predictions, coefficient analysis, baseline-improvement analysis, and regression visualisations.

Future project stages should be added to this reproducibility guide as they are completed.
