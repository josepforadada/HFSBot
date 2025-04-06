# Del Patrón Intuitivo al Trading Algorítmico: Modelo Predictivo para Criptomonedas en Alta Frecuencia

Este repositorio documenta el desarrollo de un sistema predictivo para el trading de criptomonedas en alta frecuencia (HFT), partiendo de una intuición visual inicial y culminando en un modelo de regresión para predecir subidas de precio a corto plazo.

## Resumen

El dinámico y volátil mercado de las criptomonedas presenta desafíos y oportunidades para el trading algorítmico. Este trabajo documenta el desarrollo de un sistema predictivo, partiendo de la observación de un patrón gráfico pseudo-sinusoidal recurrente y culminando en un modelo de regresión. La metodología abarcó:

1.  **Adquisición de Datos:** Descarga de datos de velas de 1 minuto desde la API de Binance para múltiples pares USDT.
2.  **Ingeniería de Características "Row-Independent":** Generación de lags temporales y cálculo de numerosos indicadores técnicos y patrones de velas, diseñados para HFT, usando solo información disponible en cada timestamp.
3.  **Definición de Objetivos:**
    *   Clasificación: Intentar capturar formalmente el patrón sinusoidal y predecir una subida subsiguiente (con bajo éxito).
    *   Regresión: Predecir el porcentaje máximo de subida del precio en los siguientes 5-15 minutos (`future_max_increase_capped`).
4.  **Modelado y Evaluación:** Experimentación con modelos de clasificación, detección de anomalías y regresión. Se utilizó validación cruzada temporal (`TimeSeriesSplit`) y se evaluaron diversas métricas.
5.  **Resultados:** Los enfoques de clasificación y detección de anomalías para el patrón sinusoidal no fueron efectivos. Sin embargo, el enfoque de **regresión** (usando Gradient Boosting Regressor) mostró resultados prometedores para predecir la subida porcentual a corto plazo.

La principal contribución es la validación de un framework metodológico completo para HFT en cripto, incluyendo la ingeniería de características "row-independent" y la evaluación de modelos de regresión.

**Nota:** La validación definitiva requiere un backtesting riguroso (trabajo futuro).

## Motivación

El proyecto surgió de la observación visual de un patrón recurrente (descenso pronunciado -> valle redondeado -> inicio de recuperación) en gráficos intradía de 1 minuto, hipotetizando que podría predecir subidas de precio inminentes (5-15 min). El objetivo fue formalizar esta intuición y evaluarla algorítmicamente.

## Metodología y Flujo de Trabajo

El proyecto sigue una secuencia lógica implementada a través de varios scripts (notebooks Jupyter):

1.  **`TFM_01_scrapping_data.ipynb`**: Descarga de datos de velas de 1 minuto desde Binance.
2.  **`TFM_02_indicadores.ipynb`**: Cálculo de lags/leads e indicadores técnicos / patrones de velas ("row-independent").
3.  **`TFM_03_objectives_and_plots.ipynb`**: Definición de variables objetivo (clasificación y regresión) y análisis exploratorio/visualización del patrón inicial.
4.  **`TFM_04_data_cleaning_and_split.ipynb`**: Limpieza final de datos, eliminación de columnas redundantes y división cronológica en conjuntos de entrenamiento (80%) y prueba (20%).
5.  **`TFM_05_normalization_and_generate_models.ipynb`**: Normalización de datos por símbolo (MinMax con escaladores ajustados *solo* en train) y entrenamiento/evaluación mediante CV (TimeSeriesSplit) de diversos modelos de clasificación y regresión (RF, GB, LR, XGB, LSTM).
6.  **`TFM_06_modeling_anomalies.ipynb`**: Entrenamiento y evaluación específicos de modelos de detección de anomalías (Isolation Forest, One-Class SVM) para el target de clasificación.
7.  **`TFM_07_final_taninig_col_importance_and_export.ipynb`**: Re-entrenamiento del mejor modelo de regresión identificado (Gradient Boosting) en todo el conjunto de entrenamiento, evaluación final en el conjunto de prueba, cálculo de importancia de características y guardado del modelo final.

## Enfoque "Row-Independent"

Una característica clave de este proyecto es el cálculo de indicadores de forma "independiente de la fila" (*row-independent*). Esto significa que para calcular un indicador en un instante `t`, solo se utiliza la información disponible en la fila `t` (incluyendo los valores pasados almacenados en columnas `_lag_X`), sin depender de cálculos recursivos o acumulativos de filas anteriores. Esto es crucial para la aplicabilidad en sistemas de HFT en tiempo real. Para indicadores como EMA, MACD, OBV, etc., esto produce una *aproximación* basada en la ventana de lags, que difiere del cálculo estándar pero es consistente y aplicable.

