# Landslide Susceptibility Mapping — Sikkim, India

Machine learning-based Landslide Susceptibility Mapping (LSM) for Sikkim, India,
integrating topographic, hydrological, geological, environmental, and anthropogenic
conditioning factors. Random Forest and XGBoost classifiers were trained, validated,
and compared for spatial risk zonation. SHAP analysis was used for model interpretability.

**Author:** Varun Saxena | NSUT  
**Study Area:** Sikkim, India  
**Methods:** Random Forest, XGBoost, SHAP Analysis, VIF Filtering, Spearman Correlation  
**Tools:** Python, Scikit-learn, XGBoost, SHAP, QGIS  
**Status:** ML phase complete | DL extension in progress  
**Conference:** Accepted at ICCDRH 2026

---

## Study Area

Sikkim is a small Himalayan state in northeastern India, characterised by steep terrain,
active tectonics, high seismicity, intense monsoon rainfall, and rapid land-use change —
making it one of the most landslide-prone regions in India. The study covers the entire
state, encompassing diverse geological zones, elevation gradients, and a dense network
of rivers and fault systems.

---

## Conditioning Factors

Factors were selected based on literature review, data availability, and
multicollinearity analysis using Spearman Correlation and VIF (Variance Inflation
Factor) filtering.

| Category | Factors |
|----------|---------|
| Topographic | Slope, Sin_Aspect, Profile Curvature, Relative Relief, LS Factor, TWI |
| Hydrological | SPI (Stream Power Index), Distance to Drainage |
| Geological | Lithology, Distance to Tectonic Faults, Soil Type |
| Environmental | NDVI, Land Use / Land Cover (LULC) |
| Anthropogenic | Distance to Roads, NTL (Night Time Lights), Rainfall (30-year avg) |
| SAR-derived | VV/VH ratio, VV−VH difference (Sentinel-1) |

**Multicollinearity handling:**
- Spearman Correlation Matrix computed for all variables
- VIF filtering applied to remove highly correlated features
- Final feature set selected after filtering (see `RF_feature_matrix.png` vs
  `RF_allfeature_matrix.png`)

---

## Methodology

### Data Preparation
- Landslide inventory compiled from historical records and remote sensing
- Non-landslide points sampled with spatial constraints to avoid class overlap
- Stratified train-test split applied for model validation
- Categorical variables (lulc, lithology, soil_type) one-hot encoded

### Models

**Random Forest**
- Ensemble of decision trees with majority voting
- Hyperparameter tuning via GridSearchCV
- Feature importance from mean decrease in impurity

**XGBoost**
- Gradient boosted trees with L1/L2 regularisation
- Hyperparameter tuning via RandomizedSearchCV
- SHAP (SHapley Additive exPlanations) used for interpretable feature importance

### Validation Metrics
- Accuracy, Precision, Recall, F1-Score
- Confusion Matrix
- ROC-AUC

---

## Results

### Model Performance

| Model | Accuracy | TP | TN | FP | FN |
|-------|----------|----|----|-----|-----|
| XGBoost | **84.9%** | 107 | 101 | 21 | 16 |
| Random Forest | 83.7% | 102 | 103 | 19 | 21 |

XGBoost achieved marginally better overall accuracy and landslide recall.
Random Forest showed slightly fewer false positives.

### Key Findings from SHAP Analysis

Top predictors of landslide susceptibility (XGBoost SHAP):

1. **Distance to Roads** — strongest predictor; low distance = high susceptibility
2. **NDVI** — high vegetation cover reduces susceptibility; degraded areas more prone
3. **Slope** — steep slopes consistently increase susceptibility
4. **NTL (Night Time Lights)** — proxy for anthropogenic pressure
5. **Distance to Tectonic Faults** — proximity to fault lines increases risk
6. **Distance to Drainage** — areas near drainage channels show higher susceptibility
7. **SPI** — high stream power zones are erosion-prone

Categorical factors (lulc, lithology, soil_type) showed lower but non-negligible
SHAP contributions, consistent with their role as background conditioning factors.

### LSM Output

The final susceptibility map was generated using XGBoost probability outputs,
classified and visualised in QGIS (EPSG:32645). High and very high susceptibility
zones are concentrated along river valleys, steep slopes adjacent to fault zones,
and areas with high road density and low vegetation cover.

---

## Output Files

| File | Description |
|------|-------------|
| `LSM.png` | Final Landslide Susceptibility Map — QGIS output |
| `XGB_Confusion_matrix.png` | XGBoost confusion matrix |
| `RF_Confusion_matrix.png` | Random Forest confusion matrix |
| `XGB_feature_imp.png` | SHAP beeswarm plot — XGBoost |
| `RF_feature_imp.png` | SHAP beeswarm plot — Random Forest |
| `XGB_feature_matrix.png` | Spearman correlation matrix after VIF filtering (XGB) |
| `RF_feature_matrix.png` | Spearman correlation matrix after VIF filtering (RF) |
| `RF_allfeature_matrix.png` | Spearman correlation matrix — all variables before filtering |

---

## Current Status & Future Work

- [x] Conditioning factor preparation and multicollinearity analysis
- [x] Random Forest — training, tuning, validation
- [x] XGBoost — training, SHAP interpretability, comparison
- [x] Susceptibility map generation and QGIS visualisation
- [x] Accepted at ICCDRH 2026
- [ ] Deep learning extension using multi-band satellite imagery
- [ ] Spatial cross-validation refinement
- [ ] Rainfall trigger integration for dynamic susceptibility updating

---

## Tools & Libraries

- **Python:** Scikit-learn, XGBoost, SHAP, Pandas, NumPy, Matplotlib, Seaborn,
  Rasterio, GeoPandas
- **GIS:** QGIS
- **Data Sources:** SRTM DEM, Sentinel-1, Sentinel-2, GSI Geological Maps,
  SSDMA Landslide Inventory, IMD Rainfall Data, OpenStreetMap, VIIRS NTL
