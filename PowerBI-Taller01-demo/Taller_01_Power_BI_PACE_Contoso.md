# Taller resuelto. Mi primer reporte comercial con Contoso en Power BI
**Metodología PACE · Análisis descriptivo**

<a id="indice"></a>
## Índice de fases y pasos

| Fase | Pregunta orientadora | Producto de la fase |
| --- | --- | --- |
| [1. P: Planear](#fase-1) | ¿Qué necesita conocer el negocio? | Problema, preguntas, alcance y criterios de éxito. |
| [2. A: Analizar](#fase-2) | ¿Qué contienen los datos y qué calidad tienen? | Tabla preparada y controles resueltos. |
| [3. C: Construir](#fase-3) | ¿Cómo representar y comprobar las respuestas? | Medidas, gráficos, filtros y verificación manual. |
| [4. E: Ejecutar y comunicar](#fase-4) | ¿Qué hallazgo se comunica y qué sigue? | Respuestas, storytelling y seguimiento. |

**Fase 1. Planear**

- [1.1. Problema de negocio y decisión apoyada](#paso-1-1)
- [1.2. Preguntas de negocio e información necesaria](#paso-1-2)
- [1.3. Alcance del análisis](#paso-1-3)
- [1.4. Ficha de planeación y criterios de éxito](#paso-1-4)

**Fase 2. Analizar**

- [2.1. Entorno de trabajo y organización de archivos](#paso-2-1)
- [2.2. Descarga y extracción de Contoso V2](#paso-2-2)
- [2.3. Granularidad: líneas, unidades y pedidos](#paso-2-3)
- [2.4. Archivos y campos que responden las preguntas](#paso-2-4)
- [2.5. Importación de los CSV en Power Query](#paso-2-5)
- [2.6. Tipos de datos, fechas, precios y textos](#paso-2-6)
- [2.7. Combinación de ventas con productos](#paso-2-7)
- [2.8. Nombres finales e importe por línea](#paso-2-8)
- [2.9. Año y mes de análisis](#paso-2-9)
- [2.10. Controles de calidad resueltos](#paso-2-10)
- [2.11. Tabla final del reporte](#paso-2-11)

**Fase 3. Construir**

- [3.1. Elección de gráficos y organización del reporte](#paso-3-1)
- [3.2. Medidas de ventas, unidades y pedidos](#paso-3-2)
- [3.3. Tarjetas de resumen](#paso-3-3)
- [3.4. Tabla por categoría y comparación de indicadores](#paso-3-4)
- [3.5. Gráfico de barras: categoría con más ventas](#paso-3-5)
- [3.6. Gráfico de líneas: evolución mensual](#paso-3-6)
- [3.7. Filtros del período y de categoría](#paso-3-7)
- [3.8. Interacciones y resultados por selección](#paso-3-8)
- [3.9. Comprobación manual resuelta](#paso-3-9)

**Fase 4. Ejecutar y comunicar**

- [4.1. Respuestas resueltas a las preguntas de negocio](#paso-4-1)
- [4.2. Storytelling del hallazgo](#paso-4-2)
- [4.3. Acción propuesta y seguimiento](#paso-4-3)
- [4.4. Documentación y reproducción de la solución](#paso-4-4)
- [4.5. Resultado integral del taller](#paso-4-5)

**Consulta**

- [Caso, objetivo y fuente de datos](#caso).
- [Valores de control del archivo completo](#valores-control).
- [Solución de dificultades frecuentes](#dificultades).
- [Fuentes oficiales y documentación](#fuentes).

---

<a id="caso"></a>
## Caso, objetivo y fuente de datos

**Caso:** la dirección comercial de Contoso necesita describir su actividad comercial, reconocer la categoría que más aporta a las ventas y observar los cambios mensuales.

**Objetivo del taller:** responder cuatro preguntas de negocio mediante un reporte de Power BI, comprobar un resultado con sus transacciones y comunicar un hallazgo sustentado.

**Fuente:** Contoso V2, paquete `csv-100k.7z` de la publicación **Ready to use data (2024)** de SQLBI. El conjunto representa una empresa ficticia. Todo el desarrollo corresponde a Contoso. [F2]

**Solución presentada:** reporte descriptivo de 2020, con tres indicadores, comparación por categoría, evolución mensual, filtros y una comprobación manual. Las cifras se calcularon directamente sobre los CSV originales el **8 de octubre de 2026**, conservando la precisión de los precios. La configuración en Power BI explica cómo reproducir estos resultados.

**Base monetaria:** importe de cada línea calculado como `Quantity × NetPrice`, presentado en moneda base del generador. Ventas e indicadores de rentabilidad tienen significados distintos; este importe describe el valor vendido. [F3]

**Preparación:** `sales.csv` y `product.csv` se combinan en Power Query para obtener una sola tabla final, `Ventas`, con una fila por línea de pedido.

**Metodología PACE:** *Plan, Analyze, Construct, Execute*. En este caso se organiza así: definir la necesidad de negocio; comprender y preparar los datos; construir y comprobar el reporte; comunicar el hallazgo y establecer el seguimiento. [F1]

Las rutas de menú corresponden a Power BI Desktop en español. Los nombres **Datos/Campos** y **Crear objeto visual/Compilar objeto visual** pueden variar entre versiones. [F4] [F17]

---

<a id="fase-1"></a>
## Fase 1. P: Planear

**Propósito:** definir la necesidad de información y las condiciones de una respuesta útil.

**Producto:** planeación resuelta del caso. La exploración de archivos, los conteos y los cálculos corresponden a las fases siguientes.

<a id="paso-1-1"></a>
### Paso 1.1. Problema de negocio y decisión apoyada

**Propósito:** relacionar el reporte con una necesidad comercial concreta.

**Desarrollo resuelto:**

| Elemento | Definición del caso |
| --- | --- |
| Situación | La dirección comercial necesita una visión organizada de las ventas de Contoso por categoría y mes. |
| Problema | Requiere conocer el volumen comercial y reconocer categorías y cambios mensuales que merezcan revisión. |
| Destinatario | Dirección comercial de Contoso. |
| Decisión apoyada | Priorizar una categoría y un cambio mensual para un análisis posterior. |
| Objetivo | Describir ventas, unidades y pedidos de 2020 y comunicar un hallazgo sustentado. |

**Explicación:** el problema define la necesidad de información; la decisión explica para qué se utilizará. El reporte permite priorizar una revisión comercial. Una decisión sobre compras o promociones necesitaría también información sobre inventario, costos y acciones comerciales.

**Resultado:** el análisis tiene destinatario, objetivo y propósito de uso definidos.

[Volver al índice](#indice)

<a id="paso-1-2"></a>
### Paso 1.2. Preguntas de negocio e información necesaria

**Propósito:** definir qué debe responder el reporte y qué información requiere cada respuesta.

**Preguntas del caso:**

| Código | Pregunta de negocio | Información necesaria | Respuesta requerida |
| --- | --- | --- | --- |
| Q1 | ¿Cuál fue el volumen comercial de Contoso en 2020: ventas, unidades y pedidos? | Fecha, identificador de pedido, cantidad y precio neto. | Tres totales del período. |
| Q2 | ¿Qué categoría registró el mayor importe de ventas? | Categoría de cada producto y valor vendido. | Nombre de la categoría e importe. |
| Q3 | ¿La categoría con más unidades coincide con la de mayor importe? | Categoría, cantidades e importes vendidos. | Comparación de las clasificaciones. |
| Q4 | ¿Cómo cambiaron las ventas mes a mes durante 2020? | Fecha y valor vendido, agrupables por mes y año. | Evolución mensual y variación destacada. |

**Explicación:** cada pregunta determina la información necesaria. En Analizar se identifican los archivos y campos disponibles; en Construir se calculan los indicadores y se escogen los gráficos.

**Resultado:** quedan establecidas Q1–Q4, que se responden en el [paso 4.1](#paso-4-1).

[Volver al índice](#indice)

<a id="paso-1-3"></a>
### Paso 1.3. Alcance del análisis

**Propósito:** establecer el período, la cobertura y los límites del análisis.

**Alcance definido:**

| Elemento | Definición |
| --- | --- |
| Empresa | Contoso, caso ficticio del conjunto de datos. |
| Fuente | Contoso V2, publicación 2024, paquete CSV 100K. |
| Período propuesto | Del 1 de enero al 31 de diciembre de 2020. Su cobertura se revisa en Analizar. |
| Categorías | Todas las categorías, con posibilidad de filtrar una. |
| Nivel analítico | Descriptivo: ventas, unidades, pedidos y variación mensual. |
| Decisión apoyada | Seleccionar aspectos que requieren una revisión comercial posterior. |

**Explicación:** declarar el alcance permite interpretar las cifras en su contexto. Un resultado de una categoría o de un mes representa esa selección específica. Una explicación causal requiere evidencia adicional.

**Resultado:** fuente, período, cobertura comercial y nivel del análisis están delimitados.

[Volver al índice](#indice)

<a id="paso-1-4"></a>
### Paso 1.4. Ficha de planeación y criterios de éxito

**Propósito:** establecer qué características debe tener la solución.

**Ficha de planeación resuelta:**

| Elemento | Solución |
| --- | --- |
| Problema | Describir el volumen comercial y reconocer categorías y cambios mensuales que merezcan revisión. |
| Destinatario | Dirección comercial de Contoso. |
| Objetivo | Responder Q1–Q4 para 2020 con un reporte descriptivo y un hallazgo sustentado. |
| Información necesaria | Fecha, pedido, producto, categoría, cantidad y precio neto. |
| Alcance | Contoso V2, 2020, todas las categorías y filtros de exploración. |
| Decisión apoyada | Priorizar una revisión comercial posterior. |
| Diseño del reporte | Páginas `Resumen comercial`, `Comprobacion` y `PACE y hallazgos`. |

**Criterios de éxito:**

| Criterio | Evidencia definida |
| --- | --- |
| Las preguntas tienen respuestas. | Totales, comparación por categoría y serie mensual. |
| Los datos están preparados correctamente. | Tipos, claves, combinación y cobertura revisados. |
| Los indicadores se pueden comprobar. | Cálculo manual de dos pedidos y contraste con sus indicadores. |
| El alcance es visible. | Fecha y categoría seleccionadas en el reporte. |
| El hallazgo está sustentado. | Cifra, período, contexto, interpretación y acción propuesta. |
| La solución se puede reproducir. | Fuente, transformaciones, medidas y filtros documentados. |

**Explicación:** estos criterios definen la calidad de la solución. Las siguientes fases producen las evidencias correspondientes.

**Resultado:** la planeación está completa y permite iniciar la revisión de los datos.

[Volver al índice](#indice)

---

<a id="fase-2"></a>
## Fase 2. A: Analizar

**Propósito:** comprender la estructura de Contoso, revisar su calidad y preparar la información del reporte.

**Producto:** una tabla `Ventas` de 11 columnas, con una fila por línea de pedido, y controles de calidad resueltos.

<a id="paso-2-1"></a>
### Paso 2.1. Entorno de trabajo y organización de archivos

**Propósito:** organizar las fuentes y el proyecto para conservar una preparación reproducible.

**Entorno de referencia:**

1. **Aplicación:** Power BI Desktop en Windows, disponible en la [fuente oficial](https://learn.microsoft.com/es-es/power-bi/fundamentals/desktop-get-the-desktop). [F5]
2. **Carpeta de trabajo:** `Taller_01_PowerBI`.
3. **Fuentes:** subcarpeta `Contoso`, con los CSV originales.
4. **Reporte:** subcarpeta `Reportes`, destinada al archivo `Taller_01_Contoso_PACE.pbix`.
5. **Transformaciones:** conservadas en Power Query, manteniendo los archivos originales.

**Explicación:** Power Query guarda la secuencia de preparación. Conservar las fuentes en una ubicación estable permite repetirla al actualizar.

**Resultado:** datos de origen y reporte tienen ubicaciones diferenciadas y una ruta de trabajo definida.

[Volver al índice](#indice)

<a id="paso-2-2"></a>
### Paso 2.2. Descarga y extracción de Contoso V2

**Propósito:** utilizar el mismo paquete del que se obtuvieron las cifras de la solución.

**Descarga y extracción:**

1. **Publicación:** [Ready to use data (2024), Contoso V2](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/tag/ready-to-use-data-2024).
2. **Archivo en Assets:** `csv-100k.7z`, disponible también mediante el [enlace directo](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/download/ready-to-use-data-2024/csv-100k.7z).
3. **Ubicación:** carpeta `Contoso`.
4. **Extracción:** herramienta compatible con `.7z`, como [7-Zip](https://www.7-zip.org/).
5. **Archivos utilizados:** `sales.csv` y `product.csv`.

**Explicación:** el paquete utiliza compresión `.7z`. La denominación 100K corresponde al tamaño aproximado de pedidos. La tabla de ventas contiene **199.873 líneas**, porque un pedido puede incluir varios productos. [F2]

**Resultado:** las dos fuentes de la solución están identificadas dentro del paquete original.

[Volver al índice](#indice)

<a id="paso-2-3"></a>
### Paso 2.3. Granularidad: líneas, unidades y pedidos

**Propósito:** reconocer qué representa cada fila antes de sumar o contar.

| Concepto | Significado | Campo |
| --- | --- | --- |
| Línea | Un producto incluido en un pedido. | `OrderKey` y `LineNumber`. |
| Unidades | Cantidad de artículos en la línea. | `Quantity`. |
| Pedido | Compra que puede tener varias líneas. | `OrderKey`. |
| Precio neto | Precio unitario después del descuento generado. | `NetPrice`. |

**Ejemplo real resuelto: pedidos 1000 y 1001, del 1 de enero de 2015:**

| Pedido | Línea | ProductKey | Unidades | Precio neto | Importe de la línea |
| --- | --- | --- | ---: | ---: | ---: |
| 1000 | 0 | 48 | 1 | 98,967 | 98,967 |
| 1000 | 1 | 460 | 1 | 659,780 | 659,780 |
| 1001 | 0 | 1730 | 2 | 54,376 | 108,752 |

**Cálculos resueltos:**

- Líneas: **3**.
- Unidades: `1 + 1 + 2 = 4`.
- Pedidos distintos: **2**, identificados como 1000 y 1001.
- Ventas: `98,967 + 659,780 + (2 × 54,376) = 867,499`, que se presenta como **867,50**.

**Explicación:** el pedido 1000 aparece en dos filas porque contiene dos líneas. Para contar compras se cuentan identificadores distintos. Este ejemplo de 2015 se utiliza para verificar el cálculo; las preguntas comerciales se responden para 2020. [F9]

**Resultado:** quedan diferenciados filas, unidades y pedidos. La guía usa coma decimal; el CSV original utiliza punto decimal.

[Volver al índice](#indice)

<a id="paso-2-4"></a>
### Paso 2.4. Archivos y campos que responden las preguntas

**Propósito:** relacionar las necesidades de información con los campos disponibles.

**Correspondencia resuelta:**

| Pregunta | Campos de `sales.csv` | Campos de `product.csv` | Tratamiento |
| --- | --- | --- | --- |
| Q1. Volumen comercial. | `OrderDate`, `OrderKey`, `Quantity`, `NetPrice`. | — | Filtrar fecha, sumar importes y cantidades, contar pedidos distintos. |
| Q2. Mayor importe por categoría. | `OrderDate`, `ProductKey`, `Quantity`, `NetPrice`. | `ProductKey`, `CategoryName`. | Añadir categoría y comparar las ventas de 2020. |
| Q3. Unidades frente a ventas. | `OrderDate`, `ProductKey`, `Quantity`, `NetPrice`. | `ProductKey`, `CategoryName`. | Comparar cantidades e importes por categoría. |
| Q4. Variación mensual. | `OrderDate`, `Quantity`, `NetPrice`. | — | Agrupar las ventas por mes y año. |

`ProductName` identifica el producto en la comprobación. `ProductKey` es la clave común entre ventas y productos.

**Explicación:** `sales.csv` ya contiene las líneas de venta. Los archivos `orders.csv` y `orderrows.csv` representan pedidos y líneas de la misma fuente, por lo que incorporarlos como ventas adicionales duplicaría la actividad. [F2]

**Resultado:** todas las preguntas tienen información disponible y una preparación definida.

[Volver al índice](#indice)

<a id="paso-2-5"></a>
### Paso 2.5. Importación de los CSV en Power Query

**Propósito:** disponer de las fuentes dentro de Power Query.

**Secuencia de importación en Power BI:**

1. **Inicio → Obtener datos → Texto/CSV:** archivo `sales.csv`.
2. **Delimitador:** coma; cada campo ocupa su propia columna.
3. **Detección de tipos:** “No detectar tipos de datos”, cuando la opción esté disponible; después, **Transformar datos**.
4. **Nombre de consulta:** `OrigenVentas`.
5. **Inicio → Nuevo origen → Texto/CSV:** archivo `product.csv`, con el mismo delimitador.
6. **Nombre de consulta:** `OrigenProductos`.
7. **Encabezados:** los nombres originales de los campos. Si la vista muestra `Column1`, `Column2`, corresponde aplicar **Usar la primera fila como encabezado**.

**Explicación:** Transformar datos permite preparar las fuentes antes de cargarlas al reporte. Una vista de una sola columna indica un problema de delimitador. [F6]

**Resultado:** dos consultas de origen con sus campos separados y encabezados identificados.

[Volver al índice](#indice)

<a id="paso-2-6"></a>
### Paso 2.6. Tipos de datos, fechas, precios y textos

**Propósito:** interpretar correctamente identificadores, fechas, números y textos.

**Tipos definidos para la solución:**

| Consulta | Campos | Tipo |
| --- | --- | --- |
| OrigenVentas | `OrderKey` | Texto. |
| OrigenVentas | `LineNumber`, `ProductKey`, `Quantity` | Número entero. |
| OrigenVentas | `OrderDate` | Fecha. |
| OrigenVentas | `NetPrice` | Número decimal. |
| OrigenProductos | `ProductKey` | Número entero. |
| OrigenProductos | `ProductName`, `CategoryName` | Texto. |

**Transformaciones:**

1. `NetPrice`: **Cambiar tipo → Usar configuración regional → Número decimal → Inglés (Estados Unidos)**, porque el CSV utiliza punto decimal. [F7]
2. `OrderDate`: conversión a fecha desde el formato año-mes-día; `2015-01-01` corresponde al **01/01/2015**.
3. `ProductName` y `CategoryName`: **Transformar → Formato → Recortar**.

**Explicación:** el valor `98.967` del archivo equivale a **98,967** en notación española. Conservar esa precisión permite calcular correctamente el importe. Recortar elimina el espacio final del nombre original `Cameras and camcorders `, que queda como `Cameras and camcorders`.

**Resultado:** campos con los tipos de referencia y categorías sin espacios al inicio o al final. Un paso automático de conversión con errores debe corregirse desde el texto original.

[Volver al índice](#indice)

<a id="paso-2-7"></a>
### Paso 2.7. Combinación de ventas con productos

**Propósito:** incorporar los atributos del producto conservando una fila por línea de venta.

**Control previo resuelto:** `product.csv` contiene **2.517 productos y 2.517 claves distintas**. El control agrupado por `ProductKey`, con recuento de filas mayor que 1, devuelve **0 claves repetidas**.

**Configuración de la combinación:**

1. **Consulta principal:** `OrigenVentas`.
2. **Ruta:** Inicio → Combinar consultas → Combinar consultas como nuevas.
3. **Segunda consulta:** `OrigenProductos`.
4. **Campo común:** `ProductKey`, de tipo entero en ambas.
5. **Tipo de unión:** externa izquierda, todas las filas de ventas y sus coincidencias de producto. [F8]
6. **Coincidencia:** exacta, sin coincidencia aproximada.
7. **Consulta resultante:** `Ventas`.
8. **Expansión:** `ProductName` y `CategoryName`, con el prefijo del nombre de columna desactivado.

**Explicación:** cada clave de producto tiene una sola coincidencia. La combinación añade atributos y conserva las líneas. Varias coincidencias para una clave multiplicarían los registros al expandir.

**Resultado resuelto:** **199.873 líneas antes y después de la combinación**, y **0 ventas sin coincidencia de producto**.

[Volver al índice](#indice)

<a id="paso-2-8"></a>
### Paso 2.8. Nombres finales e importe por línea

**Propósito:** disponer de campos claros y del importe correspondiente a cada línea.

**Nombres finales de las columnas:**

| Campo original | Nombre final |
| --- | --- |
| `OrderDate` | `Fecha` |
| `OrderKey` | `Pedido` |
| `LineNumber` | `Linea` |
| `ProductKey` | `ProductKey` |
| `ProductName` | `Producto` |
| `CategoryName` | `Categoria` |
| `Quantity` | `Unidades` |
| `NetPrice` | `PrecioUnitario` |

**Columna de importe:** Agregar columna → Columna personalizada; nombre `ImporteVenta`, tipo número decimal. [F14]

```powerquery
[Unidades] * [PrecioUnitario]
```

**Ejemplo resuelto:** para el pedido 1001, `2 × 54,376 = 108,752`.

**Explicación:** `NetPrice` contiene el precio neto de la línea, después del descuento generado. La definición conserva la moneda base del generador y utiliza ese precio, con su precisión original. Los precios y la incorporación separada de los campos de cambio se contrastaron con el código del generador. [F3]

**Resultado:** una columna `ImporteVenta` lista para agregarse por período y categoría.

[Volver al índice](#indice)

<a id="paso-2-9"></a>
### Paso 2.9. Año y mes de análisis

**Propósito:** comparar meses conservando el año al que pertenecen.

**Columnas personalizadas en Power Query:**

| Columna nueva | Fórmula | Tipo |
| --- | --- | --- |
| `Anio` | `Date.Year([Fecha])` | Número entero. |
| `InicioMes` | `Date.StartOfMonth([Fecha])` | Fecha. |

**Ejemplo de transformación resuelto:** `15/11/2020` produce `Anio = 2020` e `InicioMes = 01/11/2020`. [F23]

**Explicación:** un campo con el nombre “noviembre” juntaría ese mes de distintos años. `InicioMes` mantiene mes y año y permite ordenar la evolución cronológicamente.

**Resultado:** el año facilita revisar la cobertura y `InicioMes` proporciona el eje temporal del gráfico de líneas.

[Volver al índice](#indice)

<a id="paso-2-10"></a>
### Paso 2.10. Controles de calidad resueltos

**Propósito:** comprobar que la preparación conserva el nivel de detalle y datos válidos para el análisis.

**Procedimientos de control:**

1. **Perfilado:** Ver → Calidad, Distribución y Perfil de columna, con alcance **conjunto de datos completo**. [F15]
2. **Número de líneas:** referencias de `OrigenVentas` y `Ventas`, con Transformar → Contar filas.
3. **Duplicados de productos:** agrupación por `ProductKey`, recuento de filas y filtro mayor que 1.
4. **Duplicados de líneas:** referencia de `Ventas`, Agrupar por → Avanzado, campos `Pedido` y `Linea`, recuento de filas y filtro mayor que 1. [F16]
5. **Valores esenciales:** revisión de vacíos, coincidencias de producto, cantidades y precios.
6. **Cobertura:** revisión del rango de fechas y de los meses con registros de 2020.

**Controles resueltos sobre la fuente:**

| Control | Resultado |
| --- | --- |
| Líneas antes de combinar. | 199.873. |
| Líneas después de expandir productos. | 199.873. |
| Productos / claves distintas. | 2.517 / 2.517. |
| Claves de producto repetidas. | 0. |
| Combinaciones repetidas de Pedido + Linea. | 0. |
| Vacíos en fecha, pedido, línea, clave de producto, cantidad o precio neto. | 0. |
| Ventas sin producto coincidente; nombres o categorías vacíos. | 0. |
| Cantidades cero o negativas. | 0. |
| Precios netos cero o negativos. | 0. |
| Rango de fechas del archivo. | 01/01/2015 a 20/04/2024. |
| Meses de 2020 con registros. | 12. |
| Líneas de 2020. | 11.267. |

**Explicación:** repetir un pedido en distintas filas puede ser válido; repetir la combinación de pedido y línea requiere revisión. Estos resultados confirman que la combinación conserva los datos y que el período propuesto está disponible.

**Resultado:** la fuente supera los controles definidos para esta solución.

[Volver al índice](#indice)

<a id="paso-2-11"></a>
### Paso 2.11. Tabla final del reporte

**Propósito:** cargar una estructura sencilla para construir el reporte.

**Configuración final de Power Query:**

1. **Consulta con carga habilitada:** `Ventas`.
2. **Columnas:** `Fecha`, `Pedido`, `Linea`, `ProductKey`, `Producto`, `Categoria`, `Unidades`, `PrecioUnitario`, `ImporteVenta`, `Anio` e `InicioMes`.
3. **Consultas sin carga:** `OrigenVentas`, `OrigenProductos` y consultas de control. Permanecen disponibles como parte de la preparación.
4. **Aplicación al modelo:** Inicio → Cerrar y aplicar.
5. **Nombre del proyecto de referencia:** `Taller_01_Contoso_PACE.pbix`, en `Reportes`.

**Explicación:** las consultas auxiliares preparan la información; la tabla final concentra los datos utilizados por los visuales.

**Resultado:** modelo de una sola tabla, con **11 columnas y 199.873 líneas de venta**, listo para responder las preguntas.

[Volver al índice](#indice)

---

<a id="fase-3"></a>
## Fase 3. C: Construir

**Propósito:** convertir los datos preparados en indicadores y visuales que respondan las preguntas de negocio.

**Producto:** reporte con tres páginas, tres medidas, visuales adecuados y comprobación manual.

Las cifras comerciales de esta fase corresponden a **2020 y todas las categorías**, excepto la prueba de interacción de Computers y la muestra de comprobación identificadas en sus respectivos pasos.

<a id="paso-3-1"></a>
### Paso 3.1. Elección de gráficos y organización del reporte

**Propósito:** elegir la representación que facilite responder cada pregunta.

**Elección resuelta:**

| Pregunta | Datos o medidas | Visual elegido | Justificación |
| --- | --- | --- | --- |
| Q1. Volumen comercial. | Ventas, unidades y pedidos distintos del período. | Tres tarjetas. | Facilitan la lectura de los totales. |
| Q2. Mayor importe por categoría. | Categoria y ventas. | Barras horizontales ordenadas. | Comparan magnitudes y permiten leer nombres largos. |
| Q3. Liderazgo en unidades y ventas. | Categoria, ventas, unidades y pedidos. | Tabla por categoría. | Permite comparar cifras exactas y cambiar la clasificación. |
| Q4. Cambios mensuales. | InicioMes y ventas. | Línea mensual. | Conserva la secuencia temporal y hace visibles las variaciones. |
| Comprobación del cálculo. | Pedido, Linea, Unidades, PrecioUnitario e ImporteVenta. | Tabla de detalle y tarjetas. | Permite reconstruir una selección de transacciones. |

**Organización del reporte:**

| Página | Contenido |
| --- | --- |
| `Resumen comercial` | Filtros y tarjetas en la zona superior; barras y línea en la zona central; tabla por categoría en la zona inferior. |
| `Comprobacion` | Detalle de los pedidos seleccionados, indicadores y cálculo manual. |
| `PACE y hallazgos` | Planeación, respuestas, storytelling y documentación del caso. |

Las páginas se organizan desde la vista **Informe**, mediante sus pestañas y el botón **+**. [F17]

**Explicación:** la pregunta determina el tipo de comparación. La tabla mantiene ventas y unidades en columnas separadas, porque son magnitudes diferentes. Las barras comparan categorías y la línea muestra cambios en el tiempo. [F11] [F12] [F13]

**Resultado:** cada pregunta tiene un visual justificado y una ubicación definida.

[Volver al índice](#indice)

<a id="paso-3-2"></a>
### Paso 3.2. Medidas de ventas, unidades y pedidos

**Propósito:** definir indicadores que respondan al contexto de los filtros.

**Ubicación:** tabla `Ventas`, opción **Modelado → Nueva medida**. Cada definición corresponde a una medida independiente. [F18]

```dax
Ventas Totales = SUM('Ventas'[ImporteVenta])
```

```dax
Unidades Totales = SUM('Ventas'[Unidades])
```

```dax
Pedidos = DISTINCTCOUNT('Ventas'[Pedido])
```

**Formato:** ventas con dos decimales; unidades y pedidos como enteros con separador de miles.

**Explicación:** `SUM` agrega cantidades e importes; `DISTINCTCOUNT` cuenta identificadores diferentes. Así se evita confundir líneas de venta con pedidos. [F9] [F19]

**Resultados resueltos:**

| Contexto | Ventas, moneda base | Unidades | Pedidos distintos |
| --- | ---: | ---: | ---: |
| Archivo completo, sin filtros. | 203.723.865,76 | 628.370 | 83.130 |
| 2020, todas las categorías. | **11.066.206,89** | **35.234** | **4.806** |

**Resultado:** tres medidas explícitas para todos los visuales del reporte.

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### Paso 3.3. Tarjetas de resumen

**Propósito:** responder Q1 con los totales del período.

**Configuración en `Resumen comercial`:**

| Visual | Campo en Valores | Título | Formato |
| --- | --- | --- | --- |
| Tarjeta. | `Ventas Totales`. | Ventas de Contoso (moneda base). | Dos decimales; unidades de visualización: Ninguno. |
| Tarjeta. | `Unidades Totales`. | Unidades. | Sin decimales; unidades de visualización: Ninguno. |
| Tarjeta. | `Pedidos`. | Pedidos distintos. | Sin decimales; unidades de visualización: Ninguno. |

Las tres tarjetas se presentan alineadas en la zona superior. [F10]

**Solución de Q1, con el filtro 2020:**

| Indicador | Resultado |
| --- | ---: |
| Ventas, moneda base | **11.066.206,89** |
| Unidades | **35.234** |
| Pedidos distintos | **4.806** |

**Explicación:** las tarjetas responden cuánto se vendió, cuántos artículos se vendieron y cuántas compras hubo. Las **11.267 líneas de 2020** representan el detalle de esas compras.

**Resultado:** los tres indicadores comerciales del período quedan identificados y legibles.

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### Paso 3.4. Tabla por categoría y comparación de indicadores

**Propósito:** comparar indicadores por categoría y responder Q3.

**Configuración del visual:** Tabla con `Categoria`, `Ventas Totales`, `Unidades Totales` y `Pedidos`; totales activados y clasificación descendente por ventas. El orden por unidades permite contrastar las posiciones. [F11]

**Tabla resuelta para 2020:**

| Categoría | Ventas, moneda base | Unidades | Pedidos distintos de la categoría |
| --- | ---: | ---: | ---: |
| Computers | 5.041.111,59 | 8.364 | 2.094 |
| Cell phones | 1.861.564,14 | 6.945 | 1.822 |
| Cameras and camcorders | 1.283.114,43 | 3.114 | 848 |
| TV and Video | 978.424,93 | 1.918 | 554 |
| Home Appliances | 728.028,31 | 1.583 | 518 |
| Music, Movies and Audio Books | 672.661,83 | 5.147 | 1.393 |
| Audio | 362.807,58 | 3.187 | 914 |
| Games and Toys | 138.494,09 | 4.976 | 1.370 |
| **Total general** | **11.066.206,89** | **35.234** | **4.806** |

**Respuesta de Q3:** **sí**, Computers lidera tanto en importe vendido como en unidades: **5.041.111,59** y **8.364 unidades**.

Music, Movies and Audio Books ocupa el **tercer lugar en unidades**, con **5.147**, y el **sexto en ventas**, con **672.661,83**. La clasificación de todas las categorías cambia según el indicador elegido.

**Explicación:** más unidades no siempre implican mayor importe, porque los precios y la composición de productos pueden diferir. Además, un pedido puede incluir productos de distintas categorías; el total general de **4.806 pedidos** es un conteo distinto, no la suma de los conteos de cada categoría.

**Precisión monetaria:** los valores originales se suman antes de redondear. La suma de las ocho cifras visibles, ya redondeadas, es 11.066.206,90; el total calculado con la precisión original es **11.066.206,89**.

**Resultado:** el liderazgo y las diferencias de clasificación tienen cifras exactas de respaldo.

[Volver al índice](#indice)

<a id="paso-3-5"></a>
### Paso 3.5. Gráfico de barras: categoría con más ventas

**Propósito:** identificar visualmente la categoría con mayor importe de ventas.

**Configuración del gráfico:**

| Elemento | Configuración |
| --- | --- |
| Tipo | Barras agrupadas, orientación horizontal. |
| Eje Y | `Categoria`. |
| Eje X | `Ventas Totales`. |
| Orden | Ventas de mayor a menor. |
| Etiquetas | Etiquetas de datos activadas. |
| Eje monetario | Inicio en cero. |
| Título | ¿Qué categoría tiene el mayor importe de ventas? |

**Respuesta de Q2:** **Computers**, con **5.041.111,59**. La segunda categoría es **Cell phones**, con **1.861.564,14**.

**Explicación:** las barras permiten comparar longitudes y reconocer el mayor valor. Su orientación facilita leer los nombres completos de las categorías. El orden coincide con la tabla del paso anterior. [F12]

**Resultado:** la barra de Computers identifica el liderazgo en ventas de 2020.

[Volver al índice](#indice)

<a id="paso-3-6"></a>
### Paso 3.6. Gráfico de líneas: evolución mensual

**Propósito:** describir la evolución mensual y responder Q4.

**Configuración del gráfico:** Línea con `InicioMes` directamente en el eje X, sin jerarquía de fecha, y `Ventas Totales` en el eje Y. Orden cronológico ascendente, etiquetas de mes y año, y título **¿Cómo cambian las ventas mensuales de Contoso?**. [F13]

**Serie mensual resuelta:**

| Mes | Ventas, moneda base |
| --- | ---: |
| Enero de 2020 | 2.124.215,68 |
| Febrero de 2020 | 2.658.349,04 |
| Marzo de 2020 | 1.104.361,28 |
| Abril de 2020 | 502.924,03 |
| Mayo de 2020 | 1.160.430,61 |
| Junio de 2020 | 808.351,16 |
| Julio de 2020 | 593.685,68 |
| Agosto de 2020 | 524.873,87 |
| Septiembre de 2020 | 320.093,34 |
| Octubre de 2020 | 388.181,27 |
| Noviembre de 2020 | 341.405,95 |
| Diciembre de 2020 | 539.334,97 |
| **Total 2020** | **11.066.206,89** |

**Respuesta de Q4:** febrero registra el máximo, **2.658.349,04**; septiembre registra el mínimo, **320.093,34**. Entre febrero y marzo las ventas disminuyen **58,46 %**, hasta **1.104.361,28**.

**Cálculo resuelto de la variación:**

```text
Variación = ((Ventas de marzo − Ventas de febrero) ÷ Ventas de febrero) × 100
          = ((1.104.361,281484 − 2.658.349,037698) ÷ 2.658.349,037698) × 100
          = −58,46 %
```

La operación utiliza los importes originales y puede reproducirse con una calculadora a partir de los resultados del reporte.

**Explicación:** la línea mantiene el orden del tiempo y hace visible la caída. El descenso es un resultado observado; su causa requiere evidencia adicional.

**Precisión monetaria:** sumar los doce valores mensuales ya redondeados da 11.066.206,88. El total de **11.066.206,89** se obtiene sumando con la precisión original y redondeando al final.

**Resultado:** doce puntos mensuales en orden, con un máximo, un mínimo y una variación cuantificada.

[Volver al índice](#indice)

<a id="paso-3-7"></a>
### Paso 3.7. Filtros del período y de categoría

**Propósito:** mantener visible y consistente el alcance del análisis.

**Configuración de segmentaciones en `Resumen comercial`:**

| Segmentación | Campo | Configuración | Selección de la solución |
| --- | --- | --- | --- |
| Período. | `Fecha`, directamente. | Modo Entre. | 01/01/2020–31/12/2020. |
| Categoría. | `Categoria`. | Lista o desplegable. | Todas las categorías incluidas. |

**Alcance de los filtros:** aplicados a la página `Resumen comercial`, sin restricciones adicionales de visual o informe. La segmentación de fecha no está sincronizada con `Comprobacion`, cuya muestra corresponde a 2015. [F21]

**Resultado resuelto de la selección:** **11.066.206,89** en ventas, **35.234 unidades**, **4.806 pedidos**, ocho categorías y doce meses.

**Explicación:** los filtros indican qué datos se están leyendo. El historial se conserva en la tabla y la selección de 2020 se realiza en el reporte.

**Resultado:** los visuales comerciales comparten período y cobertura de categorías.

[Volver al índice](#indice)

<a id="paso-3-8"></a>
### Paso 3.8. Interacciones y resultados por selección

**Propósito:** comprobar que una selección afecta los indicadores y visuales correspondientes.

**Prueba resuelta:** período 2020; selección de Computers en la segmentación de categoría y posterior recuperación de todas las categorías.

| Selección | Ventas, moneda base | Unidades | Pedidos distintos |
| --- | ---: | ---: | ---: |
| 2020, Computers. | **5.041.111,59** | **8.364** | **2.094** |
| 2020, todas las categorías. | **11.066.206,89** | **35.234** | **4.806** |

**Configuración de interacción:** Formato → Editar interacciones, opción **Filtrar** desde las segmentaciones hacia las tarjetas, la tabla, las barras y la línea. [F22]

**Explicación:** al seleccionar Computers se cuentan los pedidos con líneas de esa categoría. Todas las categorías recupera el contexto general del año. Un filtro propio del visual o una interacción desactivada produciría una lectura diferente.

**Resultado:** la selección de Computers tiene un resultado definido y las cifras generales se recuperan al incluir todas las categorías.

[Volver al índice](#indice)

<a id="paso-3-9"></a>
### Paso 3.9. Comprobación manual resuelta

**Propósito:** reconstruir un indicador a partir de sus transacciones.

**Configuración de `Comprobacion`:** tabla con `Pedido`, `Linea`, `Fecha`, `Categoria`, `Unidades`, `PrecioUnitario` e `ImporteVenta`, con opción **No resumir** donde esté disponible. El detalle conserva pedido y línea; precio e importe se muestran con al menos tres decimales. La página incluye tarjetas con las tres medidas y una segmentación por `Pedido` con selección múltiple.

**Selección:** pedidos **1000 y 1001** completos, del **01/01/2015**, sin restricciones adicionales de categoría ni el filtro comercial de 2020.

**Detalle resuelto:**

| Pedido | Línea | Categoría | Unidades | Precio unitario | Importe |
| --- | --- | --- | ---: | ---: | ---: |
| 1000 | 0 | Audio | 1 | 98,967 | 98,967 |
| 1000 | 1 | Computers | 1 | 659,780 | 659,780 |
| 1001 | 0 | Games and Toys | 2 | 54,376 | 108,752 |

**Cálculo manual resuelto:**

```text
Ventas = (1 × 98,967) + (1 × 659,780) + (2 × 54,376)
       = 867,499 → 867,50 con dos decimales
Unidades = 1 + 1 + 2 = 4
Pedidos distintos = {1000, 1001} = 2
```

**Comparación de resultados:**

| Indicador | Cálculo manual | Valor de referencia para las tarjetas | Diferencia |
| --- | ---: | ---: | ---: |
| Ventas, dos decimales. | 867,50 | 867,50 | 0,00 |
| Unidades. | 4 | 4 | 0 |
| Pedidos distintos. | 2 | 2 | 0 |

**Explicación:** las tres líneas permiten reconstruir el resultado. La cantidad de unidades y el conteo de pedidos responden a conceptos diferentes. El precio mantiene su precisión y el total se redondea al presentarlo.

**Resultado:** **867,50** en ventas, **4 unidades** y **2 pedidos**, comprobados directamente con las filas originales. La muestra verifica el método; el análisis comercial mantiene el alcance de 2020.

[Volver al índice](#indice)

---

<a id="fase-4"></a>
## Fase 4. E: Ejecutar y comunicar

**Propósito:** comunicar las respuestas, distinguir evidencia e interpretación y definir un seguimiento.

**Producto:** respuestas finales, storytelling resuelto y documentación de la solución.

<a id="paso-4-1"></a>
### Paso 4.1. Respuestas resueltas a las preguntas de negocio

**Propósito:** transformar los visuales en respuestas de negocio completas.

**Contexto comercial:** Contoso V2, **01/01/2020–31/12/2020**, todas las categorías, moneda base.

| Pregunta | Respuesta resuelta | Evidencia |
| --- | --- | --- |
| Q1. Volumen comercial. | Ventas de **11.066.206,89**, **35.234 unidades** y **4.806 pedidos distintos**. | Tres tarjetas. |
| Q2. Categoría con mayor importe. | **Computers**, con **5.041.111,59**, equivalente al **45,55 %** de las ventas de 2020. | Barras y tabla. |
| Q3. Coincidencia del liderazgo. | **Sí**. Computers lidera en unidades y ventas, con **8.364 unidades** y **5.041.111,59**. El resto de posiciones puede cambiar entre indicadores. | Tabla ordenada por ventas y luego por unidades. |
| Q4. Evolución mensual. | Máximo en febrero: **2.658.349,04**; mínimo en septiembre: **320.093,34**. Entre febrero y marzo hay una caída del **58,46 %**, hasta **1.104.361,28**. | Línea y valores mensuales. |

**Participación de Computers, cálculo resuelto:**

```text
Participación = (Ventas de Computers en 2020 ÷ Ventas totales de 2020) × 100
              = (5.041.111,58625 ÷ 11.066.206,893045) × 100
              = 45,55 %
```

**Explicación:** el numerador y el denominador pertenecen al mismo período. El denominador incluye todas las categorías; un total filtrado solamente en Computers no representaría el total comercial de 2020.

**Comprobación complementaria:** los pedidos 1000 y 1001, del 01/01/2015, producen **867,50**, **4 unidades** y **2 pedidos**, según el [paso 3.9](#paso-3-9).

**Resultado:** cada pregunta tiene cifra, período, alcance y visual de respaldo.

[Volver al índice](#indice)

<a id="paso-4-2"></a>
### Paso 4.2. Storytelling del hallazgo

**Propósito:** comunicar una idea central respaldada por cifras y contexto.

**Título del hallazgo:** Computers lidera las ventas de 2020; la caída de marzo merece revisión.

**Storytelling resuelto:**

> En Contoso, durante 2020 y considerando todas las categorías, las ventas fueron 11.066.206,89 en moneda base, con 35.234 unidades y 4.806 pedidos distintos. Computers lideró la actividad comercial: registró 5.041.111,59, equivalentes al 45,55 % de las ventas del año, y 8.364 unidades. La evolución mensual mostró un máximo de 2.658.349,04 en febrero y una caída del 58,46 % en marzo, hasta 1.104.361,28. Estos resultados permiten priorizar la revisión de Computers y del cambio entre febrero y marzo. La siguiente acción propuesta es revisar el detalle de productos y contrastar el cambio con las acciones comerciales y la disponibilidad de inventario. El análisis describe una empresa ficticia; los datos examinados no demuestran las causas de la caída ni permiten concluir sobre utilidad neta.

**Estructura del relato:**

| Parte | Contenido del caso |
| --- | --- |
| Contexto | Contoso, 2020, todas las categorías, moneda base. |
| Hallazgo principal | Computers lidera y aporta el 45,55 % de las ventas. |
| Evidencia complementaria | Caída del 58,46 % entre febrero y marzo. |
| Interpretación | La categoría líder y el cambio mensual requieren una revisión más detallada. |
| Acción propuesta | Revisar productos y contrastar acciones comerciales e inventario. |
| Límite | La descripción no demuestra una causa ni mide utilidad neta. |

**Explicación:** el dato corresponde a lo observado y calculado; la interpretación relaciona su importancia con el problema comercial. La acción define el siguiente análisis. Las tres partes deben poder distinguirse.

**Resultado:** un relato completo y sustentado en el reporte de 2020.

[Volver al índice](#indice)

<a id="paso-4-3"></a>
### Paso 4.3. Acción propuesta y seguimiento

**Propósito:** conectar el hallazgo con un siguiente paso concreto.

**Seguimiento resuelto como propuesta del caso:**

| Elemento | Definición |
| --- | --- |
| Prioridad | Revisar la composición de ventas de Computers y el cambio general entre febrero y marzo de 2020. |
| Próximo análisis con Contoso | Desglosar ventas y unidades por producto y mes, manteniendo las definiciones del reporte. |
| Información adicional | Registros de acciones comerciales y disponibilidad de inventario; costos si se pretende estudiar rentabilidad. |
| Responsable propuesto | Analista comercial, con revisión de la dirección comercial. |
| Momento propuesto | Próxima reunión mensual de revisión comercial del caso. |
| Evidencia de seguimiento | Comparación por producto, categoría y mes, con cobertura temporal equivalente. |
| Criterio de cierre | Identificar qué productos contribuyen al cambio y qué evidencia adicional respalda una decisión. |

**Explicación:** el análisis actual señala dónde profundizar. La propuesta de seguimiento, el responsable y la reunión forman parte del ejercicio; no son hechos registrados en los archivos de ventas.

**Resultado:** la comunicación incluye una acción definida, evidencia requerida y criterio de seguimiento.

[Volver al índice](#indice)

<a id="paso-4-4"></a>
### Paso 4.4. Documentación y reproducción de la solución

**Propósito:** documentar las condiciones necesarias para reproducir las respuestas.

**Registro de la solución:**

| Elemento | Definición utilizada |
| --- | --- |
| Fuente | SQLBI, Contoso V2, Ready to use data (2024), `csv-100k.7z`. |
| Archivos | `sales.csv` y `product.csv`. |
| Fecha de cálculo de las referencias | 08/10/2026. |
| Granularidad | Una fila por Pedido + Linea. |
| Combinación | Externa izquierda por ProductKey, con nombre y categoría de producto. |
| Importe de línea | Quantity × NetPrice, con precisión original. |
| Medidas | SUM de importe; SUM de unidades; DISTINCTCOUNT de pedido. |
| Filtro comercial | 01/01/2020–31/12/2020, todas las categorías. |
| Filtro de comprobación | Pedidos 1000 y 1001 completos, 01/01/2015. |
| Páginas del diseño | Resumen comercial; Comprobacion; PACE y hallazgos. |

**Condiciones de reproducción en Power BI:** mismo paquete, tipos y transformaciones; acceso a los CSV desde sus rutas de origen; medidas explícitas y filtros documentados. La opción **Inicio → Actualizar** vuelve a ejecutar la preparación. Una ruta distinta se corrige en **Configuración de origen de datos → Cambiar origen** o en el paso **Origen** de Power Query.

**Valores que permiten contrastar la reproducción:**

| Selección | Ventas | Unidades | Pedidos |
| --- | ---: | ---: | ---: |
| 2020, todas las categorías. | 11.066.206,89 | 35.234 | 4.806 |
| Pedidos 1000 y 1001 completos. | 867,50 | 4 | 2 |

**Explicación:** documentar la fuente, los cálculos y los filtros permite reconstruir cada respuesta y localizar la causa de una diferencia.

**Resultado:** la solución tiene definiciones y valores de control suficientes para su reproducción.

[Volver al índice](#indice)

<a id="paso-4-5"></a>
### Paso 4.5. Resultado integral del taller

**Propósito:** relacionar los resultados finales con los criterios de éxito de Planear.

**Solución integral:**

| Criterio | Evidencia del taller resuelto |
| --- | --- |
| Preguntas de negocio definidas. | Problema, destinatario, alcance y Q1–Q4 del caso. |
| Datos preparados y revisados. | Tabla de 11 columnas; 199.873 líneas; claves y combinación conservadas. |
| Indicadores comerciales. | **11.066.206,89** en ventas, **35.234 unidades**, **4.806 pedidos en 2020**. |
| Elección de visuales justificada. | Tarjetas para totales; barras para categorías; tabla para cifras exactas; línea para evolución. |
| Filtros coherentes. | Selecciones de 2020 y Computers con valores de referencia definidos. |
| Comprobación independiente. | Dos pedidos reales: **867,50**, **4 unidades** y **2 pedidos**, reconstruidos manualmente. |
| Storytelling sustentado. | Liderazgo de Computers, cambio de marzo, interpretación y acción propuesta. |
| Reproducción documentada. | Fuente, transformaciones, medidas, filtros y cifras de control. |

**Explicación:** las respuestas atienden el problema comercial definido en Planear. Los controles de preparación y la cuenta manual sustentan las cifras utilizadas en los gráficos y el relato.

**Resultado:** el caso conecta la necesidad comercial con datos concretos, visuales adecuados y un hallazgo que puede verificarse.

[Volver al índice](#indice)

---

<a id="valores-control"></a>
## Valores de control del archivo completo

Valores recalculados sobre el paquete **`csv-100k.7z`, publicación 2024**, el **8 de octubre de 2026**. [F2]

| Control | Archivo completo, sin filtros | 2020, todas las categorías |
| --- | ---: | ---: |
| Líneas de venta | 199.873 | 11.267 |
| Unidades | 628.370 | 35.234 |
| Pedidos distintos | 83.130 | 4.806 |
| Ventas en moneda base, dos decimales | 203.723.865,76 | 11.066.206,89 |

- Fecha mínima: **01/01/2015**. Fecha máxima: **20/04/2024**.
- Importe exacto del archivo completo: **203.723.865,758428**.
- Importe exacto de 2020: **11.066.206,893045**.
- Cálculo de importe: `Quantity × NetPrice`, por línea.

La comparación sin filtros revisa la fuente completa; el filtro 2020 corresponde a las respuestas comerciales. Otras versiones del paquete pueden producir cifras distintas. El archivo termina el 20 de abril de 2024, por lo que las comparaciones temporales deben considerar esa cobertura.

[Volver al índice](#indice)

<a id="dificultades"></a>
## Solución de dificultades frecuentes

| Situación | Causa a revisar | Solución |
| --- | --- | --- |
| Todo el CSV aparece en una columna. | Delimitador. | Coma en la importación o en el paso Origen. |
| Encabezados Column1, Column2. | Encabezados sin promover. | Usar la primera fila como encabezado. |
| Precios distintos de los valores originales. | Interpretación del punto decimal. | Conversión desde el texto con Inglés (Estados Unidos). |
| Fechas incorrectas. | Tipo o interpretación regional. | Conversión de OrderDate desde año-mes-día y contraste con 2015-01-01. |
| Aumento de filas al expandir productos. | Claves de producto repetidas. | Control de unicidad de ProductKey y revisión de la combinación. |
| Espacios al final de categorías. | Texto de origen. | Transformar → Formato → Recortar. |
| 11.267 aparece como número de pedidos de 2020. | Conteo de filas. | DISTINCTCOUNT de Pedido; resultado: 4.806. |
| Meses de distintos años agrupados. | Nombre de mes sin año. | InicioMes directamente y orden cronológico. |
| Tarjetas con valores abreviados. | Unidades de visualización automáticas. | Unidades de visualización: Ninguno. |
| Un visual conserva cifras al cambiar una categoría. | Interacción o filtro propio. | Opción Filtrar en Editar interacciones y revisión de filtros. |
| Muestra de 2015 vacía. | Filtro 2020 de todo el informe o sincronización. | Filtro comercial limitado a Resumen comercial. |
| Diferencia con el cálculo manual. | Filtros, líneas incompletas o redondeo. | Mismos pedidos completos, precisión original y mismo alcance. |
| Total de pedidos menor que la suma por categorías. | Pedidos con varias categorías. | Conteo distinto en el contexto general. |
| Diferencia de 0,01 al sumar cifras visibles. | Redondeo de presentación. | Suma de valores originales y redondeo del total al final. |
| Actualización sin acceso a los CSV. | Ruta de origen. | Corrección del origen de datos. |

[Volver al índice](#indice)

<a id="fuentes"></a>
## Fuentes oficiales y documentación

Consulta realizada el **8 de octubre de 2026**. Las cifras proceden de los CSV originales de Contoso. El problema de negocio, la elección didáctica de visuales y el seguimiento propuesto forman parte del caso resuelto.

- **[F1]** Google Career Certificates. [Foundations of Data Science: flujo PACE](https://www.coursera.org/learn/foundations-of-data-science).
- **[F2]** SQLBI. [Contoso V2: publicación de datos 2024](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/tag/ready-to-use-data-2024) y [documentación del generador](https://docs.sqlbi.com/contoso-data-generator/).
- **[F3]** SQLBI. [Código del generador, Engine.cs](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/Engine.cs). Consultado para verificar la generación del precio neto y la incorporación separada de la tasa de cambio.
- **[F4]** Microsoft Learn. [Obtención de datos en Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/connect-data/desktop-data-sources).
- **[F5]** Microsoft Learn. [Descarga de Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/fundamentals/desktop-get-the-desktop).
- **[F6]** Microsoft Learn. [Conector de texto y CSV](https://learn.microsoft.com/es-es/power-query/connectors/text-csv).
- **[F7]** Microsoft Learn. [Tipos de datos y configuración regional](https://learn.microsoft.com/es-es/power-query/data-types).
- **[F8]** Microsoft Learn. [Combinación externa izquierda](https://learn.microsoft.com/es-es/power-query/merge-queries-left-outer).
- **[F9]** Microsoft Learn. [Función DISTINCTCOUNT](https://learn.microsoft.com/es-es/dax/distinctcount-function-dax).
- **[F10]** Microsoft Learn. [Visual de tarjeta](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-card).
- **[F11]** Microsoft Learn. [Visualizaciones de tabla](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-tables).
- **[F12]** Microsoft Learn. [Visualizaciones para comparar categorías y mostrar tendencias](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualizations-overview).
- **[F13]** Microsoft Learn. [Gráficos de líneas](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-line-chart).
- **[F14]** Microsoft Learn. [Agregar una columna personalizada](https://learn.microsoft.com/es-es/power-query/add-custom-column).
- **[F15]** Microsoft Learn. [Herramientas de perfilado de datos](https://learn.microsoft.com/es-es/power-query/data-profiling-tools).
- **[F16]** Microsoft Learn. [Agrupar y resumir filas](https://learn.microsoft.com/es-es/power-query/group-by).
- **[F17]** Microsoft Learn. [Vista de informe en Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-report-view).
- **[F18]** Microsoft Learn. [Medidas en Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-measures).
- **[F19]** Microsoft Learn. [Función SUM](https://learn.microsoft.com/es-es/dax/sum-function-dax).
- **[F21]** Microsoft Learn. [Segmentaciones de datos](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-slicers).
- **[F22]** Microsoft Learn. [Interacciones entre objetos visuales](https://learn.microsoft.com/es-es/power-bi/create-reports/service-reports-visual-interactions).
- **[F23]** Microsoft Learn. [Date.Year](https://learn.microsoft.com/es-es/powerquery-m/date-year) y [Date.StartOfMonth](https://learn.microsoft.com/es-es/powerquery-m/date-startofmonth).

[Volver al índice](#indice)

