# Landslide Susceptibility Mapping — Sikkim, India

Machine learning and deep learning-based Landslide Susceptibility Mapping (LSM) for
Sikkim, India, integrating 17 topographic, hydrological, geological, environmental,
anthropogenic, and SAR-derived conditioning factors. Six models - Random Forest,
XGBoost, ResNet18, SE-CNN, EfficientNetB0, and TabNet - were trained, validated, and
compared for spatial risk zonation, with the final susceptibility map built from an
equal-weighted ensemble of the top-performing models. SHAP analysis was used for
model interpretability.

**Author:** Varun Saxena | NSUT
**Study Area:** Sikkim, India
**Methods:** Random Forest, XGBoost, ResNet18, SE-CNN, EfficientNetB0, TabNet, SHAP Analysis, VIF Filtering, Spearman Correlation
**Tools:** Python, Scikit-learn, XGBoost, PyTorch/TensorFlow, SHAP, QGIS
**Status:** ML + DL phases complete — ensemble susceptibility mapping
**Conference:** Accepted & Presented — ICCDRH 2026

---

## Study Area

Sikkim is a small Himalayan state in northeastern India, characterised by steep terrain,
active tectonics, high seismicity, intense monsoon rainfall, and rapid land-use change —
making it one of the most landslide-prone regions in India. The study covers the entire
state, encompassing diverse geological zones, elevation gradients, and a dense network
of rivers and fault systems.

---

## Input Feature Generation

17 landslide conditioning factors were derived from topographic, hydrological,
geological, environmental, and remote sensing datasets. All layers were resampled to
30 m resolution, projected to a common coordinate system, and clipped to the Sikkim
boundary. Categorical variables (LULC, Lithology, and Soil Type) were one-hot encoded
for both ML and DL models.

| Category      | Factors                                                                |
| ------------- | ---------------------------------------------------------------------- |
| Topographic   | Slope (°), Sin Aspect, Profile Curvature, Relative Relief, LS Factor, TWI |
| Hydrological  | SPI (Stream Power Index), Distance to Drainage (km)                    |
| Geological    | Lithology, Soil Type                                                    |
| Environmental | NDVI, Land Use / Land Cover (LULC)                                     |
| Anthropogenic | Distance to Roads (km), NTL (Night-Time Lights), Rainfall               |
| SAR-derived   | SAR (VV−VH), SAR (VV/VH) (Sentinel-1)                                   |

**Multicollinearity handling:**

- Spearman Correlation Matrix computed for all variables
- VIF (Variance Inflation Factor) filtering applied to eliminate redundant variables and retain the most informative predictors

---

## Methodology

### Data Preparation

- Landslide inventory compiled from historical records and remote sensing
- Non-landslide points sampled with spatial constraints to avoid class overlap
- Stratified train-test split applied for model validation
- Categorical variables (LULC, Lithology, Soil Type) one-hot encoded for both ML and DL models

### Models

**Machine Learning**

- **Random Forest** — ensemble of decision trees with majority voting; hyperparameter tuning via GridSearchCV; feature importance from mean decrease in impurity
- **XGBoost** — gradient boosted trees with L1/L2 regularisation; hyperparameter tuning via RandomizedSearchCV
- **TabNet** — attention-based deep tabular learning model

**Deep Learning (patch-based, on rasterized conditioning factors)**

- **ResNet18**
- **SE-CNN** (Squeeze-and-Excitation CNN)
- **EfficientNetB0**

**Interpretability:** SHAP (SHapley Additive exPlanations) used across models for feature importance and interpretable predictor ranking.

### Validation Metrics

- Accuracy, Precision, Recall, F1-Score
- Confusion Matrix
- ROC-AUC

---

## Results

### Model Performance

| Model          | F1 Score | ROC-AUC   | Accuracy | Recall | Precision |
| -------------- | -------- | --------- | -------- | ------ | --------- |
| Random Forest  | 0.900    | **0.972** | 0.90     | 0.89   | 0.90      |
| XGBoost        | 0.899    | 0.964     | 0.90     | 0.90   | 0.90      |
| TabNet         | 0.850    | 0.932     | 0.85     | 0.86   | 0.85      |
| EfficientNetB0 | 0.885    | 0.918     | 0.88     | 0.94   | 0.88      |
| ResNet18       | 0.893    | 0.907     | 0.88     | 0.98   | 0.90      |
| SE-CNN         | 0.896    | 0.901     | 0.89     | 0.97   | 0.90      |

Random Forest achieved the highest ROC-AUC (0.972) and the highest overall F1-score
(0.900). Among the deep learning models specifically, SE-CNN obtained the highest
F1-score (0.896). Transportation networks and environmental factors showed strong
control on landslide susceptibility patterns.

### Key Findings from SHAP Analysis

Top predictors of landslide susceptibility, in order:

1. **NTL (Night-Time Lights)** — proxy for anthropogenic pressure
2. **Distance to Roads** — low distance = high susceptibility
3. **Rainfall** — higher rainfall zones show elevated susceptibility
4. **NDVI** — high vegetation cover reduces susceptibility; degraded areas more prone
5. **Slope** — steep slopes consistently increase susceptibility

NTL and Distance to Roads stand out as the strongest signals overall, indicating a
strong relationship between human activity and landslide occurrence - the overlap of
dense population, transportation corridors, and highly susceptible terrain highlights
the need for sustainable development and risk-informed planning.

### LSM Output

Individual susceptibility maps were generated for all six models (Random Forest,
XGBoost, ResNet18, SE-CNN, EfficientNetB0, TabNet), visualised in QGIS (EPSG:32645).
The **final susceptibility map** is an equal-weighted ensemble of the top-performing
models — Random Forest, XGBoost, ResNet18, SE-CNN, and EfficientNetB0 (TabNet was
benchmarked but excluded from the final ensemble) — combining tabular ML and
patch-based deep learning to reduce single-model bias.

Generated susceptibility zones show landslide occurrences concentrated mainly within
moderate-to-very-high susceptibility classes, and validation shows good agreement
between predicted zones and observed landslide locations, supporting the reliability
of the framework. High and very high susceptibility zones are concentrated along river
valleys, steep slopes, and areas with high road density and low vegetation cover.

### Population Exposure

| Susceptibility Class | Population |
| --------------------- | ---------- |
| Very Low               | 13,326     |
| Low                    | 27,356     |
| Moderate               | 88,731     |
| High                   | 3,02,250   |
| Very High              | 1,02,334   |

**76% of Sikkim's population resides within High and Very High susceptibility zones**,
underscoring the direct overlap between human settlement, transportation
infrastructure, and landslide-prone terrain.

---

## Output Files

| File                       | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| `LSM_ensemble.png`         | Final ensemble Landslide Susceptibility Map — QGIS output    |
| `Per-model_susceptibility_maps.png` | Susceptibility maps of RF, XGB, ResNet18, SECNN, EfficientNetB0 & TabNet models |
| `feature_imp.png`        | SHAP beeswarm plots                                |
| `population_exposure.png`  | Population-by-susceptibility-class chart                      |

---

## Tools & Libraries

- **Python:** Scikit-learn, XGBoost, PyTorch/TensorFlow, SHAP, Pandas, NumPy, Matplotlib, Seaborn, Rasterio, GeoPandas
- **GIS:** QGIS, GEE
