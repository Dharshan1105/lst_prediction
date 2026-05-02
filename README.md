# 🌡️ Land Surface Temperature (LST) Analysis — Coimbatore District (1999–2025)

A machine learning pipeline for monitoring, predicting, and projecting Land Surface Temperature over the Coimbatore region using harmonized multi-sensor Landsat imagery from Google Earth Engine (GEE).

---

## 📌 Overview

This project builds a fully reproducible LST analysis workflow that:
- Retrieves 27 years (1999–2025) of cloud-free Landsat composites via Google Earth Engine
- Derives spectral indices (NDVI, NDBI, MNDWI, BSI) and LST in °C
- Trains and evaluates three regression models (XGBoost, LSTM, Decision Tree)
- Predicts spatial LST rasters for the 2021–2025 test period
- Projects future LST for 2030 and 2040 using drift-based extrapolation
- Performs high-LST zone classification with ROC/AUC evaluation

---

## 🗺️ Study Area

**Coimbatore District, Tamil Nadu, India**
AOI bounding box: `[76.70, 10.72, 77.60, 11.25]` (WGS-84)

---

## 🛰️ Data Sources

| Satellite | Collection | Period |
|-----------|-----------|--------|
| Landsat 5 TM | LANDSAT/LT05/C02/T1_L2 | 1999–2013 |
| Landsat 7 ETM+ | LANDSAT/LE07/C02/T1_L2 | 1999–2025 |
| Landsat 8 OLI | LANDSAT/LC08/C02/T1_L2 | 2013–2025 |
| Landsat 9 OLI-2 | LANDSAT/LC09/C02/T1_L2 | 2021–2025 |

All collections are harmonized to a common band naming scheme and cloud-masked using the QA_PIXEL band.

---

## 🧪 Features / Spectral Indices

| Feature | Description |
|---------|-------------|
| NDVI | Normalized Difference Vegetation Index |
| NDBI | Normalized Difference Built-up Index |
| MNDWI | Modified Normalized Difference Water Index |
| BSI | Bare Soil Index |
| LST_C | Land Surface Temperature (°C) — target variable |

---

## 🤖 Models

| Model | MAE (°C) | RMSE (°C) | R² |
|-------|----------|-----------|-----|
| XGBoost | 2.344 | 2.944 | 0.4602 |
| **LSTM** | **2.300** | **2.877** | **0.4778** |
| Decision Tree | 2.542 | 3.182 | 0.3609 |

> ✅ **LSTM selected** as the primary model based on best RMSE and R² on the 2021–2025 test set.

---

## 📁 Repository Structure

```
├── LST_Prediction.ipynb   # Main notebook (all steps)
├── data/
│   └── data.md           # Download the GeoTIFF File from the drive link 
├── outputs/
│   ├── actual_lst_grid_1999_2025_6x5_boundary.png
│   ├── lst_actual_vs_predicted_2021.png
│   ├── lst_actual_vs_predicted_2022.png
│   ├── lst_actual_vs_predicted_2023.png
│   ├── lst_actual_vs_predicted_2024.png
│   ├── lst_actual_vs_predicted_2025.png
│   ├── lst_predicted_2021_2025_2030_2040.png
│   ├── lst_residual_2021_2025.png
│   └── roc_auc_models.png
└── README.md
```

---

## ⚙️ Setup & Requirements

### 1. Install dependencies
```bash
pip install earthengine-api geemap numpy pandas rasterio scikit-learn \
    matplotlib joblib gdown xgboost tensorflow geopandas shapely
```

### 2. Authenticate Google Earth Engine
```bash
earthengine authenticate
```

### 3. Set your GEE project ID
In the notebook, update:
```python
ee.Initialize(project='your-gee-project-id')
```

### 4. Download the GeoTIFF
Set your Google Drive file ID in Step 3 of the notebook:
```python
GDRIVE_FILE_ID = 'your_google_drive_file_id'
```

---

## 🚀 How to Run

Run the notebook cells in order:

| Step | Description |
|------|-------------|
| Step 1 | Build multi-sensor GEE composite (1999–2025) |
| Step 2 | Export GeoTIFF to Google Drive |
| Step 3 | Download GeoTIFF locally via `gdown` |
| Step 4 | Read raster band-by-band (RAM-efficient) |
| Step 5 | Train models + evaluate + predict rasters |
| Step 6 | Plot year-wise LST grid, residuals, projections |

---

## 📊 Output Figures

| Figure | Description |
|--------|-------------|
| Fig. 1 | Year-wise actual LST raster composites (1999–2025) |
| Fig. 2–6 | Actual vs Predicted LST for each year (2021–2025) |
| Fig. 7 | Predicted LST grid (2021–2025 + 2030/2040 projections) |
| Fig. 8 | Spatial residual maps — Actual minus Predicted (2021–2025) |
| Fig. 9 | ROC curves for high-LST zone classification (2021–2025) |

---

## ⚠️ Notes

- The `.tif` raster file is **not tracked by git** due to size. Download it via the `gdown` step in the notebook.
- LSTM uses sequence length = 1 (pixel-wise), so true temporal autocorrelation between years is not exploited.
- 2030 and 2040 projections are based on a **linear drift** assumption on spectral indices — results should be interpreted as indicative trends, not precise forecasts.
- GEE rasters are downloaded at 120–240 m resolution for yearly composites and upsampled to the base 30 m grid, which introduces minor spatial smoothing.

---

## 👤 Author

**Student ID:** 103123025
Department of Civil Engineering — Remote Sensing and GIS (CELR 10)

---

## 📄 License

This project is submitted as part of an academic course. For reuse or citation, please contact the author.
