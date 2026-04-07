# 🩺 Dataset 3: NHANES — National Health and Nutrition Examination Survey

## Descripción General

El **NHANES** es un programa continuo de estudios diseñado para evaluar el estado de salud y nutrición de adultos y niños en los **Estados Unidos**. Es gestionado por el **Centro Nacional de Estadísticas de Salud (NCHS)** de los **Centros para el Control y la Prevención de Enfermedades (CDC)**.

Lo que hace único a NHANES es que combina **entrevistas domiciliarias** con **exámenes físicos y pruebas de laboratorio** realizadas en Centros de Examen Móviles (MEC — Mobile Examination Centers) que viajan por todo el país.

> **En este proyecto:** El dataset se provee en formato `.xlsx` y contiene una selección de variables de ciclos recientes de NHANES, enfocadas en salud metabólica, nutrición y factores de riesgo cardiovascular.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | CDC — National Center for Health Statistics |
| **Dominio** | Salud pública, Epidemiología, Nutrición |
| **Tipo de Problema** | Clasificación supervisada / Análisis epidemiológico |
| **Variable Objetivo Sugerida** | Obesidad/Sobrepeso (IMC ≥ 25) derivado de `BMXBMI` |
| **Formato del Archivo** | `dataset3.xlsx` |
| **Diseño Muestral** | Probabilidad estratificada, multietápica, agrupada |
| **Cobertura** | Nacional — EE. UU. |

---

## ⚠️ Consideración Estadística Fundamental: Pesos de Encuesta

NHANES **NO es una muestra aleatoria simple**. Utiliza un diseño complejo de muestreo para sobre-representar ciertos grupos (minorías étnicas, personas de bajos ingresos, adultos mayores, etc.).

Para que los resultados sean **representativos a nivel nacional**, en análisis estadísticos formales se deben usar los **pesos de encuesta**:

| Variable | Descripción |
|---|---|
| `WTINT2YR` | Peso para análisis de entrevistas de 2 años |
| `WTMEC2YR` | Peso para análisis de examen médico de 2 años |
| `SDMVPSU` | Unidad primaria de muestreo (para cálculo de varianza) |
| `SDMVSTRA` | Estrato de diseño muestral (para cálculo de varianza) |

> **Para Machine Learning:** En modelos predictivos puros (no inferencia poblacional), los pesos pueden ignorarse, pero se debe mencionar esta limitación en las conclusiones.

---

## Diccionario de Variables Principales

### 👤 Variables Demográficas (Módulo DEMO)

| Variable | Descripción | Valores / Rango |
|---|---|---|
| `SEQN` | Número de secuencia del respondente — **identificador único** | Entero |
| `RIAGENDR` | Género | 1=Masculino, 2=Femenino |
| `RIDAGEYR` | Edad en años al momento del examen | 0–80+ (80 = 80 o más) |
| `RIDRETH3` | Etnia/Raza | 1=Hispano mexicano, 2=Otro hispano, 3=Blanco no hispano, 4=Negro no hispano, 6=Asiático no hispano, 7=Otra raza/Multirracial |
| `DMDEDUC2` | Nivel educativo (adultos ≥20 años) | 1=<9° grado, 2=9–11° grado, 3=Graduado de preparatoria, 4=Algo de universidad, 5=Licenciatura o más |
| `INDFMPIR` | Razón ingreso/pobreza familiar | 0–5 (ratio: 1.0 = línea de pobreza, >3.5 = por encima) |
| `DMDMARTL` | Estado civil | 1=Casado, 2=Viudo, 3=Divorciado, 4=Separado, 5=Nunca casado, 6=Pareja de hecho |

---

### ⚖️ Variables Antropométricas (Módulo BMX)

