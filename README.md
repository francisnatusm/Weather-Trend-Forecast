# Weather Trend Forecasting

**PM Accelerator — Tech Assessment (Data Scientist / Analyst Dual Role)**  
**Name:** Francis Natus Mugisha  
**Track:** Advanced  

Analyze the [Global Weather Repository](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository) dataset to forecast weather trends and demonstrate end-to-end data science skills: cleaning, EDA, multi-model forecasting + ensemble, and advanced analyses (anomalies, climate, air quality, feature importance, spatial patterns).

---

## PM Accelerator Mission

> Our mission is to break down financial barriers and achieve educational fairness. With the goal of establishing 200 schools worldwide over the next 20 years, we aim to empower more kids for a better future in their life and career, simultaneously fostering a diverse landscape in the tech industry.

Source: [pmaccelerator.io](https://www.pmaccelerator.io/)

---

## Project structure

```text
weather-trend-forecast/
├── data/
│   ├── raw/                 # GlobalWeatherRepository.csv (download from Kaggle)
│   └── processed/           # cleaned + scaled datasets
├── notebooks/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_eda.ipynb
│   ├── 03_forecasting.ipynb
│   └── 04_advanced_analysis.ipynb
├── outputs/
│   ├── eda/
│   ├── forecasting/
│   └── advanced/
├── report/
│   └── REPORT.md            # full written report
├── requirements.txt
└── README.md
```

---

## Dataset

| Item | Detail |
|------|--------|
| Source | [Kaggle — Global Weather Repository](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository) |
| File | `GlobalWeatherRepository.csv` |
| Size (this run) | ~153,776 rows × 41 columns (raw) |
| Coverage | 211 countries, 286 locations |
| Time span | May 2024 → Jul 2026 (`last_updated`) |

**Place the CSV at:** `data/raw/GlobalWeatherRepository.csv`  
(Or unzip the Kaggle archive into `data/raw/`.)

---

## How to run

### 1. Environment

```bash
cd weather-trend-forecast
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
```

### 2. Run notebooks (in order)

Open Jupyter or VS Code and run **Restart & Run All** on each:

1. `notebooks/01_data_cleaning.ipynb` → writes `data/processed/weather_cleaned.csv`
2. `notebooks/02_eda.ipynb` → charts in `outputs/eda/`
3. `notebooks/03_forecasting.ipynb` → Kyiv daily forecast + metrics in `outputs/forecasting/`
4. `notebooks/04_advanced_analysis.ipynb` → anomalies, climate, AQI, maps in `outputs/advanced/`

### 3. Read the report

See [`report/REPORT.md`](report/REPORT.md) for methodology, results, and insights.

---

## Methodology (short)

1. **Cleaning:** parse `last_updated`, remove city+timestamp duplicates, domain-cap impossible values, drop redundant unit columns, optional MinMax/Standard scaling artifacts  
2. **EDA:** temperature & precipitation trends, correlations, latitude patterns  
3. **Forecasting:** daily series from `last_updated` for **Kyiv**; time-based 80/20 split; Naive, Moving Average, Ridge, Random Forest, Gradient Boosting, and ensembles; metrics MAE / RMSE / MAPE / R²  
4. **Advanced:** Isolation Forest anomalies; continent climate seasonality; PM2.5 vs weather; RF + permutation + Ridge feature importance; lat/lon spatial maps  

---

## Key results (summary)

| Area | Highlight |
|------|-----------|
| Cleaning | 153,776 → 153,775 rows; clipped impossible wind/pressure/temp |
| EDA | ~33% wet rows; temp↔feels_like ≈ 0.98; \|lat\|↔temp ≈ −0.56 |
| Forecast (Kyiv) | Best MAE: **Ridge ≈ 2.20°C** (beats Naive 2.23); R² ≈ **0.89** |
| Anomalies | ~**2%** flagged by Isolation Forest |
| Climate | Warmest mean: Oceania; coolest: Europe; highest PM2.5: **Asia** |
| Importance | All 3 methods: top feature = **`abs_latitude`** |

Full tables and figures: `outputs/` and `report/REPORT.md`.

---

## Deliverables checklist

- [x] Data cleaning & preprocessing  
- [x] EDA (temperature & precipitation visualizations)  
- [x] Forecasting with `last_updated` + multiple models + ensemble  
- [x] Advanced: anomalies, climate, AQI, feature importance, spatial/geo  
- [x] Report with **PM Accelerator mission**  
- [x] README + `requirements.txt`  
- [x] Demo video (~5 min) — screen-share of code + results  

**Demo video URL:** [https://youtu.be/sxVaFXo49EY](https://youtu.be/sxVaFXo49EY)

---

## Author

**Francis Natus Mugisha** — ML & AI Engineer  
Portfolio: [francisnatusm.com](https://francisnatusm.com) · GitHub: [francisnatusm](https://github.com/francisnatusm)
