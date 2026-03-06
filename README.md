# Optimización Aeroportuaria: Predicción de Atrasos en Vuelos

## 1. Visión General del Proyecto

Este proyecto tiene como objetivo desarrollar un modelo predictivo para estimar los atrasos en vuelos, utilizando un conjunto de datos que contiene información detallada sobre operaciones aeroportuarias. La meta es identificar los factores clave que contribuyen a los atrasos y construir un modelo robusto que pueda predecir el tiempo de demora, lo que podría ser útil para la planificación operativa y la mejora de la experiencia del pasajero.

## 2. Origen de los Datos

El análisis se basa en el archivo `flights.csv`, que incluye las siguientes columnas:

- `flight_id`: Identificador único del vuelo.
- `airline`: Aerolínea operadora.
- `aircraft_type`: Tipo de aeronave utilizada.
- `schengen`: Indica si el vuelo es dentro o fuera del espacio Schengen.
- `origin`: Aeropuerto de origen.
- `arrival_time`: Hora programada de llegada.
- `departure_time`: Hora programada de salida.
- `day`: Día del año.
- `year`: Año de la operación del vuelo.
- `is_holiday`: Booleano que indica si el día es festivo.
- `delay`: Atraso real del vuelo en minutos (variable objetivo).

## 3. Preprocesamiento y Feature Engineering

Para preparar los datos para el modelado, se realizaron los siguientes pasos:

- **Creación de columna `date`**: Se combinaron las columnas `year` y `day` para crear una columna de fecha (`YYYY-DDD`) y se convirtió a tipo `datetime`.
- **Creación de `is_weekend` y `day_name`**: A partir de la columna `date`, se derivaron nuevas características para identificar si el vuelo ocurre en fin de semana y el nombre del día de la semana.
- **Codificación de variables categóricas**: Las columnas `schengen`, `is_holiday` e `is_weekend` se transformaron a valores numéricos (0 y 1).
- **One-Hot Encoding**: Las variables categóricas nominales (`airline`, `aircraft_type`, `origin`, `day_name`) se convirtieron a representaciones numéricas mediante One-Hot Encoding para ser adecuadas para los modelos de aprendizaje automático.
- **Eliminación de columnas no necesarias**: Se eliminaron columnas redundantes o no útiles para el modelado, como `flight_id`, `departure_time` (debido a su alta correlación con `arrival_time`), `day`, `year` y `date`.

## 4. Análisis Exploratorio de Datos (EDA)

El EDA incluyó la visualización y análisis de la distribución de los atrasos, la relación entre los atrasos y las aerolíneas, tipos de aeronaves, tipo de vuelo (Schengen/no Schengen), y días festivos. Los hallazgos clave fueron:

- **Distribución de atrasos**: Los atrasos muestran una distribución asimétrica con una cola larga hacia valores positivos, indicando que la mayoría de los vuelos tienen atrasos pequeños o nulos, pero algunos experimentan atrasos significativos.
- **Factores influyentes**: Se observaron diferencias en los atrasos promedio según la aerolínea, el tipo de aeronave y si el día era festivo o no.

## 5. Modelado Predictivo

Se implementó un modelo `RandomForestRegressor` para predecir el `delay`. Se realizaron los siguientes pasos:

- **División de datos**: Los datos se dividieron en conjuntos de entrenamiento y prueba (70% entrenamiento, 30% prueba).
- **Modelo Baseline**: Se estableció un `DummyRegressor` para tener una referencia de rendimiento.
- **Entrenamiento del modelo**: Se entrenó un `RandomForestRegressor` con `max_depth=5`.
- **Evaluación inicial**: Se calcularon métricas de regresión (RMSE, MAE, R2) para el modelo entrenado.
- **Validación Cruzada**: Se utilizó `KFold` con 5 splits para evaluar la estabilidad del modelo.
- **Importancia de las características**: Se analizó la importancia de las características para entender qué variables tienen mayor impacto en la predicción de atrasos.
- **Selección de Características**: Se realizó un proceso de selección de características basado en la importancia para optimizar el modelo.
- **Optimización de Hiperparámetros**: Se utilizó `GridSearchCV` para encontrar la mejor combinación de hiperparámetros (`max_depth`, `min_samples_leaf`, `min_samples_split`, `n_estimators`) para el `RandomForestRegressor`.

## 6. Resultados y Conclusiones

El modelo `RandomForestRegressor` optimizado mostró un rendimiento significativamente mejor que el modelo *baseline*. Las métricas de evaluación (RMSE, MAE, R2) indican que el modelo es capaz de explicar una porción considerable de la varianza en los atrasos de los vuelos.

Las características más importantes identificadas para la predicción de atrasos fueron la aerolínea (particularmente `airline_BZ`), si es día festivo (`is_holiday`), y los tipos de aeronave (`aircraft_type_Airbus A320`, `aircraft_type_Airbus A330`, `aircraft_type_Embraer E175`). Esto sugiere que la aerolínea y las características del vuelo son los principales impulsores de los atrasos.

El modelo final se serializó (`champion.pkl`) para su futura implementación o uso en la predicción de nuevos datos.