| Variable | Descripción | Unidades | Rango Típico |
|---|---|---|---|
| `BMXWT` | Peso corporal | kg | 3–200 kg |
| `BMXHT` | Estatura | cm | 80–210 cm |
| `BMXBMI` | **Índice de Masa Corporal** | kg/m² | 10–70+ |
| `BMXWAIST` | Circunferencia de cintura | cm | 50–200 cm |
| `BMXHIP` | Circunferencia de cadera | cm | 60–200 cm |
| `BMXTHICR` | Circunferencia de muslo | cm | — |
| `BMXARMC` | Circunferencia de brazo (mitad) | cm | — |
| `BMXARML` | Longitud de brazo | cm | — |
| `BMXLEG` | Longitud de pierna | cm | — |
| `BMXRECUM` | Longitud recumbente (niños) | cm | — |
| `BMXSAD1`–`BMXSAD4` | Diámetros sagitales abdominales | cm | — |

> **Clasificación IMC (OMS):**
> - < 18.5 → Bajo peso
> - 18.5–24.9 → Normal
> - 25.0–29.9 → Sobrepeso
> - ≥ 30.0 → Obesidad (I: 30-34.9, II: 35-39.9, III: ≥40)

---

### 🩸 Variables de Presión Arterial (Módulo BPX)

| Variable | Descripción | Unidades |
|---|---|---|
| `BPXSY1`–`BPXSY4` | Presión sistólica medición 1–4 | mmHg |
| `BPXDI1`–`BPXDI4` | Presión diastólica medición 1–4 | mmHg |
| `BPXPLS` | Pulso en 60 segundos | latidos/min |
| `BPXPULS` | Calidad del pulso | Regular/Irregular |

> **Uso en modelo:** Promediar las 3 últimas mediciones (excluir primera) para obtener valor estable.

---

### 🧪 Variables de Laboratorio — Química Sanguínea (Módulo BIOPRO)

| Variable | Descripción | Unidades | Rango Normal |
|---|---|---|---|
| `LBXSGL` | Glucosa en sangre | mg/dL | 70–100 (ayunas) |
| `LBXSCH` | Colesterol total | mg/dL | <200 deseable |
| `LBXSHDL` | Colesterol HDL ("bueno") | mg/dL | >40 hombres, >50 mujeres |
| `LBXSTR` | Triglicéridos | mg/dL | <150 normal |
| `LBXSGB` | Globulina | g/dL | — |
| `LBXSCA` | Calcio | mg/dL | 8.5–10.2 |
| `LBXSPH` | Fósforo | mg/dL | 2.5–4.5 |
| `LBXSUA` | Ácido úrico | mg/dL | 3.5–7.2 |
| `LBXSCR` | Creatinina sérica | mg/dL | 0.6–1.2 |
| `LBXSBU` | Nitrógeno ureico (BUN) | mg/dL | 7–20 |
| `LBXSTP` | Proteína total | g/dL | 6.3–8.2 |
| `LBXSALB` | Albúmina | g/dL | 3.5–5.0 |
| `LBXSATSI` | AST (enzima hepática) | U/L | 10–40 |
| `LBXSASSI` | ALT (enzima hepática) | U/L | 7–56 |

---

### 🩸 Variables de Laboratorio — Hemograma (Módulo CBC)

| Variable | Descripción | Unidades |
|---|---|---|
| `LBXWBCSI` | Glóbulos blancos (WBC) | 1000 células/µL |
| `LBXRBCSI` | Glóbulos rojos (RBC) | millones/µL |
| `LBXHGB` | Hemoglobina | g/dL |
| `LBXHCT` | Hematocrito | % |
| `LBXMCVSI` | Volumen corpuscular medio (MCV) | fL |
| `LBXPLTSI` | Plaquetas | 1000 células/µL |

---

### 🍎 Variables Dietéticas — Recordatorio 24 Horas (Módulo DR)

| Variable | Descripción | Unidades |
|---|---|---|
| `DR1TKCAL` | Energía total consumida (día 1) | kcal |
| `DR1TPROT` | Proteínas (día 1) | g |
| `DR1TCARB` | Carbohidratos (día 1) | g |
| `DR1TTFAT` | Grasa total (día 1) | g |
| `DR1TSFAT` | Grasa saturada (día 1) | g |
| `DR1TCFAT` | Grasa poliinsaturada (día 1) | g |
| `DR1TSODI` | Sodio (día 1) | mg |
| `DR1TSUGR` | Azúcares totales (día 1) | g |
| `DR1TFIBE` | Fibra dietética (día 1) | g |
| `DR2TKCAL` | Energía total consumida (día 2) | kcal |

