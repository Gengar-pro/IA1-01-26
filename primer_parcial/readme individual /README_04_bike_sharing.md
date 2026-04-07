# 🚴 Dataset 4: Bike Sharing Demand

## Descripción General

El **Bike Sharing Dataset** contiene registros históricos del sistema de bicicletas compartidas **Capital Bikeshare** de Washington D.C. (EE. UU.), correspondientes a los años **2011 y 2012**. Es ampliamente utilizado para modelado de **series temporales** y **predicción de demanda**.

Este dataset fue la base de una competencia en **Kaggle** donde los participantes debían predecir el número de bicicletas alquiladas por hora, considerando variables climáticas, calendáricas y de comportamiento de usuarios.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Capital Bikeshare / UCI ML Repository / Kaggle |
| **Dominio** | Transporte urbano / Sistemas de movilidad compartida |
| **Tipo de Problema** | Regresión de series temporales / Predicción de demanda |
| **Variable Objetivo** | `count` — total de bicicletas alquiladas por hora |
| **Registros (train)** | 10,886 |
| **Registros (test)** | 6,493 |
| **Periodo Cubierto** | Enero 2011 – Diciembre 2012 |
| **Granularidad Temporal** | Horaria |
| **Archivos incluidos** | `train.csv`, `test.csv`, `sampleSubmission.csv` |

---

## Archivos del Dataset

| Archivo | Registros | Descripción |
|---|---|---|
| `train.csv` | 10,886 | Primeros 19 días de cada mes — **incluye la variable objetivo `count`** |
| `test.csv` | 6,493 | Del día 20 en adelante de cada mes — **sin `count`** (para predicción) |
| `sampleSubmission.csv` | 6,493 | Formato de ejemplo para enviar predicciones a Kaggle |

> **Nota sobre la división:** La separación train/test se hace por día del mes (no aleatoriamente), para simular predicción hacia el futuro — es una división temporal realista.

---

## Diccionario de Variables

### 📅 Variables Temporales

| Columna | Tipo | Descripción | Valores |
|---|---|---|---|
| `datetime` | Timestamp | Fecha y hora del registro (formato: YYYY-MM-DD HH:MM:SS) | Frecuencia horaria |

> **Feature Engineering recomendado a partir de `datetime`:**
> - `hour` — hora del día (0–23): el predictor más importante
> - `day` — día del mes (1–31)
> - `month` — mes del año (1–12)
> - `year` — año (2011 o 2012)
> - `dayofweek` — día de la semana (0=Lunes…6=Domingo)
> - `season_name` — nombre de la estación (legibilidad)

---

### 📆 Variables Calendáricas

| Columna | Tipo | Descripción | Valores |
|---|---|---|---|
| `season` | Ordinal | Estación del año | 1=Primavera, 2=Verano, 3=Otoño, 4=Invierno |
| `holiday` | Binario | ¿Es día festivo oficial? | 0=No, 1=Sí |
| `workingday` | Binario | ¿Es día laborable? (ni fin de semana ni festivo) | 0=No, 1=Sí |

> **Relación entre variables:** Si `holiday=1` → `workingday=0`. Los fines de semana también tienen `workingday=0`.

---

### 🌤️ Variables Climáticas

| Columna | Tipo | Descripción | Rango / Valores |
|---|---|---|---|
| `weather` | Ordinal | Categoría climática | Ver tabla abajo |
| `temp` | Continua | Temperatura en Celsius **normalizada** | 0.0–1.0 (0=−8°C, 1=39°C) |
| `atemp` | Continua | Temperatura de sensación térmica **normalizada** | 0.0–1.0 (0=−16°C, 1=50°C) |
| `humidity` | Continua | Humedad relativa **normalizada** | 0.0–1.0 (0=0%, 1=100%) |
| `windspeed` | Continua | Velocidad del viento **normalizada** | 0.0–1.0 |

#### Detalle de la Variable `weather`

| Valor | Categoría | Descripción Completa |
|---|---|---|
| 1 | ☀️ Despejado/Pocas nubes | Clear, Few clouds, Partly cloudy |
| 2 | 🌫️ Niebla/Nublado | Mist + Cloudy, Mist + Broken clouds, Mist + Few clouds |
| 3 | 🌧️ Lluvia/Nieve ligera | Light Snow, Light Rain + Thunderstorm, Light Rain + Scattered clouds |
| 4 | ⛈️ Lluvia intensa | Heavy Rain + Ice Pallets + Thunderstorm + Mist, Snow + Fog |

