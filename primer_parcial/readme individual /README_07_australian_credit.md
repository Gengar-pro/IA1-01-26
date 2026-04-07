# 🦘 Dataset 7: Statlog — Australian Credit Approval

## Descripción General

El **Australian Credit Approval Dataset** forma parte del proyecto **STATLOG**, una iniciativa europea de los años 90 cuyo objetivo era comparar sistemáticamente algoritmos de clasificación en datasets del mundo real bajo condiciones controladas.

Este dataset es considerado el "hermano gemelo procesado" del Credit Approval Dataset (Dataset 6). Se cree que proviene de la misma fuente original, pero el equipo de STATLOG lo **limpió, codificó y estandarizó** completamente, eliminando valores faltantes y convirtiendo todas las variables categóricas a formato numérico.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Proyecto STATLOG — UCI ML Repository |
| **Dominio** | Finanzas / Crédito bancario (posiblemente Australia) |
| **Tipo de Problema** | Clasificación binaria supervisada |
| **Variable Objetivo** | `A15` — 0 (rechazado) / 1 (aprobado) |
| **Total de Registros** | 690 |
| **Total de Variables** | 14 atributos + 1 variable objetivo |
| **Valores Faltantes** | ✅ **NINGUNO** — dataset completamente limpio |
| **Archivos incluidos** | `australian.dat`, `australian.doc`, `Index` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `australian.dat` | Datos principales (690 registros, separados por espacio, **sin encabezado**) |
| `australian.doc` | Documentación técnica del dataset en formato texto |
| `Index` | Índice de archivos UCI |

> **Nota de carga:**
> ```python
> df = pd.read_csv('australian.dat', sep=' ', header=None,
>                  names=[f'A{i}' for i in range(1,15)] + ['TARGET'])
> ```

---

## Diccionario de Variables

> Al igual que el Dataset 6, los nombres reales de las variables son confidenciales. Sin embargo, gracias a la documentación `australian.doc`, se conocen los **tipos de dato** y la naturaleza de cada variable.

### 📝 Variables Categóricas (convertidas a entero)

| Columna | Tipo Original | Valores Posibles (post-encoding) | Descripción Aproximada |
|---|---|---|---|
| `A1` | Categórica (0/1) | 0, 1 | Variable binaria (posiblemente género) |
| `A4` | Categórica (1/2/3) | 1, 2, 3 | Variable de 3 categorías |
| `A5` | Categórica (1–14) | 1–14 | Múltiples categorías (posiblemente ocupación) |
| `A6` | Categórica (1–9) | 1–9 | 9 categorías |
| `A8` | Categórica (0/1) | 0, 1 | Variable binaria |
| `A9` | Categórica (0/1) | 0, 1 | Variable binaria |
| `A11` | Categórica (0/1) | 0, 1 | Variable binaria |
| `A12` | Categórica (1/2/3) | 1, 2, 3 | 3 categorías |

---

### 🔢 Variables Continuas (Numéricas)

| Columna | Tipo | Rango Observado | Descripción Aproximada |
|---|---|---|---|
| `A2` | Float | 15.17–76.75 | Posiblemente edad (rango coherente) |
| `A3` | Float | 0.0–480.0 | Posiblemente ingresos o deuda |
| `A7` | Float | 0.0–67.0 | Posiblemente años de empleo o experiencia |
| `A10` | Entero | 0–67 | Posiblemente número de consultas crediticias |
| `A13` | Entero | 1–10,000 | Posiblemente código postal o variable financiera |
| `A14` | Entero | 0–100,000 | Posiblemente saldo o monto de crédito solicitado |

---

### 🎯 Variable Objetivo

| Columna | Valores | Distribución | Descripción |
|---|---|---|---|
| `TARGET` (A15) | 0 / 1 | 383 rechazados (55.5%) / 307 aprobados (44.5%) | 0 = Crédito rechazado, 1 = Crédito aprobado |

---

## 🔄 Diferencias Clave con el Dataset 6 (Credit Approval)