---

### ❓ Variables de Cuestionario — Actividad Física (Módulo PAQ)

| Variable | Descripción | Valores |
|---|---|---|
| `PAQ605` | ¿Actividad física intensa en trabajo? | 1=Sí, 2=No |
| `PAQ620` | ¿Actividad física moderada en trabajo? | 1=Sí, 2=No |
| `PAQ635` | ¿Camina o va en bicicleta? | 1=Sí, 2=No |
| `PAD660` | Minutos/día de actividad moderada recreativa | Minutos |
| `PAD675` | Minutos/día de actividad vigorosa recreativa | Minutos |
| `PAD680` | Minutos/día sedentario | Minutos |

---

### 🚬 Variables de Cuestionario — Tabaquismo (Módulo SMQ)

| Variable | Descripción | Valores |
|---|---|---|
| `SMQ020` | ¿Ha fumado al menos 100 cigarrillos en su vida? | 1=Sí, 2=No |
| `SMQ040` | ¿Fuma actualmente? | 1=Todos los días, 2=Algunos días, 3=No fuma |
| `SMD030` | Edad en que empezó a fumar | Años |

---

### 🍺 Variables de Cuestionario — Alcohol (Módulo ALQ)

| Variable | Descripción | Valores |
|---|---|---|
| `ALQ101` | ¿Al menos 12 bebidas alcohólicas en el último año? | 1=Sí, 2=No |
| `ALQ110` | ¿Al menos 12 bebidas en cualquier año de su vida? | 1=Sí, 2=No |
| `ALQ120Q` | Frecuencia de consumo en los últimos 12 meses | Número |
| `ALQ130` | Bebidas promedio por día en días que bebe | Número |

---

## 🎯 Variables Objetivo Posibles

| Objetivo | Derivación | Tipo |
|---|---|---|
| **Sobrepeso/Obesidad** | `BMXBMI >= 25` | Clasificación binaria |
| **Obesidad** | `BMXBMI >= 30` | Clasificación binaria |
| **Hipertensión** | `BPXSY1 >= 130 OR BPXDI1 >= 80` | Clasificación binaria |
| **Diabetes** | `LBXSGL >= 126` (ayunas) | Clasificación binaria |
| **Síndrome Metabólico** | Combinación de cintura + glucosa + PA + triglicéridos | Clasificación binaria |
| **Valor continuo IMC** | `BMXBMI` directo | Regresión |

---

## ⚠️ Valores Nulos — Guía de Tratamiento

| Causa | Estrategia |
|---|---|
| El examen no fue realizado (normal) | Imputación por mediana/KNN |
| Negativa del participante | Considerar como MAR — imputación múltiple |
| Error técnico del equipo | Eliminar o imputar |
| Variables inaplicables por edad | Separar en grupos etarios |

---

## 🧹 Checklist de Preparación

- [ ] Identificar el ciclo exacto de NHANES (ej: 2017–2018, 2015–2016)
- [ ] Calcular IMC si no está precalculado: `BMXWT / (BMXHT/100)²`
- [ ] Crear variable objetivo: `OBESO = (BMXBMI >= 30).astype(int)`
- [ ] Tratar valores como `9`, `99`, `999` como valores perdidos (códigos NHANES)
- [ ] Imputar nulos con mediana para variables continuas
- [ ] Eliminar columna `SEQN` del modelado (es ID)
- [ ] Escalar variables con rangos muy diferentes

---

## 📚 Referencias

- CDC NHANES Portal: https://www.cdc.gov/nchs/nhanes/index.htm
- Documentación técnica: https://wwwn.cdc.gov/nchs/nhanes/
- Tutorial de análisis con pesos: https://www.cdc.gov/nchs/tutorials/nhanes/
