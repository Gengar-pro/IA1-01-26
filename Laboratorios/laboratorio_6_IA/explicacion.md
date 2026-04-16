# 🧠 Red Neuronal para Clasificación Multiclase de Meningitis
### Laboratorio 6 — SIS420: Inteligencia Artificial

---

## 📋 Descripción General

Este laboratorio implementa un modelo de clasificación multiclase utilizando **Regresión Logística con activación Softmax** (equivalente a un Perceptrón Multicapa de una sola capa) construido desde cero con **NumPy**, aplicado a un dataset de diagnóstico de meningitis. Se aplicaron técnicas de preprocesamiento, regularización implícita, optimización y se evaluó el modelo con métricas de clasificación multiclase estándar.

---

## 📂 Dataset

- **Nombre:** Meningitis Dataset (`mening missing 12.csv`)
- **Tarea:** Clasificación multiclase del tipo de diagnóstico (`Diagnosis`)
- **Preprocesamiento aplicado:**
  - Eliminación de duplicados y valores nulos
  - Codificación de variables categóricas con `LabelEncoder` y `get_dummies`
  - Balanceo de clases con **SMOTE** (Synthetic Minority Over-sampling Technique)
  - Normalización manual (media cero, desviación estándar unitaria)

---

## 🏗️ Arquitectura del Modelo

```
Entradas (Features normalizadas)
         ↓
   [Capa Lineal: W·x + b]
         ↓
   [Activación Softmax]
         ↓
 Probabilidades por clase (multiclase)
         ↓
   argmax → Clase predicha
```

### Clase `LogisticRegressionMulticlass`

| Componente | Descripción |
|---|---|
| **Inicialización de pesos** | Distribución normal con escala He (`sqrt(2/(n+1))`) |
| **Activación** | Softmax estabilizada numéricamente |
| **Función de pérdida** | Cross-Entropy (Entropía Cruzada Multiclase) |
| **Optimización** | Batch Gradient Descent |

---

## ⚙️ Técnicas Aplicadas

### 1. Preprocesamiento y Regularización Implícita

#### 🔁 SMOTE (Synthetic Minority Over-sampling Technique)
**¿Qué es?** Técnica de sobremuestreo que genera muestras sintéticas para las clases minoritarias interpolando entre ejemplos existentes.

**¿Por qué importa para este modelo?** Un dataset desbalanceado lleva al modelo a sesgar sus predicciones hacia la clase mayoritaria. SMOTE equilibra las clases antes del entrenamiento, lo que actúa como una forma de **regularización implícita** al evitar que el modelo "memorice" la clase dominante.

#### 📏 Normalización (Z-score)
**¿Qué es?** Transformación que lleva cada feature a media 0 y desviación estándar 1:

```
x_norm = (x - μ) / σ
```

**¿Por qué importa?** Garantiza que el gradiente descent converja de forma estable y rápida. Sin normalización, features con escalas muy distintas generan gradientes desproporcionados que dificultan el aprendizaje.

> **Implementación crítica:** Los parámetros `μ` y `σ` se calculan **solo sobre el conjunto de entrenamiento** y se aplican al conjunto de prueba, evitando fuga de información (*data leakage*).

---

### 2. Optimización

#### 📉 Batch Gradient Descent
**¿Qué es?** Algoritmo de optimización que calcula el gradiente de la función de pérdida usando **todo el conjunto de entrenamiento** en cada iteración (epoch).

**Fórmula de actualización de pesos:**
```
W = W - lr · (1/m) · Xᵀ · (ŷ - y)
```

**Hiperparámetros utilizados:**
| Parámetro | Valor | Justificación |
|---|---|---|
| `epochs` | 1000 | Suficiente para convergencia sin sobreajuste |
| `learning_rate` | 0.05 | Balance entre velocidad y estabilidad |

**Tradeoffs respecto a otras variantes:**
- *Stochastic GD*: más rápido por iteración, pero más ruidoso
- *Mini-batch GD*: compromiso entre ambos (usado en PyTorch/TensorFlow)
- *Batch GD* (nuestro caso): convergencia más suave, adecuado para datasets de tamaño moderado

---

### 3. Función de Pérdida — Cross-Entropy Multiclase

**¿Qué mide?** La diferencia entre la distribución de probabilidades predicha por el modelo y la distribución real (one-hot).

```
L = -(1/m) · Σ Σ y_ij · log(ŷ_ij + ε)
```

Donde `ε = 1e-15` previene `log(0)` (inestabilidad numérica).

**Interpretación:** Cuanto más el modelo asigna probabilidad alta a la clase correcta, menor es la pérdida. Minimizarla es equivalente a maximizar la verosimilitud del modelo.

---

## 📊 Métricas de Evaluación

### Métricas Globales (Macro Average)

| Métrica | Valor Alcanzado | Significado |
|---|---|---|
| **Accuracy** | Ver salida del modelo | % de predicciones correctas sobre el total |
| **Precision (Macro)** | Ver salida del modelo | Promedio de precisión por clase: TP / (TP + FP) |
| **Recall (Macro)** | Ver salida del modelo | Promedio de sensibilidad por clase: TP / (TP + FN) |
| **F1-Score (Macro)** | Ver salida del modelo | Media armónica de Precision y Recall |
| **AUC-ROC (OvR)** | Ver salida del modelo | Capacidad discriminativa promedio entre clases |