| Aspecto | Dataset 6 (Credit Approval) | Dataset 7 (Australian Credit) |
|---|---|---|
| **Valores nulos** | Sí (`?` en 7 columnas) | ❌ **Ninguno** |
| **Encoding categóricas** | Texto (`a`, `b`, `v`, `gg`...) | Enteros (0, 1, 2, 3...) |
| **Número de atributos** | 15 | 14 (se eliminó uno) |
| **Variable objetivo** | `+` / `-` (texto) | `0` / `1` (numérico) |
| **Propósito** | Dataset real "sucio" | Dataset limpio para comparar algoritmos |
| **Distribución de clases** | Idéntica (690 registros) | Idéntica |

---

## ✅ Ventaja Principal: Benchmark Limpio

Al no requerir limpieza ni preprocesamiento de missingness, este dataset es **ideal para**:

1. **Comparar algoritmos** directamente (el propósito original de STATLOG)
2. **Benchmarking** — cualquier diferencia en rendimiento entre modelos se debe al algoritmo, no al preprocesamiento
3. **Validación cruzada** rigurosa sin preocuparse por leakage de imputación
4. **Enseñanza** de conceptos de clasificación sin distracciones de limpieza

---

## 📐 Protocolo de Evaluación Recomendado: 10-Fold Cross Validation

```python
from sklearn.model_selection import KFold, cross_val_score
from sklearn.preprocessing import StandardScaler
import numpy as np

kf = KFold(n_splits=10, shuffle=True, random_state=42)
X_scaled = StandardScaler().fit_transform(X)

modelos = {
    'Logistic Regression': LogisticRegression(),
    'Random Forest': RandomForestClassifier(n_estimators=100),
    'Gradient Boosting': GradientBoostingClassifier(),
    'SVM (RBF)': SVC(probability=True)
}

for nombre, modelo in modelos.items():
    scores = cross_val_score(modelo, X_scaled, y, cv=kf, scoring='roc_auc')
    print(f"{nombre}: {scores.mean():.4f} ± {scores.std():.4f}")
```

---

## 📊 Rendimiento Esperado de Modelos (Referencias Históricas del Proyecto STATLOG)

| Algoritmo | Accuracy Histórica (STATLOG) |
|---|---|
| Linear Discriminant Analysis | ~85% |
| Logistic Regression | ~85–87% |
| Neural Network (MLP) | ~85–88% |
| Decision Tree (C4.5) | ~84–86% |
| Nearest Neighbour | ~80–83% |
| Naive Bayes | ~77–80% |

> **Modelos modernos** (Random Forest, Gradient Boosting, SVM RBF) típicamente alcanzan 87–92% de accuracy y 0.92–0.95 de ROC-AUC.

---

## 📐 Función de Costo — Binary Cross-Entropy

La función de pérdida usada en regresión logística (y en redes neuronales) es la **Binary Cross-Entropy** (también llamada Log-Loss):

```
L = -[y · log(p) + (1-y) · log(1-p)]
```

Donde:
- `y` = clase real (0 o 1)
- `p` = probabilidad predicha por la función sigmoide σ(z) = 1/(1+e^{-z})

```python
import numpy as np
eps = 1e-15
y_pred_prob = np.clip(y_prob, eps, 1-eps)
log_loss = -np.mean(y_test * np.log(y_pred_prob) + (1-y_test) * np.log(1-y_pred_prob))
print(f"Binary Cross-Entropy: {log_loss:.4f}")
```

---

## 🧹 Checklist de Preparación

- [ ] Cargar con `sep=' '` y sin header, asignando nombres A1–A14 + TARGET
- [ ] Verificar que no hay nulos: `df.isnull().sum().sum()` debe ser 0
- [ ] Escalar variables numéricas con `StandardScaler` para modelos lineales y SVM
- [ ] Los modelos de árbol (Random Forest, GBM) no requieren escalado
- [ ] Usar 10-Fold Cross Validation para comparación justa de modelos
- [ ] Reportar tanto Accuracy como ROC-AUC (más robusto con clases desbalanceadas)

---

## 📚 Referencias

- Michie, D., Spiegelhalter, D.J. & Taylor, C.C. (1994). *Machine Learning, Neural and Statistical Classification*. Proyecto STATLOG.
- UCI: https://archive.ics.uci.edu/ml/datasets/statlog+(australian+credit+approval)
