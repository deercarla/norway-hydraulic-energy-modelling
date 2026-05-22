# Norwegian Hydro Power Forecasting Project

This repository contains a data analysis and forecasting project for Norwegian electricity generation, with a focus on hydro power and its seasonal behavior.

## Overview

- `analysis.Rmd` / `analysis.html`: Main forecasting analysis of Norway's hydro power generation.
- `energy_data_loading.Rmd` / `energy_data_loading.html`: Data loading, cleaning, and transformation for the raw Norwegian energy dataset.
- `reservoir_data_loading.Rmd` / `reservoir_data_loading.html`: Additional work related to reservoir inflow and storage datasets.

## Key goals

- Load and clean Norwegian hydro and electricity generation data.
- Compare total electricity generation with hydro generation.
- Analyze seasonality and trends in hydro power output.
- Build time series forecasting models with `fable` and `tsibble`.
- Evaluate ARIMA and ETS models for future hydro generation forecasting.

## Data

Source files are stored in the `data/` folder:

- `data/hydro-dataset.csv` - raw Norwegian energy dataset used for the analysis.
- `data/total_electricity_norway.csv` - derived total electricity generation series.
- `data/hydro_norway.csv` - derived hydro power generation series.
- `data/NVE_ReservoirInflow_1958-2026_GWh.csv` - reservoir inflow dataset.
- `data/reservoir_fill_rate.csv` - reservoir fill rate dataset.

## Files

- `analysis.Rmd` - main R Markdown analysis and forecasting workflow.
- `energy_data_loading.Rmd` - code for raw data ingestion and dataset preparation.
- `reservoir_data_loading.Rmd` - reservoir and inflow dataset processing.
- `analysis.html` - generated HTML report for the main analysis.
- `energy_data_loading.html` - generated HTML report for the data loading workflow.
- `reservoir_data_loading.html` - generated HTML report for reservoir data work.

## Requirements

This project is built in R and assumes the following package families are installed:

- `tidyverse` (or at least `readr`, `dplyr`, `tidyr`, `ggplot2`)
- `tsibble`
- `fable`
- `feasts`
- `lubridate`
- `janitor`

If you use `renv`, restore the environment with:

```r
# install if needed
install.packages("renv")
renv::restore()
```

## How to run

1. Open the project in RStudio or another R-compatible editor.
2. Knit `analysis.Rmd` to generate the forecast report.
3. Knit `energy_data_loading.Rmd` to reproduce the data preprocessing steps.
4. Knit `reservoir_data_loading.Rmd` to reproduce reservoir-related data workflows.

## Notes

- The main analysis focuses on monthly hydro power generation in Norway.
- Forecasting compares multiple time series models and selects the best-performing seasonal model.
- The repository includes both source R Markdown files and rendered HTML outputs for review.
