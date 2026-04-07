# 🏥 Dataset 2: MIMIC-III — Medical Information Mart for Intensive Care

## Descripción General

**MIMIC-III** (Medical Information Mart for Intensive Care III) es la base de datos médica de acceso abierto más importante del mundo. Fue desarrollada por el **MIT Lab for Computational Physiology** en colaboración con el **Beth Israel Deaconess Medical Center (BIDMC)** en Boston, Massachusetts.

Contiene datos de salud de alta resolución, completamente **desidentificados**, de más de **40,000 pacientes** ingresados en Unidades de Cuidados Intensivos (UCI) entre **2001 y 2012**.

> ⚠️ **Esta es la versión DEMO (100 pacientes).** El dataset completo requiere completar un curso de ética en investigación humana (CITI Program) y firmar un acuerdo de uso de datos en PhysioNet.

---

## Metadatos

| Atributo | Valor |
|---|---|
| **Fuente** | MIT Lab for Computational Physiology / PhysioNet |
| **Dominio** | Medicina Intensiva (UCI) |
| **Tipo de Dato** | Base de datos relacional (26 tablas interconectadas) |
| **Tipo de Problema** | Clasificación, Series Temporales, NLP |
| **Variable Objetivo Sugerida** | `HOSPITAL_EXPIRE_FLAG` (mortalidad hospitalaria) |
| **Pacientes (demo)** | 100 |
| **Admisiones (demo)** | 129 |
| **Periodo Cubierto** | 2001 – 2012 |
| **Claves Relacionales** | `SUBJECT_ID` (paciente), `HADM_ID` (admisión), `ICUSTAY_ID` (estancia UCI) |

---

## Estructura Relacional — Las 26 Tablas

La base de datos MIMIC-III está organizada como una base de datos relacional. Las tablas se conectan entre sí mediante claves primarias y foráneas.

### 🔗 Diagrama de Relaciones Principal

```
PATIENTS (SUBJECT_ID)
    └── ADMISSIONS (SUBJECT_ID, HADM_ID)
            └── ICUSTAYS (HADM_ID, ICUSTAY_ID)
            └── DIAGNOSES_ICD (HADM_ID)
            └── PROCEDURES_ICD (HADM_ID)
            └── PRESCRIPTIONS (HADM_ID)
            └── LABEVENTS (HADM_ID)
            └── CHARTEVENTS (HADM_ID, ICUSTAY_ID)
            └── NOTEEVENTS (HADM_ID)
            └── SERVICES (HADM_ID)
            └── TRANSFERS (HADM_ID)
```

---

## Diccionario de Tablas

### 📋 PATIENTS — Datos Demográficos del Paciente

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila en la tabla |
| `SUBJECT_ID` | Entero | **Clave primaria del paciente** — identificador único por persona |
| `GENDER` | String | Género del paciente (M = Masculino, F = Femenino) |
| `DOB` | Timestamp | Fecha de nacimiento (desplazada al futuro para anonimizar). Para pacientes > 89 años, la fecha se ajusta artificialmente para que aparenten tener ~300 años |
| `DOD` | Timestamp | Fecha de muerte (si aplica) — puede ser en hospital o fuera |
| `DOD_HOSP` | Timestamp | Fecha de muerte registrada en el hospital |
| `DOD_SSN` | Timestamp | Fecha de muerte según el Seguro Social de EE. UU. |
| `EXPIRE_FLAG` | Binario | 1 si el paciente murió (en cualquier momento), 0 si sobrevivió |

> **Nota sobre edades:** Debido a la anonimización, los pacientes mayores de 89 años aparecen con edades de ~300 años cuando se calcula desde `DOB`. Deben limitarse (`clip`) a 89 años en el análisis.

---

### 🏨 ADMISSIONS — Datos de Admisión Hospitalaria

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | **Clave primaria de admisión** — un paciente puede tener múltiples admisiones |
| `ADMITTIME` | Timestamp | Fecha y hora de admisión al hospital |
| `DISCHTIME` | Timestamp | Fecha y hora de alta hospitalaria |
| `DEATHTIME` | Timestamp | Fecha y hora de muerte si ocurrió en el hospital |
| `ADMISSION_TYPE` | String | Tipo de admisión: EMERGENCY, URGENT, ELECTIVE, NEWBORN |
| `ADMISSION_LOCATION` | String | Origen de la admisión (EMERGENCY ROOM, TRANSFER, CLINIC REFERRAL, etc.) |
| `DISCHARGE_LOCATION` | String | Destino al alta (HOME, SNF, REHAB, DIED, etc.) |
| `INSURANCE` | String | Tipo de seguro médico (Medicare, Medicaid, Private, Government, Self Pay) |
| `LANGUAGE` | String | Idioma del paciente |
| `RELIGION` | String | Religión del paciente |
| `MARITAL_STATUS` | String | Estado civil |
| `ETHNICITY` | String | Etnia del paciente |
| `EDREGTIME` | Timestamp | Hora de registro en urgencias |
| `EDOUTTIME` | Timestamp | Hora de salida de urgencias |
| `DIAGNOSIS` | String | Diagnóstico de ingreso (texto libre, preliminary) |
| `HOSPITAL_EXPIRE_FLAG` | Binario | **1 = El paciente murió en el hospital (variable objetivo principal)** |
| `HAS_CHARTEVENTS_DATA` | Binario | 1 si existen registros en CHARTEVENTS para esta admisión |

