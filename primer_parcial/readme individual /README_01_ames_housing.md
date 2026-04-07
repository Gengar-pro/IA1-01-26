# 🏠 Dataset 1: Ames Housing Dataset

## Descripción General

El **Ames Housing Dataset** fue compilado por el Dr. **Dean De Cock** en 2011 como una alternativa moderna, rica y éticamente superior al famoso (y ahora obsoleto) *Boston Housing Dataset*. Contiene información detallada sobre ventas de propiedades residenciales en la ciudad de **Ames, Iowa (EE. UU.)**, entre los años **2006 y 2010**.

Es uno de los datasets más utilizados en competencias de regresión avanzada, especialmente en Kaggle, por la complejidad y riqueza de sus variables.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Dean De Cock (2011) / Kaggle |
| **Dominio** | Bienes Raíces |
| **Tipo de Problema** | Regresión supervisada |
| **Variable Objetivo** | `SalePrice` (precio de venta en USD) |
| **Total de Registros** | 2,930 |
| **Total de Columnas** | 82 (80 features + ID + SalePrice) |
| **Variables Nominales** | 23 |
| **Variables Ordinales** | 23 |
| **Variables Discretas** | 14 |
| **Variables Continuas** | 20 |
| **Periodo Cubierto** | 2006 – 2010 |
| **Archivos incluidos** | `AmesHousing.csv`, `train.csv`, `test.csv`, `target.csv` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `AmesHousing.csv` | Dataset completo con todas las observaciones (2,930 filas) |
| `train.csv` | Subconjunto de entrenamiento (con `SalePrice`) |
| `test.csv` | Subconjunto de prueba (sin `SalePrice`, para predicción) |
| `target.csv` | Valores reales de `SalePrice` para el set de prueba |

---

## Diccionario de Variables

### 🔑 Identificadores

| Columna | Tipo | Descripción |
|---|---|---|
| `Order` | Entero | Número de observación (ID interno, no predictivo) |
| `PID` | Entero | ID de parcela — código de identificación de propiedad |

---

### 🏗️ Variables de Clasificación del Inmueble

| Columna | Tipo | Descripción | Valores Posibles |
|---|---|---|---|
| `MS SubClass` | Nominal | Tipo de vivienda involucrada en la venta | 20=1 piso/1946+, 30=1 piso/1945−, 40=1 piso c/ático, 45=1.5 pisos sin terminar, 50=1.5 pisos terminados, 60=2 pisos/1946+, 70=2 pisos/1945−, 75=2.5 pisos, 80=split/multi-nivel, 85=split foyer, 90=dúplex, 120=1 piso PUD, 150=1.5 pisos PUD, 160=2 pisos PUD, 180=PUD multi-nivel, 190=conversión 2 familias |
| `MS Zoning` | Nominal | Clasificación de zonificación del predio | A=Agrícola, C=Comercial, FV=Villa flotante, I=Industrial, RH=Residencial alta densidad, RL=Residencial baja densidad, RP=Residencial baja densidad parque, RM=Residencial media densidad |
| `Lot Frontage` | Continua | Metros lineales de calle conectados al predio (pies) | Numérico, muchos NaN (casa sin acceso directo a calle) |
| `Lot Area` | Continua | Tamaño del lote en pies cuadrados | Numérico |
| `Street` | Nominal | Tipo de acceso vial al predio | Grvl=Gravilla, Pave=Pavimentado |
| `Alley` | Nominal | Tipo de acceso por callejón | Grvl=Gravilla, Pave=Pavimentado, **NaN=Sin callejón** |
| `Lot Shape` | Ordinal | Forma general del predio | Reg=Regular, IR1=Ligeramente irregular, IR2=Moderadamente irregular, IR3=Muy irregular |
| `Land Contour` | Nominal | Planitud del terreno | Lvl=Casi plano, Bnk=Desnivel brusco, HLS=Ladera, Low=Depresión |
| `Utilities` | Ordinal | Servicios públicos disponibles | AllPub=Todos, NoSewr=Sin alcantarillado, NoSeWa=Sin agua ni alcantarillado, ELO=Solo electricidad |
| `Lot Config` | Nominal | Configuración del lote | Inside=Interior, Corner=Esquina, CulDSac=Calle sin salida, FR2=Frente a 2 calles, FR3=Frente a 3 calles |
| `Land Slope` | Ordinal | Pendiente del terreno | Gtl=Suave, Mod=Moderada, Sev=Severa |
| `Neighborhood` | Nominal | Ubicación dentro de los límites de Ames | 28 vecindarios distintos (Blmngtn, Blueste, BrDale, BrkSide, ClearCr, CollgCr, Crawfor, Edwards, Gilbert, IDOTRR, MeadowV, Mitchel, Names, NoRidge, NPkVill, NridgHt, NWAmes, OldTown, SWISU, Sawyer, SawyerW, Somerst, StoneBr, Timber, Veenker) |
| `Condition 1` | Nominal | Proximidad a vía/característica principal | Artery=Calle arterial, Feedr=Calle alimentadora, Norm=Normal, RRNn=Cerca del tren, RRAn=Adyacente al tren, PosN=Positivo cercano, PosA=Positivo adyacente, RRNe=Cerca del tren E-O, RRAe=Adyacente al tren E-O |
| `Condition 2` | Nominal | Segunda condición (si aplica) | Mismos valores que Condition 1 |
| `Bldg Type` | Nominal | Tipo de vivienda | 1Fam=Unifamiliar, 2FmCon=Conversión 2 familias, Duplx=Dúplex, TwnhsE=Casa adosada esquina, TwnhsI=Casa adosada interior |
| `House Style` | Nominal | Estilo de la vivienda | 1Story, 1.5Fin, 1.5Unf, 2Story, 2.5Fin, 2.5Unf, SFoyer, SLvl |

