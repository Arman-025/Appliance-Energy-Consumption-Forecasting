# Appliance Energy Consumption Forecasting

## Project Overview

This project investigates short-term forecasting of household appliance energy consumption using real-world time-series data from the **UCI Appliance Energy Prediction dataset**.

The original dataset contains electricity consumption measurements recorded at 10-minute intervals together with indoor sensor, outdoor weather and time-related variables. The data were aggregated to hourly observations and used to develop and compare several forecasting approaches.

The main objective was to forecast appliance energy consumption for the **next 24 hours** and determine which modelling approach provides the best balance of forecasting accuracy, interpretability and practical usability.

The project compares:

* Mean forecast
* Naive forecast
* Daily Seasonal Naive
* Weekly Seasonal Naive
* Drift forecast
* SARIMA
* SARIMAX
* XGBoost
* Chronos-T5-tiny

The analysis includes exploratory data analysis, stationarity testing, benchmark forecasting, autoregressive modelling, feature-based machine learning, foundation-model forecasting and final model comparison.

---

## Dataset

The dataset used in this project is the **Appliances Energy Prediction** dataset from the UCI Machine Learning Repository.

Original dataset:

https://archive.ics.uci.edu/ml/machine-learning-databases/00374/energydata_complete.csv

The original observations are recorded every 10 minutes.

For this project:

1. The timestamp was parsed and converted to a datetime index.
2. Missing values and data quality were checked.
3. The data were aggregated from 10-minute observations to hourly observations.
4. Exploratory analysis was performed to identify trends, seasonality and temporal dependence.
5. The `Appliances` variable was used as the forecasting target.

After preprocessing, the hourly dataset contained approximately **3,290 observations**.

---

## Forecasting Problem

The forecasting task was defined as a short-term electricity demand forecasting problem.

| Component            | Setting             |
| -------------------- | ------------------- |
| Target               | `Appliances`        |
| Original frequency   | 10 minutes          |
| Modelling frequency  | Hourly              |
| Forecast horizon     | 24 hours            |
| Final ML test period | Last 14 days        |
| Primary evaluation   | MAE, RMSE, MAPE, R² |

A chronological train/test approach was used to preserve the temporal ordering of the data and avoid randomly mixing past and future observations.

---

## Project Structure

```text
Appliance-Energy-Consumption-Forecasting/
│
├── notebooks/
│   ├── 01_EDA.ipynb
│   ├── 02_BenchmarkModels.ipynb
│   ├── 03_Sarimax.ipynb
│   ├── 04_Sarimax_Feature_Engineering.ipynb
│   ├── 05_XGboost_Forecasting.ipynb
│   ├── 06_Chronos_Foundation_Model.ipynb
│   └── 07_Final_Model_Evaluation_and_Comparison.ipynb
│
├── data/
│   ├── energydata_complete.csv
│   ├── hourly_energy_data.csv
│   ├── hourly_features_energy.csv
│   └── ml_features_energy.csv
│
├── models/
│   └── xgboost_appliance_model.pkl
│
├── outputs/
│   ├── figures/
│   └── tables/
│
├── report/
│   └── Appliance Energy Consumption Forecasting.pdf
│
├── requirements.txt
├── README.md
└── LICENSE
```

The exact filenames of figures may vary depending on the saved outputs. The notebooks contain the complete analysis and code used to generate the reported results.

---

## Analysis Workflow

### 1. Data Preparation and EDA

The first stage prepared the raw UCI dataset for forecasting.

The analysis included:

* Timestamp parsing
* Checking missing values
* Conversion to hourly frequency
* Inspection of appliance consumption
* Time-series visualisation
* Rolling mean and standard deviation
* ADF stationarity testing
* KPSS stationarity testing
* ACF analysis
* PACF analysis
* Investigation of daily and weekly patterns

The exploratory analysis indicated strong temporal dependence and recurring daily/weekly structure in appliance consumption.

---

### 2. Benchmark Forecasting

Several simple forecasting methods were implemented to provide baselines for evaluating more complex models:

* Mean
* Naive
* Daily Seasonal Naive
* Weekly Seasonal Naive
* Drift

These benchmarks establish how much predictive information can be obtained from simple historical patterns before introducing statistical or machine-learning models.

The **Weekly Seasonal Naive** model was the strongest benchmark, with:

* MAE: 43.46
* RMSE: 81.41
* MAPE: 37.35%
* R²: -0.183

---

### 3. SARIMA

A seasonal autoregressive integrated moving average model was developed using `statsmodels`.

The model parameters were selected using AIC-based grid search.

The selected non-seasonal order was:

```text
(2, 0, 6)
```

The seasonal order used was:

```text
(1, 1, 1, 24)
```

The seasonal period of 24 represents the daily cycle in hourly data.

The SARIMA model was evaluated using a 24-hour forecast horizon and residual diagnostics.

---

### 4. SARIMAX

SARIMAX extended the autoregressive approach by including additional explanatory variables from the dataset.

These included sensor, weather and time-related variables where appropriate.

The purpose was to determine whether external information could improve forecasting beyond the temporal structure captured by SARIMA.

In this experiment, SARIMAX performed substantially worse than SARIMA and the benchmark models. This demonstrates that adding additional variables does not necessarily improve a forecasting model, particularly when the variables are not sufficiently predictive or may not be reliably available at the forecast origin.

---

### 5. Feature-Based Machine Learning

XGBoost was used as the feature-based machine-learning model.

Additional predictive features included:

* Lagged appliance consumption
* Hour of day
* Working-hours information
* Rolling statistics
* Cyclical time features

The five most important features identified by the model were:

1. `lag_1`
2. `hour`
3. `rolling_std_24`
4. `working_hours`
5. `hour_sin`

This indicates that recent appliance consumption and time-of-day behaviour were particularly important predictors.

