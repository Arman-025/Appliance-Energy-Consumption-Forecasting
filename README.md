# Appliance Energy Consumption Forecasting

## Overview

This project investigates the use of time-series analysis and machine learning techniques to forecast electricity appliance energy consumption. The aim is to develop and compare different forecasting approaches and identify a model that can provide accurate short-term predictions of appliance energy demand.

The project was developed as part of the **Data Analysis with AI** module for the MSc Data Science programme at the University of Hertfordshire.

## Objectives

The main objectives of this project are to:

* Analyse historical appliance electricity consumption data.
* Identify trends, seasonality and patterns in energy usage.
* Prepare and transform the data for forecasting.
* Investigate the stationarity of the time series.
* Develop and compare statistical, machine learning and AI-based forecasting models.
* Evaluate model performance using multiple forecasting metrics.
* Generate short-term appliance energy consumption forecasts.
* Identify the most suitable approach for the forecasting task.

## Dataset

The project uses historical electricity appliance energy consumption data recorded at regular time intervals.

The dataset was cleaned and transformed into an hourly time series for modelling. Additional time-based and statistical features were created where appropriate, including:

* Hour of day
* Working/non-working hours
* Lagged consumption values
* Rolling statistics
* Cyclical time features

The original dataset and its associated licensing/attribution information should be consulted before reuse.

## Methodology

The forecasting workflow consisted of the following stages:

1. Data loading and initial exploration
2. Data cleaning and preprocessing
3. Resampling to hourly frequency
4. Exploratory data analysis
5. Feature engineering
6. Stationarity analysis
7. Train/test splitting
8. Baseline forecasting
9. Statistical time-series modelling
10. Machine learning modelling
11. AI-based forecasting
12. Model evaluation and comparison
13. Short-term forecasting
14. Critical analysis of the results

## Models

Several forecasting approaches were investigated and compared.

### Baseline Models

* Mean forecast
* Naive forecast
* Daily seasonal naive
* Weekly seasonal naive
* Drift forecast

### Statistical Models

* SARIMA
* SARIMAX

### Machine Learning Model

* XGBoost

### AI-Based Model

* Chronos-T5-Tiny

The models were evaluated using the same test period where possible to provide a fair comparison.

## Evaluation Metrics

Model performance was evaluated using:

* **MAE (Mean Absolute Error)** – measures the average absolute difference between predicted and actual consumption.
* **RMSE (Root Mean Squared Error)** – gives greater weight to larger forecasting errors.
* **MAPE (Mean Absolute Percentage Error)** – measures prediction error relative to actual values.
* **R² (Coefficient of Determination)** – indicates how well the model explains variation in the target variable.

Lower MAE, RMSE and MAPE values indicate better performance, while a higher R² indicates better explanatory performance.

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
├── src/
│
├── requirements.txt
├── README.md
└── LICENSE

## Results

The developed models were compared against both simple forecasting baselines and more advanced approaches.

The analysis found that the performance of forecasting methods varied considerably depending on the modelling approach. The statistical time-series models were particularly useful for capturing temporal patterns in appliance energy consumption, while machine learning and AI-based approaches provided alternative methods for modelling complex relationships.

The final report contains the complete evaluation results, forecasting plots, model comparisons and critical discussion of the findings.

## Reproducibility

To reproduce the analysis:

1. Clone the repository.
2. Create a Python virtual environment.
3. Install the dependencies listed in `requirements.txt`.
4. Place the required dataset in the appropriate `data/` directory.
5. Run the notebooks in the recommended order.

```bash
git clone https://github.com/Arman-025/Appliance-Energy-Consumption-Forecasting.git
cd Appliance-Energy-Consumption-Forecasting
pip install -r requirements.txt
```

Some models, particularly transformer-based forecasting models, may require additional computational resources and model downloads.

## Academic Context

This repository contains the implementation and supporting materials for an MSc **Data Analysis with AI** project. The accompanying report provides detailed explanations of the methodology, experiments, results, limitations and conclusions.

## License

This project is released under the **MIT License**. See the `LICENSE` file for details.