## Tecnologías Utilizadas

*   **Lenguaje:** Python 3.x
*   **Librerías Principales:**
    *   `pandas`: Manipulación y análisis de datos.
    *   `numpy`: Operaciones numéricas.
    *   `scikit-learn`: Modelos de Machine Learning (Regresión Logística, Random Forest, Gradient Boosting, SVM), métricas, preprocesamiento (MinMaxScaler), validación cruzada (TimeSeriesSplit).
    *   `xgboost`: (Opcional) Modelos XGBoost Classifier/Regressor.
    *   `tensorflow`/`keras`: (Opcional) Modelos LSTM.
    *   `statsmodels`: (Opcional) Modelos ARIMA, Exponential Smoothing.
    *   `plotly`: Visualizaciones interactivas.
    *   `aiohttp` & `asyncio`: Descargas asíncronas de la API de Binance.
    *   `pytz`: Manejo de zonas horarias (UTC).
    *   `joblib`: Guardar/cargar objetos Python (escaladores, modelos).
    *   `tqdm`: Barras de progreso.
    *   `logging`: Registro de eventos.
    *   `json`: Manejo de archivos JSON.
    *   `scipy`: Funciones científicas (ej., para análisis de patrones).

## Estructura del Repositorio
```
├── TFM_01_scrapping_data.ipynb                           # Script 1: Descarga de datos
├── TFM_02_indicadores.ipynb                              # Script 2: Cálculo de indicadores
├── TFM_03_objectives_and_plots.ipynb                     # Script 3: Definición de targets y análisis
├── TFM_04_data_cleaning_and_split.ipynb                  # Script 4: Limpieza y división train/test
├── TFM_05_normalization_and_generate_models.ipynb        # Script 5: Normalización y CV de modelos
├── TFM_06_modeling_anomalies.ipynb                       # Script 6: Modelos de detección de anomalías
├── TFM_07_final_taninig_col_importance_and_export.ipynb  # Script 7: Entrenamiento final GBR y exportación
│
├── README.md                                             # Este archivo

```

*(Nota: Los archivos generados a lo largo del proceso así como de los modelos de predicción no estan disponibles).*

## Uso

1.  **Instalar dependencias:**

2.  **Ejecutar los scripts en orden:**
    *   `TFM_01_scrapping_data.ipynb`: Descargar datos para una fecha.
    *   `TFM_02_indicadores.ipynb`: Generar características (asegúrate de que use el CSV correcto de salida del paso 1).
    *   `TFM_03_objectives_and_plots.ipynb`: Crear targets y analizar (usar salida del paso 2).
    *   `TFM_04_data_cleaning_and_split.ipynb`: Limpiar y dividir (usar salida del paso 3).
    *   `TFM_05_normalization_and_generate_models.ipynb`: Normalizar y realizar la validación cruzada de modelos (usar salidas del paso 4).
    *   `TFM_06_modeling_anomalies.ipynb`: Entrenar y evaluar modelos de anomalías (usar salidas del paso 4 y escaladores del paso 5).
    *   `TFM_07_final_taninig_col_importance_and_export.ipynb`: Entrenar, evaluar y guardar el mejor modelo de regresión final (usar salidas del paso 4 y escaladores del paso 5/7).

3.  **Revisar Resultados:** Examinar los archivos CSV, JSON, logs y gráficos generados en las carpetas correspondientes.

## Resultados Clave

*   La detección del patrón sinusoidal inicial mediante clasificación o detección de anomalías no fue efectiva (bajo F1, bajo PR AUC, alta tasa de falsos positivos).
*   El modelo de **regresión** (Gradient Boosting Regressor con 3000 estimadores) mostró un rendimiento prometedor en la predicción del porcentaje máximo de subida futura (`future_max_increase_capped`) en el conjunto de prueba, con métricas alentadoras (ej. R² ~0.964, bajo MAE/MSE en la evaluación final).
*   El enfoque "row-independent" y la normalización por símbolo fueron fundamentales para el éxito relativo del modelo de regresión.

## Trabajo Futuro

El paso más crucial es realizar un **backtesting riguroso** de una estrategia de trading basada en las predicciones del modelo de regresión final, incorporando comisiones, slippage y latencia para evaluar su rentabilidad práctica. Otras líneas incluyen la optimización continua del modelo, la exploración de más características y la implementación/prueba en tiempo real (paper/live trading).

