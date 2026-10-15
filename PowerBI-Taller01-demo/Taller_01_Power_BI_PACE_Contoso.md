# Taller resuelto 01. Reporte comercial de Contoso en Power BI

**Metodología PACE. Análisis descriptivo de ventas de 2020.**

Contoso es una empresa ficticia. Su dirección comercial necesita conocer el volumen vendido, la categoría líder y los cambios mensuales. La solución utiliza `sales.csv` y `product.csv` del paquete `csv-100k.7z`, conserva sus nombres de campo y mantiene las dos tablas relacionadas por `ProductKey`.

<a id="roadmap"></a>
## Ruta de construcción y validación

El trabajo comienza en la **[fase 1. Planear](#fase-1)**, donde se [define el problema y el alcance del análisis (1.1)](#seccion-1-1) y se [relaciona cada pregunta de negocio con los campos y visuales necesarios (1.2)](#seccion-1-2). Así queda establecido qué se necesita responder, para quién y durante qué período. Estas decisiones orientan la preparación de los datos en la siguiente fase.

Con ese propósito definido, la **[fase 2. Analizar](#fase-2)** comienza al [descargar y reconocer los datos (2.1)](#seccion-2-1). Antes de cargarlos, se [configura su interpretación regional (2.2)](#seccion-2-2); después se [importan los CSV y se conservan los campos necesarios (2.3)](#seccion-2-3). La preparación continúa al [revisar separadores, tipos y precisión (2.4)](#seccion-2-4) y [crear el importe por línea y el mes de análisis (2.5)](#seccion-2-5). Una vez [comprobada la calidad y cargadas las tablas (2.6)](#seccion-2-6), se [verifica su relación por ProductKey (2.7)](#seccion-2-7). El resultado es una base preparada para calcular indicadores sin trasladar errores de importación a las cifras del reporte.

A partir de esa base, la **[fase 3. Construir](#fase-3)** transforma los datos en respuestas. Primero se [crean las medidas y se revisa el total sin filtros (3.1)](#seccion-3-1), se [define la paleta y la distribución del tablero (3.2)](#seccion-3-2) y se [preparan las páginas y los filtros (3.3)](#seccion-3-3). Después, el volumen comercial se muestra en [tres tarjetas para Q1 (3.4)](#seccion-3-4), el liderazgo por categoría en [barras para Q2 (3.5)](#seccion-3-5), la comparación de ventas y unidades en [una tabla para Q3 (3.6)](#seccion-3-6) y la evolución temporal en [una línea mensual para Q4 (3.7)](#seccion-3-7). La construcción se comprueba al [probar la relación y las interacciones (3.8)](#seccion-3-8) y [reconstruir manualmente dos pedidos (3.9)](#seccion-3-9). Los resultados se contrastan con los [totales de control](#controles) antes de llevarlos a la presentación.

Con los resultados verificados, la **[fase 4. Ejecutar y explicar](#fase-4)** comienza al [consolidar las respuestas (4.1)](#seccion-4-1). La evidencia se organiza siguiendo [un orden de presentación con storytelling (4.2)](#seccion-4-2) y se comunica mediante [el relato del hallazgo (4.3)](#seccion-4-3), manteniendo las cifras vinculadas al período y a los filtros visibles. Finalmente, se [propone una acción y se guarda la solución (4.4)](#seccion-4-4). El recorrido conecta así la necesidad comercial inicial con una respuesta sustentada y deja planteadas las preguntas que pueden orientar un nuevo ciclo de análisis.

<a id="controles"></a>
## Totales de control para comenzar

Estas referencias se recalcularon sobre los CSV del taller el **15 de octubre de 2026**. Se contrastan al construir las medidas y aplicar los filtros. Los importes corresponden a `Quantity × NetPrice`, en moneda base del generador. [1] [5]

| Selección | Líneas de venta | Unidades | Pedidos distintos | Ventas, dos decimales |
| --- | ---: | ---: | ---: | ---: |
| Archivo completo, sin filtros. | 199.873 | 628.370 | 83.130 | 203.723.865,76 |
| 2020, todas las categorías. | 11.267 | 35.234 | 4.806 | 11.066.206,89 |
| Pedidos 1000 y 1001 completos, de 2015. | 3 | 4 | 2 | 867,50 |

<img width="557" height="332" alt="image" src="https://github.com/user-attachments/assets/d3bcb2bb-d552-4aae-9691-262c19b30cdf" />

<img width="119" height="59" alt="image" src="https://github.com/user-attachments/assets/56f8d221-28eb-45dd-a7fe-46898908628b" />


<img width="131" height="77" alt="image" src="https://github.com/user-attachments/assets/1afb760b-193e-4bf4-a8c7-86dc7fde3baa" />


`product.csv` contiene **2.517 productos**, con `ProductKey` único. El historial de ventas abarca del **01/01/2015 al 20/04/2024**. Las cifras de esta guía se muestran con **punto de miles y coma decimal**; la apariencia puede variar según la configuración regional.

**Por qué validar los totales de control**

Un total de control es una referencia calculada de forma independiente sobre los mismos datos y con la misma definición. En este taller procede de los CSV originales. Comparar las medidas del reporte con esa referencia permite detectar errores antes de trasladarlos a gráficos y conclusiones. La referencia se usa para contrastar el cálculo; no se escribe como un valor fijo en las tarjetas.

| Resultado de la comparación | Qué conviene revisar |
| --- | --- |
| Unidades y pedidos coinciden, pero ventas no. | Interpretación decimal de `NetPrice`, uso del precio correcto y redondeos anticipados. |
| Aparecen 11.267 pedidos de 2020 en lugar de 4.806. | Se están contando líneas; el indicador debe contar valores distintos de `OrderKey`. |
| El total completo coincide, pero el resultado de 2020 no. | Conversión de fechas, período seleccionado y filtros de página, informe o visual. |
| El total anual coincide, pero las categorías muestran cifras incorrectas. | Relación por `ProductKey`, procedencia de `CategoryName` e interacciones. |


La coincidencia del total general, por sí sola, no demuestra que cada categoría o mes esté bien calculado: dos errores podrían compensarse. Por eso la solución también contrasta los resultados por categoría y mes, prueba una selección y reconstruye dos pedidos.

**Criterio para avanzar:** comparar siempre el mismo paquete, período, selección y fórmula. Los conteos deben coincidir exactamente y los importes deben coincidir a dos decimales, conservando la precisión original durante el cálculo. Si hay una diferencia, localizar su causa, corregirla y repetir la comparación antes de presentar el hallazgo. Así se evita comunicar cifras incorrectas con una apariencia de exactitud.

<a id="indice"></a>
## Tabla de contenido por fases PACE

PACE corresponde a *Plan, Analyze, Construct, Execute*. La secuencia del taller se organiza en las cuatro fases siguientes. [2]

| Fase / apartado | Contenido |
| --- | --- |
| **[Fase 1. Planear](#fase-1)** | **Necesidad, preguntas y alcance.** |
| [1.1](#seccion-1-1) | [Definir el problema y el alcance](#seccion-1-1) |
| [1.2](#seccion-1-2) | [Relacionar cada pregunta con sus campos y visuales](#seccion-1-2) |
| **[Fase 2. Analizar](#fase-2)** | **Datos preparados y relacionados.** |
| [2.1](#seccion-2-1) | [Descargar y reconocer los datos](#seccion-2-1) |
| [2.2](#seccion-2-2) | [Configurar los datos antes de importarlos](#seccion-2-2) |
| [2.3](#seccion-2-3) | [Importar los CSV y conservar los campos](#seccion-2-3) |
| [2.4](#seccion-2-4) | [Resolver separadores, tipos y precisión](#seccion-2-4) |
| [2.5](#seccion-2-5) | [Crear las dos columnas calculadas](#seccion-2-5) |
| [2.6](#seccion-2-6) | [Comprobar calidad y cargar las tablas](#seccion-2-6) |
| [2.7](#seccion-2-7) | [Conservar y verificar la relación automática](#seccion-2-7) |
| **[Fase 3. Construir](#fase-3)** | **Indicadores y visuales comprobados.** |
| [3.1](#seccion-3-1) | [Crear las medidas y revisar el total sin filtros](#seccion-3-1) |
| [3.2](#seccion-3-2) | [Definir la paleta y la distribución del tablero](#seccion-3-2) |
| [3.3](#seccion-3-3) | [Preparar las páginas y los filtros](#seccion-3-3) |
| [3.4](#seccion-3-4) | [Resolver Q1 con tres tarjetas](#seccion-3-4) |
| [3.5](#seccion-3-5) | [Resolver Q2 con barras horizontales](#seccion-3-5) |
| [3.6](#seccion-3-6) | [Resolver Q3 con una tabla comparativa](#seccion-3-6) |
| [3.7](#seccion-3-7) | [Resolver Q4 con una línea mensual](#seccion-3-7) |
| [3.8](#seccion-3-8) | [Comprobar la relación y las interacciones](#seccion-3-8) |
| [3.9](#seccion-3-9) | [Crear Comprobacion y reconstruir dos pedidos](#seccion-3-9) |
| **[Fase 4. Ejecutar y explicar](#fase-4)** | **Hallazgos y siguiente acción.** |
| [4.1](#seccion-4-1) | [Consolidar las respuestas](#seccion-4-1) |
| [4.2](#seccion-4-2) | [Seguir un orden de presentación con storytelling](#seccion-4-2) |
| [4.3](#seccion-4-3) | [Presentar el relato resuelto](#seccion-4-3) |
| [4.4](#seccion-4-4) | [Cerrar con una acción y guardar](#seccion-4-4) |

**Consulta complementaria**

- [Ruta de construcción y validación](#roadmap).
- [Totales de control y criterios de validación](#controles).
- [Dificultades frecuentes](#dificultades).
- [Encabezados completos de los CSV](#encabezados).
- [Fuentes](#fuentes).

<a id="fase-1"></a>
## Fase 1. Planear

Esta fase inicia el trabajo a partir de la necesidad de la dirección comercial planteada en el caso. Define qué se quiere conocer, para quién, con qué alcance y mediante cuáles preguntas. Esta planeación orienta la fase siguiente, **Analizar**, donde se revisará si los datos disponibles permiten responder esas preguntas y se preparará la información necesaria.

<a id="seccion-1-1"></a>
### 1.1. Definir el problema y el alcance

| Elemento | Definición del caso |
| --- | --- |
| Destinatario | Dirección comercial de Contoso. |
| Necesidad | Describir la actividad y priorizar una revisión comercial posterior. |
| Objetivo | Comparar ventas, unidades y pedidos por categoría y mes. |
| Período | 01/01/2020–31/12/2020, todas las categorías. |
| Producto | Reporte con dos páginas: `Resumen comercial` y `Comprobacion`. |
| Criterio de éxito | Responder las cuatro preguntas, coincidir con los controles y comunicar un hallazgo sustentado. |

<a id="seccion-1-2"></a>
### 1.2. Relacionar cada pregunta con sus campos y visuales

| Código y pregunta | Campos originales necesarios | Visual previsto |
| --- | --- | --- |
| Q1. ¿Cuáles fueron las ventas, unidades y pedidos de 2020? | `sales[OrderDate]`, `sales[Quantity]`, `sales[NetPrice]`, `sales[OrderKey]`. | Tres tarjetas: ventas, unidades y pedidos distintos. |
| Q2. ¿Qué categoría tuvo mayores ventas? | `product[CategoryName]`, `sales[ProductKey]`, `sales[Quantity]`, `sales[NetPrice]`, `sales[OrderDate]`. | Barras horizontales ordenadas por ventas. |
| Q3. ¿La categoría líder en ventas también lideró en unidades? | `product[CategoryName]`, `sales[ProductKey]`, `sales[Quantity]`, `sales[NetPrice]`, `sales[OrderDate]`; `sales[OrderKey]` para mostrar pedidos. | Tabla por categoría con ventas y unidades en columnas separadas. |
| Q4. ¿Cómo cambiaron las ventas mensuales? | `sales[OrderDate]`, `sales[Quantity]`, `sales[NetPrice]`. | Línea mensual, con mes y año en el eje temporal. |

`ImporteVenta` e `InicioMes` se crearán en 2.5. Las medidas y la asignación exacta de campos a cada visual se desarrollan en la fase 3.

[Volver al índice](#indice)

<a id="fase-2"></a>
## Fase 2. Analizar

A partir de las preguntas y el alcance definidos en **Planear**, esta fase examina y prepara los datos de ventas y productos. Se configuran la importación y los tipos, se revisa la calidad y se valida la relación entre las tablas. El resultado permite pasar a **Construir** con una base coherente para calcular indicadores y contrastarlos con los totales de control.

<a id="seccion-2-1"></a>
### 2.1. Descargar y reconocer los datos

1. Descargar [csv-100k.7z](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z), de la publicación 2024 de Contoso V2 de SQLBI. [1]
2. Extraerlo en una carpeta estable, por ejemplo `Taller_01_PowerBI/Contoso`, con una herramienta compatible con `.7z`.
3. Localizar `sales.csv` y `product.csv`.

En `sales.csv`, cada fila representa una **línea de pedido**. `OrderKey` identifica el pedido, `LineNumber` identifica la línea y `Quantity` indica sus unidades. Un pedido puede ocupar varias filas. `product.csv` describe los productos.

<a id="seccion-2-2"></a>
### 2.2. Configurar los datos antes de importarlos

Abrir un archivo nuevo de Power BI Desktop y entrar en **Archivo → Opciones y configuración → Opciones → Archivo actual → Configuración regional**.

| Opción | Selección para este taller | Qué controla |
| --- | --- | --- |
| Configuración regional para la importación. | **Inglés (Estados Unidos)**. | Interpretación de los textos del CSV al convertirlos a números y fechas. [19] |
| Configuración regional predeterminada de la cadena de formato para fechas y números, cuando esté disponible. | **Español (Colombia)**. | Presentación de números y fechas en los visuales. No cambia el valor importado. [20] |
| Unidades de visualización predeterminadas en “none”, cuando esté disponible. | Activar para mostrar cifras completas. | Evita abreviaturas automáticas como K o M donde no se hayan fijado unidades explícitas. [20] |

Aceptar y continuar con la importación. La opción de **importación** y la de **formato visible** tienen funciones distintas: se puede leer el punto decimal del origen y presentar después una coma decimal.

<a id="seccion-2-3"></a>
### 2.3. Importar los CSV y conservar los campos

1. Seleccionar **Inicio → Obtener datos → Texto/CSV** y abrir `sales.csv`.
2. Elegir delimitador **coma** y, si aparece la opción, **no detectar tipos de datos**. Seleccionar **Transformar datos**. [3]
3. En Power Query, nombrar la consulta `sales`. Seleccionar con **Ctrl** sus seis campos de la tabla siguiente y usar **Quitar otras columnas**.
4. Desde **Inicio → Nuevo origen → Texto/CSV**, importar `product.csv`, nombrar la consulta `product` y conservar sus tres campos indicados.

Si aparecen `Column1`, `Column2`, etc., aplicar **Usar la primera fila como encabezado**. Si todo aparece en una columna, corregir el delimitador.

**Diccionario de los campos utilizados:**

| Consulta | Campo exacto del CSV | Significado | Tipo que se asigna |
| --- | --- | --- | --- |
| `sales` | `OrderKey` | Identificador del pedido. | Texto |
| `sales` | `LineNumber` | Número de línea dentro del pedido. | Número entero |
| `sales` | `OrderDate` | Fecha del pedido. | Fecha |
| `sales` | `ProductKey` | Identificador del producto vendido. | Número entero |
| `sales` | `Quantity` | Unidades vendidas en la línea. | Número entero |
| `sales` | `NetPrice` | Precio neto **unitario**, después del descuento. | Número decimal |
| `product` | `ProductKey` | Identificador único del producto. | Número entero |
| `product` | `ProductName` | Nombre del producto. | Texto |
| `product` | `CategoryName` | Nombre de la categoría. | Texto |

**Conservar los nombres originales.** `ProductKey` debe tener el mismo tipo en ambas consultas. Para las claves, usar **No resumir** cuando se utilicen como identificadores en los visuales.

<a id="seccion-2-4"></a>
### 2.4. Resolver separadores, tipos y precisión

**Conversión utilizada en la solución:**

1. Asignar los tipos del diccionario mediante el icono de cada columna.
2. En `sales[NetPrice]`, aplicar **Cambiar tipo → Usar configuración regional → Número decimal → Inglés (Estados Unidos)**. Dejar esta conversión explícita facilita reproducirla en otros equipos. [4]
3. Convertir `OrderDate` a **Fecha**. Comprobar que `2015-01-01` representa el **1 de enero de 2015**.
4. En `product`, seleccionar `ProductName` y `CategoryName` y aplicar **Transformar → Formato → Recortar**. Esto elimina el espacio final de `Cameras and camcorders `.

Si existe un paso automático **Tipo cambiado** que ya interpretó mal el número, eliminarlo y volver a convertir **desde el texto original**. Cambiar solo el formato de un valor incorrecto no recupera el dato.

**Opciones para manejar los separadores:**

| Opción | Cuándo sirve | Efecto y cuidado |
| --- | --- | --- |
| Configuración regional de importación antes de cargar, como en 2.2. | El origen utiliza una convención común. | Define la interpretación general; no reemplaza pasos de conversión con otra región explícita. [19] |
| Usar configuración regional por columna, como en este apartado. | Hay fuentes con convenciones distintas o se quiere una conversión reproducible. | Interpreta el texto según la región elegida. En este CSV, `NetPrice` requiere punto decimal. [4] |
| Formato regional del informe y formato de la medida. | El número ya está bien importado y se quiere cambiar su apariencia. | Puede mostrar `1.234,56` en lugar de `1,234.56`; no corrige cálculos ni valores mal interpretados. [20] |
| Configuración regional de Windows. | Se necesita cambiar la convención general del equipo. | Puede afectar otras aplicaciones y nuevos archivos. Para un único CSV, es preferible fijar la región del archivo o de la columna. [19] |
| Reemplazar punto por coma en una columna de texto. | Alternativa limitada a un formato conocido y sin separadores de miles. | Después hay que convertir con una región compatible. Puede corromper valores que mezclen ambos símbolos; no reemplazar símbolos en todo el CSV, porque la coma también delimita columnas. |

**Cómo un error puede alterar el resultado:**

- El texto `98.967` equivale a **98,967**. Si una transformación elimina el punto y lo convierte en `98967` (noventa y ocho mil novecientos sesenta y siete), ese precio queda multiplicado por 1.000. Según el texto y la región, también pueden aparecer errores de conversión.
- Un error en `NetPrice` puede dejar correctos los conteos de pedidos y unidades, pero producir ventas y comparaciones equivocadas.
- Dar formato de dos decimales cambia la apariencia. **Redondear el precio antes de multiplicar cambia el cálculo.** En los pedidos 1000 y 1001, redondear previamente los precios a dos decimales produce **867,51**, frente al resultado correcto **867,50**.

`NetPrice` contiene hasta seis decimales en este paquete. Mantener **Número decimal**. **Número decimal fijo** admite cuatro decimales y puede alterar los resultados de esta fuente. [4]

**Separadores de fórmulas DAX:** se configuran aparte, en **Archivo → Opciones y configuración → Opciones → Global → Configuración regional → Separadores DAX**. Los predeterminados usan punto decimal y coma entre argumentos; los localizados siguen Windows. Esto afecta la escritura de fórmulas, no la lectura del CSV. Las tres medidas de este taller tienen un solo argumento y no requieren cambiar su separador. [19]

Por ejemplo, `DIVIDE(1, 2)` utiliza coma entre argumentos; en una configuración localizada con punto y coma se escribe `DIVIDE(1; 2)`. Ambas expresiones representan la misma operación.

<a id="seccion-2-5"></a>
### 2.5. Crear las dos columnas calculadas

En la consulta `sales`, seleccionar **Agregar columna → Columna personalizada** para cada definición. [7]

| Columna nueva | Fórmula de Power Query | Tipo posterior |
| --- | --- | --- |
| `ImporteVenta` | `[Quantity] * [NetPrice]` | Número decimal |
| `InicioMes` | `Date.StartOfMonth([OrderDate])` | Fecha |

`ImporteVenta` calcula el importe de la línea. `InicioMes` transforma, por ejemplo, `15/11/2020` en `01/11/2020`, conservando mes y año para ordenar el gráfico. [8]

El cálculo utiliza `sales[NetPrice]`, precio neto unitario. `sales[UnitPrice]` es el precio antes del descuento y `product[Price]` es el precio del catálogo del generador. No son intercambiables. Los importes de esta guía se presentan en moneda base del generador, sin etiquetarlos como COP o como moneda local del cliente. [5]

<a id="seccion-2-6"></a>
### 2.6. Comprobar calidad y cargar las tablas

| Control | Resultado verificado |
| --- | ---: |
| Filas de `sales` / filas de `product`. | 199.873 / 2.517 |
| Claves `ProductKey` repetidas en `product`. | 0 |
| Combinaciones `OrderKey` + `LineNumber` repetidas en `sales`. | 0 |
| Vacíos en los seis campos seleccionados de `sales`. | 0 |
| Ventas cuyo `ProductKey` no existe en `product`. | 0 |
| Nombres de producto o categoría vacíos en las ventas. | 0 |
| Valores de `Quantity` o `NetPrice` menores o iguales a cero. | 0 |

Para revisar vacíos y errores, activar **Ver → Calidad de columna** y cambiar el perfilado al **conjunto de datos completo**. [9] Para contar filas o buscar duplicados, crear **Referencias** auxiliares: usar **Contar filas**, o **Agrupar por → Recuento de filas** y filtrar recuentos mayores que 1. En líneas, agrupar por `OrderKey` y `LineNumber`. [10]

Mantener **Habilitar carga** en `sales` y `product`, desactivarla en las consultas auxiliares y seleccionar **Cerrar y aplicar**.

**Estructura final:** `sales` conserva sus seis campos originales y añade `ImporteVenta` e `InicioMes`: **8 columnas**. `product` conserva `ProductKey`, `ProductName` y `CategoryName`: **3 columnas**.

<a id="seccion-2-7"></a>
### 2.7. Conservar y verificar la relación automática

Power BI puede detectar la relación al cargar las tablas. Si la crea correctamente, **se conserva**; no es necesario combinar consultas ni crear otra relación. La detección automática no garantiza que siempre se cree la relación esperada. [17]

1. Abrir la vista **Modelo** y revisar la línea entre `product` y `sales`.
2. Abrir sus propiedades y comprobar esta configuración:

| Propiedad | Configuración requerida |
| --- | --- |
| Tabla y columna del lado uno. | `product[ProductKey]`. |
| Tabla y columna del lado varios. | `sales[ProductKey]`. |
| Cardinalidad. | Uno a varios (`1:*`); o varios a uno (`*:1`) si el diálogo presenta `sales` primero. |
| Relación activa. | Sí. |
| Dirección de filtro cruzado. | Única, desde `product` hacia `sales`. |

3. Si la línea no existe, usar **Administrar relaciones → Nuevo** y configurar esos mismos campos y propiedades.
4. Si Power BI propone varios a varios, revisar duplicados y tipos de `ProductKey`; no aceptar esa cardinalidad para resolver el aviso.

**Por qué relacionar en lugar de combinar en este caso:**

| Relación entre tablas, utilizada aquí | Combinación en Power Query, alternativa |
| --- | --- |
| Conserva los productos separados de las líneas de venta. | Añade nombre y categoría a cada línea al expandir la combinación. |
| La categoría filtra las ventas mediante `ProductKey`. | Los visuales utilizan los atributos de la tabla resultante. |
| Distingue atributos descriptivos de datos que se agregan. | Permite trabajar con una sola tabla en una introducción centrada en visuales. |

Trabajar con una sola tabla puede simplificar una introducción centrada en visuales, pero no hay una necesidad técnica de combinar estos dos archivos. Aquí se mantiene `product` como dimensión y `sales` como tabla de hechos, en línea con la orientación de Microsoft. Combinar sigue siendo útil para enriquecer una tabla cuando el diseño lo requiere; una unión izquierda debe conservar el detalle y controlar claves repetidas. [18] [6]

[Volver al índice](#indice)

<a id="fase-3"></a>
## Fase 3. Construir

Con las tablas preparadas y relacionadas en **Analizar**, esta fase transforma los datos en medidas y visuales que responden las preguntas de negocio. Se aplica el diseño del tablero y se comprueban cálculos, filtros y resultados frente a las referencias. Los hallazgos verificados se llevan a **Ejecutar y explicar**, donde se organizarán para su presentación y uso.

<a id="seccion-3-1"></a>
### 3.1. Crear las medidas y revisar el total sin filtros

Seleccionar `sales` y usar **Modelado → Nueva medida** para cada definición. [11]

```dax
Ventas Totales = SUM('sales'[ImporteVenta])
```

```dax
Unidades Totales = SUM('sales'[Quantity])
```

```dax
Pedidos = DISTINCTCOUNT('sales'[OrderKey])
```

Dar a `Ventas Totales` formato numérico con dos decimales y separador de miles; a las otras medidas, cero decimales. **No modificar la precisión de `NetPrice`** para conseguir ese formato.

`SUM` agrega importes y cantidades. `DISTINCTCOUNT` cuenta pedidos diferentes; contar filas daría líneas. [12] Al crear las tarjetas de 3.4, contrastar primero los totales **sin filtros** con la primera fila de los controles iniciales.

<a id="seccion-3-2"></a>
### 3.2. Definir la paleta y la distribución del tablero

Esta paleta es una propuesta para el caso. Se aplica de forma consistente y con pocos colores de énfasis.

| Uso | Color | Código hexadecimal |
| --- | --- | --- |
| Fondo de página. | Gris muy claro. | `#F4F7FB` |
| Fondo de tarjetas y gráficos. | Blanco. | `#FFFFFF` |
| Títulos y texto principal. | Azul oscuro. | `#172B4D` |
| Serie de ventas mensuales. | Azul. | `#1F5A85` |
| Categoría destacada: Computers. | Verde azulado. | `#007F7A` |
| Resto de categorías y texto secundario. | Gris azulado. | `#5F6F84` |
| Anotación sobre la caída de marzo. | Ámbar oscuro. | `#B45309` |

1. Abrir **Personalizar tema** desde los comandos de temas de la cinta **Diseño**. En interfaces anteriores, revisar **Vista → Temas → Personalizar tema actual**. Definir colores y una fuente común, como Segoe UI. [21]
2. Aplicar fondos claros, texto oscuro, títulos consistentes y espacios entre visuales. Como referencia de diseño, usar título de página de 22–24 puntos, cifras de tarjetas de 28–32 y etiquetas de al menos 11–12; comprobar su lectura al proyectar.
3. Usar barras grises y destacar Computers en verde azulado, acompañado de su nombre y cifra. Mantener la línea mensual azul y añadir una anotación ámbar para marzo. No asignar un color distinto a cada categoría ni usar solo el color para comunicar el hallazgo. [22]

| Zona de `Resumen comercial` | Contenido y orden de lectura |
| --- | --- |
| Encabezado. | Título, período y fuente breve. |
| Franja superior. | Segmentaciones y tres tarjetas. |
| Zona central izquierda. | Ranking de ventas por categoría, Q2. |
| Zona central derecha. | Evolución mensual, Q4. |
| Franja inferior. | Tabla comparativa de ventas y unidades, Q3. |

El tablero orienta la mirada; la exposición oral seguirá el orden narrativo de 4.2. El relato completo se presenta oralmente o en el documento, sin llenar el tablero de párrafos.

<a id="seccion-3-3"></a>
### 3.3. Preparar las páginas y los filtros

1. Nombrar la primera página `Resumen comercial`.
2. Agregar una segmentación con `sales[OrderDate]`, **sin jerarquía**, en modo **Entre**. Durante la comprobación inicial dejar todo el historial; después establecer **01/01/2020–31/12/2020**.
3. Agregar una segmentación de `product[CategoryName]`, en lista o desplegable, con todas las categorías incluidas. [13]

El filtro de fecha corresponde a esta página. No convertirlo en filtro de todo el informe ni sincronizarlo con `Comprobacion`, cuya muestra es de 2015.

<a id="seccion-3-4"></a>
### 3.4. Resolver Q1 con tres tarjetas

| Pregunta | Visual | Campo en Valores | Resultado de 2020 |
| --- | --- | --- | ---: |
| ¿Cuánto se vendió? | Tarjeta. | `[Ventas Totales]`, medida de `sales`. | 11.066.206,89 |
| ¿Cuántas unidades? | Tarjeta. | `[Unidades Totales]`, medida de `sales`. | 35.234 |
| ¿Cuántos pedidos? | Tarjeta. | `[Pedidos]`, medida de `sales`. | 4.806 |

Crear las tres tarjetas. Con todo el historial deben coincidir con los controles iniciales; al aplicar 2020 deben mostrar los valores de esta tabla.

Usar títulos “Ventas en moneda base”, “Unidades” y “Pedidos distintos”. Establecer **Unidades de visualización → Ninguno** en cada tarjeta si el ajuste general no se aplica. La ruta depende de la tarjeta: **Formato → Visual → Valores del destacador → Valores**, o **Valor destacado**. [14]

<a id="seccion-3-5"></a>
### 3.5. Resolver Q2 con barras horizontales

| Elemento | Asignación exacta |
| --- | --- |
| Visual. | Barras agrupadas horizontales. |
| Eje Y. | `product[CategoryName]`. |
| Eje X. | `[Ventas Totales]`. |
| Orden. | Ventas de mayor a menor. |
| Presentación. | Eje monetario desde cero, etiquetas legibles y Computers destacado. |
| Título. | “Computers lidera las ventas de 2020”. |

**Respuesta:** Computers, con **5.041.111,59**. La segunda categoría es Cell phones, con **1.861.564,14**. Si se cambia el período, ajustar el título para que siga correspondiendo a la selección.

<a id="seccion-3-6"></a>
### 3.6. Resolver Q3 con una tabla comparativa

| Elemento | Asignación exacta |
| --- | --- |
| Visual. | Tabla. |
| Columnas. | `product[CategoryName]`, `[Ventas Totales]`, `[Unidades Totales]`, `[Pedidos]`. |
| Orden inicial. | Descendente por ventas. Después, ordenar por unidades para comparar posiciones. |
| Totales. | Mantener el cálculo predeterminado de las medidas. |

**Tabla resuelta por categoría para 2020:**

| CategoryName | Ventas Totales | Unidades Totales | Pedidos |
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

**Lectura:** Computers lidera en ventas y unidades. Music, Movies and Audio Books es tercera en unidades y sexta en ventas; una clasificación por volumen físico no equivale a otra por importe.

El total general de pedidos es un conteo distinto. Un pedido puede contener varias categorías, por lo que **4.806 no debe sustituirse por la suma de los conteos de cada categoría**. [12]

<a id="seccion-3-7"></a>
### 3.7. Resolver Q4 con una línea mensual

| Elemento | Asignación exacta |
| --- | --- |
| Visual. | Gráfico de líneas. |
| Eje X. | `sales[InicioMes]`, directamente y sin jerarquía. |
| Eje Y. | `[Ventas Totales]`. |
| Orden. | Cronológico ascendente; mostrar mes y año. |
| Presentación. | Línea azul, marcadores y una anotación: “Marzo: −58,46 % frente a febrero”. |
| Título. | “Evolución mensual de las ventas de 2020”. |

La línea conserva la secuencia temporal. Evitar un eje que ordene alfabéticamente nombres de meses o mezcle años. [15]

**Serie mensual resuelta:**

| InicioMes | Ventas Totales |
| --- | ---: |
| 01/01/2020 | 2.124.215,68 |
| 01/02/2020 | 2.658.349,04 |
| 01/03/2020 | 1.104.361,28 |
| 01/04/2020 | 502.924,03 |
| 01/05/2020 | 1.160.430,61 |
| 01/06/2020 | 808.351,16 |
| 01/07/2020 | 593.685,68 |
| 01/08/2020 | 524.873,87 |
| 01/09/2020 | 320.093,34 |
| 01/10/2020 | 388.181,27 |
| 01/11/2020 | 341.405,95 |
| 01/12/2020 | 539.334,97 |
| **Total 2020** | **11.066.206,89** |

Febrero registra el máximo y septiembre el mínimo. El cambio de febrero a marzo se cuantifica en la fase 4.

**Redondeo de presentación:** sumar categorías ya redondeadas da 11.066.206,90; sumar meses ya redondeados da 11.066.206,88. El total con los valores originales es **11.066.206,89**. No redondear líneas para forzar la suma de cifras visibles.

<a id="seccion-3-8"></a>
### 3.8. Comprobar la relación y las interacciones

Con 2020 activo, seleccionar **Computers** en la segmentación de `product[CategoryName]`:

| Selección | Ventas Totales | Unidades Totales | Pedidos |
| --- | ---: | ---: | ---: |
| Computers. | 5.041.111,59 | 8.364 | 2.094 |
| Todas las categorías. | 11.066.206,89 | 35.234 | 4.806 |

La selección debe filtrar los indicadores y los gráficos mediante la relación. Si se repite el total general en cada categoría, revisar primero la relación activa por `ProductKey`. Si falla solo un visual, revisar **Formato → Editar interacciones → Filtrar**. [16] Borrar la selección de categoría antes de presentar el resultado general.

<a id="seccion-3-9"></a>
### 3.9. Crear `Comprobacion` y reconstruir dos pedidos

1. Agregar la página `Comprobacion`.
2. Crear una tabla con `sales[OrderKey]`, `sales[LineNumber]`, `sales[OrderDate]`, `product[CategoryName]`, `sales[Quantity]`, `sales[NetPrice]` y `sales[ImporteVenta]`.
3. Usar **No resumir** donde esté disponible y mostrar precio e importe con al menos tres decimales.
4. Añadir las tres tarjetas y una segmentación de `sales[OrderKey]` con selección múltiple. Elegir **1000 y 1001**, sin otros filtros.

**Detalle real de la selección, del 1 de enero de 2015:**

| OrderKey | LineNumber | CategoryName | Quantity | NetPrice | ImporteVenta |
| --- | --- | --- | ---: | ---: | ---: |
| 1000 | 0 | Audio | 1 | 98,967 | 98,967 |
| 1000 | 1 | Computers | 1 | 659,780 | 659,780 |
| 1001 | 0 | Games and Toys | 2 | 54,376 | 108,752 |

**Comprobación manual resuelta:**

- Ventas: `(1 × 98,967) + (1 × 659,780) + (2 × 54,376) = 867,499`, mostrado como **867,50**.
- Unidades: `1 + 1 + 2 = 4`.
- Pedidos distintos: **2**.

Las tarjetas deben coincidir. La selección contiene tres líneas, cuatro unidades y dos pedidos. Este ejemplo también permite revisar que el precio conserve su precisión.

[Volver al índice](#indice)

<a id="fase-4"></a>
## Fase 4. Ejecutar y explicar

Esta fase retoma los indicadores y visuales verificados en **Construir** para responder las preguntas y presentar un relato sustentado. Se ordena la evidencia, se explican los límites del análisis y se propone una acción comercial. Con ello se cierra el taller; las nuevas preguntas que surjan de esa acción pueden iniciar otro ciclo de **Planear**, ahora con un objetivo de análisis más específico.

<a id="seccion-4-1"></a>
### 4.1. Consolidar las respuestas

Restaurar **2020 y todas las categorías** en `Resumen comercial`.

| Pregunta | Respuesta resuelta |
| --- | --- |
| Q1. Ventas, unidades y pedidos. | **11.066.206,89** en moneda base, **35.234 unidades** y **4.806 pedidos**. |
| Q2. Categoría con mayores ventas. | **Computers**, con **5.041.111,59**, el **45,55 %** del total anual. |
| Q3. Coincidencia del liderazgo. | **Sí**. Computers también lidera en unidades, con **8.364**. |
| Q4. Evolución mensual. | Máximo en **febrero: 2.658.349,04**. Mínimo en **septiembre: 320.093,34**. De febrero a marzo, caída de **58,46 %**. |

Comprobaciones con precisión original:

- Participación de Computers: `(5.041.111,58625 ÷ 11.066.206,893045) × 100 = 45,55 %`.
- Variación febrero-marzo: `((1.104.361,281484 − 2.658.349,037698) ÷ 2.658.349,037698) × 100 = −58,46 %`.

<a id="seccion-4-2"></a>
### 4.2. Seguir un orden de presentación con storytelling

El orden narrativo propone **contexto breve, mensaje principal, evidencia y acción**. Se presenta el hallazgo antes de recorrer sus pruebas; los detalles de importación quedan para preguntas técnicas.

| Orden | Qué se presenta | Visual o apoyo | Qué se dice y para qué |
| --- | --- | --- | --- |
| 1 | Contexto y pregunta comercial. | Título y filtro de fecha. | “Analizamos Contoso en 2020 para reconocer el volumen, la categoría líder y los cambios mensuales”. Delimita el alcance. |
| 2 | Mensaje principal. | Encabezado breve o exposición oral. | “Computers aporta el 45,55 % de las ventas; la caída de marzo requiere revisión”. Orienta la lectura. |
| 3 | Tamaño de la actividad, Q1. | Tres tarjetas. | Presentar ventas, unidades y pedidos. Da magnitud al caso. |
| 4 | Evidencia del liderazgo, Q2 y Q3. | Barras y luego tabla. | Mostrar Computers y contrastar ventas con unidades. Explica qué se está comparando. |
| 5 | Cambio temporal, Q4. | Línea y anotación de marzo. | Mostrar febrero, marzo y la variación de −58,46 %. Expone el cambio que merece análisis. |
| 6 | Interpretación, límite y siguiente acción. | Cierre oral y anotación breve. | Revisar contribuciones por producto y categoría, y contrastar inventario y acciones comerciales. Los datos no demuestran causas. |

No atribuir la caída a Computers por el solo hecho de liderar el año. Esa contribución debe analizarse por separado. La página `Comprobacion` respalda la validez del cálculo y se muestra después del relato si se solicita el detalle.

<a id="seccion-4-3"></a>
### 4.3. Presentar el relato resuelto

> Analizamos las ventas de Contoso en 2020 para identificar su volumen comercial y los cambios que merecen revisión. Computers fue la categoría líder: aportó el 45,55 % de las ventas. En el año se vendieron 11.066.206,89 en moneda base, con 35.234 unidades y 4.806 pedidos. El ranking confirma que Computers también lideró en unidades. La evolución mensual muestra otro hallazgo: después del máximo de febrero, las ventas cayeron un 58,46 % en marzo. Proponemos identificar qué productos y categorías contribuyeron a esa variación y contrastar el resultado con información comercial e inventario. El caso utiliza datos ficticios y no demuestra la causa de la caída.

Este relato y los títulos con cifras corresponden a **2020, todas las categorías**. Si cambia la selección durante la demostración, restaurarla antes de retomar el relato o ajustar lo que se comunica.

<a id="seccion-4-4"></a>
### 4.4. Cerrar con una acción y guardar

| Elemento | Propuesta del caso |
| --- | --- |
| Acción. | Descomponer la diferencia febrero-marzo por producto y categoría. |
| Responsable propuesto. | Analista comercial, con revisión de la dirección comercial. |
| Información adicional. | Disponibilidad de inventario y acciones comerciales del período. |
| Próxima revisión. | Reunión mensual del caso, con resultados comparables y limitaciones explícitas. |

Guardar `Taller_01_Contoso_PACE.pbix` con las dos tablas relacionadas, las tres medidas, las dos páginas y los filtros de presentación restaurados. Conservar los CSV en su ubicación de origen.

[Volver al índice](#indice)

<a id="dificultades"></a>
## Dificultades frecuentes

| Situación | Corrección |
| --- | --- |
| Todo el CSV aparece en una columna. | Establecer delimitador coma. |
| `NetPrice` cambia de valor o genera errores. | Eliminar la conversión equivocada y convertir el texto original con Inglés (Estados Unidos). |
| Cambia el separador visible pero las ventas siguen mal. | El formato no corrige el valor importado. Revisar Power Query. |
| No aparece `CategoryName` dentro de `sales`. | Es correcto: se usa `product[CategoryName]` mediante la relación. |
| Cada categoría muestra el mismo importe total. | Revisar relación activa, campos `ProductKey` y dirección del filtro. |
| Se propone una relación varios a varios. | Revisar unicidad de `product[ProductKey]` y tipos de ambas claves. |
| Aparecen 11.267 pedidos en 2020. | Se contaron líneas. Usar `DISTINCTCOUNT('sales'[OrderKey])`; son 4.806 pedidos. |
| Tarjetas abreviadas con K o M. | Unidades de visualización: Ninguno. |
| Meses mezclados o desordenados. | Usar `sales[InicioMes]` directamente y ordenar cronológicamente. |
| `Comprobacion` queda vacía. | Quitar el filtro 2020 de nivel informe y evitar sincronizarlo con esa página. |
| El relato no coincide con los visuales. | Restaurar 2020 y todas las categorías o actualizar el relato. |

<a id="encabezados"></a>
## Encabezados completos de los CSV

Nombres y orden exactos del paquete revisado. El taller conserva únicamente los campos del diccionario de 2.3.

**`sales.csv`, 13 campos:**

`OrderKey`, `LineNumber`, `OrderDate`, `DeliveryDate`, `CustomerKey`, `StoreKey`, `ProductKey`, `Quantity`, `UnitPrice`, `NetPrice`, `UnitCost`, `CurrencyCode`, `ExchangeRate`.

**`product.csv`, 14 campos:**

`ProductKey`, `ProductCode`, `ProductName`, `Manufacturer`, `Brand`, `Color`, `WeightUnit`, `Weight`, `Cost`, `Price`, `CategoryKey`, `CategoryName`, `SubCategoryKey`, `SubCategoryName`.

`ImporteVenta` e `InicioMes` son columnas de Power Query en `sales`. `Ventas Totales`, `Unidades Totales` y `Pedidos` son medidas DAX alojadas en `sales`. `ProductName` y `CategoryName` permanecen en `product`.

<a id="fuentes"></a>
## Fuentes

Consulta: **15 de octubre de 2026**. Los resultados se calcularon directamente desde los CSV. La paleta, el orden narrativo y la acción propuesta son decisiones didácticas del caso.

1. [Repositorio del taller y paquete utilizado](https://github.com/adanbeltran/Labs/tree/main/PowerBI-Taller01-demo). Fuente del conjunto: [SQLBI, Contoso V2, publicación 2024](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/tag/ready-to-use-data-2024).
2. Google Career Certificates. [Foundations of Data Science: flujo PACE](https://www.coursera.org/learn/foundations-of-data-science).
3. Microsoft Learn. [Conector Texto/CSV](https://learn.microsoft.com/es-es/power-query/connectors/text-csv).
4. Microsoft Learn. [Tipos de datos y configuración regional](https://learn.microsoft.com/es-es/power-query/data-types).
5. SQLBI. [Código del generador: precios y construcción de las líneas de venta](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/Engine.cs).
6. Microsoft Learn. [Combinación externa izquierda](https://learn.microsoft.com/es-es/power-query/merge-queries-left-outer).
7. Microsoft Learn. [Agregar una columna personalizada](https://learn.microsoft.com/es-es/power-query/add-custom-column).
8. Microsoft Learn. [Date.StartOfMonth](https://learn.microsoft.com/es-es/powerquery-m/date-startofmonth).
9. Microsoft Learn. [Herramientas de perfilado de datos](https://learn.microsoft.com/es-es/power-query/data-profiling-tools).
10. Microsoft Learn. [Agrupar filas](https://learn.microsoft.com/es-es/power-query/group-by).
11. Microsoft Learn. [Medidas en Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-measures).
12. Microsoft Learn. [SUM](https://learn.microsoft.com/es-es/dax/sum-function-dax) y [DISTINCTCOUNT](https://learn.microsoft.com/es-es/dax/distinctcount-function-dax).
13. Microsoft Learn. [Segmentaciones de datos](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-slicers).
14. Microsoft Learn. [Formato de tarjetas](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-card) y [unidades de visualización](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-custom-format-strings).
15. Microsoft Learn. [Gráficos de líneas](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-line-chart).
16. Microsoft Learn. [Interacciones entre visuales](https://learn.microsoft.com/es-es/power-bi/create-reports/service-reports-visual-interactions).

17. Microsoft Learn. [Crear, detectar y administrar relaciones](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-create-and-manage-relationships).
18. Microsoft Learn. [Tablas de hechos, dimensiones y esquema de estrella](https://learn.microsoft.com/es-es/power-bi/guidance/star-schema).
19. Microsoft Learn. [Configuración regional de importación y separadores DAX](https://learn.microsoft.com/es-es/power-bi/fundamentals/supported-languages-countries-regions).
20. Microsoft Learn. [Configuración regional del formato de fechas y números del informe](https://learn.microsoft.com/es-es/power-bi/create-reports/power-bi-default-format-string-locale).
21. Microsoft Learn. [Personalizar el tema del informe](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-report-themes).
22. Microsoft Learn. [Accesibilidad, color y legibilidad de los informes](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-accessibility-creating-reports).

[Volver al índice](#indice)