---

### 🛏️ ICUSTAYS — Estancias en UCI

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | FK → ADMISSIONS |
| `ICUSTAY_ID` | Entero | **Clave primaria de estancia UCI** |
| `DBSOURCE` | String | Fuente del sistema: carevue (sistema antiguo) o metavision (sistema nuevo) |
| `FIRST_CAREUNIT` | String | Primera unidad de cuidados: CCU, CSRU, MICU, NICU, NWARD, SICU, TSICU |
| `LAST_CAREUNIT` | String | Última unidad de cuidados |
| `FIRST_WARDID` | Entero | ID de la primera sala |
| `LAST_WARDID` | Entero | ID de la última sala |
| `INTIME` | Timestamp | Hora de ingreso a UCI |
| `OUTTIME` | Timestamp | Hora de salida de UCI |
| `LOS` | Float | **Duración de estancia en UCI en días** — feature muy predictiva |

---

### 📊 CHARTEVENTS — Eventos de Monitor (La Tabla Más Grande)

> Esta es la tabla más voluminosa. En el dataset completo tiene **cientos de millones de filas**. Contiene cada medición de los monitores de UCI con timestamp exacto.

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | FK → ADMISSIONS |
| `ICUSTAY_ID` | Entero | FK → ICUSTAYS |
| `ITEMID` | Entero | FK → D_ITEMS — código del parámetro medido |
| `CHARTTIME` | Timestamp | Momento exacto de la medición |
| `STORETIME` | Timestamp | Momento en que el dato fue guardado en el sistema |
| `CGID` | Entero | FK → CAREGIVERS — quién registró el dato |
| `VALUE` | String | Valor de la medición (texto, puede ser numérico o categórico) |
| `VALUENUM` | Float | Valor numérico de la medición (cuando aplica) |
| `VALUEUOM` | String | Unidad de medida (mmHg, bpm, %, °F, etc.) |
| `WARNING` | Binario | 1 si se activó una alerta |
| `ERROR` | Binario | 1 si hay un error en la medición |
| `RESULTSTATUS` | String | Estado del resultado |
| `STOPPED` | String | Indica si la infusión fue detenida |

> **Parámetros clave medidos:** Frecuencia cardíaca, Presión arterial sistólica/diastólica/media, Saturación de oxígeno (SpO2), Temperatura, Frecuencia respiratoria, Glasgow Coma Scale.

---

### 🧪 LABEVENTS — Resultados de Laboratorio

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | FK → ADMISSIONS (puede ser NULL si es ambulatorio) |
| `ITEMID` | Entero | FK → D_LABITEMS — código del examen de laboratorio |
| `CHARTTIME` | Timestamp | Momento de la toma de muestra |
| `VALUE` | String | Resultado (texto) |
| `VALUENUM` | Float | Resultado numérico |
| `VALUEUOM` | String | Unidades (mg/dL, mmol/L, K/uL, etc.) |
| `FLAG` | String | `abnormal` si el resultado está fuera de rango normal |

> **Exámenes frecuentes:** Hemograma (WBC, RBC, Hemoglobina, Plaquetas), Química sanguínea (Glucosa, Creatinina, BUN), Gases arteriales (pH, pO2, pCO2), Electrolitos (Sodio, Potasio, Cloro).

---

### 💊 PRESCRIPTIONS — Medicamentos Recetados

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | FK → ADMISSIONS |
| `ICUSTAY_ID` | Entero | FK → ICUSTAYS |
| `STARTDATE` | Timestamp | Fecha de inicio del medicamento |
| `ENDDATE` | Timestamp | Fecha de fin del medicamento |
| `DRUG_TYPE` | String | Tipo: MAIN, ADDITIVE, BASE |
| `DRUG` | String | Nombre del medicamento |
| `DRUG_NAME_POE` | String | Nombre en sistema de órdenes |
| `DRUG_NAME_GENERIC` | String | Nombre genérico |
| `FORMULARY_DRUG_CD` | String | Código del formulario |
| `GSN` | String | Generic Sequence Number |
| `NDC` | String | National Drug Code |
| `PROD_STRENGTH` | String | Concentración del producto |
| `DOSE_VAL_RX` | String | Dosis recetada |
| `DOSE_UNIT_RX` | String | Unidad de la dosis |
| `FORM_VAL_DISP` | String | Cantidad dispensada |
| `FORM_UNIT_DISP` | String | Unidad dispensada |
| `ROUTE` | String | Vía de administración (IV, PO, SC, IM, etc.) |

---

### 📝 NOTEEVENTS — Notas Clínicas (NLP)

