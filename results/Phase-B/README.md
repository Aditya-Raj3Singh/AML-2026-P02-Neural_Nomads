# P02: Next-Day Air Quality for an Indian City

## Phase B — Checkpoint 1

This folder contains the Checkpoint-1 work for the Delhi next-day air-quality project. Phase A selected Delhi, created a continuous daily calendar and documented the missing-data situation. Phase B tests whether a first machine-learning model can beat a simple persistence forecast.

## What Phase B does

1. Loads the reindexed Delhi daily series produced in Phase A.
2. Creates the next-day PM2.5 target.
3. Builds the persistence baseline: **tomorrow's PM2.5 = today's PM2.5**.
4. Creates a small lag feature set using recent PM2.5 plus PM10, NO2 and CO.
5. Verifies one row by hand to confirm the lag direction and avoid look-ahead leakage.
6. Uses a time-ordered split: **2015–2019 for training, 2020 for testing**.
7. Fits a first Linear Regression model. Missing predictor values are median-imputed using training data only.
8. Compares Linear Regression with persistence on the same 182 test dates.

## Checkpoint-1 results

| Model | MAE | RMSE | R² | Test rows |
|---|---:|---:|---:|---:|
| Persistence | 19.158 | 28.002 | 0.705 | 182 |
| Linear Regression | 18.444 | 25.451 | 0.756 | 182 |

Linear Regression reduces MAE by about **3.73%** and RMSE by about **9.11%** compared with persistence. The gain is moderate, which is reasonable because persistence is already a strong baseline for daily pollution.

## Feature definition

Lags are named relative to the day being predicted. For example, when predicting 11 January, `lag1` is 10 January, `lag2` is 9 January and `lag3` is 8 January. This convention is written explicitly in the notebook to avoid shift-direction mistakes.

The Checkpoint-1 feature set is intentionally small:

- PM2.5 lag 1, lag 2 and lag 3
- PM10 lag 1
- NO2 lag 1
- CO lag 1

Full lag/rolling/calendar feature engineering is left for the next project phase.

## Folder contents

```text
P02_phaseB/
├── data/
│   └── processed/
│       └── delhi_daily_reindexed.csv
├── notebooks/
│   └── checkpoint1.ipynb
├── results/
│   ├── checkpoint1_metrics.csv
│   └── checkpoint1_predictions.csv
├── README.md
└── requirements.txt
```

## How to run

1. Keep the folder structure above.
2. Open `notebooks/checkpoint1.ipynb`.
3. Run the cells from top to bottom.
4. The notebook writes the metrics and test predictions into `results/`.

The notebook does not randomly shuffle dates. The test period is held out chronologically to represent forecasting future observations from past data.

## What comes next

After Checkpoint 1, the preprocessing phase should create the complete lag set, 3-day and 7-day rolling statistics, calendar/cyclic features and both t+1 targets. Later models can then include Ridge, Lasso, a nonlinear regressor and the AQI-bucket classifiers.

## Git checkpoint

After the notebook has been run successfully in the group repository and the generated files have been checked, commit the Phase B work, create the tag `checkpoint-1`, and push the commit and tag.
