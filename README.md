# PREDICTIVE-PARADOX: Electricity Demand Forecasting

## Project Overview
This repository contains a machine learning pipeline to predict the next hour's electricity demand on the national grid using LightGBM.

## Setup
1. Ensure the datasets (`weather_data.xlsx`, `PGCB_date_power_demand.xlsx`, `economic_full_1.csv`) are in the root folder.
2. Install dependencies: `pip install pandas numpy scikit-learn lightgbm pyod matplotlib seaborn`
3. Run the `.ipynb` notebook top-to-bottom.

## Technical Approach

### 1. Handling Missing Data and Anomalies
* **Outliers:** Used **PyOD (Isolation Forest)** with a 1% contamination rate to identify massive spikes/drops in demand.
* **Missing Data:** Replaced anomalies with `NaN` and used **linear interpolation** (forward/backward fill) to ensure a continuous time-series flow.

### 2. Feature Engineering
* **Time-Based Patterns:** Converted hours and months into `sine` and `cosine` waves to capture the daily and seasonal cycles of the grid.
* **Historical Lags:** Created `demand_lag_1`, `demand_lag_24`, `demand_lag_48`, and `demand_lag_168` (one week ago) to capture immediate and long-term repeating trends.
* **Stability Check:** Added `rolling_std_6` to capture how much the demand was fluctuating in the past 6 hours.
* **External Factors:** Integrated temperature, GDP, and Population data, along with weekend indicators, to provide full context to the model.

### 3. Key Insights from Feature Importance
* **Strongest Predictor:** "Time-of-day cycles (hour_cos, hour_sin) are the most significant predictors, followed closely by recent historical demand (demand_lag_1). This proves that demand follows a highly predictable daily schedule."* **Time vs. Weather:** The specific time of day (`hour_cos`) is a more significant driver of demand than external weather factors.
* **Stability Matters:** Recent demand fluctuations (`rolling_std_6`) are highly important for the model to adjust its predictions for the next hour.
* **Economic Baseline:** Population and GDP help set the structural baseline, but the model relies mostly on short-term historical data for accuracy.

## Results
* **Model:** LightGBM Regressor (Learning rate: 0.005, 2000 estimators).
* **Training Period:** Data up to the end of 2023.
* **Testing Period:** Starting from January 2024.
* **Performance:** Achieved an optimized **MAPE of 1.7574%**.
