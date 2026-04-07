# 🧠 Dataset 9: Meningitis Dataset with Missing Values

## Descripción General

El **Meningitis Dataset with Missing Values** es un dataset clínico diseñado específicamente como **campo de entrenamiento para técnicas avanzadas de manejo de datos faltantes** en un contexto epidemiológico/médico real. Fue publicado en **Kaggle** y simula fielmente los problemas de datos incompletos que enfrentan los médicos en la práctica clínica diaria.

La **meningitis** es una inflamación de las membranas (meninges) que rodean el cerebro y la médula espinal. Puede ser causada por bacterias, virus u hongos. La meningitis bacteriana es una **emergencia médica** con alta mortalidad si no se trata rápidamente.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Kaggle — chantest |
| **Dominio** | Medicina — Neurología / Enfermedades Infecciosas |
| **Tipo de Problema** | Clasificación supervisada / Análisis de missingness |
| **Variable Objetivo** | `Diagnosis`, `Risk_Level` u `Outcome` |
| **Total de Registros** | 1,200 |
| **Total de Variables** | 14 |
| **Archivos incluidos** | `mening missing 12.csv` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `mening missing 12.csv` | Dataset completo (1,200 pacientes) con valores faltantes estructurales |

---

## Diccionario de Variables

### 👤 Variables Demográficas

| Columna | Tipo | Descripción | Rango / Valores | Nulos Esperados |
|---|---|---|---|---|
| `Patient_ID` | Entero | Identificador único del paciente — **NO usar en modelo** | Numérico secuencial | No |
| `Age` | Continua | Edad del paciente en años | 0–90+ | Pocos |
| `Gender` | Categórica | Género del paciente | M=Masculino, F=Femenino | Pocos |

---

### 🩸 Variables de Laboratorio — Sangre

| Columna | Tipo | Descripción Clínica | Unidades | Rango Normal | Nulos Esperados |
|---|---|---|---|---|---|
| `WBC_Blood_Count` | Continua | **Conteo de Glóbulos Blancos en sangre periférica** — elevado en infecciones | células×10³/µL | 4.5–11.0 | Moderados |
| `Hemoglobin` | Continua | **Hemoglobina** — puede estar baja si hay infección severa o anemia | g/dL | Hombres: 13.5–17.5, Mujeres: 12.0–15.5 | Moderados |
| `Platelets` | Continua | **Plaquetas** — pueden descender en infecciones graves (trombocitopenia) | células×10³/µL | 150–400 | Moderados |
| `CRP_Level` | Continua | **Proteína C Reactiva** — marcador inflamatorio de fase aguda. Muy elevada en meningitis bacteriana | mg/L | <10 (normal), >100 (infección severa) | Significativos |

---

### 🧪 Variables de Líquido Cefalorraquídeo (LCR)

> El **LCR** se obtiene mediante una **punción lumbar** (procedimiento invasivo). Es el estudio definitivo para diagnosticar meningitis. Su ausencia en el dataset es **MNAR** — los médicos no lo realizan si el paciente tiene contraindicaciones o síntomas muy leves/graves.

| Columna | Tipo | Descripción Clínica | Unidades | Meningitis Bacteriana | Meningitis Viral | Normal |
|---|---|---|---|---|---|---|
| `WBC_Count` | Continua | **Leucocitos en LCR** (pleocitosis). El hallazgo más importante — el LCR normal casi no tiene glóbulos blancos | células/µL | >1000 (predominio PMN) | 10–1000 (predominio linfocitos) | <5 |
| `Protein_Level` | Continua | **Proteínas en LCR.** Las bacterias dañan la barrera hematoencefálica, aumentando proteínas | mg/dL | >100 (a veces >500) | 50–100 | 15–45 |
| `Glucose_Level` | Continua | **Glucosa en LCR.** Las bacterias consumen glucosa — es la señal más específica de meningitis bacteriana | mg/dL | <40 (muy bajo) | Normal o ligeramente bajo | 50–80 |

