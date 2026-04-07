# 🔬 Dataset 8: Breast Cancer Wisconsin (Original)

## Descripción General

El **Breast Cancer Wisconsin (Original) Dataset** fue donado al Repositorio UCI en **1992** por el **Dr. William H. Wolberg** de la Universidad de Wisconsin, Madison. Es uno de los datasets médicos más ampliamente utilizados en la historia del Machine Learning.

Los datos provienen de **Aspiraciones con Aguja Fina (FNA — Fine Needle Aspiration)** de masas mamarias. En este procedimiento, el médico introduce una aguja fina en el tumor, extrae células y las examina bajo el microscopio para evaluar sus características citológicas — sin necesidad de cirugía.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Dr. William H. Wolberg, Universidad de Wisconsin (1992) |
| **Dominio** | Medicina — Oncología de mama |
| **Tipo de Problema** | Clasificación binaria supervisada (médica) |
| **Variable Objetivo** | `Class` — 2 (Benigno) / 4 (Maligno) |
| **Total de Registros** | 699 |
| **Total de Variables** | 9 atributos citológicos + ID + Clase |
| **Clase Benigna** | 458 instancias (65.5%) |
| **Clase Maligna** | 241 instancias (34.5%) |
| **Archivos incluidos** | `breast-cancer-wisconsin.data`, `breast-cancer-wisconsin.names`, `wdbc.data`, `wdbc.names`, `wpbc.data`, `wpbc.names`, `unformatted-data`, `Index` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `breast-cancer-wisconsin.data` | **Dataset principal (Original)** — 699 registros, sin encabezado, nulos con `?` |
| `breast-cancer-wisconsin.names` | Documentación oficial del dataset original |
| `wdbc.data` | Dataset Wisconsin Diagnostic BC (versión más nueva, sin nulos, 30 features) |
| `wdbc.names` | Documentación del WDBC |
| `wpbc.data` | Dataset Wisconsin Prognostic BC (pronóstico, eventos de recurrencia) |
| `wpbc.names` | Documentación del WPBC |
| `unformatted-data` | Datos en formato original sin procesar |
| `Index` | Índice de archivos UCI |

> **Para este análisis usamos `breast-cancer-wisconsin.data`** (el dataset Original).

---

## Diccionario de Variables — Dataset Original

> Los 9 atributos son evaluaciones subjetivas realizadas por el patólogo observando las células bajo el microscopio. Todos se califican en una **escala del 1 al 10**.

### 🔑 Identificador

| Columna | Tipo | Descripción |
|---|---|---|
| `Sample_ID` | Entero | Código de muestra del paciente — **no predictivo, eliminar** |

---

### 🔬 Los 9 Atributos Citológicos (Escala 1–10)

> **Interpretación de la escala:**
> - **1** = Normal / saludable
> - **10** = Muy anormal / altamente maligno

| Columna | Escala | Descripción Clínica Completa |
|---|---|---|
| `Clump_Thickness` | 1–10 | **Grosor del grupo celular.** Las células benignas tienden a agruparse en monocapas; las malignas se agrupan en multicapas irregulares. Escala: 1=monocapa, 10=múltiples capas densas |
| `Uniformity_Cell_Size` | 1–10 | **Uniformidad del tamaño celular.** Las células cancerosas suelen variar significativamente en tamaño. Escala: 1=todas iguales, 10=variación extrema |
| `Uniformity_Cell_Shape` | 1–10 | **Uniformidad de la forma celular.** Altamente correlacionado con el anterior. Determina si las células son redondas y uniformes (benigno) o irregulares y pleomórficas (maligno) |
| `Marginal_Adhesion` | 1–10 | **Adhesión marginal.** Cuantifica cuánto se adhieren las células entre sí. Las células normales se pegan firmemente; las malignas pierden esta adhesión (característica del proceso metastásico). Escala: 1=alta adhesión, 10=sin adhesión |
| `Single_Epithelial_Cell_Size` | 1–10 | **Tamaño de célula epitelial individual.** Las células epiteliales normales son pequeñas y uniformes. Escala: 1=pequeñas/normales, 10=agrandadas significativamente |
| `Bare_Nuclei` | 1–10 | **Núcleos desnudos.** Los núcleos que no están rodeados por citoplasma. Son raros en tejido benigno pero comunes en cáncer. ⚠️ **Tiene 16 valores nulos** |
| `Bland_Chromatin` | 1–10 | **Cromatina uniforme (*blanda*).** La cromatina (material del núcleo) es uniforme y fina en tejido benigno; granular y gruesa en tejido maligno. Escala: 1=fina/uniforme, 10=muy granular |
| `Normal_Nucleoli` | 1–10 | **Nucléolos normales.** El nucléolo es una estructura dentro del núcleo. Normalmente pequeño e invisible; en células cancerosas se agranda y se vuelve prominente. Escala: 1=no visible, 10=muy prominente |
| `Mitoses` | 1–10 | **Tasa de mitosis.** La mitosis es la división celular. Alta tasa de mitosis = crecimiento descontrolado = malignidad. Es el predictor más específico de agresividad. Escala: 1=mitosis raras, 10=mitosis frecuentes |

