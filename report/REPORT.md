# Weather Trend Forecasting — Project Report

**PM Accelerator Tech Assessment**  
**Role:** Data Scientist / Analyst (Dual Role)  
**Name:** Francis Natus Mugisha  
**Dataset:** [Global Weather Repository (Kaggle)](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository)  
**Track completed:** Advanced  

---

## 1. PM Accelerator Mission

Product Manager Accelerator (PMA) also runs the non-profit **PMA Kids**.

> We are committed to offering free Product Management education to teenagers from underserved families.  
> **Our mission is to break down financial barriers and achieve educational fairness.**  
> With the goal of establishing **200 schools worldwide over the next 20 years**, we aim to empower more kids for a better future in their life and career, simultaneously fostering a diverse landscape in the tech industry.

Source: [https://www.pmaccelerator.io/](https://www.pmaccelerator.io/)

---

## 2. Objective

Analyze global daily weather data to:

1. Clean and prepare a reliable dataset  
2. Explore temperature and precipitation trends, correlations, and patterns  
3. Build and compare multiple forecasting models (plus an ensemble) using `last_updated`  
4. Deliver advanced analyses: anomaly detection, regional climate, air quality vs weather, feature importance, and spatial/geographic patterns  

---

## 3. Dataset overview

| Attribute | Value |
|-----------|--------|
| Raw shape | 153,776 rows × 41 columns |
| Cleaned shape | 153,775 rows × 37 columns |
| Countries / locations | 211 / 286 |
| Time range (`last_updated`) | 2024-05-16 → 2026-07-17 |
| Key features | Temperature, precip, humidity, wind, pressure, UV, air quality (PM2.5, PM10, gases), lat/lon |

---

## 4. Data cleaning & preprocessing

**Notebook:** `notebooks/01_data_cleaning.ipynb`

### Steps

1. **Missing values:** none found — no imputation required  
2. **Datetime:** convert `last_updated` to datetime; derive `date`, `year`, `month`, `day_of_week`  
3. **Duplicates:** remove duplicate `(country, location_name, last_updated)` — 1 row removed  
4. **Outliers:**  
   - IQR used to **flag** unusual values  
   - **Domain caps** used to fix impossible values (e.g. wind 2963 → 250 kph; pressure 3006 → 1085 mb; temp 79.3 → 55°C)  
   - Real extremes kept when physically plausible (e.g. Mongolia −29.8°C; heavy rain 42 mm)  
5. **Redundant columns dropped:** °F / mph / inches duplicates and `last_updated_epoch`  
6. **Normalization:** MinMax and StandardScaler copies saved for modeling options; primary analysis uses original units  
7. **Outputs:** `data/processed/weather_cleaned.csv` (+ scaled variants, location coverage)

### Cleaning principle

> IQR finds unusual points; domain knowledge decides keep vs clip. Unusual ≠ wrong; impossible ≈ wrong.

---

## 5. Exploratory Data Analysis (EDA)

**Notebook:** `notebooks/02_eda.ipynb` · **Figures:** `outputs/eda/`

### Temperature

- Global mean ≈ **21.4°C**, median ≈ **23.7°C**  
- Daily global average trend and monthly seasonality plotted  
- City example series used for intuition before forecasting  

### Precipitation

- Zero-inflated: only ~**33%** of rows have precip > 0  
- Overall mean looks tiny (~0.13 mm) because of zeros; wet-only view needed for intensity  

### Correlations & patterns

| Relationship | Correlation | Interpretation |
|--------------|-------------|----------------|
| temp ↔ feels_like | ≈ **0.98** | Near-duplicate signal |
| temp ↔ humidity | ≈ **−0.34** | Warmer rows often somewhat drier |
| \|latitude\| ↔ temp | ≈ **−0.56** | Farther from equator → cooler |

### Figures to review

- `01_temperature_distribution.png`  
- `02_global_daily_temperature.png`  
- `05_precipitation_distribution.png`  
- `08_correlation_heatmap.png`  
- `10_latitude_temperature.png`  

---

## 6. Forecasting models

**Notebook:** `notebooks/03_forecasting.ipynb` · **Figures:** `outputs/forecasting/`

### Setup

| Choice | Reason |
|--------|--------|
| Target | Daily mean temperature (°C) |
| Time source | `last_updated` resampled to daily |
| City | **Kyiv, Ukraine** (strong seasonality; clearer skill than flat tropical series) |
| Split | **Time-based** 80% train / 20% test (no random shuffle — avoids leakage) |
| Features (ML) | Lags 1,2,3,7,14; 7-day rolling mean/std; month, dayofweek, dayofyear |

### Models compared

1. Naive (lag-1)  
2. Moving average (7-day)  
3. Ridge regression (lags)  
4. Random Forest  
5. Gradient Boosting  
6. Ensemble mean (Ridge + RF + GB)  
7. VotingRegressor ensemble  

### Test metrics (Kyiv)

| Model | MAE (°C) | RMSE | R² |
|-------|----------|------|-----|
| **Ridge (lags)** | **2.204** | **2.977** | **0.894** |
| Naive (lag-1) | 2.232 | 3.127 | 0.884 |
| Ensemble (mean) | 2.295 | 3.071 | 0.888 |
| Ensemble (Voting) | 2.295 | 3.071 | 0.888 |
| Random Forest | 2.353 | 3.136 | 0.883 |
| Gradient Boosting | 2.519 | 3.348 | 0.867 |
| Moving Avg (7d) | 3.214 | 4.137 | 0.796 |

### Forecasting insights

- Ridge slightly **beats the naive baseline** (2.20 vs 2.23 MAE)  
- R² ≈ **0.89** — models track Kyiv’s seasonal swings well  
- Ensemble is strong but Ridge alone won on MAE for this city/split  
- Always report MAE first in demos (interpretable °C error)

---

## 7. Advanced analyses

**Notebook:** `notebooks/04_advanced_analysis.ipynb` · **Figures:** `outputs/advanced/`

### 7.1 Anomaly detection

- **Isolation Forest** on temp, wind, pressure, precip, humidity, PM2.5  
- `contamination=0.02` → ~**2%** flagged  
- Anomalies = rare combinations (not automatically bad data)

### 7.2 Climate / regional patterns

- Countries mapped to continents  
- Seasonal temperature & precipitation curves by continent  

| Continent | Mean temp (°C) | Mean PM2.5 |
|-----------|----------------|------------|
| Oceania | ~25.0 (warmest) | ~6.6 |
| Africa | ~24.8 | ~23.9 |
| Asia | ~24.4 | **~39.9 (highest)** |
| Europe | ~13.5 (coolest) | ~15.5 |

### 7.3 Environmental impact (air quality)

Correlations of weather features with **PM2.5**:

| Feature | Corr with PM2.5 |
|---------|-----------------|
| humidity | **−0.22** |
| cloud | −0.17 |
| wind_kph | −0.05 |
| temperature | +0.06 |

Interpretation: weather alone does not fully explain pollution, but humidity shows the clearest (moderate) negative association in this dataset; Asia has the highest average PM2.5.

### 7.4 Feature importance (3 methods)

Predicting temperature from weather/geo/calendar features (**excluding** `feels_like` to avoid leakage-like duplication):

| Method | Top feature |
|--------|-------------|
| Random Forest impurity | **`abs_latitude`** |
| Permutation importance | **`abs_latitude`** |
| \|Ridge coefficients\| | **`abs_latitude`** |

Next: `uv_index`, `pressure_mb`, `month`.  
Agreement across methods increases confidence that **geography** is the dominant driver of temperature in this global table.

### 7.5 Spatial & geographical patterns

- Lat/lon scatter maps of city mean temperature and mean PM2.5  
- Country leaderboards: coolest / warmest / highest PM2.5 (n ≥ 200)  
- Confirms cooler cities at higher latitudes; pollution hotspots concentrated regionally (notably Asia in this sample)

---

## 8. Overall conclusions

1. **Data quality matters:** domain caps fixed sensor-like errors without deleting real extremes.  
2. **EDA shows structure:** dry-biased precip, strong temp–feels_like duplication, clear latitude–temperature link.  
3. **Forecasting works best with seasonality:** Kyiv daily Ridge forecast MAE ≈ 2.2°C, R² ≈ 0.89, beating naive.  
4. **Advanced value:** anomalies (~2%), continent climate differences, Asia PM2.5 elevation, and unanimous `|latitude|` importance.  
5. **Product mindset:** results are documented, reproducible, and demo-ready for PM Accelerator evaluation.

---

## 9. Reproducibility

```bash
pip install -r requirements.txt
# Place GlobalWeatherRepository.csv in data/raw/
# Run notebooks 01 → 04 in order
```

See root `README.md` for full instructions.

---

## 10. Appendix — key output files

| Path | Contents |
|------|----------|
| `outputs/eda/` | Temperature/precip/correlation figures |
| `outputs/forecasting/model_metrics.csv` | Full model comparison |
| `outputs/forecasting/03_forecast_comparison.png` | Actual vs predicted |
| `outputs/advanced/01_anomaly_detection.png` | Anomaly visuals |
| `outputs/advanced/07_spatial_maps.png` | Geo maps |
| `outputs/advanced/06_feature_importance.png` | Importance comparison |
| Demo video URL (in README) | 1–2 minute screen-share of code + results |
