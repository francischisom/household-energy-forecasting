# Forecasting Household Appliance Energy Consumption

A comparative time-series study forecasting hourly appliance energy use on the
UCI Appliances Energy Prediction dataset, benchmarking naive, statistical,
machine-learning and foundation models over a 14-day horizon.

**Module:** 7PAM2032 Time Series Forecasting — Assignment 2

## Overview

The goal is not only to find the most accurate model, but the one best suited to
practical deployment once bias, uncertainty, interpretability and the information
genuinely available at the forecast origin are all taken into account. Five model
families are compared on a common test window:

- **Benchmarks** — mean, naive, drift, daily and weekly seasonal-naive
- **Statistical** — SARIMA (target-only) and SARIMAX (with exogenous variables)
- **Machine learning** — recursive XGBoost on engineered lag/calendar features
- **Foundation models** — Chronos-Bolt and Chronos-2 (zero-shot)

## Key Findings

- Demand follows a **weekly** behavioural routine — the weekly seasonal-naive
  (MASE 0.798) is the benchmark to beat, far ahead of the daily one (MASE 1.628).
- A parsimonious **SARIMA** beats that benchmark by ~16% (MASE 0.670); adding
  weather does **not** help.
- **Recent history and time-of-day carry almost all the signal**; adding sensor
  and weather features degrades the machine-learning model.
- **Chronos-2** has the lowest MASE (0.619) but systematically under-forecasts
  peaks (bias -18.98 Wh) and gives no uncertainty interval.
- **Recommendation: deploy SARIMA** — near-zero bias, calibrated intervals,
  interpretable, and dependent only on data known at the forecast origin.

## Repository Structure

```
.
├── Appliance_Energy_Forecast.ipynb    # Main analysis notebook (Parts 1-9)
├── README.md                          # This file
├── requirements.txt                   # Python dependencies
├── .gitignore                         # Files git should ignore
├── data/
│   ├── raw/                           # Raw UCI dataset (not tracked)
│   └── processed/                     # Hourly aggregated series
├── outputs/
│   ├── figures/                       # Generated plots
│   ├── forecasts/                     # Per-model forecast CSVs
│   └── metrics/                       # Model comparison and answers
└── report/
    └── Appliance_Energy_Report.docx   # Final written report
```

## Dataset

UCI Appliances Energy Prediction — 19,735 observations at 10-minute resolution
(Jan-May 2016), aggregated to 3,290 hourly observations. The final 14 days
(336 hours) are held out as the test window.
Source: https://archive.ics.uci.edu/dataset/374/appliances+energy+prediction

## Setup and Usage

```bash
# Clone
git clone https://github.com/<your-username>/appliance-energy-forecasting.git
cd appliance-energy-forecasting

# (Optional) virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the notebook
jupyter notebook Appliance_Energy_Forecast.ipynb
```

Run the cells top to bottom; figures and metrics are written to `outputs/`.

## Requirements

Python 3.10+ with: numpy, pandas, matplotlib, seaborn, scipy, statsmodels,
scikit-learn, xgboost. Foundation models additionally need torch and
chronos-forecasting (the notebook falls back gracefully if these are unavailable).

## Evaluation Metrics

MAE and RMSE (absolute error, in Wh), MASE (scaled against an in-sample daily
seasonal-naive; < 1 beats that baseline), and Bias (mean signed error, to expose
systematic over/under-forecasting).