> **Nota:** El valor 4 es extremadamente raro en el dataset (< 0.1% de registros).

---

### 🎯 Variables Objetivo — ⚠️ DATA LEAKAGE CRÍTICO

| Columna | Tipo | Descripción | ¿Usar en modelo? |
|---|---|---|---|
| `casual` | Continua | Número de alquileres por **usuarios no registrados** | ❌ **ELIMINAR** — causa leakage |
| `registered` | Continua | Número de alquileres por **usuarios con suscripción** | ❌ **ELIMINAR** — causa leakage |
| `count` | Continua | **Total de bicicletas alquiladas** = `casual + registered` | ✅ Variable objetivo |

> ### ⚠️ ADVERTENCIA — Data Leakage
> 
> **`count` = `casual` + `registered`** — Esta es una identidad matemática perfecta (correlación = 1.0).
> 
> Si se incluyen `casual` o `registered` como features, el modelo "hace trampa": aprende la identidad aritmética en lugar del patrón real de demanda. En producción, estas variables **no estarían disponibles** al momento de predecir.
> 
> **Acción:** Eliminar `casual` y `registered` antes de entrenar cualquier modelo.

---

## 🔍 Patrones de Demanda Conocidos

Estos patrones se observan claramente en el EDA y son fundamentales para el modelo:

### Por hora del día
- **Pico mañana:** 7–9 AM (commuters hacia el trabajo)
- **Pico tarde:** 17–19 PM (commuters de regreso)
- **Valle nocturno:** 0–5 AM (mínimo absoluto)
- **Fin de semana:** Distribución más uniforme, pico al mediodía

### Por estación
- **Verano y Otoño:** Mayor demanda (clima agradable)
- **Invierno:** Menor demanda (frío, posible nieve)
- **Primavera:** Demanda creciente

### Por condición climática
- Temperatura más alta → Mayor demanda (correlación positiva)
- Humedad más alta → Menor demanda (correlación negativa)
- Lluvia/Nieve → Caída significativa en la demanda

---

## 🔄 Transformaciones Recomendadas

| Variable | Transformación | Razón |
|---|---|---|
| `count` | `log1p(count)` | La distribución tiene sesgo positivo — la transformación logarítmica mejora el ajuste de modelos lineales |
| `temp`, `atemp` | Desnormalizar si se desea interpretabilidad | `temp_real = temp * 47 - 8` |
| `humidity` | Desnormalizar si se desea | `hum_real = humidity * 100` |
| `datetime` | Descomponer en `hour`, `month`, `year`, `dayofweek` | Los modelos de árbol no interpretan fechas directamente |

---

## 📊 Correlaciones con `count` (aproximadas)

| Feature | Correlación con `count` |
|---|---|
| `registered` | 0.97 (**leakage** — excluir) |
| `casual` | 0.69 (**leakage** — excluir) |
| `temp` | 0.39 |
| `atemp` | 0.39 |
| `hour` (derivada) | ~0.40 |
| `humidity` | −0.32 |
| `windspeed` | −0.10 |
| `holiday` | −0.03 |

---

## 📐 Métricas de Evaluación

Kaggle usa el **RMSLE** (Root Mean Squared Logarithmic Error):

```
RMSLE = sqrt( (1/n) * Σ (log(pred+1) - log(real+1))² )
```

Esta métrica penaliza menos los errores en valores grandes y favorece predicciones proporcionales — más apropiada que RMSE para demanda con alta varianza.

---

## 🧹 Checklist de Preparación

- [ ] Eliminar columnas `casual` y `registered` (data leakage)
- [ ] Convertir `datetime` a tipo Timestamp con `pd.to_datetime()`
- [ ] Extraer `hour`, `month`, `year`, `dayofweek` de `datetime`
- [ ] Aplicar `log1p()` al target `count`
- [ ] Verificar que no hay valores nulos (dataset ya limpio)
- [ ] Mantener división temporal: no mezclar train/test aleatoriamente

---

## 📚 Referencias

- Fanaee-T, H. & Gama, J. (2014). *Event labeling combining ensemble detectors and background knowledge*. Progress in Artificial Intelligence, 2(2-3), 113–127.
- UCI Repository: https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset
- Kaggle Competition: https://www.kaggle.com/c/bike-sharing-demand
