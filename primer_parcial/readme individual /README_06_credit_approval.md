# 💳 Dataset 6: Credit Approval — UCI

## Descripción General

El **Credit Approval Dataset** contiene registros de solicitudes de tarjetas de crédito. A diferencia de casi todos los datasets del mundo, este tiene un nivel de anonimización **absoluto y total**: no solo se ocultaron nombres de personas, sino que todos los nombres de atributos y sus valores fueron reemplazados por símbolos sin sentido, para proteger información comercialmente sensible.

Se cree que el dataset proviene de una institución financiera **japonesa**, aunque esto nunca fue confirmado oficialmente. Forma parte del Repositorio UCI de Machine Learning desde los años 90.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | UCI ML Repository — origen desconocido (posiblemente Japón) |
| **Dominio** | Finanzas / Crédito Bancario |
| **Tipo de Problema** | Clasificación binaria supervisada |
| **Variable Objetivo** | `A16` — `+` (aprobado) o `-` (rechazado) |
| **Total de Registros** | 690 |
| **Total de Variables** | 15 atributos + 1 variable objetivo |
| **Variables Categóricas** | 9 (A1, A4, A5, A6, A7, A9, A10, A12, A13) |
| **Variables Continuas** | 6 (A2, A3, A8, A11, A14, A15) |
| **Archivos incluidos** | `crx.data`, `crx.names`, `credit.lisp`, `credit.names`, `Index` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `crx.data` | Datos principales (690 registros, **sin encabezado**, valores nulos como `?`) |
| `crx.names` | Documentación oficial UCI con tipos de variables |
| `credit.names` | Versión alternativa de nombres (con más descripción de tipos) |
| `credit.lisp` | Archivo en formato LISP — representación del dataset en lenguaje LISP (ignorar) |
| `Index` | Índice de archivos del repositorio UCI |

> **Nota de carga:** Usar `na_values='?'` al cargar con pandas. Los nombres de columnas deben asignarse manualmente (A1–A15 + TARGET).

---

## Diccionario de Variables

> ⚠️ **ADVERTENCIA:** Todos los nombres de atributos y valores son **completamente anónimos**. No existe documentación oficial que revele qué representa cada columna. El análisis debe hacerse sin suposiciones del dominio.

### 📝 Variables Categóricas

| Columna | Tipo Dato | Valores Conocidos | Nulos |
|---|---|---|---|
| `A1` | Binaria | `a`, `b` | Sí (`?`) |
| `A4` | Nominal | `u`, `y`, `l`, `t` | Sí (`?`) |
| `A5` | Nominal | `g`, `p`, `gg` | Sí (`?`) |
| `A6` | Nominal | `c`, `d`, `cc`, `i`, `j`, `k`, `m`, `r`, `q`, `w`, `x`, `e`, `aa`, `ff` | Sí (`?`) |
| `A7` | Nominal | `v`, `h`, `bb`, `j`, `n`, `z`, `dd`, `ff`, `o` | Sí (`?`) |
| `A9` | Binaria | `t`, `f` | No |
| `A10` | Binaria | `t`, `f` | No |
| `A12` | Binaria | `t`, `f` | No |
| `A13` | Nominal | `g`, `p`, `s` | No |

---

### 🔢 Variables Continuas (Numéricas)

| Columna | Tipo Dato | Rango Observado | Nulos |
|---|---|---|---|
| `A2` | Float | 13.75–80.25 | Sí (`?`) |
| `A3` | Float | 0.0–28.0 | No |
| `A8` | Float | 0.0–28.5 | No |
| `A11` | Entero | 0–67 | No |
| `A14` | Entero | 0–2,000 | Sí (`?`) |
| `A15` | Entero | 0–100,000 | No |

---

### 🎯 Variable Objetivo

| Columna | Valores | Descripción | Distribución |
|---|---|---|---|
| `A16` (TARGET) | `+` / `-` | `+` = Crédito **aprobado**, `-` = Crédito **rechazado** | 307 aprobados (44.5%) / 383 rechazados (55.5%) |

