# 📊 Análisis y Preparación de 9 Datasets — Machine Learning

Este repositorio contiene notebooks de Jupyter con análisis exploratorio, limpieza y preparación para modelado de 9 datasets clásicos de Machine Learning.

## 📁 Estructura

| Notebook | Dataset | Tipo de Problema | Modelo Aplicado |
|---|---|---|---|
| `01_ames_housing.ipynb` | Ames Housing | Regresión | Random Forest Regressor |
| `02_mimic_iii.ipynb` | MIMIC-III (Demo UCI) | Clasificación Binaria | Regresión Logística |
| `03_nhanes.ipynb` | NHANES (CDC) | Clasificación Binaria | Gradient Boosting |
| `04_bike_sharing.ipynb` | Bike Sharing Demand | Regresión | Gradient Boosting Regressor |
| `05_adult_census.ipynb` | Adult Census Income | Clasificación Binaria | Random Forest + Log. Reg. |
| `06_credit_approval.ipynb` | Credit Approval (UCI) | Clasificación Binaria | Regresión Logística |
| `07_australian_credit.ipynb` | Australian Credit (Statlog) | Clasificación Binaria | Gradient Boosting + CV 10-Fold |
| `08_breast_cancer.ipynb` | Breast Cancer Wisconsin | Clasificación Médica | SVM + Random Forest |
| `09_meningitis.ipynb` | Meningitis Missing Values | Clasificación Clínica | Random Forest |

## 🔧 Librerías Requeridas

```bash
pip install pandas numpy scikit-learn matplotlib seaborn openpyxl nbformat
```

## 📌 Contenido de cada Notebook

Cada notebook incluye:
1. **Carga de Datos** — lectura del archivo raw
2. **Descripción General** — shape, tipos, estadísticas
3. **Análisis de Valores Nulos** — detección y estrategia de imputación
4. **Limpieza** — tratamiento específico por dataset
5. **EDA** — visualizaciones (histogramas, correlaciones, heatmaps)
6. **Preparación** — encoding, normalización, train/test split
7. **Modelado** — al menos un modelo supervisado con métricas
8. **Conclusiones** — resumen y decisiones técnicas documentadas

## 📐 Conceptos Cubiertos

- Función Sigmoide: σ(z) = 1/(1+e^{-z})
- Binary Cross-Entropy: L = -[y·log(p) + (1-y)·log(1-p)]
- Data Leakage (Dataset 4)
- Fairness in ML / Sesgo Algorítmico (Dataset 5)
- Imputación KNN vs Media (Datasets 6, 9)
- Validación Cruzada 10-Fold (Dataset 7)
- Missingness MCAR / MAR / MNAR (Dataset 9)