> **La regla de oro del LCR en meningitis bacteriana:**
> - WBC_Count > 1000 (PMN predomina)
> - Protein_Level > 100 mg/dL
> - Glucose_Level < 40 mg/dL (o ratio LCR/sangre < 0.4)
> - La tríada completa confirma meningitis bacteriana con alta especificidad

---

### 🌡️ Variables Clínicas / Síntomas

| Columna | Tipo | Descripción Clínica | Valores | Notas |
|---|---|---|---|---|
| `Pathogen_Present` | Binaria | ¿Se detectó algún patógeno en cultivo o tinción de Gram? | 0=No, 1=Sí | Cultivos negativos no descartan meningitis bacteriana (hasta 30% falsos negativos) |

---

### 🎯 Variables Objetivo

| Columna | Tipo | Descripción | Valores |
|---|---|---|---|
| `Diagnosis` | Categórica | Diagnóstico final del caso | Bacterial, Viral, Fungal, No Meningitis (varía según versión) |
| `Outcome` | Categórica | Resultado clínico del paciente | Survived, Died / 0=Supervivencia, 1=Muerte (varía) |
| `Risk_Level` | Ordinal | Nivel de riesgo asignado al paciente | Low, Medium, High / 1, 2, 3 (varía) |

> **Recomendación:** Usar `Risk_Level` como target principal para clasificación multiclase, o binarizar `Outcome` para clasificación binaria de mortalidad.

---

## 🔴 El Desafío Central: Tipos de Missingness

Este dataset está diseñado para enseñar los **3 mecanismos de datos faltantes en medicina**:

### MCAR — Missing Completely At Random (Falta Completamente al Azar)

**Ejemplo:** El sistema de laboratorio tuvo un fallo técnico y perdió 5 muestras al azar.

**Características:**
- Los datos faltantes son independientes de todas las variables (observadas y no observadas)
- La submuestra completa es representativa de la muestra total
- **Impacto:** Reduce el poder estadístico pero no genera sesgo

**Diagnóstico:** Test de Little's MCAR Test

```python
# Si la tasa de missingness no varía entre subgrupos → posiblemente MCAR
df.groupby('Diagnosis')['WBC_Blood_Count'].apply(lambda x: x.isna().mean())
```

---

### MAR — Missing At Random (Falta al Azar — Condicionalmente)

**Ejemplo:** Las mujeres son menos propensas a dar su peso, pero esto no depende del peso en sí, sino del género (variable observable).

**Características:**
- Los datos faltantes dependen de otras variables **observadas** en el dataset
- La probabilidad de que falte un dato puede modelarse con las otras variables
- **Impacto:** Genera sesgo si se ignora, pero puede corregirse con imputación

**En meningitis:** Los datos de proteína en LCR faltan más en pacientes jóvenes porque los médicos son menos invasivos con ellos → depende de `Age` (observable).

---

### MNAR — Missing Not At Random (Falta por Razón del Propio Dato)

**Ejemplo:** Los pacientes con peor salud mental evitan reportar síntomas de depresión.

**Características:**
- Los datos faltantes dependen del valor del dato en sí
- **El más peligroso** — no puede corregirse solo con imputación
- Requiere modelos de selección o supuestos explícitos

**En meningitis (ejemplo clave):**
- `Glucose_Level` en LCR falta cuando el médico decide NO hacer punción lumbar
- ¿Por qué no la hace? Porque el paciente tiene síntomas muy leves (glucosa probablemente normal) **O** está muy inestable (glucosa probablemente anormal)
- En ambos casos, el valor faltante está relacionado con el propio valor → **MNAR**

---

## 💉 Patrones de Missingness Clínicamente Esperados

