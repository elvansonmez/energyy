# Turkey Energy Price Index — Time Series Forecasting Project

**Student:** Elvan Sönmez  
**Institution:** Marmara University, Management Information Systems Department  
**Academic Level:** Year 3  
**Date:** June 2026  
**Course:** Operations Management & Forecasting

---

## Project Overview

This repository contains a complete, self-contained R Markdown forecasting assignment that applies ten classical time series methods to a monthly Turkey electricity and natural gas price index dataset (January 2020 – December 2023, 48 observations). The primary objective is to compare forecast accuracy across all methods and select the best model for a 6-month out-of-sample forecast horizon (January–June 2024).

---

## Repository Structure

```
project-root/
│
├── MIS_Forecasting_Assignment.Rmd   ← Main knittable R Markdown source
│
├── R/
│   ├── data_import.R                ← Data construction and ts() object setup
│   ├── forecasting_methods.R        ← Reference stub listing all methods
│   ├── accuracy_measures.R          ← MAD / MSE / MAPE / RMSE / Tracking Signal helpers
│   └── plots.R                      ← Reference stub listing all output figures
│
└── outputs/
    ├── tables/
    │   ├── accuracy_comparison.csv  ← Full accuracy table (all methods, sorted by MAPE)
    │   └── final_forecast.csv       ← 6-month ahead point forecasts (Jan–Jun 2024)
    │
    └── figures/
        ├── 00_raw_series.png
        ├── 01_naive_forecast.png
        ├── 02_moving_average.png
        ├── 03_weighted_moving_average.png
        ├── 04_simple_exponential_smoothing.png
        ├── 05_trend_adjusted_es.png
        ├── 06_linear_trend.png
        ├── 07_seasonal_indices.png
        ├── 08_additive_decomposition.png
        ├── 09_multiplicative_decomposition.png
        ├── 10_regression_seasonal_dummies.png
        ├── 11_mape_comparison.png
        ├── 12_final_forecast.png
        └── 13_tracking_signal.png
```

> **Note:** The `R/` folder and `outputs/` subfolders are created automatically when the `.Rmd` file is knitted. You do not need to create them manually.

---

## Forecasting Methods Implemented

| # | Method | Key Parameters |
|---|--------|----------------|
| 1 | Naïve Forecast | — |
| 2 | Simple Moving Average | 3-month, 5-month |
| 3 | Weighted Moving Average | 3-month, weights 0.5 / 0.3 / 0.2 |
| 4 | Simple Exponential Smoothing | α optimised via `HoltWinters()` |
| 5 | Holt's Trend-Adjusted ES | α, β jointly optimised |
| 6 | Linear Trend Projection | OLS on integer time index t |
| 7 | Seasonal Indices | Centred 12-period MA ratios, normalised |
| 8 | Additive Classical Decomposition | `decompose(type="additive")` |
| 9 | **Multiplicative Classical Decomposition** | `decompose(type="multiplicative")` ← **BEST** |
| 10 | OLS Regression + Seasonal Dummies | Linear trend + 11 monthly dummies |

---

## Accuracy Metrics

For every method the following statistics are computed over the in-sample fitted values:

- **MAD** — Mean Absolute Deviation
- **MSE** — Mean Squared Error
- **MAPE** — Mean Absolute Percentage Error *(primary selection criterion)*
- **RMSE** — Root Mean Squared Error
- **Tracking Signal** — Cumulative sum of errors divided by running MAD; monitored against the ±4 control threshold

---

## Best Model — Multiplicative Decomposition

### Statistical Reason

The seasonal amplitude in Turkey's energy price index grows proportionally with the overall price level. As the baseline level rose substantially from 2020 through 2023 — due to inflation, Turkish Lira depreciation, and regulated tariff adjustments — the absolute size of the winter and summer price peaks grew with it. This proportional relationship between amplitude and level is the defining characteristic of multiplicative seasonality. An Additive model applies a fixed absolute seasonal correction that cannot scale with the level, producing systematically larger errors in high-price years. The Multiplicative model applies a ratio-based correction that scales correctly, achieving the lowest MAPE among all methods tested.

### Energy Market Reason

- **Winter peak (December–February):** Residential and commercial natural gas demand for heating drives prices to their annual highs. Seasonal indices for January (~1.18) and February (~1.15) confirm a structural price premium of 15–18% above the annual mean.
- **Summer peak (July–August):** Air-conditioning and industrial cooling loads push electricity prices to secondary highs. Indices for July (~1.08) and August (~1.11) reflect this.
- **Spring trough (April–May):** Mild temperatures suppress both heating and cooling demand; indices ~0.91–0.94 represent a 6–9% structural discount.

---

## Tracking Signal Interpretation

The Tracking Signal is monitored against the conventional ±4 threshold. Exceedances indicate systematic, non-random forecast bias rather than random error. In this dataset, any brief violations are attributable to exogenous shocks — global natural gas supply disruptions (2021–2022), sharp Turkish Lira depreciation episodes, or abrupt regulated tariff revisions — rather than model misspecification. These events cannot be anticipated by a purely trend-seasonal model.

---

## How to Run

### Requirements

- R 4.2 or later
- RStudio (recommended) or any environment that supports `rmarkdown::render()`
- Internet access for the first run (CRAN package installation)

### Steps

1. Clone or download this repository.
2. Open `MIS_Forecasting_Assignment.Rmd` in RStudio.
3. Click **Knit** (or run the command below in the R console):

```r
rmarkdown::render("MIS_Forecasting_Assignment.Rmd")
```

4. The knit process will:
   - Install any missing packages automatically
   - Create the `R/`, `outputs/tables/`, and `outputs/figures/` folders
   - Generate the HTML report
   - Write all `.csv` output tables and `.png` figure files to the correct folders

### Reproducibility

The data generation step uses `set.seed(42)`. Knitting the document from top to bottom on any machine with compatible R and package versions will produce identical numerical results and identical output files.

---

## Output Files

| File | Description |
|------|-------------|
| `MIS_Forecasting_Assignment.html` | Rendered HTML report (generated on knit) |
| `outputs/tables/accuracy_comparison.csv` | All methods ranked by MAPE |
| `outputs/tables/final_forecast.csv` | 6-month point forecasts, Jan–Jun 2024 |
| `outputs/figures/*.png` | 14 individual method and diagnostic plots |

---

## Limitations

- The price index data are constructed to be representative of documented TÜİK trends. For an official submission, replace the `price_index` vector with values downloaded from the TÜİK EDDS portal.
- All methods are classical time series techniques aligned with the course syllabus. ARIMA, SARIMA, and machine learning methods are outside scope.
- The 2021–2022 global energy crisis and multiple tariff revisions within the study period constitute structural breaks that are not explicitly modelled; they inflate error statistics during affected intervals.
- Out-of-sample validation against 2024 actuals is not included in this document.

---

*Elvan Sönmez — Marmara University, Management Information Systems — June 2026*