---

### 6. Foundation Model

Chronos-T5-tiny was evaluated as a time-series foundation model.

The model was used to produce a 24-hour forecast and its results were evaluated using the same core metrics as the other models.

The purpose was to investigate whether a general-purpose time-series foundation model could outperform models specifically designed or trained around the structure of this dataset.

---

## Final Model Comparison

The final evaluation produced the following results:

| Rank | Model                 |       MAE |      RMSE |  MAPE (%) |        R² |
| ---: | --------------------- | --------: | --------: | --------: | --------: |
|    1 | **SARIMA**            | **18.62** | **25.78** | **19.06** | **0.727** |
|    2 | XGBoost               |     42.74 |     51.44 |     45.15 |    -0.088 |
|    3 | Mean                  |     50.26 |     74.94 |     53.70 |    -0.003 |
|    4 | Weekly Seasonal Naive |     43.46 |     81.41 |     37.35 |    -0.183 |
|    5 | Daily Seasonal Naive  |     48.31 |     85.56 |     43.33 |    -0.307 |
|    6 | Naive                 |     85.55 |    110.39 |    112.91 |    -1.176 |
|    7 | Drift                 |     85.80 |    110.68 |    113.31 |    -1.187 |
|    8 | Chronos-T5-tiny       |     67.67 |    111.67 |     31.05 |    -0.276 |
|    9 | SARIMAX               |    298.52 |    323.18 |    361.33 |   -41.932 |

### Main Finding

SARIMA achieved the strongest overall performance.

Compared with the strongest benchmark, Weekly Seasonal Naive:

* MAE was approximately **57.1% lower**
* RMSE was approximately **68.3% lower**

Compared with XGBoost:

* MAE was approximately **56.4% lower**
* RMSE was approximately **49.9% lower**

This indicates that explicitly modelling autocorrelation and daily seasonality was particularly effective for this dataset.

---

## Evaluation Metrics

The models were compared using:

### Mean Absolute Error (MAE)

Measures the average absolute difference between actual and predicted values.

Lower values indicate better forecasting accuracy.

### Root Mean Squared Error (RMSE)

Penalises larger forecasting errors more strongly than MAE.

Lower values indicate better performance.

### Mean Absolute Percentage Error (MAPE)

Expresses the forecasting error as a percentage.

MAPE was treated as a supplementary metric because appliance consumption can become relatively low, causing percentage errors to become disproportionately large.

### R²

Measures the proportion of variation explained by the model relative to a baseline.

Higher values indicate better explanatory performance.

---

## Key Findings

The analysis showed that appliance consumption contains strong temporal structure.

The strongest benchmark was Weekly Seasonal Naive, indicating that recurring weekly behaviour contains useful predictive information.

SARIMA improved substantially on this benchmark by modelling both autoregressive behaviour and daily seasonality.

XGBoost provided a competitive alternative and demonstrated that recent appliance consumption and time-of-day features were important predictors. However, its negative R² indicated weaker overall performance than SARIMA.

SARIMAX performed poorly despite having additional covariates. This highlights that additional predictors do not automatically produce better forecasts.

Chronos-T5-tiny also did not outperform the specialised statistical and feature-based approaches in this experiment. Its zero-shot nature provided flexibility but did not exploit the dataset-specific seasonal structure as effectively as SARIMA.

---

## Forecast-Time Data Availability

A practical forecasting system must distinguish between variables known at the forecast origin and variables that require their own forecasts.

For example, future appliance consumption is unknown and must be predicted. Future weather, humidity or indoor temperature values may also be unknown unless separate forecasting systems provide them.

Using observed future covariates from the test set would therefore represent a **conditional forecast**, rather than a fully operational real-world forecast.

This is an important consideration when interpreting models that use future exogenous variables.

---

## Reproducibility

The notebooks contain the complete analysis workflow, including:

* Data preparation
* Exploratory analysis
* Stationarity testing
* Benchmark forecasting
* SARIMA modelling
* SARIMAX modelling
* Feature engineering
* XGBoost modelling
* Chronos forecasting
* Model evaluation
* Forecast visualisation
* Final model comparison

The CSV files in `outputs/` contain the main numerical results used in the report.

The figures used in the report are stored in the project figure/output directory.

---

## Installation

Create a Python virtual environment and install the dependencies:

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

Then install the required packages:

```bash
pip install -r requirements.txt
```

Open Jupyter Notebook:

```bash
jupyter notebook
```

Run the notebooks in numerical order.

---

## Important Note About Chronos

The Chronos foundation-model notebook may require downloading model weights from Hugging Face.

The model files are **not included in this repository** because they are large.

The notebook downloads the required model when executed.

A Hugging Face account/token may be useful for higher download limits, but no authentication token should be stored directly in the repository.

---

## Data and Large Files

The original dataset is publicly available from the UCI Machine Learning Repository and can be downloaded using the URL provided above.

Large model files, caches and generated temporary files should not be committed to GitHub.

The `.gitignore` file is used to prevent unnecessary files such as Python caches, virtual environments, notebook checkpoints and downloaded model files from being committed.

---

## Limitations and Future Improvements

The analysis uses a chronological hold-out evaluation, meaning that results may depend on the selected test period.

Future improvements could include:

* Rolling-origin or walk-forward validation
* More extensive XGBoost feature ablation
* Improved SARIMAX feature selection
* Multicollinearity analysis for exogenous variables
* Probabilistic forecasting
* Prediction interval evaluation
* MASE or other scale-independent metrics
* Testing additional foundation models
* More robust deployment-time covariate forecasting
* Periodic model retraining to account for changes in household behaviour

---

## Author

**MSc Data Science Project**

University of Hertfordshire

This repository was developed as part of the Data Analysis with AI assignment.