| Variable | Razón Clínica de Faltantes | Tipo Probable |
|---|---|---|
| `WBC_Count` (LCR) | No se hace punción lumbar en todos los pacientes | MNAR |
| `Protein_Level` (LCR) | Igual que WBC_Count — mismo procedimiento | MNAR |
| `Glucose_Level` (LCR) | Igual — además, la glucosa baja es señal de bacteriana, su ausencia sesgada | MNAR |
| `CRP_Level` | No se ordena en todos los casos (criterio médico) | MAR (depende de severidad observada) |
| `Hemoglobin` | A veces no se realiza hemograma completo en urgencias | MAR |
| `WBC_Blood_Count` | Similar al hemograma | MAR |
| `Platelets` | Similar | MAR |

---

## 🔧 Estrategias de Imputación — Comparación

### ❌ Imputación por Media/Moda (Simple) — NO recomendada

```python
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy='mean')
df_mean = pd.DataFrame(imputer.fit_transform(df_numeric), columns=df_numeric.columns)
```

**Problema:** Destruye la señal diagnóstica. Una proteína en LCR de 350 mg/dL (alta → bacteriana) imputada con la media (ej: 80 mg/dL) convierte un caso altamente sugestivo de meningitis bacteriana en algo aparentemente normal.

---

### ✅ Imputación KNN — Recomendada

```python
from sklearn.impute import KNNImputer
imputer = KNNImputer(n_neighbors=7)
df_knn = pd.DataFrame(imputer.fit_transform(df_numeric), columns=df_numeric.columns)
```

**Ventaja:** Usa los 7 pacientes más similares (en las variables observadas) para estimar el valor faltante. Preserva relaciones multivariadas — si un paciente tiene WBC_Count=2000 y Protein_Level faltante, sus vecinos con WBC alto también tendrán Protein alto, dando una imputación clínicamente coherente.

---

### ✅✅ MICE — Imputación Múltiple por Ecuaciones Encadenadas (Gold Standard)

```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer

mice = IterativeImputer(max_iter=10, random_state=42)
df_mice = pd.DataFrame(mice.fit_transform(df_numeric), columns=df_numeric.columns)
```

**Ventaja:** El método más robusto — imputa cada variable como función de todas las demás variables, iterando hasta convergencia. Maneja mejor el MNAR que el KNN.

---

## 🧹 Checklist de Preparación

- [ ] Eliminar `Patient_ID` (identificador, no predictivo)
- [ ] Visualizar el patrón de missingness con heatmap
- [ ] Analizar si los faltantes son MCAR/MAR/MNAR (análisis de Little's test si es posible)
- [ ] Comparar distribuciones antes y después de imputación (histogramas superpuestos)
- [ ] Usar KNN o MICE Imputer (NO media simple para variables clínicas)
- [ ] Encodear `Gender` (Label Encoding)
- [ ] Definir variable objetivo: `Risk_Level`, `Diagnosis` u `Outcome`
- [ ] Documentar el impacto de diferentes estrategias de imputación en el modelo

---

## 📐 Reglas Diagnósticas Clave — Contexto Clínico

| Hallazgo | Interpretación |
|---|---|
| `WBC_Count` > 1000 + `Protein_Level` > 100 + `Glucose_Level` < 40 | Alta sospecha de **meningitis bacteriana** → emergencia |
| `WBC_Count` 10–500 (linfocitos) + glucosa normal | Sugiere **meningitis viral** → menos urgente |
| `CRP_Level` > 100 mg/L | Marcador de inflamación severa, apoya bacteriana |
| `Pathogen_Present` = 1 | Confirma etiología infecciosa |

---

## 📚 Referencias

- Tunkel, A.R. et al. (2004). *Practice Guidelines for the Management of Bacterial Meningitis*. Clinical Infectious Diseases, 39(9), 1267–1284.
- Kaggle Dataset: https://www.kaggle.com/datasets/chantest/meningitis-dataset-with-missing-values
- Sterne, J.A.C. et al. (2009). *Multiple imputation for missing data in epidemiological and clinical research*. BMJ, 338, b2393.