---

### 🎯 Variable Objetivo

| Columna | Valores | Descripción | Distribución |
|---|---|---|---|
| `Class` | 2 / 4 | 2 = **Benigno**, 4 = **Maligno** | 458 Benignos (65.5%) / 241 Malignos (34.5%) |

> **Para el modelo:** Binarizar como `TARGET = (Class == 4).astype(int)` → 1=Maligno, 0=Benigno

---

## ⚠️ El Valor Nulo Crítico: `Bare_Nuclei`

| Columna | Nulos | % | Impacto Clínico |
|---|---|---|---|
| `Bare_Nuclei` | 16 | 2.3% | **ALTO** — es uno de los mejores predictores de malignidad |

### Estrategia de Imputación Recomendada

```python
# ✅ CORRECTO: Imputar por la mediana DENTRO de cada clase
df['Bare_Nuclei'] = df.groupby('Class')['Bare_Nuclei'].transform(
    lambda x: x.fillna(x.median())
)
```

**¿Por qué por clase?**
- Mediana de `Bare_Nuclei` en tumores benignos ≈ **1** (pocos núcleos desnudos)
- Mediana de `Bare_Nuclei` en tumores malignos ≈ **10** (muchos núcleos desnudos)

Usar la mediana global (~1 o ~3) destruiría la señal diagnóstica para los 16 casos faltantes que son malignos. Al imputar dentro de cada clase, preservamos la información clínica correcta.

---

## 🔍 Correlaciones con la Variable Objetivo (`Class`)

| Atributo | Correlación con Class |
|---|---|
| `Uniformity_Cell_Size` | 0.82 |
| `Uniformity_Cell_Shape` | 0.82 |
| `Bare_Nuclei` | 0.82 |
| `Bland_Chromatin` | 0.76 |
| `Normal_Nucleoli` | 0.72 |
| `Clump_Thickness` | 0.72 |
| `Marginal_Adhesion` | 0.70 |
| `Single_Epithelial_Cell_Size` | 0.69 |
| `Mitoses` | 0.42 |

> **Nota:** Las dos variables de uniformidad tienen correlación de 0.91 entre sí (multicolinealidad). Podría considerarse eliminar una en modelos lineales.

---

## ⚕️ Consideraciones Médicas para el Modelo

### Tipos de Error y su Costo Clínico

| Tipo de Error | Descripción | Consecuencia |
|---|---|---|
| **Falso Negativo (FN)** | Predecir Benigno cuando es **Maligno** | ⚠️ **ERROR MÁS GRAVE** — el paciente no recibe tratamiento, el cáncer avanza |
| **Falso Positivo (FP)** | Predecir Maligno cuando es **Benigno** | Genera ansiedad innecesaria y posibles biopsias adicionales, pero no pone en riesgo la vida |

### Implicación para el Umbral de Decisión

```python
# En medicina se prioriza RECALL alto para la clase Maligno
# Bajar el umbral de 0.5 a 0.3 reduce FN a costa de más FP
y_pred_30 = (y_prob >= 0.30).astype(int)

# Comparar métricas
from sklearn.metrics import recall_score, precision_score
recall_maligno = recall_score(y_test, y_pred_30)  # Sensibilidad
precision = precision_score(y_test, y_pred_30)
```

---

## 📊 Versiones del Dataset Wisconsin

| Dataset | Variables | Registros | Objetivo | Estado |
|---|---|---|---|---|
| **Original (BCW-O)** | 9 citológicas (escala 1–10) | 699 | Benigno/Maligno | Este análisis |
| **Diagnostic (WDBC)** | 30 features numéricas | 569 | Benigno/Maligno | Versión más moderna y limpia |
| **Prognostic (WPBC)** | 33 features + tiempo | 198 | Recurrencia | Para análisis de supervivencia |

---

## 🧹 Checklist de Preparación

- [ ] Cargar con `na_values='?'` y asignar nombres de columnas manualmente
- [ ] Eliminar `Sample_ID` (identificador de muestra, no predictivo)
- [ ] Imputar `Bare_Nuclei` **por mediana dentro de cada clase** (no mediana global)
- [ ] Binarizar el target: `TARGET = (Class == 4).astype(int)`
- [ ] Verificar que todos los valores están en rango 1–10 después de la imputación
- [ ] Priorizar **Recall** de clase Maligno sobre Accuracy general
- [ ] Considerar ajustar el umbral de decisión por debajo de 0.5

---

## 📚 Referencias

- Wolberg, W.H. & Mangasarian, O.L. (1990). *Multisurface method of pattern separation for medical diagnosis applied to breast cytology*. Proceedings of the National Academy of Sciences, 87(23), 9193–9196.
- UCI Repository: https://archive.ics.uci.edu/ml/datasets/breast+cancer+wisconsin+(original)
