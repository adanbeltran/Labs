# Taller resuelto 01. Reporte comercial de Contoso en Power BI

**Metodología PACE | Análisis descriptivo de ventas de 2020**

Contoso es una empresa ficticia. La dirección comercial necesita conocer el importe vendido, las unidades, los pedidos distintos, la categoría líder y los cambios mensuales. Se utilizan `sales.csv` y `product.csv` del paquete de datos indicado en las [fuentes](#fuentes). Este es un taller demostrativo resuelto: las cifras de referencia sirven para validar los cálculos y no deben copiarse manualmente en tarjetas.

## Modalidad de trabajo en cada fase

| Fase PACE | ¿Se utiliza Power BI? | Propósito | Producto |
| --- | --- | --- | --- |
| **1. Planear** | **No** | Comprender el problema y establecer preguntas de negocio. | Alcance y preguntas justificadas. |
| **2. Analizar** | **No** | Comprender el conjunto de datos, su estructura, precisión, indicadores y controles. | Interpretación del modelo y riesgos de calidad. |
| **3. Construir** | **Sí** | Importar, limpiar, transformar, modelar, calcular y visualizar. | Informe funcional y validado. |
| **4. Ejecutar y explicar** | **Sí** | Utilizar el tablero, comprobar filtros, comunicar resultados y guardar. | Archivo `.pbix` final y hallazgos sustentados. |

**Regla metodológica:** las fases 1 y 2 son exclusivamente de lectura, discusión y razonamiento. La descarga de los archivos y todas las operaciones sobre Power BI comienzan en la fase 3. La fase 4 utiliza la solución construida para presentar los resultados; no introduce cálculos improvisados.

<a id="indice"></a>
## Índice hipervinculado por fases y pasos

| Paso | Actividad | Modalidad |
|:---|:---|:---|
| [Fase 1. Planear](#fase-1) | 1. Planear | Comprensión |
| [1.1 Contexto y alcance](#paso-1-1) | Contexto y alcance | Comprensión |
| [1.2 Preguntas y visuales](#paso-1-2) | Preguntas y visuales | Comprensión |
| [Fase 2. Analizar](#fase-2) | 2. Analizar | Comprensión |
| [2.1 Granularidad](#paso-2-1) | Granularidad | Comprensión |
| [2.2 Relaciones](#paso-2-2) | Relaciones | Comprensión |
| [2.3 Precisión](#paso-2-3) | Precisión | Comprensión |
| [2.4 Totales de control](#paso-2-4) | Totales de control | Comprensión |
| [Fase 3. Construir](#fase-3) | 3. Construir | Power BI |
| [3.1 Configuración inicial](#paso-3-1) | Configuración inicial | Power BI |
| [3.1.1 Descargar y organizar archivos](#paso-3-1-1) | Descargar y organizar archivos | Power BI |
| [3.1.2 Localizar Obtener datos](#paso-3-1-2) | Localizar Obtener datos | Power BI |
| [3.1.3 Configuración regional](#paso-3-1-3) | Configuración regional | Power BI |
| [3.2 Calidad de datos – Power Query](#paso-3-2) | Calidad de datos – Power Query | Power BI |
| [3.2.1 Importar CSV](#paso-3-2-1) | Importar CSV | Power BI |
| [3.2.2 Tipos de datos](#paso-3-2-2) | Tipos de datos | Power BI |
| [3.2.3 Perfil de calidad](#paso-3-2-3) | Perfil de calidad | Power BI |
| [3.2.4 Conteos y duplicados](#paso-3-2-4) | Conteos y duplicados | Power BI |
| [3.2.5 Columnas M](#paso-3-2-5) | Columnas M | Power BI |
| [3.2.6 Cerrar y aplicar](#paso-3-2-6) | Cerrar y aplicar | Power BI |
| [3.3 Modelo de datos](#paso-3-3) | Modelo de datos | Power BI |
| [3.3.1 Relación ProductKey](#paso-3-3-1) | Relación ProductKey | Power BI |
| [3.3.2 M vs DAX](#paso-3-3-2) | M vs DAX | Power BI |
| [3.3.3 Medidas DAX](#paso-3-3-3) | Medidas DAX | Power BI |
| [3.3.4 Totales de control](#paso-3-3-4) | Totales de control | Power BI |
| [3.4 Visualizaciones](#paso-3-4) | Visualizaciones | Power BI |
| [3.4.1 Diseño de página](#paso-3-4-1) | Diseño de página | Power BI |
| [3.4.2 Segmentaciones](#paso-3-4-2) | Segmentaciones | Power BI |
| [3.4.3 Tarjetas](#paso-3-4-3) | Tarjetas | Power BI |
| [3.4.4 Barras](#paso-3-4-4) | Barras | Power BI |
| [3.4.5 Tabla comparativa](#paso-3-4-5) | Tabla comparativa | Power BI |
| [3.4.6 Línea mensual](#paso-3-4-6) | Línea mensual | Power BI |
| [3.4.7 Interacciones](#paso-3-4-7) | Interacciones | Power BI |
| [3.4.8 Comprobación de pedidos](#paso-3-4-8) | Comprobación de pedidos | Power BI |
| [Fase 4. Ejecutar y explicar](#fase-4) | 4. Ejecutar y explicar | Informe y comunicación |
| [4.1 Restaurar filtros](#paso-4-1) | Restaurar filtros | Informe |
| [4.2 Storytelling](#paso-4-2) | Storytelling | Informe |
| [4.3 Hallazgos](#paso-4-3) | Hallazgos | Informe |
| [4.4 Guardar](#paso-4-4) | Guardar | Informe |

## Recorrido de las cuatro fases

La **[fase 1. Planear](#fase-1)** establece el problema comercial, las preguntas que importan y los resultados esperados, sin ejecutar acciones técnicas. Define el sentido del trabajo antes de examinar herramientas.

La **[fase 2. Analizar](#fase-2)** estudia las tablas y su nivel de detalle, qué hace cada campo, qué cálculos son legítimos y cómo reconocer errores de interpretación. Todo sigue siendo conceptual; conocer estos riesgos será indispensable al operar Power BI.

La **[fase 3. Construir](#fase-3)** implementa el análisis en Power BI Desktop. Se organiza en configuración inicial (3.1), calidad y transformación en Power Query (3.2), modelo y medidas DAX (3.3), y visualizaciones con sus comprobaciones (3.4). Cada subpaso especifica la ruta de menú y el resultado esperado.

La **[fase 4. Ejecutar y explicar](#fase-4)** utiliza el informe validado para responder las preguntas en orden narrativo, mostrar evidencia, explicar límites y proponer una revisión posterior. Se conserva la selección de filtros correcta y se guarda el PBIX final.

<a id="controles"></a>
## Totales de control del conjunto de referencia

Estas cifras corresponden a la versión original del paquete `csv-100k.7z` utilizada por el taller. Los importes se calculan como `Quantity × NetPrice` con toda la precisión disponible, y se muestran con dos decimales.

| Selección | Líneas | Unidades | Pedidos distintos | Ventas |
| --- | ---: | ---: | ---: | ---: |
| Historial completo sin filtros | 199.873 | 628.370 | 83.130 | 203.723.865,76 |
| 2020, todas las categorías | 11.267 | 35.234 | 4.806 | 11.066.206,89 |
| Pedidos 1000 y 1001, de 2015 | 3 | 4 | 2 | 867,50 |

`product.csv` contiene 2.517 productos, cada uno con `ProductKey` único. Los conteos deben coincidir exactamente; los importes, a dos decimales. Si el resultado no coincide, revise los datos, el filtro, el tipo de columna y el cálculo antes de continuar.

<a id="fase-1"></a>
## Fase 1. Planear

**SOLO COMPRENSIÓN. No abrir Power BI.** Se define qué busca la dirección comercial, qué decisiones pretende apoyar el reporte y cómo se responderán las preguntas con evidencias. Esta fase da sentido a la lectura crítica de los datos en la fase siguiente.

<a id="paso-1-1"></a>
### 1.1. Comprender el problema y delimitar el alcance

La dirección comercial de Contoso quiere describir la actividad de ventas de **01/01/2020 a 31/12/2020**, comprender qué categoría contribuye más y reconocer cambios por mes. El resultado debe ser un informe claro y comprobado, no una explicación causal de los cambios. **Actividad de comprensión:** explicar quién utilizará el reporte, qué período se analiza y qué afirmaciones no pueden derivarse únicamente de ventas y categorías.

<a id="paso-1-2"></a>
### 1.2. Comprender las preguntas, los campos y los visuales

| Código | Pregunta de negocio | Datos necesarios | Visual propuesto y razón |
| --- | --- | --- | --- |
| Q1 | ¿Cuánto se vendió, cuántas unidades y cuántos pedidos se registraron en 2020? | `OrderDate`, `Quantity`, `NetPrice`, `OrderKey` | Tres tarjetas: síntesis inmediata de magnitudes. |
| Q2 | ¿Qué categoría lidera las ventas? | `CategoryName`, `ProductKey`, importe de venta | Barras horizontales: permiten comparar categorías y ordenarlas. |
| Q3 | ¿La categoría líder en dinero también lidera en unidades? | `CategoryName`, ventas y cantidad | Tabla: compara medidas distintas por categoría. |
| Q4 | ¿Cómo evolucionaron las ventas por mes? | `OrderDate` e importe de venta | Línea temporal: muestra secuencia, máximos y mínimos. |

**Criterio de comprensión:** justificar por qué el recuento de pedidos debe ser distinto del recuento de filas, y por qué una línea cronológica es preferible a ordenar nombres de meses alfabéticamente.

[Volver al índice](#indice)

<a id="fase-2"></a>
## Fase 2. Analizar

**SOLO COMPRENSIÓN. No abrir Power BI, no descargar archivos ni modificar datos.** Se estudian los conceptos que permiten reconocer problemas y diseñar cálculos correctos. En la fase 3 se aplicarán estas decisiones en la herramienta.

<a id="paso-2-1"></a>
### 2.1. Comprender las tablas y la granularidad

`sales` es la tabla de hechos: cada fila representa una **línea de pedido**. `OrderKey` identifica el pedido, `LineNumber` una línea dentro del pedido, `OrderDate` su fecha, `ProductKey` el artículo vendido, `Quantity` las unidades y `NetPrice` el precio neto unitario. `product` es la dimensión que describe productos mediante `ProductKey`, `ProductName` y `CategoryName`.

**Actividad de comprensión:** si un pedido tiene tres productos distintos, explique por qué puede ocupar tres filas pero solo debe contar como un pedido. Este concepto afectará el uso de `DISTINCTCOUNT`.

<a id="paso-2-2"></a>
### 2.2. Comprender por qué se relacionan las tablas

`product[ProductKey]` contiene una fila por producto; `sales[ProductKey]` se repite porque el mismo producto puede venderse muchas veces. Se necesita una relación **uno a varios**, con filtro desde `product` hacia `sales`. Es un modelo de hechos y dimensión que evita repetir los atributos del catálogo en cada venta.

**Actividad de comprensión:** explicar qué falla si ambas columnas tienen tipos distintos, si hay claves duplicadas en el catálogo o si la relación se deja inactiva. No se requiere combinar archivos por el simple hecho de que provienen de CSV diferentes.

<a id="paso-2-3"></a>
### 2.3. Comprender separadores, precisión y medidas

El texto `98.967` en `NetPrice` debe interpretarse como **98,967** (noventa y ocho unidades con 967 milésimas), no como 98.967 unidades enteras. El formato visual puede mostrar separadores diferentes sin cambiar el dato almacenado. Redondear el precio **antes** de multiplicarlo altera el cálculo.

Se utilizarán tres indicadores: **Ventas Totales** = suma de `Quantity × NetPrice` por línea, **Unidades Totales** = suma de `Quantity` y **Pedidos** = cantidad de valores distintos de `OrderKey`. El mes se representará con una fecha correspondiente al inicio del mes, no con un texto ordenado alfabéticamente.

**Actividad de comprensión:** explicar por qué un error decimal puede mantener correcto el conteo de pedidos y unidades, pero distorsionar las ventas.

<a id="paso-2-4"></a>
### 2.4. Comprender los totales de control

Un total de control se calcula independientemente sobre el mismo origen y sirve para comparar resultados. La coincidencia del total general no garantiza que cada categoría y cada mes sean correctos. Por eso se validarán también filtros, relaciones y líneas individuales.

**Actividad de comprensión:** interpretar la tabla de controles inicial: 11.267 líneas de venta no son 11.267 pedidos, ya que hay 4.806 pedidos distintos en 2020. Explicar por qué el proceso debe detenerse si una cifra no coincide.

**Criterio para pasar a Construir:** reconocer las dos tablas, su clave, los tres indicadores, la precisión decimal y los cuatro visuales previstos. Toda la operación técnica empieza a continuación.

[Volver al índice](#indice)

<a id="fase-3"></a>
## Fase 3. Construir en Power BI

**Modalidad: práctica guiada.** Realice los apartados en orden: **[3.1 Configuración inicial](#paso-3-1) → [3.2 Calidad de datos](#paso-3-2) → [3.3 Modelo de datos](#paso-3-3) → [3.4 Visualizaciones](#paso-3-4)**. Al finalizar cada subpaso, contraste el resultado esperado antes de avanzar. No copie los totales de referencia como valores fijos dentro de los visuales.

> **Organización de las capturas:** cada paso tiene una referencia `3.x.y` y una celda en la columna derecha. Para reemplazar una captura pendiente, copie desde GitHub la dirección de la imagen y sustituya el texto `🖼️ Captura ... pendiente` por `<img src="URL" width="900" alt="Paso 3.x.y">` dentro de la misma celda. Mantenga toda la fila de la tabla en una única línea del archivo `.md`. La columna de acciones se mantiene breve; la columna de imágenes recibe prioridad visual. En tablas Markdown GitHub ajusta el ancho automáticamente y no garantiza una proporción fija.

**Mapa visual de la fase:**

<img src="https://github.com/user-attachments/assets/64439d66-7728-4cd0-ad9a-338e2e690e49" width="900" alt="Captura: general">

<a id="paso-3-1"></a>
### 3.1. Configuración inicial

**Propósito.** Descargar los insumos, establecer una carpeta de trabajo y definir la interpretación regional antes de leer los CSV. En esta sección aún no se calculan indicadores.

<a id="paso-3-1-1"></a>
#### 3.1.1. Descargar y organizar los archivos

Descargue el paquete de [datos Contoso `csv-100k.7z`](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z) y extráigalo sin modificar los CSV.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Descargar el paquete `.7z` y extraerlo en `Taller_01_PowerBI/datos`. Verificar que aparezcan `sales.csv` y `product.csv`. Conservar los nombres originales. | 🖼️ **Captura 3.1.1 pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Guardar el futuro `.pbix` en `Taller_01_PowerBI`. No editar los CSV en Excel ni cambiar sus separadores. | 🖼️ **Captura 3.1.1b pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** se localizan dos CSV originales en una carpeta conocida. Al cambiar de computador, las rutas absolutas de las fuentes pueden requerir actualización desde Power Query.

<a id="paso-3-1-2"></a>
#### 3.1.2. Abrir Power BI y localizar Obtener datos

Prepare un informe en blanco y reconozca el conector antes de importarlo.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Abrir Power BI Desktop, crear un informe en blanco e identificar **Inicio → Obtener datos → Texto/CSV**. No pulse **Cargar** todavía. | <img src="https://github.com/user-attachments/assets/f000ec3e-725d-4090-80d2-c2ecec6bf46a" width="850" alt="Captura: get"> |

**Resultado esperado:** se identifica correctamente el conector Texto/CSV.

<a id="paso-3-1-3"></a>
#### 3.1.3. Configurar la interpretación regional

La fuente original representa números decimales con punto. Defina la región de importación antes de transformarlos.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Ir a **Archivo → Opciones y configuración → Opciones → Archivo actual → Configuración regional**. Seleccionar **Inglés (Estados Unidos)** para la importación y aceptar. Si aparece una opción independiente de formato visible, puede escoger **Español (Colombia)** para presentación. | <img src="https://github.com/user-attachments/assets/7b18bcd3-0ef3-4d16-8cba-162229f93835" width="850" alt="Captura: regional"> |

**Verificación:** interpretar `98.967` como 98,967 (con coma decimal al mostrarlo en Colombia); **no** como 98.967 unidades enteras. El formato de pantalla no arregla un valor mal convertido.

<a id="paso-3-2"></a>
### 3.2. Calidad de datos – Power Query

**Propósito.** Importar las dos fuentes y controlar tipos, vacíos, duplicados y precisión antes de trabajar en el modelo. **Importante:** la guía original ya muestra capturas de creación de columnas desde la **vista Tabla usando DAX**. Esas capturas se preservan como referencia alternativa en 3.3.2; para seguir la secuencia principal se crearán `ImporteVenta` e `InicioMes` en **Power Query (M)**, pues allí sí estarán disponibles para consultas y controles posteriores.

<a id="paso-3-2-1"></a>
#### 3.2.1. Importar sales.csv y product.csv

Use **Transformar datos** en lugar de **Cargar** para poder controlar la calidad antes de aplicar.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** **Inicio → Obtener datos → Texto/CSV**. Localizar `sales.csv`. | <img src="https://github.com/user-attachments/assets/f000ec3e-725d-4090-80d2-c2ecec6bf46a" width="850" alt="Captura: get"> |
| **2.** Comprobar **Delimitador: coma** y vista previa de `sales.csv`. Seleccionar **Transformar datos**, no **Cargar**. | <img src="https://github.com/user-attachments/assets/21beab77-cfd5-42dc-91e6-82d3455f2b73" width="650" alt="Captura: choose"> <img src="https://github.com/user-attachments/assets/53a8bd73-2450-4af3-8f0d-e0491fa8d4f0" width="650" alt="Captura: load"> |
| **3.** En el Editor de Power Query, renombrar la consulta `sales`. Seleccionar `OrderKey`, `LineNumber`, `OrderDate`, `ProductKey`, `Quantity`, `NetPrice` (Ctrl + clic) → **Quitar otras columnas**. | 🖼️ **Captura 3.2.1c pendiente.** Pegar aquí la imagen de este paso. |
| **4.** **Inicio → Nuevo origen → Texto/CSV**; seleccionar `product.csv`, renombrar la consulta `product` y conservar `ProductKey`, `ProductName`, `CategoryName`. | <img src="https://github.com/user-attachments/assets/9bb4ba05-db4c-4cb9-b958-84230d7cef2c" width="850" alt="Captura: loaded"> |

**Resultado esperado:** dos consultas en Power Query, `sales` con 6 campos originales y `product` con 3. **No hacer Cerrar y aplicar todavía.** Si los archivos aparecen con encabezados genéricos, usar **Usar primera fila como encabezados**.

<a id="paso-3-2-2"></a>
#### 3.2.2. Revisar y corregir los tipos de datos

Los valores deben conservarse en su tipo adecuado. El error más riesgoso es convertir el precio decimal con una región incorrecta.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** En `sales`: `OrderKey` **Texto**; `LineNumber`, `ProductKey`, `Quantity` **Entero**; `OrderDate` **Fecha**; `NetPrice` **Número decimal**. En `product`: `ProductKey` **Entero**, nombres **Texto**. | 🖼️ **Captura 3.2.2a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Para `NetPrice`, si existe un **Tipo cambiado** automático incorrecto, borrarlo antes de convertir. Clic derecho sobre el encabezado → **Cambiar tipo → Usar configuración regional → Número decimal → Inglés (Estados Unidos)**. | 🖼️ **Captura 3.2.2b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** En `product`, seleccionar nombres y **Transformar → Formato → Recortar**. Confirmar que `ProductKey` tiene igual tipo en ambas consultas. | 🖼️ **Captura 3.2.2c pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** `OrderDate` reconoce fechas, `NetPrice` conserva hasta seis decimales, y no hay errores de conversión. No utilizar **Número decimal fijo** ni redondear antes de multiplicar.

<a id="paso-3-2-3"></a>
#### 3.2.3. Examinar calidad y perfil de columnas

El perfilado debe aplicarse sobre **todo el conjunto de datos**, no solo a la muestra de 1.000 filas.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** En Power Query, ir a **Vista → Calidad de columna** y **Perfil de columna** (opcional: **Distribución de columna**). | <img src="https://github.com/user-attachments/assets/cd50a6d9-eaa2-45ef-b2a1-de3931cf79df" width="850" alt="Captura: qualitymenu"> |
| **2.** En la barra inferior, cambiar **Perfilado de columnas basado en las primeras 1.000 filas** por **basado en todo el conjunto de datos**. | <img src="https://github.com/user-attachments/assets/d1d4f80c-39a2-4fd8-adbb-fae9138b92ac" width="850" alt="Captura: quality"> |
| **3.** Seleccionar `OrderKey`, `Quantity`, `NetPrice` y `LineNumber`. Revisar errores, vacíos, mínimos, máximos y valores distintos en Estadísticas de columna. | <img src="https://github.com/user-attachments/assets/132bacf6-e815-4b08-8c2c-c7144a429128" width="700" alt="Captura: profile"> <img src="https://github.com/user-attachments/assets/0b16ef72-8496-41b5-a034-cea08ea7d3ce" width="700" alt="Captura: fullprofile"> |
| **4.** Repita la comprobación en `product[ProductKey]` y revise que no existan errores o claves nulas. | 🖼️ **Captura 3.2.3d pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** perfil completo, sin errores de conversión ni vacíos en los campos requeridos. La gráfica de perfil es una verificación de calidad, no una visualización del informe.

<a id="paso-3-2-4"></a>
#### 3.2.4. Verificar recuentos y claves

Los totales estructurales se comprueban en Power Query; las cifras agregadas de ventas, unidades y pedidos se confirmarán con medidas DAX en **3.3.3**, después de cargar el modelo.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Clic derecho en `sales` → **Referencia**, renombrar `control_filas_sales`. En esta consulta auxiliar usar **Transformar → Contar filas**. Debe devolver **199.873**. | 🖼️ **Captura 3.2.4a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Repetir con una referencia de `product`, llamada `control_filas_product`. Debe devolver **2.517**. | 🖼️ **Captura 3.2.4b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** Otra referencia de `product`: **Agrupar por → ProductKey → Recuento de filas**. Filtrar recuentos mayores que 1. Debe quedar vacía (0 claves duplicadas). | 🖼️ **Captura 3.2.4c pendiente.** Pegar aquí la imagen de este paso. |
| **4.** Desactivar **Habilitar carga** de consultas auxiliares (o eliminarlas tras verificar), sin alterar `sales` y `product`. | 🖼️ **Captura 3.2.4d pendiente.** Pegar aquí la imagen de este paso. |

**No avanzar si no coinciden los recuentos.** Los 11.267 registros de 2020 son líneas, no pedidos distintos.

<a id="paso-3-2-5"></a>
#### 3.2.5. Crear ImporteVenta e InicioMes en Power Query

La secuencia principal usa **Agregar columna → Columna personalizada** para crear estas dos columnas en lenguaje M.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** En `sales` elegir **Agregar columna → Columna personalizada**. Nombre: `ImporteVenta`. Fórmula: `[Quantity] * [NetPrice]`. Aceptar y dejar tipo **Número decimal**. | 🖼️ **Captura 3.2.5a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Repetir **Columna personalizada**. Nombre: `InicioMes`. Fórmula: `Date.StartOfMonth([OrderDate])`. Aceptar y asignar tipo **Fecha**. | 🖼️ **Captura 3.2.5b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** Comprobar un registro: cantidad × precio neto = importe de línea. `InicioMes` debe mostrar el primer día del mes del pedido. | 🖼️ **Captura 3.2.5c pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** `sales` tiene 8 columnas y `product` 3. **No use aquí fórmulas DAX:** `EOMONTH(...)` y la sintaxis de DAX corresponden a otra etapa.

<a id="paso-3-2-6"></a>
#### 3.2.6. Cerrar y aplicar las transformaciones

Verifique el panel de errores antes de pasar al modelo.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Confirmar consultas de negocio `sales` y `product`, tipos correctos, sin errores y columnas `ImporteVenta` e `InicioMes`. Seleccionar **Inicio → Cerrar y aplicar**. | 🖼️ **Captura 3.2.6 pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** las tablas quedan disponibles en Power BI Desktop. Las consultas auxiliares no deben cargarse como tablas de negocio.

<a id="paso-3-3"></a>
### 3.3. Modelo de datos

**Propósito.** Configurar la relación de hechos y dimensión y crear indicadores DAX. Aquí las medidas podrán responder dinámicamente a segmentaciones y filtros del informe.

<a id="paso-3-3-1"></a>
#### 3.3.1. Verificar relación por ProductKey

En la vista Modelo se conserva una relación automática únicamente si es correcta.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Abrir **Vista Modelo**. Revisar conexión `product[ProductKey]` (lado **1**) → `sales[ProductKey]` (lado **muchos**). | 🖼️ **Captura 3.3.1a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Doble clic sobre la relación: **Activa: Sí**; **Cardinalidad: uno a varios**; **Dirección de filtro cruzado: Única** desde `product` hacia `sales`. Si no existe: **Modelado → Administrar relaciones → Nueva**. | 🖼️ **Captura 3.3.1b pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** al filtrar una categoría de productos se filtran sus ventas. No combinar las consultas ni admitir muchos a muchos sin revisar las claves.

<a id="paso-3-3-2"></a>
#### 3.3.2. Reconocer la diferencia entre columnas Power Query y DAX

Las capturas siguientes ya estaban en el taller original y muestran la **ruta alternativa DAX**. **No repita la creación** de `ImporteVenta` ni `InicioMes` si ya existen por Power Query en 3.2.5.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **Referencia A (alternativa, no ejecutar de nuevo).** Vista Tabla → tabla `sales`. | <img src="https://github.com/user-attachments/assets/bf020ae3-e5d9-4770-8e6e-60704111f842" width="850" alt="Captura: view"> |
| **Referencia B.** **Herramientas de tablas → Nueva columna**; para DAX, la expresión de importe sería `ImporteVenta = [Quantity] * [NetPrice]`. | <img src="https://github.com/user-attachments/assets/69f07f68-df39-4f17-93be-bcabcedb2f8a" width="750" alt="Captura: newcol"> <img src="https://github.com/user-attachments/assets/1a5da758-859f-4b46-b624-11a4d3611a5c" width="750" alt="Captura: amount"> |
| **Referencia C.** En DAX, la fecha de inicio de mes puede escribirse `InicioMes = EOMONTH('sales'[OrderDate], -1) + 1` y definirse como Fecha. | <img src="https://github.com/user-attachments/assets/1907837c-e104-4ca5-b9b2-66d0d040edd8" width="850" alt="Captura: month"> |

**Regla:** las columnas creadas en Power Query aparecen en el modelo. Las creadas por DAX no vuelven a aparecer en Power Query. En este recorrido las dos columnas ya están hechas mediante M.

<a id="paso-3-3-3"></a>
#### 3.3.3. Crear las tres medidas DAX

Seleccione `sales` y después **Modelado → Nueva medida**. Crear cada medida por separado.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1. Ventas Totales:** `Ventas Totales = SUM('sales'[ImporteVenta])`. | 🖼️ **Captura 3.3.3a pendiente.** Pegar aquí la imagen de este paso. |
| **2. Unidades Totales:** `Unidades Totales = SUM('sales'[Quantity])`. | 🖼️ **Captura 3.3.3b pendiente.** Pegar aquí la imagen de este paso. |
| **3. Pedidos distintos:** `Pedidos = DISTINCTCOUNT('sales'[OrderKey])`. Formato: ventas 2 decimales; cantidades y pedidos 0 decimales. | 🖼️ **Captura 3.3.3c pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** las tres medidas muestran icono de calculadora. `Pedidos` usa `DISTINCTCOUNT`, no recuento de líneas.

<a id="paso-3-3-4"></a>
#### 3.3.4. Comprobar totales agregados sin filtros

Use un visual de tabla provisional con las tres medidas; el detalle comercial se construirá en 3.4.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Insertar **Tabla** provisional y agregar las medidas. Sin filtros debe mostrar **203.723.865,76** en ventas, **628.370** unidades y **83.130** pedidos distintos. | 🖼️ **Captura 3.3.4a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Aplicar temporalmente el intervalo **01/01/2020 a 31/12/2020** en un filtro de fecha del visual: deben aparecer **11.066.206,89**, **35.234** y **4.806**. Luego quitar el filtro y la tabla provisional. | 🖼️ **Captura 3.3.4b pendiente.** Pegar aquí la imagen de este paso. |

**Si no coinciden**, revisar `NetPrice`, cálculo de `ImporteVenta`, `OrderDate`, precisión de los decimales y filtros activos antes de diseñar el tablero.

<a id="paso-3-4"></a>
### 3.4. Visualizaciones

**Propósito.** Convertir los indicadores validados en cuatro respuestas visuales; luego comprobar interacciones y auditoría por pedido. La página principal se refiere exclusivamente a **2020** y todas las categorías, salvo cuando se realizan pruebas controladas.

<a id="paso-3-4-1"></a>
#### 3.4.1. Crear la página y definir el diseño

Se propone fondo `#F4F7FB`, tarjetas blancas `#FFFFFF`, títulos azul oscuro `#172B4D`, barras de acento `#007F7A` y línea mensual `#1F5A85`.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Renombrar la página `Resumen comercial`. Seleccionar **Vista → Temas → Personalizar tema actual**, ajustar la paleta y, si es pertinente, página **16:9**. | 🖼️ **Captura 3.4.1a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Insertar cuadro de texto con título `Resumen comercial - Contoso 2020`. Reservar zona superior para filtros y tarjetas; zona central para barras y línea; inferior para tabla. | 🖼️ **Captura 3.4.1b pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** título visible, contraste adecuado y espacios para cifras completas.

<a id="paso-3-4-2"></a>
#### 3.4.2. Agregar segmentaciones de fecha y categoría

Ambas segmentaciones pertenecen a la página Resumen, no a todo el informe.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Insertar **Segmentación de datos** → `sales[OrderDate]`, como fecha individual (no jerarquía). Elegir estilo **Entre**. | 🖼️ **Captura 3.4.2a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Insertar otra segmentación → `product[CategoryName]`. Mantener todas las categorías. Al principio dejar fechas sin filtrar para comprobar las tarjetas históricas; después seleccionar 2020. | 🖼️ **Captura 3.4.2b pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** la página `Comprobacion` debe conservar acceso a registros de 2015; no utilizar filtro de fecha a nivel informe.

<a id="paso-3-4-3"></a>
#### 3.4.3. Q1: ventas, unidades y pedidos con tarjetas

Se usarán exactamente las medidas ya comprobadas.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Insertar **Tarjeta**; asignar `Ventas Totales`; título `Ventas en moneda base`. En **Formato visual** usar **Unidades de visualización: Ninguno** y 2 decimales. | 🖼️ **Captura 3.4.3a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Duplicar tarjeta dos veces (Ctrl+C/Ctrl+V). Sustituir por `Unidades Totales` (0 decimales) y `Pedidos` (0 decimales), con títulos `Unidades` y `Pedidos distintos`. | 🖼️ **Captura 3.4.3b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** Después de comprobar el historial completo, colocar fechas **01/01/2020–31/12/2020**. Deben verse ventas **11.066.206,89**, unidades **35.234** y pedidos **4.806**. | 🖼️ **Captura 3.4.3c pendiente.** Pegar aquí la imagen de este paso. |

**Criterio:** tarjetas sin abreviaturas K/M y con el período correcto.

<a id="paso-3-4-4"></a>
#### 3.4.4. Q2: barras de ventas por categoría

Ranking descendente para reconocer la categoría líder.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Seleccionar **Gráfico de barras agrupadas**. Eje Y: `product[CategoryName]`. Eje X: `Ventas Totales`. Menú **… → Ordenar por → Ventas Totales → Descendente**. | 🖼️ **Captura 3.4.4a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Activar etiquetas y título `Ventas por categoría - 2020`. Destacar Computers si la versión permite configurar colores por categoría. | 🖼️ **Captura 3.4.4b pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** Computers **5.041.111,59**; Cell phones **1.861.564,14**. Si todas las barras repiten el mismo total, revisar relación.

<a id="paso-3-4-5"></a>
#### 3.4.5. Q3: tabla de comparación de categorías

Comparar importes, unidades y pedidos por categoría.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Insertar **Tabla**. Agregar en orden `product[CategoryName]`, `Ventas Totales`, `Unidades Totales`, `Pedidos`. Ordenar por ventas descendente. | 🖼️ **Captura 3.4.5a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Activar totales, formato legible y comparar con la tabla de referencia. Ordenar temporalmente por unidades para ver si cambia el liderazgo y volver a ventas. | 🖼️ **Captura 3.4.5b pendiente.** Pegar aquí la imagen de este paso. |

**Resultado esperado:** Computers lidera tanto ventas (**5.041.111,59**) como unidades (**8.364**). **Nota:** los pedidos por categoría no se suman para obtener el total distinto de pedidos, porque un pedido puede contener varias categorías.

<a id="paso-3-4-6"></a>
#### 3.4.6. Q4: línea de ventas mensuales

El eje debe seguir la fecha real de inicio de mes.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Insertar **Gráfico de líneas**. Eje X: `sales[InicioMes]` como **Fecha** individual, no jerarquía. Eje Y: `Ventas Totales`. | 🖼️ **Captura 3.4.6a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Ordenar cronológicamente, activar marcadores, aplicar color `#1F5A85` y título `Evolución mensual de ventas - 2020`. | 🖼️ **Captura 3.4.6b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** Agregar anotación visual `Marzo: −58,46 % frente a febrero` y contrastar meses de máximo y mínimo. | 🖼️ **Captura 3.4.6c pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** febrero **2.658.349,04** (máximo); marzo **1.104.361,28**; septiembre **320.093,34** (mínimo). Deben mostrarse 12 meses ordenados.

<a id="paso-3-4-7"></a>
#### 3.4.7. Probar interacciones entre filtros y visuales

Compruebe el filtrado antes de utilizar el tablero en la fase 4.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Con 2020 activo, seleccionar únicamente `Computers` en la segmentación. Verificar ventas **5.041.111,59**, unidades **8.364**, pedidos **2.094**. | 🖼️ **Captura 3.4.7a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Si algún visual no responde, seleccionar la segmentación → **Formato → Editar interacciones** → **Filtrar**. Limpiar la selección para regresar a los totales anuales. | 🖼️ **Captura 3.4.7b pendiente.** Pegar aquí la imagen de este paso. |

**Verificación:** todas las categorías restauradas: 11.066.206,89; 35.234; 4.806.

<a id="paso-3-4-8"></a>
#### 3.4.8. Crear una página de comprobación con dos pedidos

Esta página verifica el resultado del modelo a nivel de línea de pedido y no lleva filtro global de año.

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1.** Añadir página `Comprobacion`. Insertar **Tabla** con `OrderKey`, `LineNumber`, `OrderDate`, `CategoryName`, `Quantity`, `NetPrice`, `ImporteVenta`. En campos de detalle usar **No resumir** cuando aplique. | 🖼️ **Captura 3.4.8a pendiente.** Pegar aquí la imagen de este paso. |
| **2.** Insertar segmentación de `OrderKey`; seleccionar simultáneamente **1000** y **1001**. Añadir tarjetas con las tres medidas originales. | 🖼️ **Captura 3.4.8b pendiente.** Pegar aquí la imagen de este paso. |
| **3.** Verificar 3 líneas: pedido 1000 (Audio 1 × 98,967; Computers 1 × 659,780); pedido 1001 (Games and Toys 2 × 54,376). | 🖼️ **Captura 3.4.8c pendiente.** Pegar aquí la imagen de este paso. |

**Control manual:** 98,967 + 659,780 + 108,752 = **867,499** (visualizado como **867,50**); **4 unidades** y **2 pedidos distintos**.

**Cierre de la fase 3.** Antes de pasar a la fase 4, comprobar la relación activa, las tres medidas, las dos páginas, los filtros y la página de auditoría. Se conserva una cifra completa de control sin forzar redondeos.

[Volver al índice](#indice)

<a id="fase-4"></a>
## Fase 4. Ejecutar y explicar

**POWER BI + INTERPRETACIÓN.** Con el informe ya construido, se presentan respuestas comprobadas. No se justifican causalidades que los datos descriptivos no pueden demostrar. La secuencia se orienta a decisiones de negocio y concluye guardando el archivo.

<a id="paso-4-1"></a>
### 4.1. Restaurar el alcance de 2020 y comprobar las respuestas

1. En Power BI abrir la pestaña `Resumen comercial`.
2. En la segmentación de fechas, seleccionar **01/01/2020–31/12/2020**.
3. En categorías, eliminar selecciones particulares hasta incluir **todas**.
4. Revisar tarjetas, barras, tabla y línea. Confirmar las respuestas:

| Pregunta | Respuesta verificada |
| --- | --- |
| Q1 | 11.066.206,89 en ventas, 35.234 unidades, 4.806 pedidos distintos. |
| Q2 | Computers lidera con 5.041.111,59 (45,55 % de ventas del año). |
| Q3 | Sí. Computers también lidera unidades con 8.364. |
| Q4 | Febrero es máximo (2.658.349,04), septiembre mínimo (320.093,34); marzo cae 58,46 % frente a febrero. |

**Verificación:** los títulos que mencionan 2020 deben corresponder exactamente a filtros de 2020.

<a id="paso-4-2"></a>
### 4.2. Mostrar el informe siguiendo storytelling

Mantener el informe abierto sin cambiar el período durante el relato. Usar el siguiente orden:

1. **Contexto:** título, fuente y año seleccionado. Explicar qué decisiones busca apoyar el informe.
2. **Mensaje principal:** Computers concentra 45,55 % de las ventas; el cambio de marzo merece revisión.
3. **Magnitud:** mostrar las tres tarjetas (ventas, unidades y pedidos).
4. **Composición:** mostrar barras por categoría y la tabla para comparar liderazgo en dinero y unidades.
5. **Evolución:** mostrar la línea de febrero a marzo y el mínimo de septiembre.
6. **Conclusión y límite:** describir lo observado, aclarar que no se ha identificado la causa y proponer un análisis adicional.

Si piden prueba técnica, abrir `Comprobacion`, enseñar los pedidos 1000 y 1001 y regresar al resumen. **Criterio:** cada cifra mencionada cuenta con evidencia en el reporte.

<a id="paso-4-3"></a>
### 4.3. Comunicar el hallazgo y formular una acción

**Relato resuelto:** «En 2020, Contoso registró 11.066.206,89 en ventas, 35.234 unidades y 4.806 pedidos distintos. Computers lideró tanto en ventas como en unidades y representó el 45,55 % del importe anual. El máximo mensual ocurrió en febrero y las ventas cayeron un 58,46 % en marzo. Este resultado describe un cambio, pero no establece su causa. Recomendamos descomponer la variación por producto y categoría y contrastarla con información de inventario y campañas comerciales».

**Propuesta:** el analista comercial examinará qué productos y categorías contribuyeron a la variación entre febrero y marzo; la dirección comercial podrá revisar el hallazgo junto con disponibilidad de inventario y acciones promocionales. No atribuir el comportamiento a una causa sin nuevas evidencias.

<a id="paso-4-4"></a>
### 4.4. Guardar y verificar la solución

1. En Power BI Desktop, regresar a `Resumen comercial` y restaurar 2020 y todas las categorías.
2. Elegir **Archivo → Guardar como**.
3. Escribir el nombre `Taller_01_Contoso_PACE.pbix` y seleccionar la carpeta de trabajo.
4. Pulsar **Guardar** y comprobar que el archivo existe.
5. Confirmar que contiene las dos páginas, relación activa, tres medidas, filtros funcionales y controles correctos. Conservar los CSV en su ubicación original si luego desea actualizar consultas.

**Entregable:** archivo `.pbix` funcional. Las fases de comprensión no requieren generar visuales ni archivos por separado.

[Volver al índice](#indice)

<a id="dificultades"></a>
## Dificultades frecuentes

| Situación | Qué revisar en Power BI |
| --- | --- |
| El CSV aparece en una columna | Power Query → paso **Origen** → delimitador coma. |
| `NetPrice` queda en miles o genera error | Quitar una conversión incorrecta y volver a convertir desde texto con **Usar configuración regional: Inglés (Estados Unidos)**. |
| Las ventas son incorrectas pero cantidades correctas | Tipo y precisión de `NetPrice`; fórmula de `ImporteVenta`; filtros. |
| Las categorías repiten el mismo importe | Relación activa por `ProductKey` y dirección de filtro. |
| La relación sugerida es muchos a muchos | Revisar unicidad de la clave de `product` y tipos compatibles. |
| Aparecen 11.267 «pedidos» en 2020 | Se contaron filas. Utilizar `DISTINCTCOUNT('sales'[OrderKey])`. |
| Las tarjetas muestran K o M | Formato visual → valor destacado → unidades de visualización **Ninguno**. |
| Meses en orden incorrecto | `InicioMes` como Fecha individual; ordenar cronológicamente. |
| No aparecen los pedidos 1000 y 1001 | Quitar filtros de informe o segmentaciones sincronizadas con el año 2020. |
| Los totales visibles difieren por centavos al sumarse | Conservar decimales originales; no sumar ni redondear cantidades ya formateadas. |

<a id="fuentes"></a>
## Fuentes y documentación técnica

1. [Repositorio original del taller y datos Contoso](https://github.com/adanbeltran/Labs/tree/main/PowerBI-Taller01-demo).
2. [SQLBI, Contoso V2, datos de ejemplo](https://github.com/sql-bi/Contoso-Data-Generator-V2-Data/releases/tag/ready-to-use-data-2024).
3. [Microsoft Learn, conector Texto/CSV](https://learn.microsoft.com/es-es/power-query/connectors/text-csv).
4. [Microsoft Learn, tipos de datos de Power Query](https://learn.microsoft.com/es-es/power-query/data-types).
5. [Microsoft Learn, columna personalizada de Power Query](https://learn.microsoft.com/es-es/power-query/add-custom-column).
6. [Microsoft Learn, función Date.StartOfMonth](https://learn.microsoft.com/es-es/powerquery-m/date-startofmonth).
7. [Microsoft Learn, relaciones en Power BI](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-create-and-manage-relationships).
8. [Microsoft Learn, conceptos de esquema de estrella](https://learn.microsoft.com/es-es/power-bi/guidance/star-schema).
9. [Microsoft Learn, creación de medidas](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-measures).
10. [Microsoft Learn, segmentaciones](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-slicers).
11. [Microsoft Learn, formato de tarjetas](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-card).
12. [Microsoft Learn, gráficos de líneas](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-line-chart).
13. [Microsoft Learn, temas del informe](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-report-themes).
14. [Google Career Certificates, metodología PACE](https://www.coursera.org/learn/foundations-of-data-science).
