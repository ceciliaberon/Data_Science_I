# Data_Science_I
Actividad para el curso de Data Science I - CoderHouse

### Proyecto de Ciencia de Datos: Análisis de Licitaciones Públicas en Chile

### Objetivo del Proyecto

El objetivo principal de este trabajo es analizar los datos de licitaciones del sistema de compras públicas de Chile con el fin de detectar:

* Cuellos de botella o procesos lentos.
* Inconsistencias en la carga de datos.
* Comportamientos estacionales.
* Posibles errores en la gestión de proveedores.

### Dataset Utilizado

El dataset contiene información sobre:

* Fechas clave: publicación, envío y cierre de cotizaciones.
* Monto total de cotización.
* Estado de orden de compra (`EstadoOC`).
* Categoría de actividad del proveedor (`CategoriaActividad`).

### Limpieza y Clasificación Inicial

Se creó la columna `estado_fecha`, que clasifica los registros en base a la disponibilidad de fechas:

* `dato correcto`: tiene las tres fechas.
* `observar1`, `observar2`, `observar3`: combinaciones incompletas.
* `obsoleto`: sin ninguna fecha.

Luego, se creó `estado_binario` para un modelo binario:

* `0` = dato correcto.
* `1` = requiere observación.

### Análisis de Duración

Se calculó la variable `duracion_cotizacion` como la diferencia en días entre la publicación y el cierre de la cotización.

Esta variable fue utilizada para crear una clasificación multiclase (`grupo_duracion`):

* `rapido`, `medio`, `lento` (mediante `qcut` en terciles).

### Modelado Supervisado: Random Forest

Se entrenaron dos modelos de clasificación:

#### 1. Clasificación Binaria (`estado_binario`)

* Objetivo: detectar si un registro es "correcto" o debe ser observado.
* Validación cruzada con `StratifiedKFold`.
* Imputación de valores faltantes con `SimpleImputer`.

**Resultados:**

* Alta precisión (ver matriz de confusión).
* Predicciones guardadas en `df2['pred_estado_binario']`.

#### 2. Clasificación Multiclase de Duración

* Objetivo: predecir si un proceso será `rapido`, `medio` o `lento`.
* Variables predictoras: meses (`mes_envio`, `mes_cotin`, `mes_cotfin`) y `MontoTotalCotizacion`.

**Importancia de Variables:**

* `mes_envio` y `mes_cotfin` resultaron los más influyentes.

### Análisis de Errores del Modelo

Se exploraron los errores del modelo binario:

* Por categoría (`CategoriaActividad`).
* Por mes (`mes_cotin`).

Esto permitió detectar áreas donde el modelo se confunde más y podría necesitar mejora.

### Conclusiones Finales

* El modelo binario es efectivo para detectar registros con problemas de carga.
* Las variables temporales (meses) tienen un impacto significativo en la duración del proceso.
* El enfoque puede escalarse para monitorear procesos en tiempo real y generar alertas automáticas.

### Recomendaciones

* Capacitar equipos de carga en los meses con más errores.
* Monitorear las categorías con mayor tasa de observaciones.
* Integrar el modelo en el sistema de adquisiciones como herramienta de validación previa.