---

### 📅 Variables de Antigüedad y Construcción

| Columna | Tipo | Descripción |
|---|---|---|
| `Year Built` | Discreta | Año original de construcción |
| `Year Remod/Add` | Discreta | Año de remodelación (igual a año construcción si no hubo) |
| `Yr Sold` | Discreta | Año de venta |
| `Mo Sold` | Discreta | Mes de venta (1–12) |

---

### 🏛️ Variables de Calidad y Condición

> Todas se evalúan en escala: **Ex**=Excelente, **Gd**=Buena, **TA**=Promedio/Típica, **Fa**=Regular, **Po**=Pobre

| Columna | Descripción |
|---|---|
| `Overall Qual` | Calidad general del material y acabado (escala 1–10) |
| `Overall Cond` | Condición general de la vivienda (escala 1–10) |
| `Exter Qual` | Calidad del material exterior |
| `Exter Cond` | Condición actual del material exterior |
| `Bsmt Qual` | Altura del sótano (Ex=+100", Gd=90-99", TA=80-89", Fa=70-79", Po=<70", NaN=Sin sótano) |
| `Bsmt Cond` | Condición general del sótano |
| `Heating QC` | Calidad y condición de la calefacción |
| `Kitchen Qual` | Calidad de la cocina |
| `Fireplace Qu` | Calidad de la chimenea (**NaN=Sin chimenea**) |
| `Garage Qual` | Calidad del garage (**NaN=Sin garage**) |
| `Garage Cond` | Condición del garage (**NaN=Sin garage**) |
| `Pool QC` | Calidad de la piscina (**NaN=Sin piscina**) — la mayoría son NaN |

---

### 🏚️ Variables del Sótano

| Columna | Tipo | Descripción |
|---|---|---|
| `Bsmt Exposure` | Ordinal | Nivel de exposición del sótano (Gd=Buena, Av=Promedio, Mn=Mínima, No=Sin exposición, **NaN=Sin sótano**) |
| `BsmtFin Type 1` | Ordinal | Calidad del área terminada del sótano (GLQ, ALQ, BLQ, Rec, LwQ, Unf, **NaN=Sin sótano**) |
| `BsmtFin SF 1` | Continua | Área terminada tipo 1 (pies²) |
| `BsmtFin Type 2` | Ordinal | Calidad del segundo área terminada del sótano |
| `BsmtFin SF 2` | Continua | Área terminada tipo 2 (pies²) |
| `Bsmt Unf SF` | Continua | Área sin terminar del sótano (pies²) |
| `Total Bsmt SF` | Continua | Área total del sótano (pies²) |
| `Bsmt Full Bath` | Discreta | Baños completos en sótano |
| `Bsmt Half Bath` | Discreta | Medios baños en sótano |

---

### 🏠 Variables de Área Habitable

| Columna | Tipo | Descripción |
|---|---|---|
| `1st Flr SF` | Continua | Área del primer piso (pies²) |
| `2nd Flr SF` | Continua | Área del segundo piso (pies²) — 0 si no hay |
| `Low Qual Fin SF` | Continua | Área terminada de baja calidad en todos los pisos (pies²) |
| `Gr Liv Area` | Continua | **Área habitable sobre el nivel del suelo (pies²)** — una de las features más predictivas |
| `Full Bath` | Discreta | Baños completos sobre el nivel del suelo |
| `Half Bath` | Discreta | Medios baños sobre el nivel del suelo |
| `Bedroom AbvGr` | Discreta | Dormitorios sobre el nivel del suelo (NO incluye sótano) |
| `Kitchen AbvGr` | Discreta | Cocinas sobre el nivel del suelo |
| `TotRms AbvGrd` | Discreta | Total de habitaciones sobre el nivel del suelo (sin baños) |

---

### 🚗 Variables del Garage

| Columna | Tipo | Descripción |
|---|---|---|
| `Garage Type` | Nominal | Ubicación del garage (2Types, Attchd, Basment, BuiltIn, CarPort, Detchd, **NaN=Sin garage**) |
| `Garage Yr Blt` | Discreta | Año de construcción del garage (**NaN=Sin garage**, imputar con 0) |
| `Garage Finish` | Ordinal | Acabado interior del garage (Fin=Terminado, RFn=Terminado básico, Unf=Sin terminar, **NaN=Sin garage**) |
| `Garage Cars` | Discreta | Capacidad del garage en autos |
| `Garage Area` | Continua | Área del garage (pies²) |

---

### 🌳 Variables Exteriores y Amenidades

| Columna | Tipo | Descripción |
|---|---|---|
| `Mas Vnr Type` | Nominal | Tipo de revestimiento de mampostería (BrkCmn, BrkFace, CBlock, None, Stone, **NaN**) |
| `Mas Vnr Area` | Continua | Área de revestimiento de mampostería (pies²) |
| `Wood Deck SF` | Continua | Área de terraza de madera (pies²) |
| `Open Porch SF` | Continua | Área de porche abierto (pies²) |
| `Enclosed Porch` | Continua | Área de porche cerrado (pies²) |
| `3Ssn Porch` | Continua | Área de porche de 3 estaciones (pies²) |
| `Screen Porch` | Continua | Área de porche con malla (pies²) |
| `Pool Area` | Continua | Área de piscina (pies²) — mayoría son 0 |
| `Misc Val` | Continua | Valor en USD de características misceláneas |
| `Fireplaces` | Discreta | Número de chimeneas |

---

### 🏷️ Variables de Venta

| Columna | Tipo | Descripción | Valores |
|---|---|---|---|
| `Sale Type` | Nominal | Tipo de venta | WD=Escritura normal, CWD=Escritura efectivo, VWD=Escritura contrato, COD=Escritura corte/alguacil, Con=Contrato 15%+/5 años, ConLw=Contrato bajo interés/pago, ConLI=Contrato bajo interés, ConLD=Contrato bajo pago, Oth=Otro |
| `Sale Condition` | Nominal | Condición de la venta | Normal, Abnorml=Venta anormal, AdjLand=Tierra adyacente, Alloca=Asignación, Family=Venta familiar, Partial=Casa no terminada |

---

### 🎯 Variable Objetivo

| Columna | Tipo | Descripción |
|---|---|---|
| `SalePrice` | Continua | **Precio de venta de la propiedad en dólares americanos (USD)** |

> **Nota:** Se recomienda aplicar transformación logarítmica `log1p(SalePrice)` antes de modelar, ya que la distribución tiene sesgo positivo (cola derecha larga).

---

## ⚠️ Valores Nulos — Guía de Tratamiento

> **CRÍTICO:** En este dataset, la mayoría de los NaN **NO significan dato faltante**. Significan que la característica **no existe** en la propiedad. Tratarlos como faltantes normales destruiría información crucial.

| Columna(s) | Estrategia Correcta | Razón |
|---|---|---|
| `Pool QC`, `Misc Feature`, `Alley`, `Fence`, `Fireplace Qu`, `Garage Type/Finish/Qual/Cond`, `Bsmt Qual/Cond/Exposure/FinType1/FinType2`, `Mas Vnr Type` | Rellenar con `"None"` | NaN = La propiedad no tiene esa característica |
| `Garage Yr Blt`, `Garage Area`, `Garage Cars`, `Bsmt SF*`, `Bsmt*Bath`, `Mas Vnr Area` | Rellenar con `0` | NaN = El área/cantidad es cero porque no existe |
| `Lot Frontage` | Mediana **por vecindario** (`Neighborhood`) | Propiedades del mismo vecindario suelen tener frontales similares |
| Resto numéricos | Mediana global | Imputación conservadora |

---

## 🔍 Features Más Predictivas (correlación con SalePrice)

1. `Overall Qual` — Calidad general (r ≈ 0.79)
2. `Gr Liv Area` — Área habitable sobre suelo (r ≈ 0.71)
3. `Garage Cars` — Capacidad del garage (r ≈ 0.64)
4. `Garage Area` — Área del garage (r ≈ 0.62)
5. `Total Bsmt SF` — Área total del sótano (r ≈ 0.61)
6. `1st Flr SF` — Primer piso (r ≈ 0.60)
7. `Year Built` — Año de construcción (r ≈ 0.52)
8. `Year Remod/Add` — Año de remodelación (r ≈ 0.51)
9. `Full Bath` — Baños completos (r ≈ 0.56)
10. `TotRms AbvGrd` — Total habitaciones (r ≈ 0.53)

---

## 🧹 Checklist de Preparación

- [ ] Eliminar columnas `Order` y `PID` (identificadores, no predictivos)
- [ ] Tratar NaN semánticos → categoría `"None"` o valor `0`
- [ ] Imputar `Lot Frontage` por mediana de vecindario
- [ ] Aplicar `log1p()` a `SalePrice` (corregir sesgo)
- [ ] Encoding de variables categóricas (Label Encoding para ordinales, One-Hot para nominales)
- [ ] Detectar y tratar outliers en `Gr Liv Area` (2 casas >4000 pies² con precios bajos anómalos)

---

## 📚 Referencias

- De Cock, D. (2011). *Ames, Iowa: Alternative to the Boston Housing Data as an End of Semester Regression Project*. Journal of Statistics Education.
- Kaggle: https://www.kaggle.com/datasets/marcopale/housing
