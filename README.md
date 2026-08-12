# Appliance Energy Time-Series Forecasting and Analysis

**Student:** Md Al Arafat
**Student ID:** 23088114

---

## 1. Project Overview

This project investigates short-term household appliance energy forecasting using the **UCI Appliance Energy Prediction dataset**.

The original dataset contains appliance energy measurements recorded at 10-minute intervals together with indoor environmental measurements, outdoor weather variables and other sensor information. The data were aggregated to an hourly frequency and used to forecast appliance energy consumption over a **24-hour forecasting horizon**.

Several forecasting approaches were implemented and compared:

* Mean forecast
* Naive forecast
* Daily Seasonal Naive
* Weekly Seasonal Naive
* Drift
* SARIMAX
* XGBoost
* Chronos time-series foundation model

The main aim was to determine whether increasingly complex models provide meaningful improvements over simple forecasting benchmarks.

---

## 2. Objectives

The main objectives of the project were to:

1. Prepare and clean the Appliance Energy Prediction dataset.
2. Convert the original 10-minute observations into hourly observations.
3. Explore trends, seasonality and autocorrelation in appliance energy consumption.
4. Assess stationarity using time-series statistical methods.
5. Establish benchmark forecasting models.
6. Develop and evaluate a seasonal SARIMAX model.
7. Incorporate sensor, weather and temporal features into a feature-based XGBoost model.
8. Evaluate a time-series foundation model using Chronos.
9. Compare all models using common forecasting metrics.
10. Investigate residual behaviour and model limitations.
11. Identify the most suitable model for practical short-term appliance-energy forecasting.

---

## 3. Dataset

The project uses the publicly available:

**UCI Appliance Energy Prediction Dataset**

Dataset source:

