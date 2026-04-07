# 👤 Dataset 5: Adult Census Income (UCI)

## Descripción General

El **Adult Dataset** (también conocido como *Census Income Dataset*) fue extraído de la base de datos del **Censo de Estados Unidos de 1994** por **Barry Becker**. Es uno de los datasets más usados en la historia del Machine Learning para clasificación binaria y, especialmente, para el estudio de **sesgos algorítmicos (Fairness in ML)**.

El objetivo es predecir si una persona gana **más de $50,000 dólares al año** (aproximadamente $100,000 en dólares actuales ajustados por inflación) en función de sus características socioeconómicas y demográficas.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | Censo EE. UU. 1994 — Barry Becker / UCI ML Repository |
| **Dominio** | Ciencias Sociales / Economía |
| **Tipo de Problema** | Clasificación binaria supervisada |
| **Variable Objetivo** | `income` — `>50K` o `<=50K` |
| **Total de Registros** | 48,842 (32,561 train + 16,281 test) |
| **Total de Variables** | 14 atributos + 1 variable objetivo |
| **Variables Continuas** | 6 |
| **Variables Categóricas** | 8 |
| **Archivos incluidos** | `adult.data` (train), `adult.test` (test), `adult.names`, `old.adult.names`, `Index` |

---

## Archivos del Dataset

| Archivo | Descripción |
|---|---|
| `adult.data` | Datos de entrenamiento (32,561 registros, **sin encabezado**) |
| `adult.test` | Datos de prueba (16,281 registros, **sin encabezado**, primera línea a ignorar, labels con punto final) |
| `adult.names` | Documentación oficial de los atributos y metadatos |
| `old.adult.names` | Versión anterior de la documentación |
| `Index` | Índice de archivos del repositorio UCI |

> **Nota de carga:** Los archivos no tienen encabezado. Los nombres de columnas deben asignarse manualmente. Los valores nulos están codificados con el símbolo `?` (no como `NaN`). Al cargar con pandas usar: `na_values=' ?'` y `skipinitialspace=True`.

---

## Diccionario de Variables

### 🔢 Variables Continuas (Numéricas)

| Columna | Descripción | Rango Típico | Notas |
|---|---|---|---|
| `age` | Edad del individuo en años | 17–90 | Variable muy predictiva — ingresos crecen con edad hasta ~50 años |
| `fnlwgt` | Peso final del censo (*Final Weight*) | 12,285–1,484,705 | Indica cuántas personas del censo representa este registro. **No es predictivo del ingreso — eliminar del modelo** |
| `education-num` | Años de educación codificados numéricamente | 1–16 | Equivale a `education` en escala ordinal. No usar ambas en el mismo modelo (redundancia) |
| `capital-gain` | Ganancias de capital en el año fiscal | 0–99,999 | Muy sesgado: >90% de registros tienen 0. Altamente predictivo cuando > 0 |
| `capital-loss` | Pérdidas de capital en el año fiscal | 0–4,356 | Similarmente sesgado. Tiene correlación sorprendentemente positiva con ingresos altos |
| `hours-per-week` | Horas trabajadas por semana | 1–99 | 40 horas = tiempo completo. Distribución bimodal |

---

### 📝 Variables Categóricas (Texto)

#### `workclass` — Clase laboral del empleado

| Valor | Descripción |
|---|---|
| `Private` | Empleado en empresa privada (la categoría más frecuente: ~70%) |
| `Self-emp-not-inc` | Autoempleado, empresa no incorporada |
| `Self-emp-inc` | Autoempleado, empresa incorporada |
| `Federal-gov` | Empleado del gobierno federal |
| `Local-gov` | Empleado del gobierno local |
| `State-gov` | Empleado del gobierno estatal |
| `Without-pay` | Trabaja sin remuneración |
| `Never-worked` | Nunca ha trabajado |
| `?` → `NaN` | **Valor faltante** — imputar con moda (`Private`) |

---

#### `education` — Nivel educativo alcanzado

| Valor | Equivalencia | `education-num` |
|---|---|---|
| `Preschool` | Preescolar | 1 |
| `1st-4th` | Primaria baja | 2 |
| `5th-6th` | Primaria alta | 3 |
| `7th-8th` | Secundaria baja | 4 |
| `9th` | Secundaria | 5 |
| `10th` | Preparatoria 1° | 6 |
| `11th` | Preparatoria 2° | 7 |
| `12th` | Preparatoria 3° | 8 |
| `HS-grad` | Graduado de preparatoria | 9 |
| `Some-college` | Algo de universidad | 10 |
| `Assoc-voc` | Técnico vocacional | 11 |
| `Assoc-acdm` | Técnico académico | 12 |
| `Bachelors` | Licenciatura | 13 |
| `Masters` | Maestría | 14 |
| `Prof-school` | Escuela profesional (Médico, Abogado) | 15 |
| `Doctorate` | Doctorado | 16 |

> **Nota:** `education` y `education-num` contienen **la misma información** en diferentes formatos. Usar solo `education-num` en el modelo (ya es ordinal numérica).

---

#### `marital-status` — Estado civil

| Valor | Descripción |
|---|---|
| `Married-civ-spouse` | Casado con cónyuge civil (el más frecuente y más predictivo de >50K) |
| `Divorced` | Divorciado |
| `Never-married` | Nunca casado |
| `Separated` | Separado |
| `Widowed` | Viudo |
| `Married-spouse-absent` | Casado con cónyuge ausente |
| `Married-AF-spouse` | Casado con cónyuge de las Fuerzas Armadas |