### Explicación detallada de cada métrica en el contexto del modelo

#### ✅ Accuracy
Mide cuántos diagnósticos el modelo clasificó correctamente. En problemas multiclase balanceados (tras SMOTE), es una métrica confiable como resumen global.

#### 🎯 Precision (Macro)
Para cada tipo de meningitis predicho, mide qué fracción de esas predicciones fue realmente correcta. El promedio macro da igual peso a cada clase, importante para no invisibilizar las clases minoritarias.

#### 🔍 Recall (Macro)
De todos los casos reales de cada tipo, mide cuántos detectó el modelo. En diagnósticos médicos, un Recall alto es crítico: no queremos que el modelo "pierda" casos positivos.

#### ⚖️ F1-Score (Macro)
Combina Precision y Recall en una sola métrica. Especialmente relevante cuando hay tensión entre ambas (mejorar una puede empeorar la otra).

#### 📈 Curvas ROC y AUC
Evaluación One-vs-Rest (OvR): para cada clase de meningitis, el modelo se evalúa como si fuera un clasificador binario. El AUC mide el área bajo la curva ROC; un valor de 1.0 indica separación perfecta, 0.5 indica rendimiento aleatorio.

---

## 🔢 División del Dataset

```
Dataset completo (tras SMOTE)
├── 80% → Entrenamiento (X_train, y_train)  [stratify=y]
└── 20% → Prueba     (X_test,  y_test)   [stratify=y]
```

La estratificación garantiza que la proporción de clases sea idéntica en ambos subconjuntos.

---

## 🗂️ Estructura del Proyecto

```
Lab6/
├── Metricas_de_clasificacion_Multiclase_1_.ipynb   # Notebook principal
├── mening missing 12.csv                            # Dataset (en Google Drive)
└── README.md                                        # Este archivo
```

---

## 🚀 Cómo Ejecutar

1. Abrir el notebook en **Google Colab**
2. Montar Google Drive y colocar el dataset en:
   ```
   MyDrive/Colab Notebooks/sis420/Laboratorios/Lab 6/mening missing 12.csv
   ```
3. Ejecutar las celdas en orden (Shift+Enter o "Run All")

**Dependencias:**
```python
pandas, numpy, matplotlib, seaborn, scikit-learn, imbalanced-learn
```

---

**Materia:** SIS420 — Inteligencia Artificial 
**Gestión:** 2026

---

---

## 📊 Resultados del Modelo

### Métricas Globales (Macro Average)

| Métrica | Valor |
|---|---|
| **Accuracy** | 0.7705 |
| **Precision** | 0.7711 |
| **Recall** | 0.7706 |
| **F1-Score** | 0.7708 |

> Las cuatro métricas se encuentran en ~0.77, lo que indica que el modelo **no está sesgado hacia ninguna clase**, resultado directamente atribuible al balanceo con SMOTE.

---

### 🔲 Matriz de Confusión

| | Predijo Bacterial | Predijo Unknown | Predijo Viral |
|---|---|---|---|
| **Real Bacterial** | ✅ 81 | ❌ 18 | ❌ 2 |
| **Real Unknown** | ❌ 16 | ✅ 71 | ❌ 15 |
| **Real Viral** | ❌ 5 | ❌ 14 | ✅ 83 |

**Análisis por clase:**
- **Bacterial** (80.2% recall): Bien detectada. El error más frecuente es confundirla con *Unknown*, esperable por la ambigüedad de síntomas en etapas tempranas.
- **Viral** (81.4% recall): La clase mejor detectada. Solo 5 casos se confundieron con Bacterial, que sería el error clínicamente más crítico.
- **Unknown** (69.6% recall): La más difícil para el modelo, perdiendo 31 casos hacia las otras dos clases. Es esperable dado que esta categoría agrupa diagnósticos con síntomas incompletos o ambiguos.

---

### 📈 Curvas ROC (One-vs-Rest)

El gráfico ROC evalúa la capacidad del modelo para separar cada clase del resto. Las tres curvas se encuentran **muy por encima de la diagonal** (clasificador aleatorio), confirmando poder discriminativo real.

- 🟠 **Unknown**: Mayor AUC visual. El modelo asigna probabilidades altas a esta clase con pocos falsos positivos.
- 🟢 **Viral**: Curva alta y sostenida, consistente con su buen recall.
- 🔵 **Bacterial**: Ligeramente menos pronunciada al inicio, pero con rendimiento final equivalente.

---

### 🏁 Conclusión

Para un modelo de regresión logística multiclase (sin capas ocultas), un **77% de accuracy** en un problema médico de 3 clases es un resultado sólido y defendible. La clase *Unknown* representa el principal cuello de botella del modelo, lo que es esperable dada su naturaleza inherentemente ambigua. Las curvas ROC confirman que el modelo tiene capacidad discriminativa real en las tres clases.