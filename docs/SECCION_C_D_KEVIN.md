# Secciones C y D — Preprocesamiento, Regresión Lineal Simple y Reproducibilidad

**Responsable:** Kevin. **Dataset:** Telco Customer Churn (Kaggle).
**Ubicación del código y gráficos:** `notebooks/TPI_Analisis_de_Datos.ipynb`, secciones C.0 a C.2 y D.

Este texto puede incorporarse o adaptarse al `INFORME_EJECUTIVO.pdf` final.

## Metodología

### Preprocesamiento para el modelado

Se partió de los 7.043 registros depurados en la sección A. De las 24 columnas disponibles se seleccionaron 18 variables predictoras; quedaron afuera las variables objetivo (`Churn`, `Churn_Binary` y `TotalCharges`) y las columnas calculadas en la sección A (`TheoreticalTotalCharges`, `BillingVariance` y `AvgHistoricalMonthlyCharge`), que son funciones de variables ya incluidas. `TotalCharges` se excluyó además por ser redundante con la antigüedad y el cargo mensual (Pearson 0,83 con `TenureMonths`).

Antes de codificar se unificaron como `No` las categorías `No internet service` (seis servicios adicionales) y `No phone service` (`MultipleLines`), tras comprobar fila por fila que repiten la información de `InternetService` y `PhoneService`. Así se evitan columnas duplicadas.

| Grupo | Variables | Transformación |
| --- | --- | --- |
| Cuantitativas | `TenureMonths`, `MonthlyCharges` | Estandarización con `StandardScaler` (media 0, desvío 1) |
| Ordinal | `Contract` | `OrdinalEncoder` con orden explícito: mes a mes = 0, un año = 1, dos años = 2 |
| Nominales | Las 15 categóricas restantes | `OneHotEncoder(drop='first')`: la primera categoría queda como referencia |

El resultado son 21 columnas numéricas (2 estandarizadas, 1 ordinal y 18 indicadoras 0/1).

La partición se hizo con `train_test_split` en proporción 80/20, **estratificada** por la variable objetivo y con semilla fija (`SEMILLA = 42`). Sin estratificar, en 200 sorteos la tasa de cancelación del conjunto de prueba varió entre 23,2 % y 30,2 %; con la estratificación queda en 26,54 %, igual a la de la base completa.

**Prevención de *data leakage*.** La partición se realizó antes de ajustar cualquier transformación. El escalador y los codificadores se agruparon en un `ColumnTransformer` que se ajustó solo con el conjunto de entrenamiento (`fit_transform`); al conjunto de prueba se le aplicó únicamente `transform`. Como control, las variables estandarizadas tienen media exactamente 0 en entrenamiento, pero no en prueba (−0,02 y −0,03): el escalador no "vio" a los clientes de prueba.

### Regresión lineal simple

Se estimó `TotalCharges` (cuantitativa continua) a partir de una única variable explicativa, `TenureMonths`, que es su predictora más correlacionada (Pearson 0,83; Spearman 0,89). Se usó la misma partición del preprocesamiento. No se emplearon como predictoras las columnas derivadas del propio `TotalCharges`. La predictora no se estandarizó: en una regresión simple el escalado no modifica las predicciones ni el R² (se verificó en el notebook), y en meses la pendiente se lee directamente como dinero por mes de permanencia. Las métricas se compararon con una línea base que predice siempre la media de entrenamiento.

## Resultados principales

| Indicador | Resultado |
| --- | ---: |
| Clientes de entrenamiento / prueba | 5.634 / 1.409 |
| Tasa de cancelación en entrenamiento / prueba | 26,54 % / 26,54 % |
| Predictoras → columnas codificadas | 18 → 21 |
| Recta estimada | `TotalCharges` = −201,04 + 76,97 × `TenureMonths` |
| R² en entrenamiento / prueba | 0,688 / 0,657 |
| MAE en entrenamiento / prueba | USD 879 / USD 876 |
| RMSE en entrenamiento / prueba | USD 1.272 / USD 1.297 |
| MAE de la línea base (prueba) | USD 1.858 |
| Reducción del error frente a la línea base | 52,8 % |

### Error del modelo por tramo de antigüedad (conjunto de prueba)

| Antigüedad (meses) | Clientes | MAE |
| --- | ---: | ---: |
| 0 a 12 | 449 | USD 181 |
| 13 a 24 | 206 | USD 453 |
| 25 a 48 | 304 | USD 907 |
| 49 o más | 450 | USD 1.743 |

## Interpretación e implicancias

- **Pendiente (USD 76,97 por mes):** cada mes adicional de permanencia se asocia, en promedio, con unos USD 77 más de facturación acumulada; retener a un cliente un año más equivale a ≈ USD 920.
- **Ordenada al origen (−USD 201,04):** no tiene interpretación de negocio; es el ajuste matemático de una recta a una nube con forma de abanico.
- **Sin sobreajuste:** el R² de entrenamiento (0,688) y el de prueba (0,657) son similares, y el MAE es prácticamente el mismo.
- **Valor para el negocio:** la permanencia es el principal motor del ingreso acumulado. Sumado al hallazgo de la sección B —quienes cancelan tienen poca antigüedad—, respalda invertir en retención temprana.

## Limitaciones

- **Heterocedasticidad:** el error crece con la antigüedad (de USD 181 a USD 1.743 de MAE entre el primer tramo y el último). `TotalCharges` es aproximadamente antigüedad × cargo mensual y la regresión simple ignora este último: los clientes de cargo mensual alto quedan por encima de la recta (correlación entre el residuo y `MonthlyCharges`: 0,78).
- **Predicciones negativas:** para antigüedades menores a 2,6 meses la recta estima montos negativos (180 de los 1.409 clientes de prueba).
- **Alcance:** el modelo sirve para estimar órdenes de magnitud y cuantificar el valor de la permanencia, no para proyectar la facturación de un cliente puntual. Una regresión lineal múltiple que incorpore `MonthlyCharges` sería el paso siguiente, fuera del alcance de la consigna.
- **Codificación ordinal:** asume escalones equidistantes entre los tres plazos de contrato.

## Reproducibilidad y buenas prácticas

- **Datos originales inmutables:** el CSV de `data/raw/` no se modifica. El dataset depurado (`telco_churn_procesado.csv`) y el transformado para modelar (`telco_churn_modelado.csv`) se guardan en `data/processed/` y se regeneran al ejecutar el notebook.
- **Ejecución en Google Colab:** el notebook corre de punta a punta con *Entorno de ejecución → Ejecutar todas*, sin instalar paquetes ni subir archivos: si no encuentra el CSV local, lo lee desde el repositorio.
- **Semilla fija:** `SEMILLA = 42` en la partición; la ficha de reproducibilidad de la sección D lista los valores que deben repetirse en cada ejecución.
- **Controles automáticos:** verificaciones con `assert` y `raise` que detienen la ejecución si los datos no tienen la forma esperada.
- **Verificación:** el notebook se ejecutó completo, sin errores ni advertencias, con las versiones de librerías de Google Colab (Python 3.13, pandas 2.2.3, NumPy 2.4.6, scikit-learn 1.6.1, Matplotlib 3.10.0, Seaborn 0.13.2). Las secciones C y D dan resultados idénticos con pandas 3.1 y scikit-learn 1.9.
- **Control de versiones:** cada sección se desarrolló en una rama propia y se integró a `main` mediante *pull request*, con *commits* descriptivos.
