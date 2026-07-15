# Comparativa entre Chronos-2 y TimeXer para predicción de series temporales multivariantes

Repositorio de notebooks del Trabajo de Fin de Máster (TFM) del **Máster en Inteligencia Artificial**, autor **José Antonio Íñigo**.

## Resumen

La predicción de series temporales multivariantes constituye un problema fundamental en múltiples dominios donde anticipar fenómenos como la movilidad, la demanda o la actividad operativa resulta esencial para la toma de decisiones. La reciente aparición de los *foundation models* para *forecasting* ha planteado una pregunta relevante: si el preentrenamiento masivo sobre datos heterogéneos puede ofrecer un rendimiento igual o superior al de las arquitecturas especialistas entrenadas específicamente sobre un dominio concreto. Este trabajo aborda dicha cuestión mediante una evaluación comparativa de **Chronos-2**, como modelo fundacional representativo, y **TimeXer**, como modelo especialista.

La metodología experimental se estructuró en torno a dos dominios de naturaleza marcadamente distinta: la predicción de la **intensidad de tráfico urbano en Madrid** y la predicción de la **demanda de productos de retail** utilizando el *benchmark* **M5**. Para ello se construyeron dos conjuntos de datos multivariantes que integran información histórica y covariables específicas de cada dominio, incluyendo variables de calendario, meteorológicas y espaciales. La comparación se realizó considerando distintas ventanas de contexto y se evaluó mediante un conjunto complementario de métricas de error puntuales, escaladas y probabilísticas, analizadas desde una perspectiva global, por serie y por horizonte de predicción.

Los resultados de la investigación muestran que **Chronos-2 alcanza un rendimiento superior en la predicción de tráfico urbano**, especialmente al emplear ventanas de contexto más amplias, reduciendo el error respecto al mejor escenario de TimeXer en torno a un **20 %** en las métricas principales. En *retail*, sin embargo, las diferencias son más limitadas, con mejoras inferiores al **2 %** en las métricas agregadas. Las conclusiones destacan que los *foundation models* pueden constituir una alternativa competitiva a los modelos especialistas en escenarios de predicción multivariante, especialmente en contextos con señales densas y regulares, aunque los resultados están condicionados por el dominio y por el marco experimental definido.

## Modelos comparados

- **Chronos-2** — *foundation model* para *forecasting*, evaluado en modo cero/pocos disparos sin entrenamiento específico de dominio.
- **TimeXer** — modelo especialista, entrenado de forma supervisada sobre cada conjunto de datos.

## Estructura del repositorio

El repositorio se organiza en dos dominios experimentales, cada uno en su propia carpeta. La numeración de los notebooks refleja el orden del *pipeline*: procesamiento de datos → EDA → experimentos → gráficas.

### `traffic/` — Tráfico urbano de Madrid

Predicción de la intensidad de tráfico horaria por sensor (variable objetivo `intensidad`).

| Notebook | Propósito |
|----------|-----------|
| `1-traffic-data-processing.ipynb` | Procesamiento del dataset de tráfico de Madrid (puntos de medida urbanos `C30` e interurbanos `M30`). |
| `1-meteosat-data-processing.ipynb` | Procesamiento de datos meteorológicos horarios de Madrid (API Meteostat). |
| `1-open-meteo-weather-forecast.ipynb` | Procesamiento de la previsión meteorológica (Open-Meteo). |
| `2.1-unified-data-processing.ipynb` | Unificación de tráfico + meteorología + previsión + localización/distrito de sensores en un único dataset. |
| `2.2-sliced-data.ipynb` | Generación del subconjunto temporal (*slice*) usado en los experimentos. |
| `3-eda.ipynb` | Análisis exploratorio (EDA) del dataset unificado tráfico + meteo. |
| `4-chronos-2-PL96-HH168.ipynb` | Chronos-2 — horizonte de predicción 96, ventana de contexto 168. |
| `4-chronos-2-PL96-HH504.ipynb` | Chronos-2 — horizonte de predicción 96, ventana de contexto 504. |
| `4-chronos-2-PL96-HH1440.ipynb` | Chronos-2 — horizonte de predicción 96, ventana de contexto 1440. |
| `4-timexer-PL96-HH168.ipynb` | TimeXer — horizonte de predicción 96, ventana de contexto 168. |
| `4-timexer-PL96-HH504.ipynb` | TimeXer — horizonte de predicción 96, ventana de contexto 504. |
| `4-timexer-PL96-HH1440.ipynb` | TimeXer — horizonte de predicción 96, ventana de contexto 1440. |
| `5-additional-graphs.ipynb` | Figuras adicionales de resultados. |

