# Phase C - Feature Engineering and Regression

## Inputs
- `results/delhi_daily_reindexed.csv` from Phase A.

## Notebooks
- `notebooks/phase_c/02-feature-engineering.ipynb`
- `notebooks/phase_c/03-regression-models.ipynb`

## Feature engineering
- t+1 PM2.5 target.
- Current PM2.5 plus lags 1, 2, 3, 7 and 14 days.
- Rolling means over 3, 7 and 14 days and 7-day standard deviation.
- Month, day-of-week and day-of-year features.
- Manual rolling-window verification passed with no t+1 look-ahead.

## Evaluation split
- Train: 2015-01-01 through 2019-12-31.
- Test: 2020-01-01 through 2020-06-30.
- Predictor missing values are median-imputed inside the training pipeline.

## Results
| Model | MAE | RMSE | R2 |
|---|---:|---:|---:|
| Ridge Regression | 18.129 | 24.999 | 0.765 |
| Linear Regression | 18.127 | 25.000 | 0.765 |
| Lasso Regression | 18.134 | 25.000 | 0.765 |
| Persistence Baseline | 19.158 | 28.002 | 0.705 |

Best regression model: **Ridge Regression**.

Ridge RMSE improvement over persistence: **10.73%**.
Best Ridge alpha: **0.4941713361323833**.
Best Lasso alpha: **0.1484968262254465**.