[https://archive.ics.uci.edu/ml/machine-learning-databases/00374/energydata_complete.csv](https://archive.ics.uci.edu/ml/machine-learning-databases/00374/energydata_complete.csv)

The dataset contains approximately four and a half months of household measurements recorded every 10 minutes.

Important variables include:

* `date` – timestamp
* `Appliances` – appliance energy consumption
* Indoor temperature measurements
* Indoor humidity measurements
* Outdoor temperature measurements
* Outdoor humidity measurements
* Weather-related variables
* Other environmental sensor variables

The target variable used in this project is:

**`Appliances`**

---

## 4. Data Preprocessing

The preprocessing pipeline included:

1. Loading the original CSV dataset.
2. Converting the `date` column to a datetime format.
3. Sorting observations chronologically.
4. Checking data quality and missing values.
5. Setting the timestamp as the time-series index.
6. Resampling the original 10-minute observations to hourly observations.
7. Investigating the resulting hourly appliance-energy series.
8. Creating temporal and lagged features for the machine-learning model.

Hourly aggregation was used because it reduces high-frequency noise while retaining important daily and weekly consumption patterns.

The chronological ordering of the data was preserved throughout the analysis to avoid future information leaking into the training data.

---

## 5. Exploratory Data Analysis

The exploratory analysis investigated:

* Overall appliance-energy consumption
* Distribution of the target variable
* Trend behaviour
* Daily seasonality
* Weekly seasonality
* Autocorrelation
* Hourly consumption patterns
* Day-of-week differences
* Stationarity

The analysis identified meaningful temporal dependence.

The calculated autocorrelation values were:

|       Lag | Autocorrelation |
| --------: | --------------: |
|  24 hours |          0.3061 |
| 168 hours |          0.3295 |

The weekly autocorrelation was slightly stronger than the daily autocorrelation, indicating that household appliance consumption contains an important weekly behavioural component.

The average appliance consumption also varied substantially throughout the day, with particularly high consumption occurring during the evening period.

---

## 6. Forecasting Problem

The forecasting problem was defined as:

> **Predict the next 24 hourly observations of household appliance energy consumption using information available before the forecast period.**

A chronological train/test approach was used rather than random splitting.

The final 24-hour period was used as the evaluation horizon for the forecasting experiments.

The primary evaluation metrics were:

* **RMSE** – Root Mean Squared Error
* **MAE** – Mean Absolute Error
* **MAPE** – Mean Absolute Percentage Error
* **sMAPE** – Symmetric Mean Absolute Percentage Error

RMSE was used as the primary model-selection metric because it penalises larger forecasting errors more strongly.

---

## 7. Benchmark Models

The following benchmark forecasting methods were implemented:

* Mean
* Naive
* Daily Seasonal Naive
* Weekly Seasonal Naive
* Drift

The **Weekly Seasonal Naive** model was the strongest benchmark.

Its performance was:

| Metric | Weekly Seasonal Naive |
| ------ | --------------------: |
| RMSE   |                292.85 |
| MAE    |                180.00 |
| MAPE   |                22.44% |
| sMAPE  |                23.19% |

The strong performance of the weekly seasonal benchmark indicates that appliance consumption has meaningful recurring weekly behaviour.

---

## 8. SARIMAX Model

A seasonal ARIMA model with exogenous variables was developed to capture temporal dependence and seasonality.

The selected model was:

**SARIMAX(1,0,6) × (1,1,1,24)**

The model-selection process used AIC-based parameter searching.

The final SARIMAX results were:

| Metric |    SARIMAX |
| ------ | ---------: |
| RMSE   | **159.60** |
| MAE    | **112.72** |
| MAPE   | **19.45%** |
| sMAPE  | **17.17%** |

SARIMAX improved the RMSE by approximately:

**45.50%**

compared with the strongest Weekly Seasonal Naive benchmark.

Residual diagnostics were also performed using the Ljung–Box test.

The p-values were:

| Lag | p-value |
| --: | ------: |
|  24 |   0.382 |
|  48 |   0.318 |
| 168 |   0.152 |

All p-values were above 0.05, indicating no statistically significant residual autocorrelation at the tested lags.

However, the Shapiro–Wilk test indicated that the residuals were not normally distributed.

---

## 9. XGBoost Model

A feature-based machine-learning model was developed using **XGBoost**.

The model incorporated multiple groups of explanatory variables, including:

### Lag features

* Previous appliance-energy observations
* Recent consumption history

### Temporal features

* Hour of day
* Day of week
* Other calendar information

### Sensor variables

* Indoor temperature
* Indoor humidity

### Weather variables

* Outdoor environmental measurements

The XGBoost results were:

| Metric |    XGBoost |
| ------ | ---------: |
| RMSE   | **203.19** |
| MAE    | **146.87** |
| MAPE   | **22.15%** |
| sMAPE  | **20.63%** |

XGBoost improved on the strongest benchmark by approximately **30.61%**, but it did not outperform SARIMAX.

This demonstrates that a more flexible machine-learning model does not necessarily provide better forecasting accuracy when the underlying time-series structure is already strong.

---

## 10. Chronos Foundation Model

A time-series foundation model, **Chronos**, was also evaluated.

Chronos generated a 24-hour appliance-energy forecast using historical observations.

Results:

| Metric | Chronos |
| ------ | ------: |
| RMSE   |  308.77 |
| MAE    |  223.19 |
| MAPE   |  28.20% |
| sMAPE  |  35.29% |

Chronos did not outperform the Weekly Seasonal Naive benchmark.

Its RMSE was approximately **5.44% worse** than the strongest benchmark.

Therefore, the additional computational complexity of the foundation model did not provide an accuracy advantage for this particular dataset and forecasting horizon.

---

## 11. Overall Results

The final comparison was:

| Rank | Model                 |       RMSE |        MAE |       MAPE |      sMAPE |
| ---: | --------------------- | ---------: | ---------: | ---------: | ---------: |
|    1 | **SARIMAX**           | **159.60** | **112.72** | **19.45%** | **17.17%** |
|    2 | **XGBoost**           | **203.19** | **146.87** | **22.15%** | **20.63%** |
|    3 | Weekly Seasonal Naive |     292.85 |     180.00 |     22.44% |     23.19% |
|    4 | Mean                  |     300.04 |     236.27 |     40.46% |     37.61% |
|    5 | Chronos               |     308.77 |     223.19 |     28.20% |     35.29% |
|    6 | Daily Seasonal Naive  |     708.26 |     481.67 |     70.61% |     44.20% |
|    7 | Naive                 |    1485.61 |    1455.83 |    298.67% |    110.21% |
|    8 | Drift                 |    1492.55 |    1463.28 |    299.91% |    110.47% |

### Main finding

**SARIMAX achieved the best overall forecasting performance.**

It reduced RMSE by approximately **45.50%** compared with the strongest benchmark.

XGBoost was the second-best model, while Chronos did not outperform the strongest simple benchmark.

---

## 12. Model Recommendation

Based on forecasting accuracy, interpretability, uncertainty estimation and computational requirements, **SARIMAX is recommended as the preferred model for this dataset**.

SARIMAX provides:

* Lowest RMSE
* Lowest MAE
* Strong performance against seasonal benchmarks
* Interpretable temporal structure
* Confidence intervals for forecasts
* Lower complexity than a foundation model

XGBoost remains a useful alternative where reliable future sensor and weather covariates are available.

Chronos may be useful for other datasets, particularly where strong domain-specific temporal structures are not already captured by classical models. However, it did not provide an advantage in this experiment.

---

## 13. Important Forecasting Consideration: Data Leakage

A key consideration for feature-based forecasting is whether future explanatory variables are actually available at the time the forecast is generated.

Historical appliance consumption and calendar variables are available at the forecast origin.

However, future:

* Indoor temperature
* Indoor humidity
* Outdoor temperature
* Weather measurements

may not be known in a real-world deployment.

Using actual future sensor/weather measurements from the test period would therefore represent a **conditional forecast**, rather than a fully operational real-time forecast.

This should be considered when interpreting the XGBoost results.

---

## 14. Limitations

The main limitations of the analysis include:

1. The evaluation focuses on a 24-hour forecasting horizon.
2. A single final evaluation period may not fully represent long-term forecasting performance.
3. Future weather and sensor availability can affect the validity of feature-based forecasting.
4. Appliance consumption contains sudden peaks that are difficult to predict.
5. SARIMAX residuals were not normally distributed.
6. Foundation-model performance may depend on model configuration and computational resources.

---

## 15. Future Work

Potential improvements include:

* Rolling-origin cross-validation
* Multiple forecasting horizons
* Improved lag and rolling-window features
* More extensive XGBoost hyperparameter optimisation
* Forecasting future weather variables separately
* Probabilistic forecasting
* Ensemble SARIMAX and XGBoost models
* Evaluation over multiple time periods
* Better modelling of extreme consumption events
* Comparison with additional foundation models

An ensemble combining SARIMAX's temporal modelling ability with XGBoost's nonlinear feature learning could be particularly promising.

---

## 16. Repository Structure

A recommended repository structure is:

```text
appliance-energy-forecasting/
│
├── README.md
│
├── notebook/
│   └── appliance_energy_forecasting.ipynb
│
├── src/
│   └── forecasting_pipeline.py
│
├── data/
│   └── README.md
│
├── outputs/
│   ├── figures/
│   ├── tables/
│   └── forecasts/
│
├── requirements.txt
│
└── report/
    └── appliance_energy_forecasting_report.pdf
```

The original dataset does not need to be duplicated in the repository if it is downloaded automatically by the notebook or pipeline. The `data/README.md` file can instead document the official dataset source.

---

## 17. Reproducibility

The analysis was developed using **Python and Google Colab**.

The main libraries used include:

```text
pandas
numpy
matplotlib
seaborn
scikit-learn
statsmodels
xgboost
scipy
transformers
torch
```

The notebook contains the preprocessing, exploratory analysis, forecasting models, evaluation metrics, diagnostics and final model comparison.

To reproduce the analysis:

1. Clone or download this repository.
2. Open the forecasting notebook in Google Colab or Jupyter.
3. Install the required Python packages if necessary.
4. Download/load the UCI Appliance Energy Prediction dataset.
5. Run the notebook cells sequentially.
6. The analysis will generate the forecasts, evaluation metrics and figures used in the report.

---

## 18. Technologies Used

* **Python**
* **Google Colab**
* **Pandas**
* **NumPy**
* **Matplotlib**
* **Seaborn**
* **Statsmodels**
* **Scikit-learn**
* **XGBoost**
* **PyTorch**
* **Hugging Face Transformers**
* **Chronos**

---

## 19. References

Candanedo, L.M. and Feldheim, V. (2016). Accurate occupancy detection of an office room from light, temperature, humidity and CO₂ measurements using statistical learning models. *Energy and Buildings*, 112, pp.28–39.

Hyndman, R.J. and Athanasopoulos, G. (2021). *Forecasting: Principles and Practice*. 3rd ed. OTexts.

Seabold, S. and Perktold, J. (2010). Statsmodels: Econometric and statistical modelling with Python. *Proceedings of the 9th Python in Science Conference*.

Chen, T. and Guestrin, C. (2016). XGBoost: A scalable tree boosting system. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, pp.785–794.

Ansari, A.F. et al. (2024). Chronos: Learning the language of time series. *Transactions on Machine Learning Research*.

---

## 20. Final Summary

The project demonstrates that **carefully selected classical time-series models can outperform more complex machine-learning and foundation-model approaches when the dataset contains strong temporal structure**.

For this appliance-energy dataset:

> **SARIMAX was the best-performing model, achieving an RMSE of 159.60 and improving on the strongest benchmark by 45.50%.**

The results highlight the importance of establishing strong baselines before adopting increasingly complex forecasting models.
