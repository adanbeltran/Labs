# Taller 1. Mi primer reporte comercial con Contoso en Power BI
**Guion docente resuelto · Metodología PACE · Análisis descriptivo**

<a id="indice"></a>
## Índice de fases y pasos

| Fase | Pregunta que orienta el trabajo | Producto resuelto |
| --- | --- | --- |
| [1. P: Planear](#fase-1) | ¿Qué necesita conocer el negocio? | Problema, preguntas, alcance y criterios de éxito. |
| [2. A: Analizar](#fase-2) | ¿Qué datos responden las preguntas y qué calidad tienen? | Una tabla preparada y controles con sus resultados. |
| [3. C: Construir](#fase-3) | ¿Cómo mostrar y comprobar las respuestas? | Medidas, tarjetas, tabla, barras, línea y filtros. |
| [4. E: Ejecutar y comunicar](#fase-4) | ¿Qué hallazgo merece atención y qué sigue? | Respuestas, storytelling y seguimiento propuesto. |

**Fase 1. Planear**

- [1.1. Presentar el problema y la decisión que se apoyará](#paso-1-1)
- [1.2. Presentar las preguntas de negocio y la información necesaria](#paso-1-2)
- [1.3. Delimitar el alcance de la demostración](#paso-1-3)
- [1.4. Presentar la ficha de planeación y los criterios de éxito](#paso-1-4)

**Fase 2. Analizar**

- [2.1. Preparar el equipo y las carpetas](#paso-2-1)
- [2.2. Descargar y extraer Contoso V2](#paso-2-2)
- [2.3. Explicar qué representan filas, unidades y pedidos](#paso-2-3)
- [2.4. Relacionar las preguntas con los campos de Contoso](#paso-2-4)
- [2.5. Importar los CSV en Power Query](#paso-2-5)
- [2.6. Corregir tipos, fechas, precios y textos](#paso-2-6)
- [2.7. Combinar ventas con productos](#paso-2-7)
- [2.8. Renombrar campos y calcular el importe de cada línea](#paso-2-8)
- [2.9. Preparar el año y el mes de análisis](#paso-2-9)
- [2.10. Comprobar la calidad con resultados de referencia](#paso-2-10)
- [2.11. Cargar una sola tabla y guardar la base de la demo](#paso-2-11)

**Fase 3. Construir**

- [3.1. Escoger los visuales y organizar las páginas](#paso-3-1)
- [3.2. Crear las medidas de ventas, unidades y pedidos](#paso-3-2)
- [3.3. Construir las tarjetas de resumen](#paso-3-3)
- [3.4. Construir y leer la tabla por categoría](#paso-3-4)
- [3.5. Construir y leer el gráfico de barras](#paso-3-5)
- [3.6. Construir y leer la línea mensual](#paso-3-6)
- [3.7. Filtrar el año 2020 y todas las categorías](#paso-3-7)
- [3.8. Comprobar las interacciones con una selección resuelta](#paso-3-8)
- [3.9. Demostrar la comprobación manual de dos pedidos](#paso-3-9)

**Fase 4. Ejecutar y comunicar**

- [4.1. Presentar las respuestas resueltas a las preguntas](#paso-4-1)
- [4.2. Comunicar el storytelling resuelto](#paso-4-2)
- [4.3. Presentar la acción propuesta y el seguimiento](#paso-4-3)
- [4.4. Comprobar la reproducción del resultado](#paso-4-4)
- [4.5. Cerrar la demostración y mostrar el producto esperado](#paso-4-5)

**Apoyos para el profesor**

- [Cómo utilizar esta demostración](#uso-demo).
- [Guion de clase de 25 minutos](#guion-tiempo).
- [Valores de control del archivo completo](#valores-control).
- [Solución de dificultades frecuentes](#dificultades).
- [Fuentes oficiales y documentación](#fuentes).

---

<a id="uso-demo"></a>
## Cómo utilizar esta demostración

Este documento contiene **la solución de ejemplo que realiza y explica el profesor**. Las preguntas, la preparación, las cifras, la elección de gráficos y el relato están resueltos. El caso trabajado en todos los pasos es **Contoso V2**, paquete `csv-100k.7z` de la publicación **Ready to use data (2024)** de SQLBI. [F2]

**Objetivo de la demo:** construir un reporte que describa las ventas, unidades y pedidos de Contoso en 2020; reconocer la categoría líder, observar los cambios mensuales y comprobar un resultado a partir de sus transacciones.

**Ruta de trabajo:** se descargan `sales.csv` y `product.csv`, se combinan en Power Query y se carga una sola tabla llamada `Ventas`. Este enfoque mantiene sencillo el primer reporte. La preparación conserva una fila por línea de pedido.

**Interfaz:** las instrucciones usan Power BI Desktop en español. Los paneles pueden llamarse **Datos/Campos** y **Crear objeto visual/Compilar objeto visual**, según la versión. Las transformaciones se hacen en Power Query y las medidas se crean después en Power BI Desktop. [F4] [F17]

**Lectura del archivo:** abrir el `.md` en un visor Markdown; en VS Code, usar **Abrir vista previa** o `Ctrl + Shift + V` para utilizar el índice.


**PACE:** *Plan, Analyze, Construct, Execute*. La adaptación del taller consiste en planear la necesidad; analizar y preparar los datos; construir y comprobar el reporte; ejecutar la comunicación y definir el seguimiento. [F1]

---

<a id="fase-1"></a>
## Fase 1. P: Planear

**Propósito:** establecer qué se quiere conocer, para quién y con qué alcance.

**Producto de la fase:** ficha de planeación resuelta. La revisión de archivos, los conteos y los cálculos se realizan a partir de Analizar.

<a id="paso-1-1"></a>
### Paso 1.1. Presentar el problema y la decisión que se apoyará

**Para qué:** dar un propósito comercial al reporte.

**Hace el profesor:** presenta el caso y los siguientes elementos ya definidos.

| Elemento | Solución del ejemplo |
| --- | --- |
| Situación | La dirección comercial de Contoso necesita una visión organizada de su actividad comercial por categoría y mes. |
| Problema | Requiere reconocer el volumen vendido, la categoría que más aporta y los cambios mensuales para orientar una revisión comercial. |
| Destinatario | Dirección comercial de Contoso. |
| Decisión que se apoyará | Elegir una categoría y un cambio mensual que merezcan un análisis posterior. |
| Objetivo | Describir ventas, unidades y pedidos de Contoso en 2020, y comunicar un hallazgo sustentado. |

**Dice el profesor:** “Antes de abrir Power BI, definimos para quién trabajamos y qué información necesita. El reporte ayudará a escoger qué revisar con más detalle”.

**Explicación:** la decisión orienta el análisis. Describir las ventas permitirá priorizar una revisión; para decidir compras o promociones también harían falta datos sobre inventario, costos y acciones comerciales.

**Resultado esperado:** el grupo comprende el problema y la finalidad del reporte.

[Volver al índice](#indice)

<a id="paso-1-2"></a>
### Paso 1.2. Presentar las preguntas de negocio y la información necesaria

**Para qué:** definir preguntas concretas y la información necesaria para responderlas.

**Hace el profesor:** presenta estas cuatro preguntas del caso.

| Código | Pregunta de negocio resuelta en la demo | Información necesaria | Tipo de respuesta esperada |
| --- | --- | --- | --- |
| Q1 | ¿Cuál fue el volumen comercial de Contoso en 2020: ventas, unidades y pedidos? | Fecha, identificador de pedido, cantidad y precio neto de cada línea. | Tres totales del período. |
| Q2 | ¿Qué categoría registró el mayor importe de ventas en 2020? | Categoría de cada producto y valor vendido. | Categoría líder y su importe. |
| Q3 | ¿La categoría con más unidades coincide con la de mayor importe? | Categoría, cantidades y valor vendido. | Comparación de las dos clasificaciones. |
| Q4 | ¿Cómo cambiaron las ventas mes a mes durante 2020? | Fecha y valor vendido, agrupables por mes y año. | Evolución mensual, mes máximo y variación destacada. |

**Dice el profesor:** “Cada pregunta exige información concreta. Para conocer pedidos necesito identificar cada compra; para comparar categorías necesito saber a cuál pertenece el producto”.

**Explicación:** aquí se define la necesidad de información. En Analizar se comprueba qué archivos y campos la contienen; en Construir se escoge cómo mostrarla.

**Resultado esperado:** Q1 a Q4 quedan establecidas como preguntas de la demostración.

[Volver al índice](#indice)

<a id="paso-1-3"></a>
### Paso 1.3. Delimitar el alcance de la demostración

**Para qué:** delimitar el caso y evitar conclusiones fuera de su alcance.

**Hace el profesor:** presenta el alcance acordado.

| Elemento | Alcance definido |
| --- | --- |
| Empresa del caso | Contoso, empresa ficticia del conjunto de datos. |
| Fuente | Contoso V2, publicación 2024, paquete `csv-100k.7z`. |
| Período propuesto | Del 1 de enero al 31 de diciembre de 2020; su disponibilidad se comprobará en Analizar. |
| Cobertura comercial | Todas las categorías; el reporte permitirá seleccionar una categoría. |
| Nivel | Análisis descriptivo de ventas, unidades, pedidos y evolución mensual. |
| Alcance de la decisión | Priorizar una revisión comercial posterior. |

**Dice el profesor:** “El ejemplo responderá por 2020 y por todas las categorías. Cuando usemos filtros, indicaremos exactamente qué selección estamos leyendo”.

**Explicación:** un resultado cambia cuando cambia el período o la selección. Declarar el alcance permite comparar cifras que corresponden al mismo contexto.

**Resultado esperado:** período, fuente, destinatario y propósito están definidos antes de explorar los archivos.

[Volver al índice](#indice)

<a id="paso-1-4"></a>
### Paso 1.4. Presentar la ficha de planeación y los criterios de éxito

**Para qué:** explicar cómo se reconocerá una solución útil y reproducible.

**Hace el profesor:** muestra la ficha de planeación completa.

| Elemento | Ficha de planeación resuelta |
| --- | --- |
| Problema | La dirección comercial necesita describir el volumen comercial y reconocer categorías y meses que merezcan revisión. |
| Destinatario | Dirección comercial de Contoso. |
| Objetivo | Responder Q1–Q4 para 2020 mediante un reporte descriptivo y un hallazgo sustentado. |
| Información necesaria | Fecha de compra, identificador de pedido, producto, categoría, cantidad y precio neto. |
| Alcance | Contoso V2, año 2020, todas las categorías y posibilidad de filtrar. |
| Decisión apoyada | Priorizar una categoría y un cambio mensual para análisis posterior. |
| Producto previsto | `Demo_01_Contoso_PACE.pbix`, con las páginas `Resumen comercial`, `Comprobacion` y `PACE y hallazgos`. |

**Presenta los criterios de éxito:**

| Criterio | Evidencia que se obtendrá después |
| --- | --- |
| Q1–Q4 tienen respuestas. | Totales, clasificación por categoría y serie mensual visibles. |
| La preparación es confiable. | Tipos correctos, claves únicas y controles de combinación y cobertura. |
| Se puede comprobar un resultado. | Comparación entre el cálculo manual de dos pedidos y sus tarjetas. |
| El alcance es visible. | Fecha y categoría seleccionadas en el reporte. |
| El hallazgo está sustentado. | Cifras, período, contexto, interpretación y acción propuesta. |
| El trabajo se puede reproducir. | Fuente, transformaciones y medidas documentadas; comprobación de apertura y actualización. |

**Dice el profesor:** “Estos son los criterios que usaremos para revisar la solución al terminar”.

**Explicación:** Planear establece las evidencias que se necesitarán. Las fases siguientes las producen y comprueban.

**Resultado esperado:** la ficha está resuelta y la demostración tiene un objetivo y una entrega definidos.

[Volver al índice](#indice)

---

<a id="fase-2"></a>
## Fase 2. A: Analizar

**Propósito:** comprender los datos de Contoso, comprobar su calidad y preparar la información que utilizará el reporte.

**Producto de la fase:** tabla `Ventas`, con una fila por línea de pedido, y controles de referencia resueltos.

Los pasos 2.1–2.11 forman la preparación previa del profesor para la demo de 25 minutos. Durante la clase muestra las consultas, las transformaciones y sus resultados.

<a id="paso-2-1"></a>
### Paso 2.1. Preparar el equipo y las carpetas

**Para qué:** disponer de un entorno reproducible.

**Hace el profesor antes de la clase:**

1. Abre Power BI Desktop en Windows; si necesita instalarlo, utiliza la [descarga oficial](https://learn.microsoft.com/es-es/power-bi/fundamentals/desktop-get-the-desktop). [F5]
2. Crea la carpeta `Taller_01_PowerBI` y, dentro, las carpetas `Contoso` y `Reportes`.
3. Reserva `Contoso` para los CSV originales y `Reportes` para los archivos `.pbix`.
4. Conserva los CSV originales; realiza las transformaciones en Power Query.

**Explicación:** Power Query guarda los pasos aplicados. Mantener las fuentes en una ubicación estable permite repetir la preparación cuando se actualiza el reporte.

**Resultado esperado:** Power BI Desktop está disponible y las dos carpetas están creadas.

[Volver al índice](#indice)

<a id="paso-2-2"></a>
### Paso 2.2. Descargar y extraer Contoso V2

**Para qué:** utilizar exactamente el paquete al que corresponden las cifras resueltas.

**Hace el profesor:**

1. Abre la [publicación Ready to use data (2024)](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/tag/ready-to-use-data-2024).
2. Expande **Assets** y descarga **`csv-100k.7z`**. Puede usar este [enlace directo al paquete](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/download/ready-to-use-data-2024/csv-100k.7z).
3. Lo guarda en `Contoso` y extrae su contenido con una herramienta compatible con `.7z`, como [7-Zip](https://www.7-zip.org/).
4. Localiza `sales.csv` y `product.csv` entre los archivos extraídos.

**Dice el profesor:** “Usaremos ventas y productos. La publicación contiene otros archivos, pero estos dos incluyen la información que requieren nuestras preguntas”.

**Explicación:** el paquete es `.7z`; cambiarle la extensión a ZIP no lo convierte. La denominación 100K se refiere al tamaño aproximado de pedidos del paquete. La tabla de ventas tiene **199.873 líneas**, porque un pedido puede incluir varios productos. [F2]

**Resultado esperado:** ambos CSV están extraídos y disponibles para importar.

[Volver al índice](#indice)

<a id="paso-2-3"></a>
### Paso 2.3. Explicar qué representan filas, unidades y pedidos

**Para qué:** distinguir el número de líneas, la cantidad de artículos y el número de compras.

**Hace el profesor:** muestra los pedidos 1000 y 1001 de `sales.csv` y explica su nivel de detalle.

| Concepto | Significado en Contoso | Campo |
| --- | --- | --- |
| Línea | Un producto incluido en un pedido. | `OrderKey` junto con `LineNumber`. |
| Unidades | Cantidad de artículos de esa línea. | `Quantity`. |
| Pedido | Una compra que puede contener varias líneas. | `OrderKey`. |
| Precio neto | Precio unitario después del descuento generado. | `NetPrice`. |

**Ejemplo real resuelto, del 1 de enero de 2015:**

| Pedido | Línea | ProductKey | Unidades | Precio neto | Importe de la línea |
| --- | --- | --- | --- | --- | --- |
| 1000 | 0 | 48 | 1 | 98,967 | 98,967 |
| 1000 | 1 | 460 | 1 | 659,78 | 659,78 |
| 1001 | 0 | 1730 | 2 | 54,376 | 108,752 |

La notación de la guía utiliza coma decimal. El CSV utiliza punto decimal.

**Explica el resultado:**

- Líneas: **3**.
- Unidades: `1 + 1 + 2 = 4`.
- Pedidos distintos: **2**, identificados como 1000 y 1001.
- Ventas: `98,967 + 659,78 + (2 × 54,376) = 867,499`; con dos decimales, **867,50**.

**Dice el profesor:** “Tres filas no significan tres pedidos. El pedido 1000 aparece dos veces porque tiene dos líneas. Para contar compras debemos contar identificadores diferentes”.

**Explicación:** este ejemplo de 2015 se utiliza para comprender y comprobar el cálculo. Las respuestas comerciales de la demostración se obtendrán para 2020.

**Resultado esperado:** queda claro por qué se suman unidades y se cuentan pedidos distintos. [F9]

[Volver al índice](#indice)

<a id="paso-2-4"></a>
### Paso 2.4. Relacionar las preguntas con los campos de Contoso

**Para qué:** confirmar dónde está la información definida en Planear.

**Hace el profesor:** muestra los encabezados de los CSV y relaciona cada pregunta con sus campos.

| Pregunta | Campos de `sales.csv` | Campos de `product.csv` | Uso posterior |
| --- | --- | --- | --- |
| Q1. Volumen comercial. | `OrderDate`, `OrderKey`, `Quantity`, `NetPrice`. | — | Filtrar fecha, obtener ventas y unidades, contar pedidos. |
| Q2. Categoría con más ventas. | `OrderDate`, `ProductKey`, `Quantity`, `NetPrice`. | `ProductKey`, `CategoryName`. | Añadir categoría y comparar importes en 2020. |
| Q3. Unidades frente a ventas. | `OrderDate`, `ProductKey`, `Quantity`, `NetPrice`. | `ProductKey`, `CategoryName`. | Comparar cantidades e importes por categoría. |
| Q4. Cambios mensuales. | `OrderDate`, `Quantity`, `NetPrice`. | — | Agrupar las ventas por mes y año. |

`ProductName` se añade para identificar los productos en la página de comprobación.

**Dice el profesor:** “`ProductKey` aparece en las dos tablas y permite añadir a cada venta su nombre y categoría”.

**Explicación:** `sales.csv` ya contiene las líneas de venta. Los archivos `orders.csv` y `orderrows.csv` son otra representación de pedidos y líneas de la misma fuente; no se añaden como ventas adicionales. [F2]

**Resultado esperado:** están identificadas las dos fuentes y la clave de combinación.

[Volver al índice](#indice)

<a id="paso-2-5"></a>
### Paso 2.5. Importar los CSV en Power Query

**Para qué:** importar las fuentes y preparar sus datos antes de construir gráficos.

**Hace el profesor:**

1. En un informe nuevo, selecciona **Inicio → Obtener datos → Texto/CSV**.
2. Abre `sales.csv` y confirma **delimitador: coma**. En la vista previa, cada campo debe ocupar una columna.
3. Selecciona **No detectar tipos de datos**, si está disponible, y pulsa **Transformar datos**.
4. En Power Query, cambia el nombre de la consulta a `OrigenVentas`.
5. Usa **Inicio → Nuevo origen → Texto/CSV** para importar `product.csv`.
6. Confirma también la separación por coma y llama a la consulta `OrigenProductos`.
7. Si aparecen encabezados `Column1`, `Column2`, aplica **Usar la primera fila como encabezado**.

**Dice el profesor:** “Transformar datos abre el espacio donde revisamos y preparamos la información antes de usarla en el reporte”.

**Explicación:** importar y transformar son actividades de Analizar. Si la vista previa muestra todo en una columna, el delimitador debe corregirse antes de continuar. [F6]

**Resultado esperado:** existen las consultas `OrigenVentas` y `OrigenProductos`, con sus encabezados originales.

[Volver al índice](#indice)

<a id="paso-2-6"></a>
### Paso 2.6. Corregir tipos, fechas, precios y textos

**Para qué:** interpretar correctamente fechas, identificadores, números y categorías.

**Hace el profesor:** selecciona cada encabezado y aplica el tipo indicado.

| Consulta | Campo | Tipo |
| --- | --- | --- |
| OrigenVentas | `OrderKey` | Texto. |
| OrigenVentas | `LineNumber`, `ProductKey`, `Quantity` | Número entero. |
| OrigenVentas | `OrderDate` | Fecha. |
| OrigenVentas | `NetPrice` | Número decimal. |
| OrigenProductos | `ProductKey` | Número entero. |
| OrigenProductos | `ProductName`, `CategoryName` | Texto. |

1. Para `NetPrice`, usa **Cambiar tipo → Usar configuración regional → Número decimal → Inglés (Estados Unidos)**. El archivo utiliza punto decimal. [F7]
2. Convierte `OrderDate`, escrito como `2015-01-01`, a **Fecha**. Comprueba que se interprete como 1 de enero de 2015.
3. Si un paso automático **Tipo cambiado** produjo errores, elimínalo y convierte desde el texto original.
4. En `OrigenProductos`, selecciona `ProductName` y `CategoryName` y usa **Transformar → Formato → Recortar**.

**Dice el profesor:** “`98.967` representa noventa y ocho con novecientos sesenta y siete milésimas. Conservar esa precisión es necesario para obtener el importe correcto”.

**Explicación:** cambiar el formato visual no corrige un texto convertido incorrectamente. Además, el nombre original `Cameras and camcorders ` tiene un espacio al final; **Recortar** deja `Cameras and camcorders`, que es como aparece en esta solución.

**Resultado esperado:** los campos utilizados tienen los tipos correctos, las categorías están recortadas y la primera línea del pedido 1000 conserva el precio **98,967**.

[Volver al índice](#indice)

<a id="paso-2-7"></a>
### Paso 2.7. Combinar ventas con productos

**Para qué:** añadir nombre y categoría a cada línea sin multiplicar las ventas.

**Hace el profesor primero:**

1. Verifica que `ProductKey` sea entero en ambas consultas.
2. Hace clic derecho en `OrigenProductos` y selecciona **Referencia**. Nombra la consulta `ControlProductos`.
3. Usa **Agrupar por** `ProductKey`, agrega `Conteo` con **Recuento de filas** y filtra `Conteo > 1`.
4. Muestra el resultado de referencia: **0 claves repetidas**. El archivo tiene **2.517 productos y 2.517 claves diferentes**.

**Luego realiza la combinación:**

1. Selecciona `OrigenVentas` y usa **Inicio → Combinar consultas → Combinar consultas como nuevas**.
2. Escoge `OrigenProductos` como segunda tabla.
3. Selecciona `ProductKey` en las dos tablas.
4. Elige **Externa izquierda: todas de la primera, coincidencias de la segunda**, con coincidencia exacta. [F8]
5. Acepta y nombra la nueva consulta `Ventas`.
6. Expande la columna de productos con el botón de dos flechas.
7. Selecciona únicamente `ProductName` y `CategoryName`; desmarca el prefijo del nombre de columna.

**Dice el profesor:** “Cada producto tiene una sola coincidencia. La combinación añade atributos a una línea de venta y conserva su nivel de detalle”.

**Explicación:** una combinación izquierda conserva las ventas incluso si falta un producto; los atributos vacíos permitirían detectar esa incidencia. Varias coincidencias de producto, en cambio, multiplicarían las filas al expandir.

**Resultado esperado:** **199.873 líneas** antes y después de expandir, y **0 ventas sin coincidencia de producto**.

[Volver al índice](#indice)

<a id="paso-2-8"></a>
### Paso 2.8. Renombrar campos y calcular el importe de cada línea

**Para qué:** tener nombres comprensibles y el importe de cada línea.

**Hace el profesor:** en `Ventas`, renombra con doble clic los encabezados.

| Nombre original | Nombre final |
| --- | --- |
| `OrderDate` | `Fecha` |
| `OrderKey` | `Pedido` |
| `LineNumber` | `Linea` |
| `ProductKey` | `ProductKey` |
| `ProductName` | `Producto` |
| `CategoryName` | `Categoria` |
| `Quantity` | `Unidades` |
| `NetPrice` | `PrecioUnitario` |

1. Selecciona **Agregar columna → Columna personalizada**.
2. Escribe `ImporteVenta` como nombre y esta fórmula:

```powerquery
[Unidades] * [PrecioUnitario]
```

3. Acepta y asigna a `ImporteVenta` el tipo **Número decimal**. [F14]

**Dice el profesor:** “Primero calculo cuánto vale cada línea. Después sumaré esos importes para obtener ventas por período o categoría”.

**Explicación:** se utiliza `NetPrice`, precio neto de la línea, que incorpora el descuento generado. El precio de catálogo `Price` de productos responde a otra definición. La solución conserva los importes en moneda base del generador y no aplica `ExchangeRate` a estos precios. La definición se contrastó con el código del generador. [F3]

**Resultado esperado:** para el pedido 1001, `2 × 54,376 = 108,752`. Se conserva la precisión del importe; el formato de dos decimales se aplicará al mostrar el total.

[Volver al índice](#indice)

<a id="paso-2-9"></a>
### Paso 2.9. Preparar el año y el mes de análisis

**Para qué:** comparar meses conservando el año al que pertenecen.

**Hace el profesor:** crea dos columnas mediante **Agregar columna → Columna personalizada**.

| Columna | Fórmula de Power Query | Tipo |
| --- | --- | --- |
| `Anio` | `Date.Year([Fecha])` | Número entero. |
| `InicioMes` | `Date.StartOfMonth([Fecha])` | Fecha. |

**Muestra un ejemplo resuelto:** una fecha `15/11/2020` produce `Anio = 2020` e `InicioMes = 01/11/2020`. [F23]

**Dice el profesor:** “Si uso solamente el nombre del mes, puedo juntar noviembre de distintos años. InicioMes conserva el mes y el año, y permite ordenar la serie”.

**Explicación:** estas columnas se derivan de `Fecha`, que ya debe estar correctamente convertida. El año ayuda a revisar la cobertura; `InicioMes` será el eje temporal del reporte.

**Resultado esperado:** ambas columnas están preparadas y los meses de años diferentes permanecen separados.

[Volver al índice](#indice)

<a id="paso-2-10"></a>
### Paso 2.10. Comprobar la calidad con resultados de referencia

**Para qué:** comprobar que la preparación mantiene datos válidos para responder las preguntas.

**Hace el profesor:**

1. Activa **Ver → Calidad de columna**, **Distribución de columna** y **Perfil de columna**.
2. En la parte inferior cambia el perfil de **primeras 1000 filas** a **conjunto de datos completo**. [F15]
3. Para conteos exactos, crea una **Referencia** de cada consulta a revisar y utiliza **Transformar → Contar filas**. Nombra los controles de forma descriptiva y conserva sus consultas sin carga.
4. Para duplicados de líneas, crea `ControlLineas` como referencia de `Ventas`; usa **Agrupar por → Avanzado** con `Pedido` y `Linea`, agrega `Conteo` con **Recuento de filas** y filtra `Conteo > 1`. [F16]
5. Filtra temporalmente cantidades y precios para revisar valores cero o negativos; retira esos filtros al terminar.
6. En una referencia de `Ventas`, filtra `Anio = 2020` y comprueba los meses presentes y su número de líneas.

**Muestra los resultados resueltos de control:**

| Control | Resultado de referencia |
| --- | --- |
| Filas de ventas antes de combinar. | 199.873. |
| Filas después de expandir productos. | 199.873. |
| Productos y claves de producto distintas. | 2.517 y 2.517. |
| Claves de producto repetidas. | 0. |
| Combinaciones repetidas de Pedido + Linea. | 0. |
| Vacíos en fecha, pedido, línea, clave de producto, cantidad o precio neto. | 0. |
| Ventas sin producto coincidente; nombres o categorías vacíos. | 0. |
| Cantidades cero o negativas. | 0. |
| Precios netos cero o negativos. | 0. |
| Cobertura del archivo. | 01/01/2015 a 20/04/2024. |
| Cobertura del período de la demo. | Los 12 meses de 2020 tienen registros. |
| Líneas del año 2020. | 11.267. |

**Dice el profesor:** “La combinación conserva el número de líneas, las claves no se repiten y el año propuesto está disponible. Ya podemos construir las respuestas”.

**Explicación:** un pedido repetido entre filas puede ser válido; una combinación repetida de pedido y línea requiere revisión. Los controles tienen un resultado concreto con el que contrastar la preparación en Power BI.

**Resultado esperado:** los controles coinciden con la referencia y los tipos aplicados no presentan errores de conversión.

[Volver al índice](#indice)

<a id="paso-2-11"></a>
### Paso 2.11. Cargar una sola tabla y guardar la base de la demo

**Para qué:** comenzar la demostración con una estructura sencilla y guardada.

**Hace el profesor:**

1. En `Ventas`, conserva las ocho columnas finales del paso 2.8, además de `ImporteVenta`, `Anio` e `InicioMes`: **11 columnas**.
2. Desmarca **Habilitar carga** en `OrigenVentas`, `OrigenProductos` y todas las consultas de control.
3. Mantén habilitada la carga de `Ventas`. Conserva las consultas auxiliares porque la final depende de ellas.
4. Pulsa **Inicio → Cerrar y aplicar**.
5. Guarda en `Reportes` como `Demo_01_Contoso_Base.pbix`.
6. Para trabajar en clase, usa **Guardar como** y crea `Demo_01_Contoso_PACE.pbix`.

**Dice el profesor:** “El modelo tiene una sola tabla preparada. Las consultas de origen siguen siendo parte del proceso, aunque no aparezcan como tablas del reporte”.

**Explicación:** la base permite repetir la construcción desde el mismo punto de partida. Los controles y consultas auxiliares permanecen en Power Query, con su carga desactivada.

**Resultado esperado:** el panel de datos muestra `Ventas`, con **199.873 líneas**, y la base queda disponible para la clase.

[Volver al índice](#indice)

---

<a id="fase-3"></a>
## Fase 3. C: Construir

**Propósito:** construir visuales que respondan Q1–Q4 y comprobar sus resultados.

**Producto de la fase:** reporte comercial resuelto y una página de comprobación manual.

Las cifras comerciales de referencia corresponden a **01/01/2020–31/12/2020, todas las categorías**. Antes de activar ese filtro, el reporte mostrará los valores del archivo completo.

<a id="paso-3-1"></a>
### Paso 3.1. Escoger los visuales y organizar las páginas

**Para qué:** escoger una representación adecuada para cada pregunta.

**Hace el profesor:** presenta esta elección antes de arrastrar los campos.

| Pregunta | Datos y medida preparados | Visual escogido | Justificación |
| --- | --- | --- | --- |
| Q1. ¿Cuál es el volumen comercial? | Fecha; suma de ImporteVenta y Unidades; conteo distinto de Pedido. | Tres tarjetas. | Se leen rápidamente los tres totales del período. |
| Q2. ¿Qué categoría tiene más ventas? | Categoria y suma de ImporteVenta. | Barras horizontales ordenadas. | Comparan categorías y permiten leer sus nombres completos. |
| Q3. ¿Coincide el liderazgo en unidades y ventas? | Categoria; ventas, unidades y pedidos distintos. | Tabla por categoría. | Muestra cifras exactas y permite cambiar el orden de clasificación. |
| Q4. ¿Cómo cambian las ventas mensuales? | InicioMes y suma de ImporteVenta. | Línea mensual. | Conserva la secuencia temporal y muestra subidas y caídas. |
| ¿Se puede comprobar el cálculo? | Pedido, Linea, Unidades, PrecioUnitario e ImporteVenta. | Tabla de detalle y tres tarjetas. | Permite reconstruir una selección de transacciones. |

**Organiza las páginas:**

1. En la vista **Informe**, renombra la primera página como `Resumen comercial`. [F17]
2. Crea las páginas `Comprobacion` y `PACE y hallazgos` con el botón **+**.
3. En `Resumen comercial`, reserva la parte superior para filtros y tarjetas; la zona central para barras y línea; la inferior para la tabla por categoría.

**Dice el profesor:** “Elijo el gráfico a partir de la pregunta. Las barras comparan categorías; la línea permite seguir el tiempo; la tabla sirve para contrastar cifras exactas”.

**Explicación:** la elección responde a la comparación que se necesita realizar. Ventas y unidades tienen escalas y significados diferentes, por lo que se presentan en columnas separadas. [F11] [F12] [F13]

**Resultado esperado:** existe un visual definido para cada pregunta y las tres páginas están creadas.

[Volver al índice](#indice)

<a id="paso-3-2"></a>
### Paso 3.2. Crear las medidas de ventas, unidades y pedidos

**Para qué:** definir indicadores que se recalculen según los filtros.

**Hace el profesor:** selecciona `Ventas` en el panel de datos y utiliza **Modelado → Nueva medida**. Crea cada medida por separado, pulsando Enter al finalizar. [F18]

```dax
Ventas Totales = SUM('Ventas'[ImporteVenta])
```

```dax
Unidades Totales = SUM('Ventas'[Unidades])
```

```dax
Pedidos = DISTINCTCOUNT('Ventas'[Pedido])
```

**Configura los formatos:** `Ventas Totales`, número decimal con dos decimales; `Unidades Totales` y `Pedidos`, número entero y separador de miles.

**Dice el profesor:** “Las ventas y las unidades se suman. Los pedidos se cuentan como identificadores diferentes, porque una compra puede tener varias líneas”.

**Explicación:** `SUM` suma valores numéricos; `DISTINCTCOUNT` cuenta identificadores diferentes. Una medida responde al contexto de fecha y categoría de cada visual. [F9] [F19]

**Resultado esperado:** aparecen tres medidas con el icono de calculadora. Sin filtros producirán **203.723.865,76** en ventas, **628.370** unidades y **83.130** pedidos; para 2020 producirán **11.066.206,89**, **35.234** y **4.806**, respectivamente.

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### Paso 3.3. Construir las tarjetas de resumen

**Para qué:** responder Q1 con los indicadores del período.

**Hace el profesor en `Resumen comercial`:**

1. Selecciona **Tarjeta** y agrega únicamente `Ventas Totales` en **Valores**.
2. Titula **Ventas de Contoso (moneda base)**.
3. En Formato, busca el valor destacado o valor de llamada y ajusta **Unidades de visualización: Ninguno** y dos decimales.
4. Crea otra tarjeta con `Unidades Totales`, título **Unidades**, sin decimales.
5. Crea una tercera con `Pedidos`, título **Pedidos distintos**, sin decimales.
6. Coloca las tres tarjetas alineadas en la parte superior. [F10]

**Resultado de referencia, una vez aplicado el filtro del paso 3.7:**

| Tarjeta | Resultado para 2020 y todas las categorías |
| --- | --- |
| Ventas de Contoso (moneda base) | **11.066.206,89** |
| Unidades | **35.234** |
| Pedidos distintos | **4.806** |

**Dice el profesor:** “Estas tarjetas responden cuánto se vendió, cuántos artículos se vendieron y cuántas compras diferentes hubo”.

**Explicación:** desactivar la abreviación en miles o millones permite comparar las cifras con la solución. El número de líneas de 2020, **11.267**, no es el número de pedidos.

**Resultado esperado:** cada tarjeta contiene su medida y un título que permite identificarla.

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### Paso 3.4. Construir y leer la tabla por categoría

**Para qué:** responder Q3 y contrastar las cifras de cada categoría.

**Hace el profesor:**

1. Inserta una **Tabla** y añade `Categoria`, `Ventas Totales`, `Unidades Totales` y `Pedidos`.
2. Activa **Totales** en el formato del visual.
3. Ordena por `Ventas Totales` de mayor a menor; luego por `Unidades Totales` para comparar la clasificación.
4. Titula **Ventas, unidades y pedidos por categoría**. [F11]

**Tabla resuelta para 2020, todas las categorías, ordenada por ventas:**

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

**Lee la respuesta:** Computers lidera tanto en ventas como en unidades: **5.041.111,59** y **8.364 unidades**. Por tanto, la respuesta a Q3 es **sí**.

**Muestra otra comparación:** Music, Movies and Audio Books ocupa el **tercer lugar en unidades**, con **5.147**, y el **sexto en ventas**, con **672.661,83**. Las dos clasificaciones no son idénticas.

**Dice el profesor:** “En este caso coincide la categoría líder, pero el orden cambia en otras categorías. Más unidades no implica siempre más dinero vendido”.

**Explicación:** un pedido puede incluir productos de distintas categorías. Power BI recalcula el total general como pedidos distintos; **no suma los pedidos de las ocho categorías**. Los importes se suman con su precisión original y después se muestran con dos decimales. Por eso, sumar las ocho cifras monetarias ya redondeadas de la tabla da 11.066.206,90; el total calculado antes de redondear es **11.066.206,89**.

**Resultado esperado:** el total de ventas y unidades coincide con las tarjetas, y el total general de pedidos es **4.806**.

[Volver al índice](#indice)

<a id="paso-3-5"></a>
### Paso 3.5. Construir y leer el gráfico de barras

**Para qué:** responder Q2 con una comparación visual de categorías.

**Hace el profesor:**

1. Inserta un **Gráfico de barras agrupadas**.
2. Coloca `Categoria` en **Eje Y** y `Ventas Totales` en **Eje X**.
3. En el menú del visual, ordena por `Ventas Totales` de mayor a menor.
4. Activa **Etiquetas de datos** y utiliza un eje monetario que comience en cero.
5. Titula **¿Qué categoría tiene el mayor importe de ventas?** y usa un color consistente. [F12]

**Respuesta resuelta para 2020:** la barra mayor es **Computers, 5.041.111,59**. Le sigue **Cell phones, 1.861.564,14**.

**Dice el profesor:** “Computers tiene la mayor barra. La tabla permite comprobar su cifra exacta; las barras permiten reconocer su posición frente a las otras categorías”.

**Explicación:** las barras horizontales facilitan comparar magnitudes y leer nombres largos. La cifra corresponde a ventas en moneda base, no a utilidad.

**Resultado esperado:** el orden de las barras coincide con el de la tabla cuando está ordenada por ventas.

[Volver al índice](#indice)

<a id="paso-3-6"></a>
### Paso 3.6. Construir y leer la línea mensual

**Para qué:** responder Q4 manteniendo el orden temporal.

**Hace el profesor:**

1. Inserta un **Gráfico de líneas**.
2. Agrega `InicioMes` al **Eje X**; si aparece una jerarquía de fecha, cambia al campo `InicioMes` directamente.
3. Agrega `Ventas Totales` al **Eje Y**.
4. Ordena por `InicioMes` en sentido ascendente. Utiliza el formato mes y año y, si está disponible, un eje de fecha continuo.
5. Titula **¿Cómo cambian las ventas mensuales de Contoso?**.
6. Pasa el cursor por febrero, marzo y septiembre para leer sus importes. [F13]

**Serie mensual resuelta, para contrastar los puntos una vez filtrado 2020:**

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

El total utiliza los importes originales. La suma de los doce valores mensuales ya redondeados da 11.066.206,88; la diferencia de 0,01 se debe al redondeo de presentación.

**Lee la respuesta:** febrero registra el máximo, **2.658.349,04**; septiembre registra el mínimo, **320.093,34**. Entre febrero y marzo las ventas disminuyen **58,46 %**.

**Muestra el cálculo de esa variación en una calculadora:**

```text
((Ventas de marzo − Ventas de febrero) ÷ Ventas de febrero) × 100
((1.104.361,281484 − 2.658.349,037698) ÷ 2.658.349,037698) × 100
= −58,46 %
```

La variación está resuelta con los importes originales; este primer reporte solo necesita las tres medidas del paso 3.2.

**Dice el profesor:** “La línea muestra una caída entre febrero y marzo. Podemos describirla y medirla; los datos analizados todavía no explican su causa”.

**Explicación:** ordenar los meses por ventas destruiría la secuencia temporal. El calendario de 2020 está disponible; el extremo final del archivo completo, abril de 2024, tiene otra cobertura.

**Resultado esperado:** la línea contiene 12 puntos mensuales de 2020, en orden, y reproduce los valores de la tabla de referencia.

[Volver al índice](#indice)

<a id="paso-3-7"></a>
### Paso 3.7. Filtrar el año 2020 y todas las categorías

**Para qué:** aplicar exactamente el alcance de las respuestas resueltas.

**Hace el profesor en `Resumen comercial`:**

1. Agrega una **Segmentación de datos** con `Fecha`, usando el campo directamente.
2. Configúrala en modo **Entre** y escribe **01/01/2020** como inicio y **31/12/2020** como fin.
3. Agrega otra segmentación con `Categoria`, como lista o desplegable.
4. Deja todas las categorías incluidas. Una segmentación de categoría sin selección específica incluye todas.
5. Revisa que el panel **Filtros** no tenga restricciones adicionales. [F21]

**Muestra el resultado:** las tres tarjetas deben indicar **11.066.206,89**, **35.234** y **4.806**. La tabla contiene ocho categorías y la línea contiene los 12 meses de 2020.

**Dice el profesor:** “Ahora todas las preguntas se responden con el mismo período y las mismas categorías”.

**Explicación:** estos filtros pertenecen a la página `Resumen comercial`. Se conserva el historial en Power Query y se deja `Comprobacion` sin filtro de año, para poder revisar los dos pedidos de 2015. No se sincroniza la segmentación de fecha con esa página ni se aplica 2020 como filtro de todo el informe.

**Resultado esperado:** las cifras comerciales coinciden con la solución de 2020 y el alcance es visible.

[Volver al índice](#indice)

<a id="paso-3-8"></a>
### Paso 3.8. Comprobar las interacciones con una selección resuelta

**Para qué:** demostrar que las medidas y los visuales responden al mismo filtro.

**Hace el profesor:**

1. Mantiene el año 2020 y selecciona **Computers** en la segmentación de categoría.
2. Comprueba estas tarjetas:

| Selección | Ventas, moneda base | Unidades | Pedidos distintos |
| --- | ---: | ---: | ---: |
| 2020, Computers | **5.041.111,59** | **8.364** | **2.094** |
| 2020, todas las categorías | **11.066.206,89** | **35.234** | **4.806** |

3. Comprueba que la tabla y la línea se ajusten también a Computers.
4. Si un visual no cambia, selecciona la segmentación y usa **Formato → Editar interacciones**; activa **Filtrar** sobre ese visual. [F22]
5. Borra la selección de categoría y comprueba que se recuperen los totales de 2020.

**Dice el profesor:** “Al filtrar Computers contamos los pedidos que contienen líneas de esa categoría. Cuando volvemos a todas las categorías, obtenemos las cifras del año completo”.

**Explicación:** un visual puede tener su propia restricción o una interacción desactivada. Una comparación válida exige revisar la misma selección en todos los visuales.

**Resultado esperado:** las dos selecciones producen exactamente los valores de la tabla de prueba. La página queda en 2020 y todas las categorías.

[Volver al índice](#indice)

<a id="paso-3-9"></a>
### Paso 3.9. Demostrar la comprobación manual de dos pedidos

**Para qué:** demostrar que el reporte se puede verificar con transacciones reales.

**Hace el profesor en `Comprobacion`:**

1. Crea una tabla con `Pedido`, `Linea`, `Fecha`, `Categoria`, `Unidades`, `PrecioUnitario` e `ImporteVenta`. Puede añadir `Producto` para identificar el artículo.
2. Selecciona **No resumir** para las columnas del detalle donde esté disponible. Conserva siempre `Pedido` y `Linea`.
3. Muestra `PrecioUnitario` e `ImporteVenta` con al menos tres decimales, para revisar esta selección sin ocultar milésimas.
4. Agrega una segmentación por `Pedido` y tarjetas con las tres medidas.
5. Comprueba que esta página y los filtros de todo el informe permitan el **01/01/2015** y todas las categorías.
6. Selecciona los pedidos **1000 y 1001** completos. Desactiva selección única y usa selección múltiple o Ctrl si hace falta.
7. Muestra las tres líneas siguientes y realiza la cuenta en una calculadora.

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

**Comparación que muestra el profesor:**

| Indicador | Resultado manual | Resultado que debe mostrar Power BI | Diferencia esperada |
| --- | ---: | ---: | ---: |
| Ventas, mostradas con dos decimales | 867,50 | 867,50 | 0,00 |
| Unidades | 4 | 4 | 0 |
| Pedidos distintos | 2 | 2 | 0 |

**Dice el profesor:** “La cuenta manual reproduce las tarjetas. El resultado depende de incluir todas las líneas de los pedidos y de conservar la precisión del precio”.

**Explicación:** esta prueba usa dos pedidos de 2015 exclusivamente para comprobar el método. Las conclusiones comerciales siguen correspondiendo al año 2020. Si una tarjeta no coincide, se revisan filtros, líneas, tipos de datos y duplicados antes de continuar.

**Resultado esperado:** tres líneas, cuatro unidades, dos pedidos y ventas de **867,50**; la comprobación queda visible en el reporte.

[Volver al índice](#indice)

---

<a id="fase-4"></a>
## Fase 4. E: Ejecutar y comunicar

**Propósito:** transformar las cifras comprobadas en una comunicación útil y establecer el seguimiento.

**Producto de la fase:** respuestas finales, storytelling resuelto y documentación de la demostración.

<a id="paso-4-1"></a>
### Paso 4.1. Presentar las respuestas resueltas a las preguntas

**Para qué:** responder las preguntas de negocio con evidencia identificable.

**Hace el profesor:** vuelve a `Resumen comercial`, restablece **2020 y todas las categorías**, y presenta las siguientes respuestas. En `PACE y hallazgos`, usa **Insertar → Cuadro de texto** para registrarlas.

| Pregunta | Respuesta resuelta | Evidencia |
| --- | --- | --- |
| Q1. ¿Cuál fue el volumen comercial? | Ventas de **11.066.206,89** en moneda base, **35.234 unidades** y **4.806 pedidos distintos**. | Las tres tarjetas. |
| Q2. ¿Qué categoría tuvo más ventas? | **Computers**, con **5.041.111,59**, equivalentes al **45,55 %** del total de 2020. | Barras y tabla por categoría. |
| Q3. ¿Coincide el liderazgo en unidades y ventas? | **Sí**. Computers lidera en ambos: **8.364 unidades** y **5.041.111,59**. Music, Movies and Audio Books cambia de puesto: tercero en unidades y sexto en ventas. | Tabla ordenada sucesivamente por ventas y por unidades. |
| Q4. ¿Cómo cambiaron las ventas mensuales? | Febrero es el máximo, **2.658.349,04**; septiembre es el mínimo, **320.093,34**. Entre febrero y marzo las ventas caen **58,46 %**, hasta **1.104.361,28**. | Línea mensual y sus valores. |
| ¿Cómo se comprobó un resultado? | Los pedidos 1000 y 1001 dan **867,50**, **4 unidades** y **2 pedidos**; son la muestra de comprobación del 01/01/2015. | Detalle y cálculo manual de `Comprobacion`. |

**Muestra el cálculo resuelto de la participación de Computers:**

```text
(Ventas de Computers en 2020 ÷ Ventas de todas las categorías en 2020) × 100
(5.041.111,58625 ÷ 11.066.206,893045) × 100 = 45,55 %
```

**Dice el profesor:** “Para calcular una participación uso el total del mismo año y de todas las categorías. Si dividiera entre el total ya filtrado en Computers, obtendría una respuesta distinta”.

**Explicación:** la participación y la variación mensual se calculan aquí con una calculadora a partir de los resultados del reporte y su precisión original. El ejemplo mantiene las tres medidas básicas y deja resueltas las dos operaciones adicionales.

**Resultado esperado:** Q1–Q4 tienen respuestas completas, con cifras, período y visual de respaldo.

[Volver al índice](#indice)

<a id="paso-4-2"></a>
### Paso 4.2. Comunicar el storytelling resuelto

**Para qué:** presentar un hallazgo comprensible que conecte contexto, evidencia y acción.

**Hace el profesor:** inserta en `PACE y hallazgos` el título **Computers lidera las ventas de 2020; la caída de marzo merece revisión** y el siguiente relato resuelto.

> En Contoso, durante 2020 y considerando todas las categorías, las ventas fueron 11.066.206,89 en moneda base, con 35.234 unidades y 4.806 pedidos distintos. Computers fue la categoría líder: registró 5.041.111,59, equivalentes al 45,55 % de las ventas del año, y 8.364 unidades. La evolución mensual muestra un máximo de 2.658.349,04 en febrero y una caída del 58,46 % en marzo, hasta 1.104.361,28. Estos resultados permiten priorizar la revisión de Computers y del cambio entre febrero y marzo. Propongo contrastar ese cambio con el detalle de productos, las acciones comerciales y la disponibilidad de inventario antes de decidir ajustes. El análisis describe una empresa ficticia; no demuestra las causas de la caída ni permite concluir sobre utilidad neta.

**Explica cómo está construido el relato:**

| Parte | Contenido del ejemplo |
| --- | --- |
| Contexto | Contoso, año 2020, todas las categorías, moneda base. |
| Hallazgo principal | Computers lidera y concentra el 45,55 % de las ventas. |
| Evidencia temporal complementaria | Entre febrero y marzo las ventas caen 58,46 %. |
| Interpretación | La categoría y el cambio mensual merecen una revisión comercial. |
| Acción propuesta | Revisar detalle de productos y contrastar con acciones comerciales e inventario. |
| Límite | El análisis descriptivo no demuestra causas ni utilidad neta. |

**Dice el profesor:** “El dato es la cifra observada. Priorizar su revisión es una interpretación y una propuesta. Una causa requiere evidencia adicional”.

**Explicación:** el relato tiene una idea central y utiliza solo los visuales que la sostienen. El texto conserva el alcance de 2020 y todas las categorías; al presentarlo se restablece esa selección.

**Resultado esperado:** el storytelling está escrito y resuelto, listo para que el profesor lo lea o adapte a su forma de explicar.

[Volver al índice](#indice)

<a id="paso-4-3"></a>
### Paso 4.3. Presentar la acción propuesta y el seguimiento

**Para qué:** orientar una conversación comercial y establecer qué se revisará después.

**Hace el profesor:** presenta el hallazgo en esta secuencia.

1. **Pregunta:** “¿Qué categoría merece una revisión comercial y qué cambio mensual requiere atención?”.
2. **Contexto:** muestra el filtro 2020 y todas las categorías, y las tarjetas de **11.066.206,89**, **35.234 unidades** y **4.806 pedidos**.
3. **Evidencia principal:** muestra las barras y señala Computers; contrasta su importe en la tabla.
4. **Evidencia temporal:** señala febrero y marzo en la línea y explica la caída del **58,46 %**.
5. **Confiabilidad:** muestra brevemente la comprobación manual de los dos pedidos.
6. **Propuesta:** presenta el seguimiento de la tabla.

| Elemento | Seguimiento propuesto en el ejercicio |
| --- | --- |
| Prioridad | Revisar la composición de ventas de Computers y el cambio de febrero a marzo de 2020. |
| Siguiente análisis con Contoso | Desglosar ventas y unidades por producto y mes, manteniendo las mismas definiciones. |
| Información adicional | Registro de acciones comerciales y disponibilidad de inventario; costos si se pretende evaluar rentabilidad. |
| Responsable propuesto | Analista comercial, con revisión de la dirección comercial. |
| Momento propuesto | Próxima reunión mensual de revisión comercial del caso. |
| Evidencia para el seguimiento | Comparación de categorías, productos y meses con cobertura equivalente. |
| Criterio de cierre | Poder explicar qué productos contribuyen al cambio y qué evidencia adicional respalda una decisión. |

**Dice el profesor:** “El reporte ya indica qué revisar. El siguiente análisis debe aportar el detalle y la evidencia que hacen falta para decidir una acción comercial”.

**Explicación:** los responsables, la reunión y la acción son una propuesta didáctica. Los datos de ventas sustentan el hallazgo; no contienen una decisión comercial ya ejecutada.

**Resultado esperado:** la presentación conecta pregunta, evidencia, interpretación y siguiente paso.

[Volver al índice](#indice)

<a id="paso-4-4"></a>
### Paso 4.4. Comprobar la reproducción del resultado

**Para qué:** comprobar que otra persona pueda reconstruir el resultado.

**Hace el profesor:** añade esta documentación en `PACE y hallazgos`.

| Elemento | Documentación de la solución de referencia |
| --- | --- |
| Fuente | SQLBI, Contoso V2, Ready to use data (2024), `csv-100k.7z`. |
| Archivos utilizados | `sales.csv` y `product.csv`. |
| Fecha de cálculo de las referencias | 08/10/2026. |
| Nivel de detalle | Una fila por `Pedido` + `Linea`. |
| Combinación | Externa izquierda por `ProductKey`; expandir nombre y categoría. |
| Importe por línea | `Quantity × NetPrice`, conservando la precisión del precio. |
| Ventas | Suma de `ImporteVenta`, en moneda base. |
| Unidades | Suma de `Unidades`. |
| Pedidos | `DISTINCTCOUNT` de `Pedido`. |
| Filtro comercial | 01/01/2020 a 31/12/2020, todas las categorías. |
| Filtro de comprobación | Pedidos 1000 y 1001 completos, 01/01/2015. |

**Realiza la comprobación de reproducción:**

1. Guarda `Demo_01_Contoso_PACE.pbix`, ciérralo y vuelve a abrirlo.
2. Comprueba que existan las tres páginas y las medidas.
3. Usa **Inicio → Actualizar**, manteniendo los CSV en su ubicación.
4. Reaplica 2020 y todas las categorías; contrasta las tarjetas con **11.066.206,89**, **35.234** y **4.806**.
5. Revisa la muestra de comprobación: **867,50**, **4** y **2**.
6. Registra en el reporte el resultado de esa comprobación cuando se haya realizado en Power BI.

**Dice el profesor:** “La solución debe poder repetirse con la fuente y los pasos registrados”.

**Explicación:** el `.pbix` conserva datos importados, pero actualizar requiere acceso a los CSV. Si cambia la carpeta, se corrige **Configuración de origen de datos → Cambiar origen**, o el paso **Origen** de la consulta, y se vuelve a comprobar.

**Resultado esperado:** la reapertura y actualización del archivo del profesor mantienen los valores de referencia.

[Volver al índice](#indice)

<a id="paso-4-5"></a>
### Paso 4.5. Cerrar la demostración y mostrar el producto esperado

**Para qué:** mostrar cómo se reconoce una solución completa.

**Hace el profesor:** presenta el archivo `Demo_01_Contoso_PACE.pbix` y recorre estas evidencias.

| Evidencia | Producto esperado de la demostración |
| --- | --- |
| Planeación | Problema, destinatario, Q1–Q4, alcance y criterios de éxito definidos. |
| Preparación | Una tabla `Ventas` de 11 columnas, con tipos correctos y 199.873 líneas; controles coherentes. |
| Resumen comercial | Tres tarjetas, tabla por categoría, barras, línea mensual y segmentaciones de fecha y categoría. |
| Respuesta de 2020 | **11.066.206,89** en ventas, **35.234 unidades** y **4.806 pedidos**. |
| Interacciones | Computers produce **5.041.111,59**, **8.364 unidades** y **2.094 pedidos**. |
| Comprobación | Dos pedidos reales: **867,50**, **4 unidades**, **2 pedidos**, sin diferencia con el cálculo manual. |
| Storytelling | Relato resuelto del paso 4.2 con evidencia, interpretación y acción. |
| Reproducción | Fuente y fórmulas documentadas; comprobación de apertura y actualización realizada por el profesor. |

**Cierre que dice el profesor:** “Este fue el ejemplo completo con Contoso. En su trabajo aplicarán la misma secuencia: formular una pregunta, reconocer los datos que la responden, preparar y comprobar esos datos, escoger un gráfico útil y comunicar un hallazgo sustentado”.

**Explicación:** los estudiantes transfieren el método al caso que se les asignó. Deben obtener sus propias respuestas a partir de sus datos; las cifras de Contoso sirven para entender y comprobar esta demostración.

**Resultado esperado:** el grupo ha visto una solución completa y entiende qué procedimiento aplicar.

[Volver al índice](#indice)

---

<a id="valores-control"></a>
## Valores de control del archivo completo

Estos valores corresponden al paquete **`csv-100k.7z`, publicación 2024**, recalculado para esta guía el **8 de octubre de 2026**. Se utilizan antes de filtrar 2020 o al revisar la preparación.

| Control | Archivo completo, sin filtros | Año 2020, todas las categorías |
| --- | ---: | ---: |
| Líneas de venta | 199.873 | 11.267 |
| Unidades | 628.370 | 35.234 |
| Pedidos distintos | 83.130 | 4.806 |
| Ventas en moneda base, dos decimales | 203.723.865,76 | 11.066.206,89 |

- Fecha mínima del archivo: **01/01/2015**. Fecha máxima: **20/04/2024**.
- Importe exacto del archivo completo: **203.723.865,758428**.
- Importe exacto de 2020: **11.066.206,893045**.
- Definición de importe: `Quantity × NetPrice` por línea.

**Cómo los usa el profesor:** compara primero sin filtros, después selecciona 2020. Si las unidades coinciden y el importe cambia, revisa la conversión decimal de `NetPrice`. Si aumentan las filas tras combinar, revisa la unicidad de `ProductKey`.

El archivo termina el 20 de abril de 2024. Las comparaciones entre períodos requieren tener en cuenta esa cobertura. Las cifras de referencia de esta guía corresponden al paquete indicado; otras versiones de Contoso pueden dar valores distintos.

[Volver al índice](#indice)

<a id="dificultades"></a>
## Solución de dificultades frecuentes

| Dificultad durante la demo | Causa a revisar | Corrección |
| --- | --- | --- |
| El CSV aparece en una sola columna. | Delimitador. | Seleccionar coma en la importación o en el paso Origen. |
| Los encabezados dicen Column1, Column2. | Encabezados sin promover. | Usar la primera fila como encabezado. |
| El precio da error o un valor muy distinto. | Interpretación del punto decimal. | Convertir NetPrice desde el texto con Inglés (Estados Unidos). |
| Las fechas se interpretan incorrectamente. | Tipo o configuración regional. | Convertir OrderDate desde el valor año-mes-día y comprobar 2015-01-01. |
| Las ventas aumentan al expandir productos. | Claves de producto repetidas. | Revisar ControlProductos y corregir la combinación. |
| La categoría aparece con espacios al final. | Texto original. | Aplicar Recortar antes de combinar. |
| Los pedidos de 2020 aparecen como 11.267. | Conteo de filas. | Usar DISTINCTCOUNT: el valor correcto es 4.806. |
| Los meses mezclan diferentes años. | Se usa únicamente el nombre de mes. | Usar InicioMes directamente, sin jerarquía, y ordenar cronológicamente. |
| Las tarjetas muestran valores abreviados. | Unidades de visualización automáticas. | Elegir Ninguno para comparar la cifra completa. |
| Un visual no cambia con la categoría. | Interacción o filtro propio del visual. | Revisar Editar interacciones y el panel Filtros. |
| La muestra de 2015 aparece vacía. | Filtro 2020 aplicado a todo el informe o sincronizado. | Dejar el filtro de fecha en Resumen comercial y liberar Comprobacion. |
| La cuenta manual no coincide. | Filtros, líneas incompletas o redondeo previo. | Incluir los dos pedidos completos y conservar las milésimas. |
| El total de pedidos no coincide con la suma por categorías. | Un pedido incluye distintas categorías. | Interpretar el total como pedidos distintos del contexto general. |
| No se puede actualizar. | CSV movidos o ruta de origen distinta. | Corregir el origen y repetir la actualización. |

[Volver al índice](#indice)

<a id="fuentes"></a>
## Fuentes oficiales y documentación

Consulta realizada el **8 de octubre de 2026**. El caso, las preguntas, la elección didáctica de visuales y el seguimiento propuesto son parte de esta demostración. Las cifras resueltas se obtuvieron de los CSV originales de Contoso; las instrucciones técnicas se apoyan en estas fuentes.

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
- **[F20]** Microsoft Learn. [Función DIVIDE](https://learn.microsoft.com/es-es/dax/divide-function-dax).
- **[F21]** Microsoft Learn. [Segmentaciones de datos](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-slicers).
- **[F22]** Microsoft Learn. [Interacciones entre objetos visuales](https://learn.microsoft.com/es-es/power-bi/create-reports/service-reports-visual-interactions).
- **[F23]** Microsoft Learn. [Date.Year](https://learn.microsoft.com/es-es/powerquery-m/date-year) y [Date.StartOfMonth](https://learn.microsoft.com/es-es/powerquery-m/date-startofmonth).

[Volver al índice](#indice)

