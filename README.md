# Predicción de la baja de clientes (churn) en una empresa de telecomunicaciones

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/LauritoFarias/tpi-analisis-de-datos/blob/main/notebooks/TPI_Analisis_de_Datos.ipynb)

**Trabajo Práctico Integrador — Introducción al Análisis de Datos (segundo parcial)**

Tecnicatura Universitaria en Programación · UTN Avellaneda · 2026

Docente a cargo: Enzo Martín Zotti

## Problema de negocio

En un servicio por suscripción, cada cliente que cancela (*churn*) es un ingreso recurrente que se pierde, y reemplazarlo suele costar más que retenerlo. La empresa de telecomunicaciones analizada perdió **1.869 de sus 7.043 clientes (26,5 %)**.

El proyecto recorre el ciclo completo de un trabajo de datos para responder dos preguntas:

1. **¿Qué caracteriza a los clientes que se van?:** Limpieza de los datos y análisis exploratorio de la antigüedad, los cargos, el tipo de contrato, los servicios y la forma de pago.
2. **¿Qué se puede anticipar?:** Modelos predictivos simples e interpretables, evaluados sobre clientes que no participaron del entrenamiento.

El objetivo final es convertir esos resultados en criterios para orientar las acciones de retención.

El dataset usado en este proyecto es [Telco Customer Churn](https://www.kaggle.com/datasets/blastchar/telco-customer-churn) (Kaggle, datos de muestra de IBM). Contiene 7.043 clientes y 21 columnas con información sociodemográfica, servicios contratados, facturación y condición de baja.

## Integrantes

| Integrante | GitHub | Sección a cargo |
| --- | --- | --- |
| Iván Laurito | [LauritoFarias](https://github.com/LauritoFarias) | A. Fundamentos, ingesta y limpieza de datos |
| Ignacio Guillen | [ignacioG00](https://github.com/ignacioG00) | B. Análisis exploratorio, visualización e integración |
| Kevin Plucci | [KevinPlucci](https://github.com/KevinPlucci) | C. Preprocesamiento y regresión lineal · D. Reproducibilidad y buenas prácticas |

**División:** 343
## Abrir el notebook en Google Colab

1. Hacer clic en el badge **Open in Colab** de arriba o en [este enlace](https://colab.research.google.com/github/LauritoFarias/tpi-analisis-de-datos/blob/main/notebooks/TPI_Analisis_de_Datos.ipynb).
2. En Colab, elegir **Entorno de ejecución → Ejecutar todas**.

No hace falta instalar paquetes ni subir archivos: el notebook lee el dataset directamente desde este repositorio y se ejecuta de punta a punta, en orden.

## Stack tecnológico

| Herramienta | Uso en el proyecto |
| --- | --- |
| **Python 3** | Lenguaje del proyecto |
| **Pandas** | Carga, limpieza, filtrado con condiciones lógicas, agregación (`groupby`) y tablas de contingencia |
| **NumPy** | Cálculo numérico y columnas calculadas |
| **Matplotlib** y **Seaborn** | Histogramas/KDE, diagramas de caja, mapas de calor y gráficos de barras |
| **Scikit-Learn** | Partición estratificada, codificación, estandarización y modelos (`train_test_split`, `ColumnTransformer`, `OrdinalEncoder`, `OneHotEncoder`, `StandardScaler`, `LinearRegression`) |
| **Google Colab** | Entorno de ejecución reproducible |
| **Git y GitHub** | Control de versiones: una rama por sección y *pull requests* hacia `main` |

Verificado con las versiones que trae Google Colab (noviembre de 2026): Python 3.13, pandas 2.2.3, NumPy 2.4.6, Matplotlib 3.10.0, Seaborn 0.13.2 y scikit-learn 1.6.1.

## Estructura del repositorio

```
tpi-analisis-de-datos/
├── data/
│   ├── raw/
│   │   └── WA_Fn-UseC_-Telco-Customer-Churn.csv   # dataset original (no se modifica)
│   └── processed/
│       ├── telco_churn_procesado.csv              # dataset depurado (sección A)
│       └── telco_churn_modelado.csv               # dataset codificado, estandarizado y particionado (sección C)
├── notebooks/
│   └── TPI_Analisis_de_Datos.ipynb                # notebook oficial, ejecutable en Google Colab
├── docs/
│   └── INFORME_EJECUTIVO.pdf                      # informe técnico-ejecutivo
└── README.md                                      # este documento
```

`docs/` incluye además los textos por sección (`SECCION_*.md`) con los que se arma el informe.

## Cómo explorar el proyecto

1. **Visión general:** leer `docs/INFORME_EJECUTIVO.pdf`, que resume la metodología, los resultados y las conclusiones para el negocio.
2. **Análisis completo:** abrir el notebook en Colab y ejecutarlo. Está organizado en cuatro secciones:

| Sección | Contenido | Responsable |
| --- | --- | --- |
| **A. Fundamentos, ingesta y limpieza** | Problema de negocio e hipótesis, clasificación de variables y `dtypes`, cadenas anómalas en `TotalCharges`, valores atípicos (IQR), filtrado y agregación | Iván |
| **B. Análisis exploratorio y visualización** | Medidas descriptivas, correlación de Pearson y Spearman, tablas de contingencia, histogramas/KDE, mapas de calor y tasas de cancelación por contrato y medio de pago | Ignacio |
| **C. Preprocesamiento y modelado predictivo** | Partición estratificada 80/20, codificación ordinal y *One-Hot*, estandarización y regresión lineal simple de `TotalCharges` | Kevin |
| **D. Reproducibilidad y buenas prácticas** | Entorno de ejecución, ficha de reproducibilidad y flujo de trabajo con Git | Kevin |

3. **Datos:** `data/raw/` guarda el CSV original. `data/processed/` guarda lo que genera el notebook:

| Archivo | Filas × columnas | Contenido |
| --- | --- | --- |
| `telco_churn_procesado.csv` | 7.043 × 25 | Dataset depurado: tipos corregidos, sin nulos y con las columnas calculadas en la sección A |
| `telco_churn_modelado.csv` | 7.043 × 24 | Las 21 columnas codificadas y estandarizadas, la variable objetivo (`Churn_Binary`) y el conjunto de cada cliente (`entrenamiento` / `prueba`) |

En ambos, `customerID` se conserva solo como clave de trazabilidad hacia el archivo original.

## Resultados principales

- **Uno de cada cuatro clientes se dio de baja:** 1.869 de 7.043 (26,54 %).
- **El contrato marca la diferencia:** la tasa de cancelación es del 42,71 % en los contratos mes a mes, del 11,27 % en los de un año y del 2,83 % en los de dos años.
- **El medio de pago también:** 45,29 % con cheque electrónico, frente a 15 % a 19 % en los demás medios.
- **Las bajas se concentran en clientes nuevos y de cargo alto:** quienes cancelaron tenían 18 meses de antigüedad media (37,6 quienes permanecen) y un cargo mensual medio de 74,44 (61,27 quienes permanecen).
- **La permanencia es el motor del ingreso acumulado:** la regresión lineal `TotalCharges ≈ −201,04 + 76,97 × antigüedad` explica el 65,7 % de la variabilidad en clientes no vistos (R² = 0,657; MAE = USD 876, un 52,8 % menos que predecir la media). Cada mes de permanencia suma, en promedio, unos USD 77 de facturación.

## Reproducibilidad y buenas prácticas

- **Datos originales inmutables:** el CSV de `data/raw/` nunca se modifica; los resultados se guardan en `data/processed/` y se regeneran al ejecutar el notebook.
- **Semilla fija:** `SEMILLA = 42` en la partición de datos. La última sección del notebook muestra una ficha con los valores que deben repetirse en cada ejecución.
- **Sin fuga de información (*data leakage*):** la partición entrenamiento/prueba se hace antes de ajustar el escalado, la codificación y los modelos; el conjunto de prueba solo se usa para evaluar.
- **Controles automáticos:** las verificaciones con `assert` detienen la ejecución si los datos no tienen la forma esperada.
- **Flujo de trabajo con Git:** `main` contiene siempre una versión ejecutable. Cada sección se desarrolla en una rama propia y se integra mediante *pull request*, con mensajes de *commit* que indican la sección y el cambio realizado.

Para ejecutar el proyecto en una copia local:

```bash
git clone https://github.com/LauritoFarias/tpi-analisis-de-datos.git
cd tpi-analisis-de-datos
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook notebooks/TPI_Analisis_de_Datos.ipynb
```