---

#### `occupation` — Ocupación laboral

| Valor | Descripción |
|---|---|
| `Tech-support` | Soporte técnico |
| `Craft-repair` | Reparaciones y oficios |
| `Other-service` | Otros servicios |
| `Sales` | Ventas |
| `Exec-managerial` | Ejecutivo/Gerencial (alto ingreso) |
| `Prof-specialty` | Especialidad profesional (Médico, Ingeniero) |
| `Handlers-cleaners` | Manipuladores y limpieza |
| `Machine-op-inspct` | Operador de maquinaria |
| `Adm-clerical` | Administrativo/Secretarial |
| `Farming-fishing` | Agricultura y pesca |
| `Transport-moving` | Transporte y mudanzas |
| `Priv-house-serv` | Servicio doméstico privado |
| `Protective-serv` | Servicios de protección (policía, bomberos) |
| `Armed-Forces` | Fuerzas armadas |
| `?` → `NaN` | **Valor faltante** |

---

#### `relationship` — Relación familiar del individuo

| Valor | Descripción |
|---|---|
| `Wife` | Esposa (implica género femenino y casada) |
| `Own-child` | Hijo/a del jefe de hogar |
| `Husband` | Esposo (implica género masculino y casado) |
| `Not-in-family` | No en familia nuclear |
| `Other-relative` | Otro familiar |
| `Unmarried` | Soltero no familiar |

> **Correlación con `marital-status`:** Estas variables son parcialmente redundantes. `Husband` y `Wife` implican estar casados.

---

#### `race` — Raza/Etnia declarada

| Valor | % Aproximado en Dataset |
|---|---|
| `White` | ~85% |
| `Black` | ~10% |
| `Asian-Pac-Islander` | ~3% |
| `Amer-Indian-Eskimo` | ~1% |
| `Other` | ~1% |

> ⚠️ **Sesgo Algorítmico:** Hay diferencias significativas en ingresos por `race` que reflejan desigualdades históricas en EE. UU. Un modelo entrenado sin corrección perpetúa estas disparidades.

---

#### `sex` — Género declarado

| Valor | % en Dataset | % que gana >50K |
|---|---|---|
| `Male` | ~67% | ~31% |
| `Female` | ~33% | ~11% |

> ⚠️ **Sesgo Algorítmico:** Las mujeres tienen ~3x menos probabilidad de estar en la categoría >50K, lo que en 1994 reflejaba brecha salarial de género real. El modelo puede perpetuar esta disparidad.

---

#### `native-country` — País de origen

42 países distintos. Los más frecuentes:

| Valor | Aprox. % |
|---|---|
| `United-States` | ~90% |
| `Mexico` | ~2% |
| `Philippines`, `Germany`, `Canada` | <1% c/u |
| `?` → `NaN` | **Valor faltante** (~1.8%) |

---

### 🎯 Variable Objetivo

| Columna | Tipo | Valores | Distribución |
|---|---|---|---|
| `income` | Binaria | `>50K` / `<=50K` | ~24% gana >50K (desbalanceado) |

> **Para el archivo `adult.test`:** Los labels tienen un punto al final (`>50K.` y `<=50K.`) que debe eliminarse antes de comparar:
> ```python
> test['income'] = test['income'].str.replace('.', '', regex=False)
> ```

---

## ⚠️ Valores Nulos — Detalle

| Columna | Nulos | % | Estrategia |
|---|---|---|---|
| `workclass` | ~1,836 | ~3.8% | Imputar con moda (`Private`) |
| `occupation` | ~1,843 | ~3.8% | Imputar con moda |
| `native-country` | ~857 | ~1.8% | Imputar con moda (`United-States`) |

> Los tres campos nulos suelen correlacionar: cuando `workclass=?`, generalmente `occupation=?` también. Esto sugiere que son personas que no trabajan — considerar imputar ambos simultáneamente.

---

## 🔍 Features Más Predictivas

1. `marital-status` — Ser casado es el predictor individual más fuerte de ingreso alto
2. `education-num` — A más educación, mayor probabilidad de >50K
3. `age` — Ingresos crecen con la experiencia
4. `capital-gain` — Cuando es >0, es señal muy fuerte de riqueza
5. `occupation` — Ejecutivo/Profesional vs servicios
6. `hours-per-week` — Más horas = mayor compromiso laboral
7. `sex` — Brecha de género significativa (cuidado con bias)

---

## 🧹 Checklist de Preparación

- [ ] Asignar nombres de columnas manualmente al cargar (sin encabezado)
- [ ] Usar `na_values=' ?'` y `skipinitialspace=True` en `pd.read_csv()`
- [ ] Limpiar punto final en labels de `adult.test`
- [ ] Imputar `workclass`, `occupation`, `native-country` con moda
- [ ] Eliminar `fnlwgt` (peso estadístico, no predictivo del ingreso)
- [ ] No usar `education` y `education-num` juntos (redundancia)
- [ ] Binarizar target: `income_bin = (income == '>50K').astype(int)`
- [ ] Documentar hallazgos de fairness: disparidades por `sex` y `race`

---

## 📚 Referencias

- Becker, B. & Kohavi, R. (1996). *Adult*. UCI Machine Learning Repository.
- UCI: https://archive.ics.uci.edu/ml/datasets/adult
- Fairness ML Reference: Hardt, M. et al. (2016). *Equality of Opportunity in Supervised Learning*. NeurIPS.
