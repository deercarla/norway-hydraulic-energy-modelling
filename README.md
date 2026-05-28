# Norwegian Hydro Power Forecasting

This repository contains the full data analysis and forecasting pipeline for Norwegian hydro power generation, developed as a final exam project for **CDSCO1005U — Predictive Analytics** at CBS.

## Overview

The project analyses 399 monthly observations of Norwegian hydro power generation (January 1993 – March 2026) from Statistics Norway, and benchmarks five time series forecasting models across a common 24-month test set (April 2024 – March 2026).

- `analysis.Rmd` / `analysis.html`: Main forecasting analysis — model estimation, diagnostics, and forecast evaluation.
- `energy_data_loading.Rmd` / `energy_data_loading.html`: Data loading, cleaning, and transformation for the raw Norwegian energy dataset.
- `reservoir_data_loading.Rmd` / `reservoir_data_loading.html`: Reservoir fill rate data ingestion and preprocessing (NVE source).

## Key findings

Norwegian hydro power generation is characterised by strong, stable annual seasonality (peaking in winter, troughing in June–July) and occasional hydrological shocks from drought periods. No permanent structural break was found over the full sample.

**Out-of-sample forecast accuracy (24-month test set, ranked by MASE):**

| Model | RMSE | MAE | MAPE | MASE |
|---|---|---|---|---|
| ETS(M,N,A) | 0.841 | 0.608 | 4.97% | **0.464** |
| Airline SARIMA(0,1,1)(0,1,1)[12] | 0.841 | 0.615 | 5.08% | 0.469 |
| ARIMAX (fill rate lag 1) | 0.841 | 0.656 | 5.51% | 0.500 |
| VAR(5) | 0.898 | 0.692 | 5.91% | 0.528 |
| Manual SARIMA(3,0,0)(1,1,0)[12] | 1.276 | 1.038 | 8.41% | 0.791 |

All MASE values are below 1, indicating every model outperforms the naïve seasonal benchmark. The **ETS(M,N,A)** model achieved the best out-of-sample performance, closely followed by the Airline SARIMA. Despite substantially better in-sample fit (AICc 739.65 vs 835.43), the ARIMAX model ranked third out-of-sample, consistent with overfitting in the automatic ARIMA error structure.

## Models

| Model | Type | Notes |
|---|---|---|
| Manual SARIMA(3,0,0)(1,1,0)[12] | Univariate | Manually identified via Box-Jenkins ACF/PACF inspection |
| Airline SARIMA(0,1,1)(0,1,1)[12] | Univariate | Classical benchmark; best AICc among ARIMA specs (923.10) |
| ETS(M,N,A) | Univariate | Auto-selected; multiplicative errors, no trend, additive seasonality |
| ARIMAX(1,0,1)(1,1,2)[12] | Exogenous regressor | Lagged reservoir fill rate (NVE); fill rate coefficient β̂ = 0.087 (p < 0.001) |
| VAR(5) | Multivariate | Jointly models hydro generation and reservoir fill rate; Granger causality confirmed (F = 86.27, p < 0.001) |

## Data

Source files are stored in the `data/` folder:

| File | Description |
|---|---|
| `data/hydro-dataset.csv` | Raw Norwegian energy dataset (Statistics Norway) |
| `data/total_electricity_norway.csv` | Derived total electricity generation series |
| `data/hydro_norway.csv` | Derived hydro power generation series (TWh, monthly) |
| `data/NVE_ReservoirInflow_1958-2026_GWh.csv` | Reservoir inflow dataset (NVE) |
| `data/reservoir_fill_rate.csv` | Monthly reservoir fill rate (% of total capacity, NVE) |

**Sources:**
- Statistics Norway (SSB): https://www.ssb.no/en/energi-og-industri/energi/statistikk/elektrisitet
- NVE reservoir statistics: https://api.nve.no/doc/magasinstatistikk

## Requirements

Built in R. The following packages are required:

```r
# Core
tidyverse     # readr, dplyr, tidyr, ggplot2
tsibble       # time series tibbles
fable         # forecasting models (ARIMA, ETS, VAR)
feasts        # feature extraction and ACF/PACF tools
fabletools    # forecast evaluation
lubridate
janitor

# Statistical tests
urca          # KPSS / ADF unit root tests
strucchange   # Chow and QLR structural break tests
vars          # VAR estimation
```

If using `renv`, restore the environment with:

```r
install.packages("renv")
renv::restore()
```

## How to run

1. Open the project in RStudio.
2. Knit `energy_data_loading.Rmd` to reproduce data preprocessing.
3. Knit `reservoir_data_loading.Rmd` to reproduce reservoir data workflows.
4. Knit `analysis.Rmd` to reproduce the full forecasting analysis and generate the report.

## Notes

- The analysis uses a **train/test split** with training data prior to April 2024 and a 24-month test set (April 2024 – March 2026).
- Model selection uses AIC/AICc/BIC on the training set; out-of-sample accuracy is evaluated using RMSE, MAE, MAPE, and MASE.
- Residual diagnostics are included for all models. Some residual autocorrelation persists across all specifications, attributed to irregular hydrological shocks (drought years) that linear time series models cannot fully absorb.
- The ARIMAX model uses a **one-month lag** on reservoir fill rate to avoid simultaneity bias.
- The VAR model is estimated on the **seasonally differenced series** and de-differenced for forecast comparison.
