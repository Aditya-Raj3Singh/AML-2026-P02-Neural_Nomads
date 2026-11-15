# Reproducibility Guide

## Project

**AML-2026-P02-Neural_Nomads — Next-Day Air Quality for an Indian City**

This guide describes how to reproduce the work currently completed in
**Phase A** and **Phase B (Checkpoint 1)**.

## 1. Environment Setup

Python 3.9 or newer is recommended.

From the repository root, install the required packages:

``` bash
pip install -r requirements.txt
```

The current project uses:

- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- Jupyter Notebook

> Note: Phase A was developed in Google Colab and contains some
> Colab-specific setup/download cells. When running locally, use the
> repository data paths instead of the Colab-specific path where
> necessary.

## 2. Data

The original dataset used by the project is:

``` text
city_day.csv
```

It contains daily air-quality observations for multiple Indian cities.

Phase A selects **Delhi** and creates the processed daily dataset:

``` text
delhi_daily_reindexed.csv
```

In the current repository, the data folders have non-standard names such
as `DATA[`, `RAW[`, and `processed [`. For reliable reproduction, it is
recommended to rename them to:

``` text
data/
├── raw/
│   └── city_day.csv
└── processed/
    └── delhi_daily_reindexed.csv
```

The Phase B notebook searches for the processed dataset using standard
`data/processed/...` paths, so normalizing these folder names is
recommended before reproducing the pipeline locally.

## 3. Phase A — EDA and Data Preparation

Run:

``` text
notebooks/01-eda.ipynb
```

Phase A performs the following work:

1.  Loads the original `city_day.csv` dataset.
2.  Parses and checks the date column.
3.  Selects Delhi as the target city.
4.  Checks invalid dates, missing city values, and duplicate city-date
    records.
5.  Examines overall and year-wise missingness.
6.  Examines PM2.5 availability.
7.  Produces exploratory plots, including:
    - missingness heatmap
    - PM2.5 over time
    - monthly PM2.5 distribution
8.  Creates the processed Delhi daily dataset used by later phases.

The processed Delhi dataset covers:

``` text
2015-01-01 to 2020-07-01
```

with 2009 daily rows.

Important Phase A outputs include:

``` text
results/figures/missingness_heatmap.png
results/figures/pm25_over_time.png
results/figures/pm25_monthly_distribution.png

results/phase-a/eda_summary.csv
results/phase-a/missingness_by_year.csv
results/phase-a/missingness_observed_rows.csv
results/phase-a/missing_data_policy.md
```

and the processed dataset:

``` text
data/processed/delhi_daily_reindexed.csv
```

## 4. Phase B — Checkpoint 1

After Phase A has produced `delhi_daily_reindexed.csv`, run:

``` text
notebooks/02-checkpoint1.ipynb
```

Phase B Checkpoint 1 performs:

1.  Loading of the processed Delhi dataset.
2.  Creation of the next-day PM2.5 target: `PM2.5_target_t_plus_1`.
3.  A chronological train/test split.
4.  A persistence baseline where today’s PM2.5 is used as the prediction
    for tomorrow.
5.  Initial lag-based feature engineering.
6.  A manual lag/leakage sanity check.
7.  Median imputation inside the scikit-learn pipeline.
8.  Linear Regression training.
9.  Evaluation using MAE, RMSE, and R².
10. Comparison of Linear Regression against the persistence baseline.
11. Export of metrics and test-period predictions.

### Chronological split

The model uses observations before 2020 for training and 2020 as the
held-out test period.

The evaluated test period is:

``` text
2020-01-01 to 2020-06-30
```

The same test dates are used when comparing the persistence baseline and
Linear Regression.

## 5. Phase B Outputs

The current Checkpoint 1 results are:

| Model             |    MAE |   RMSE |    R² |
|-------------------|-------:|-------:|------:|
| Persistence       | 19.158 | 28.002 | 0.705 |
| Linear Regression | 18.444 | 25.451 | 0.756 |

The generated result files are:

``` text
results/phase-b/checkpoint1_metrics.csv
results/phase-b/checkpoint1_predictions.csv
```

`checkpoint1_predictions.csv` contains the actual next-day PM2.5 values
together with the persistence and Linear Regression predictions for the
held-out test dates.

## 6. Leakage Prevention

The project uses a time-ordered split rather than a random train/test
split.

For missing predictor values, the Phase B Linear Regression pipeline
uses:

``` text
SimpleImputer(strategy="median")
```

inside the scikit-learn pipeline. This ensures that imputation
statistics are learned from the training data during model fitting
rather than from the complete dataset.

Lag features are created using previous/current observations only;
future values must never be used as predictors for a next-day target.

## 7. Recommended Execution Order

Run the project in this order:

``` text
1. Install requirements
        ↓
2. notebooks/01-eda.ipynb
        ↓
3. Verify/create delhi_daily_reindexed.csv
        ↓
4. notebooks/02-checkpoint1.ipynb
        ↓
5. Verify checkpoint1_metrics.csv
        ↓
6. Verify checkpoint1_predictions.csv
```

## 8. Expected Checkpoint 1 Results

A successful reproduction should produce results close to:

``` text
Persistence
MAE  ≈ 19.158
RMSE ≈ 28.002
R²   ≈ 0.705

Linear Regression
MAE  ≈ 18.444
RMSE ≈ 25.451
R²   ≈ 0.756
```

Small differences may occur if the dataset, preprocessing steps, package
versions, or notebook code are changed.

## 9. Current Scope

This reproducibility guide covers the work currently completed through
**Phase B — Checkpoint 1**.

Full rolling/calendar feature engineering, regularized regression
models, nonlinear regression models, AQI-category classification, and
final model selection belong to later project stages and are not part of
this checkpoint.