| Columna | Tipo | Descripción |
|---|---|---|
| `ROW_ID` | Entero | ID único de fila |
| `SUBJECT_ID` | Entero | FK → PATIENTS |
| `HADM_ID` | Entero | FK → ADMISSIONS |
| `CHARTDATE` | Date | Fecha de la nota |
| `CHARTTIME` | Timestamp | Hora de la nota (puede ser NULL) |
| `STORETIME` | Timestamp | Hora en que fue guardada |
| `CATEGORY` | String | Tipo de nota (Discharge summary, Nursing, Physician, Radiology, etc.) |
| `DESCRIPTION` | String | Subtipo de nota |
| `CGID` | Entero | FK → CAREGIVERS |
| `ISERROR` | Binario | 1 si la nota fue marcada como error |
| `TEXT` | String | **Texto completo de la nota clínica** — mina de oro para NLP |

> ⚠️ **Nota:** En la versión demo, `NOTEEVENTS.csv` solo tiene las cabeceras sin datos de texto por privacidad.

---

### 🗂️ Tablas de Referencia (Diccionarios)

| Tabla | Descripción |
|---|---|
| `D_ITEMS` | Diccionario de todos los ítems de CHARTEVENTS e INPUTEVENTS (nombre, unidad, concepto) |
| `D_LABITEMS` | Diccionario de todos los exámenes de laboratorio en LABEVENTS |
| `D_ICD_DIAGNOSES` | Catálogo de diagnósticos ICD-9 (código + descripción) |
| `D_ICD_PROCEDURES` | Catálogo de procedimientos ICD-9 |
| `D_CPT` | Catálogo de códigos CPT (Current Procedural Terminology) |

---

### 📌 Otras Tablas Importantes

| Tabla | Descripción |
|---|---|
| `DIAGNOSES_ICD` | Diagnósticos ICD-9 asignados a cada admisión (con secuencia de prioridad) |
| `PROCEDURES_ICD` | Procedimientos ICD-9 realizados durante la admisión |
| `CPTEVENTS` | Procedimientos facturados (códigos CPT) |
| `INPUTEVENTS_CV` | Fluidos y medicamentos administrados (sistema CareVue) |
| `INPUTEVENTS_MV` | Fluidos y medicamentos administrados (sistema MetaVision) |
| `OUTPUTEVENTS` | Fluidos de salida (orina, drenajes, etc.) |
| `MICROBIOLOGYEVENTS` | Cultivos microbiológicos y antibiogramas |
| `DATETIMEEVENTS` | Eventos con valor de fecha/hora (extubación, inserción de catéter, etc.) |
| `PROCEDUREEVENTS_MV` | Procedimientos registrados en MetaVision |
| `TRANSFERS` | Movimientos del paciente entre salas/unidades |
| `SERVICES` | Servicios médicos que atendieron al paciente |
| `CALLOUT` | Solicitudes de alta de UCI |
| `CAREGIVERS` | Información de los proveedores de salud (sin nombres, solo CGID) |
| `DRGCODES` | Códigos DRG (Diagnosis Related Groups) para facturación |

---

## ⚠️ Consideraciones Éticas y Técnicas

| Aspecto | Detalle |
|---|---|
| **Anonimización de fechas** | Todas las fechas fueron desplazadas al futuro preservando los intervalos temporales reales |
| **Anonimización de edad** | Pacientes > 89 años tienen edades artificiales (~300 años). Usar `.clip(upper=89)` |
| **Acceso completo** | Requiere certificación CITI y acuerdo de uso en PhysioNet |
| **Uso permitido** | Solo investigación — **PROHIBIDO** intentar re-identificar pacientes |
| **Dataset demo** | 100 pacientes seleccionados aleatoriamente — NO representativo |

---

## 🎯 Variables Objetivo Comunes

| Objetivo | Columna/Derivación | Tipo |
|---|---|---|
| Mortalidad hospitalaria | `HOSPITAL_EXPIRE_FLAG` en ADMISSIONS | Clasificación binaria |
| Mortalidad a 30 días | Calcular desde `DISCHTIME` y `DOD` | Clasificación binaria |
| Duración de estancia UCI | `LOS` en ICUSTAYS | Regresión |
| Readmisión a 30 días | Calcular con múltiples admisiones | Clasificación binaria |

---

## 🧹 Checklist de Preparación

- [ ] Hacer JOIN de PATIENTS + ADMISSIONS + ICUSTAYS como tabla base
- [ ] Calcular `AGE = (ADMITTIME - DOB).days / 365.25` y aplicar `.clip(upper=89)`
- [ ] Agregar LABEVENTS por `HADM_ID` (número de eventos, valores medios clave)
- [ ] Agregar PRESCRIPTIONS por `HADM_ID` (número de medicamentos únicos)
- [ ] Calcular `LOS_UCI` desde ICUSTAYS
- [ ] Usar `HOSPITAL_EXPIRE_FLAG` como variable objetivo
- [ ] Escalar variables numéricas (StandardScaler) para modelos lineales

---

## 📚 Referencias

- Johnson, A.E.W., et al. (2016). *MIMIC-III, a freely accessible critical care database*. Scientific Data, 3, 160035.
- PhysioNet: https://physionet.org/content/mimiciii-demo/1.4/
- Documentación completa: https://mimic.mit.edu/docs/iii/