> **Para el modelo:** Convertir a binario: `TARGET = (A16 == '+').astype(int)` → 1=Aprobado, 0=Rechazado.

---

## ⚠️ Valores Nulos — Detalle

| Columna | Tipo | Nulos | % | Estrategia Recomendada |
|---|---|---|---|---|
| `A1` | Categórica | 12 | 1.7% | Moda |
| `A2` | Continua | 12 | 1.7% | Mediana o KNN |
| `A4` | Categórica | 6 | 0.9% | Moda |
| `A5` | Categórica | 6 | 0.9% | Moda |
| `A6` | Categórica | 9 | 1.3% | Moda |
| `A7` | Categórica | 9 | 1.3% | Moda |
| `A14` | Continua | 13 | 1.9% | Mediana o KNN |

> **Desafío único:** Al no conocer el significado de las variables, no podemos usar lógica del dominio para imputar. Por ejemplo, si `A2` fuera "edad", sabríamos que no puede ser 5 años para un solicitante de tarjeta. Sin ese conocimiento, debemos depender exclusivamente de métodos matemáticos como **KNN Imputer** que usa la similitud entre registros.

---

## 🎯 Estrategia de Imputación Recomendada: KNN Imputer

```python
from sklearn.impute import KNNImputer
from sklearn.preprocessing import LabelEncoder

# 1. Encodear categóricas
le = LabelEncoder()
for col in cat_cols:
    df[col] = le.fit_transform(df[col].astype(str))  # tratar NaN como string primero

# 2. KNN Imputer con k=5 vecinos
imputer = KNNImputer(n_neighbors=5)
df_imputed = pd.DataFrame(imputer.fit_transform(df), columns=df.columns)

# 3. Redondear columnas categóricas al entero más cercano
for col in cat_cols:
    df_imputed[col] = df_imputed[col].round().astype(int)
```

---

## 🔍 Interpretación Especulativa (NO Oficial)

Aunque los creadores nunca revelaron el significado real, la comunidad ha especulado basándose en patrones estadísticos:

| Columna | Especulación común | Base de la especulación |
|---|---|---|
| `A2` | Edad (rango 13–80) | Rango numérico coherente con edades adultas |
| `A3` | Deuda actual | Escala 0–28, podría ser en miles |
| `A8` | Años de empleo | Escala similar a A3 |
| `A11` | Número de dependientes | Entero pequeño (0–67) |
| `A14` | Código postal o código de área | Entero 0–2000 |
| `A15` | Saldo en cuenta | Rango 0–100,000 |
| `A9` / `A10` | Variables de historial crediticio (binarias) | Alta correlación con TARGET |

> ⚠️ Estas son especulaciones. Usar con extrema cautela y no incluir en documentación formal.

---

## 📊 Distribución de Clases y Separabilidad

El dataset está **ligeramente desbalanceado** (55.5% rechazados vs 44.5% aprobados), pero es manejable sin técnicas especiales de balanceo. Sin embargo, si se usa ROC-AUC como métrica, el desbalance es menos problemático.

---

## 🧹 Checklist de Preparación

- [ ] Cargar con `na_values='?'` y asignar nombres A1–A15 + TARGET
- [ ] Identificar tipos de variables (categórica vs continua)
- [ ] Encodear todas las categóricas antes de imputar
- [ ] Aplicar KNN Imputer (preferible a media/moda por las razones explicadas)
- [ ] Redondear columnas categóricas imputadas al entero más cercano
- [ ] Convertir TARGET: `+`=1 (aprobado), `-`=0 (rechazado)
- [ ] Escalar variables numéricas para modelos lineales (StandardScaler)

---

## 📚 Referencias

- Quinlan, J.R. (1987). *Simplifying Decision Trees*. International Journal of Man-Machine Studies.
- UCI ML Repository: https://archive.ics.uci.edu/ml/datasets/credit+approval