> **Nomenclatura**: `PL` = *prediction length* (horizonte de predicción) y `HH` = ventana de contexto histórico, ambos en horas.

### `retail/` — Demanda de retail (M5)

Predicción de demanda de productos sobre un subconjunto experimental de 500 series densas del *benchmark* M5 Forecasting Accuracy, con holdout de evaluación de 28 días.

| Notebook | Propósito                                                                                                 |
|----------|-----------------------------------------------------------------------------------------------------------|
| `1-dataset.ipynb` | Construcción del dataset M5 y generación de los CSV preprocesados (`m5_context.csv`, `m5_future.csv`, …). |
| `2-eda.ipynb` | EDA sobre el subconjunto de 500 series densas usado en los experimentos.                                  |
| `3-chronos2-728H.ipynb` | Chronos-2 — ventana de contexto 728 días.                                                                 |
| `3-chronos2-1092H.ipynb` | Chronos-2 — ventana de contexto 1092 días.                                                               |
| `3-chronos2-1456H.ipynb` | Chronos-2 — ventana de contexto 1456 días.                                                               |
| `3-timexer-728H.ipynb` | TimeXer — ventana de contexto 728 días.                                                                  |
| `3-timexer-1092H.ipynb` | TimeXer — ventana de contexto 1092 días.                                                                 |
| `3-timexer-1456H.ipynb` | TimeXer — ventana de contexto 1456 días.                                                                 |
| `4-additional-graphs.ipynb` | Figuras de resultados globales y por serie.                                                               |

> **Nomenclatura**: el sufijo `728H` / `1092H` / `1456H` indica la ventana de contexto en días.

## Metodología de evaluación

- **Ventanas de contexto** múltiples por dominio, para analizar el efecto del histórico disponible.
- **Métricas** complementarias: puntuales, escaladas y probabilísticas.
- **Niveles de análisis**: global, por serie y por horizonte de predicción.

## Datos

Los notebooks de procesamiento (`1-*` y `2-*`) esperan los datos de origen en carpetas `dataset/` locales (disponibles en Kaggle). Cada notebook documenta en su primera celda las rutas y los archivos de entrada/salida que utiliza.

### Fuentes de datos

- **Tráfico**: portal de datos abiertos del Ayuntamiento de Madrid (intensidad de tráfico por punto de medida).
- **Meteorología**: API de [Meteostat](https://meteostat.net) (datos históricos horarios) y [Open-Meteo](https://open-meteo.com) (previsión).
- **Retail**: *benchmark* [M5 Forecasting Accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy).

## Uso

1. Situar los datos de origen en las carpetas `dataset/` correspondientes a cada dominio.
2. Ejecutar los notebooks `1-*` y `2-*` en orden para generar los datasets procesados.
3. Ejecutar los notebooks de experimentos (`3-*` / `4-*`) para reproducir las predicciones de cada modelo y ventana de contexto.
4. Actualizar los notebooks de gráficas adicionales (`4-*` / `5-*`) con los resultados de los experimentos.
5. Ejecutar los notebooks de gráficas adicionales (`4-*` / `5-*`) para generar las figuras de resultados.
