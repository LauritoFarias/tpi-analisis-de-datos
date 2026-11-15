# Sección B — Análisis Exploratorio, Visualización e Integración

**Responsable:** Ignacio. **Dataset:** Telco Customer Churn (Kaggle).
**Ubicación del código y gráficos:** `notebooks/TPI_Analisis_de_Datos.ipynb`, al finalizar la sección A.

Este texto puede incorporarse o adaptarse al `INFORME_EJECUTIVO.pdf` final.

## Metodología

Se analizaron 7.043 registros procesados en la sección A. Se estudió la distribución de `TenureMonths`, `MonthlyCharges` y `TotalCharges` mediante medidas de tendencia central (media y mediana), posición (cuartiles) y dispersión (desvío estándar, varianza, rango e IQR). Se aplicaron Pearson y Spearman para estudiar las asociaciones entre variables numéricas, además de frecuencias absolutas y relativas y tablas de contingencia para las características sociodemográficas y contractuales. La tasa de cancelación se calculó como la proporción de clientes con `Churn == Yes` dentro de cada segmento.

Los histogramas con KDE describen la antigüedad y los cargos mensuales por estado de cancelación. El mapa de calor presenta las correlaciones numéricas. Los diagramas de barras comparan las tasas de cancelación por contrato y método de pago, indicando el tamaño de cada grupo. Como integración adicional, se cruzaron tipo de contrato y servicio de internet.

## Resultados principales

| Indicador | Resultado |
| --- | ---: |
| Clientes analizados | 7.043 |
| Clientes que cancelaron | 1.869 |
| Tasa general de cancelación | 26,54 % |
| Antigüedad media: cancelaron / no cancelaron | 17,98 / 37,57 meses |
| Cargo mensual medio: cancelaron / no cancelaron | 74,44 / 61,27 |
| Correlación Pearson: antigüedad–cargos totales | 0,826 |
| Correlación Spearman: antigüedad–cargos totales | 0,890 |

### Tasa de cancelación por tipo de contrato

| Contrato | Clientes | Cancelaron | Tasa |
| --- | ---: | ---: | ---: |
| Mes a mes | 3.875 | 1.655 | 42,71 % |
| Un año | 1.473 | 166 | 11,27 % |
| Dos años | 1.695 | 48 | 2,83 % |

### Tasa de cancelación por método de pago

| Método | Clientes | Cancelaron | Tasa |
| --- | ---: | ---: | ---: |
| Transferencia bancaria automática | 1.544 | 258 | 16,71 % |
| Tarjeta de crédito automática | 1.522 | 232 | 15,24 % |
| Cheque electrónico | 2.365 | 1.071 | 45,29 % |
| Cheque por correo | 1.612 | 308 | 19,11 % |

## Interpretación e implicancias

Los contratos mensuales presentan una mayor proporción de cancelaciones que los contratos de uno o dos años. El grupo que abona con cheque electrónico también muestra una mayor tasa observada de cancelación que quienes utilizan métodos de pago automáticos. Quienes cancelaron tenían, en promedio, menos meses de antigüedad y cargos mensuales más elevados.

La segmentación simultánea indica que la combinación de contrato mensual y servicio de fibra óptica presenta una tasa aproximada de cancelación del 54,6 %. Estos resultados sugieren investigar posibles acciones de fidelización temprana, satisfacción de clientes con fibra óptica y barreras de uso de medios de pago automáticos, en lugar de asumir que una sola variable sea la causa de las bajas.

## Limitaciones

Este estudio es **observacional y descriptivo**: no establece relaciones causales ni permite medir tendencias temporales. Las categorías tienen tamaños diferentes; por eso se informan tasas y cantidades. `TotalCharges` es un importe acumulado y naturalmente se asocia a la antigüedad. Para diseñar y evaluar intervenciones de retención conviene complementar estos hallazgos con experimentación y con la clasificación de `Churn` prevista en la sección C.
