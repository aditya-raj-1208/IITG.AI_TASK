# IITG.AI_TASK
This is a task for the club recuitment of iitg.ai club

## summary report

missing data & anomalies

PGCB had two timestamp issues — duplicates and sub-hourly readings (17:30, 18:30 from peak hour monitoring). Both were fixed by flooring to the hour and averaging. Outliers were caught using a 24h rolling z-score; anything beyond 3σ was replaced with the local rolling median. Small gaps (≤3h) were forward-filled. Weather had no real issues. Economic data had a few missing years, fixed with forward-fill before merging.

temporal features

Since tree models have no memory, we gave them one manually. `demand_lag_24` (same hour yesterday) is the most powerful — demand at 6pm today is the best predictor for 6pm tomorrow. `demand_lag_168` adds weekly patterns. Short lags (t-1, t-2, t-3) capture momentum. Rolling means smooth out noise. Calendar features like `hour` and `is_weekend` encode the predictable daily/weekly cycles.

**feature importance findings**

Lag features dominate — `demand_lag_24` is #1 by a large margin. Rolling means also rank high. Among calendar features, `hour` is the most useful. Temperature is the top weather feature, confirming AC-driven demand spikes in summer. Economic features rank lower since they change slowly (annual), but they help the model calibrate the overall demand level across years.

results on 2023 test set
 

| model | MAPE | MAE | RMSE |
|---|---|---|---|
| Random Forest | ~3.31 | ~342.31 MW | ~538.075 MW |
| XGBoost | ~3.052% | ~321.983 MW | ~503.09 MW |
| LightGBM | ~3.046% | ~317.465 MW | ~498.037 MW |

~3% MAPE is good enough
