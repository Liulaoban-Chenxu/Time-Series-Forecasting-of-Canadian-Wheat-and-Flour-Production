# Analysis of Wheat Flour Production in Canada

A time-series project using R to model monthly total wheat flour production in Canada with unregularized regression, regularized regression, exponential smoothing, and seasonal ARIMA methods.

The analysis covers data from September 2004 through August 2024 and evaluates each model on a 12-month validation period from September 2023 through August 2024.

## Project Information

| Item | Details |
|---|---|
| Group | Group 5 |
| Registered project title | *Analysis of Wheat Flour Produced in Canada From Sept. 2004 to Sept. 2021* |
| Current report coverage | September 2004 - August 2024 |

## Project Overview

This project investigates the trend and seasonal structure of monthly wheat flour production and compares several forecasting approaches:

- Polynomial regression with and without monthly seasonality
- Ridge regression
- Elastic net regression
- Lasso regression
- Simple and double exponential smoothing
- Additive and multiplicative Holt-Winters models
- Seasonal ARIMA models

Average prediction squared error (APSE) is used to compare out-of-sample performance. Lower APSE indicates better validation performance.

## Data Source

The data come from [Statistics Canada Table 32-10-0016-01, *Milled wheat and wheat flour produced (x 1,000)*](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210001601&cubeTimeFrame.startMonth=09&cubeTimeFrame.startYear=2004&cubeTimeFrame.endMonth=09&cubeTimeFrame.endYear=2024&referencePeriods=20040901%2C20240901). The official table is monthly and covers the geography of Canada.

The analysis uses `Data_Group 5.csv` and filters the dataset to retain observations classified as:

```text
Total wheat flour produced
```

The filtered observations are converted to a monthly R time series with frequency 12.

The Fligner-Killeen test reported a p-value of 0.7989, providing no evidence of unequal variance across the groups used in the analysis. The decomposition and autocorrelation plots indicate pronounced seasonality and a changing long-term trend.

## Methodology

### 1. Polynomial Regression

Polynomial trend models from degree 1 through degree 10 are evaluated both with and without monthly seasonal indicators. The strongest unregularized specification is the degree-4 trend-and-seasonality model.

### 2. Regularized Regression

The project compares three regularization settings using `glmnet`:

| Model | Alpha |
|---|---:|
| Ridge | 0.0 |
| Elastic net | 0.5 |
| Lasso | 1.0 |

Ten-fold cross-validation selects `lambda.1se` for each polynomial degree. Models with trend only and models combining trend with seasonal indicators are evaluated separately.

### 3. Exponential Smoothing

Four specifications are compared:

- Simple exponential smoothing
- Double exponential smoothing
- Additive Holt-Winters
- Multiplicative Holt-Winters

The multiplicative Holt-Winters model has the lowest APSE within this family.

### 4. Box-Jenkins Models

The series is transformed using one regular difference and one seasonal difference with period 12. Nine SARIMA candidates are then evaluated. The lowest validation APSE in this family is produced by:

```text
SARIMA(0,1,1)(0,1,1)_[12]
```

## Validation Results

The best-reported model from each family is summarized below.

| Model family | Best specification | Validation APSE |
|---|---|---:|
| Unregularized regression | Degree 4, trend and seasonality | 84.4782 |
| Regularized regression | Elastic net, degree 3, trend and seasonality | **76.1455** |
| Exponential smoothing | Multiplicative Holt-Winters | 101.1752 |
| Seasonal ARIMA | SARIMA(0,1,1)(0,1,1)_[12] | 89.4524 |

Among all reported validation results, the degree-3 elastic net model with trend and seasonality has the lowest APSE.

## Final Forecast Presented in the Report

The report ultimately presents the degree-4 unregularized polynomial regression with monthly seasonality as its final forecasting model. It is fitted to the full time series and used to generate a 12-month forecast with 95% prediction intervals.

This reported final choice is not the same as selecting strictly by the smallest APSE: the elastic net model records the lowest validation error. This distinction should be considered when reproducing the analysis or revising the model-selection rule.

## Requirements

- R
- RStudio, optional but recommended
- The following R packages:

```r
install.packages(c(
  "astsa",
  "car",
  "glmnet",
  "knitr",
  "rmarkdown"
))
```

The time-series, regression, plotting, and Holt-Winters functions used in the project are otherwise available from base R and its recommended packages.

The script uses `set.seed(443)` when cross-validating the regularized regression models, which supports reproducibility of the selected folds and tuning results.

## Main Outputs

Running the analysis produces:

- A time-series plot and autocorrelation function
- An additive decomposition into trend, seasonal, and random components
- Regression and regularized-regression diagnostics
- APSE comparison tables
- Holt-Winters forecasts and residual diagnostics
- Differenced-series ACF and PACF plots
- SARIMA diagnostics and validation forecasts
- A final 12-month forecast with prediction intervals

## Limitations

- The forecasts use only historical production and seasonality; no external economic, agricultural, or weather-related predictors are included.
- Several model specifications are compared on the same validation set, so the reported APSE values should not be interpreted as performance on an independent final test set.

## Authors

- Chenxu Liu
- Yuqi Li
- Yitian Min
- Yulin Zhang

## Report

See [`Codes_Group5.pdf`](Codes_Group5.pdf) for the complete analysis, model diagnostics, tables, and forecast figures.

