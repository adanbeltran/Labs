# Taller 02. Diagnóstico comercial de Contoso en Power BI

**Metodología PACE | Clientes, tiendas, productos y evolución mensual**

**Objetivo:** construir un informe autónomo de diagnóstico comercial para comparar el desempeño por cliente, tienda y producto, detectar cambios que requieren revisión, identificar sus contribuciones y sustentar recomendaciones ante la gerencia.

**Dirigido a:** estudiantes de último semestre de Administración de Empresas.  
**Duración sugerida:** ocho horas de trabajo guiado, distribuidas en cuatro sesiones, más la preparación de los videos.  
**Punto de partida:** un archivo nuevo de Power BI Desktop y los CSV originales del paquete Contoso. Todos los insumos, fórmulas y controles necesarios se incluyen en esta guía.  
**Caso central:** explorar todos los meses del historial disponible para descubrir variaciones que requieran revisión y sustentar prioridades comerciales.  
**Resultado:** seis vistas de negocio: `Gerencia`, `Clientes`, `Tiendas`, `Productos`, `Factores comerciales` y `Alertas comerciales`, además de la página técnica `Control_T2`.

**Arranque independiente:** la consulta `BaseContoso`, suministrada en 3.1, importa y prepara ventas y productos automáticamente. Después se incorporan clientes y tiendas y se crean las medidas dentro de este mismo archivo. El trabajo central se concentra en las nuevas vistas, comparaciones e interacciones.

La empresa dispone de registros de ventas, pero todavía no ha identificado qué meses presentan cambios relevantes ni qué segmentos requieren atención. El equipo revisará la serie completa, documentará las señales y localizará sus contribuciones por tiendas, clientes y productos. Las posibles causas se presentarán como hipótesis que requieren evidencia adicional.

**Visualizaciones incorporadas:** barras apiladas al 100 %, dispersión con burbujas, mapa de árbol (Treemap), árbol de descomposición, cintas e influenciadores clave. Se agrega el gráfico de líneas con detección de anomalías y una tabla de alertas por umbral. La cascada permite interpretar contribuciones al cambio y las matrices permiten comprobar el detalle.

**Alcance temporal:** todos los meses comprendidos entre el primer y el último mes registrados en sales. Alertas comerciales y las cintas conservan la serie completa. Gerencia, Clientes, Tiendas y Productos permiten investigar un mes a la vez frente al anterior, después de identificarlo en el seguimiento. Factores comerciales explora los pedidos del historial completo. Las dos vistas históricas quedan fuera de las segmentaciones sincronizadas de las cuatro vistas de detalle mensual.

## Modalidad de trabajo en cada fase

| Fase PACE | ¿Se utiliza Power BI? | Propósito | Producto |
|---|---|---|---|
| **1. Planear** | **No** | Definir preguntas y decisiones empresariales. | Alcance del diagnóstico. |
| **2. Analizar** | **No** | Comprender dimensiones, indicadores y controles. | Diseño del análisis. |
| **3. Construir** | **Sí** | Incorporar dimensiones, modelar, calcular y visualizar. | Informe con seis vistas y gráficos de diagnóstico. |
| **4. Ejecutar y explicar** | **Sí** | Validar, investigar y comunicar resultados. | PBIX y dos videos. |

**Regla metodológica:** las fases 1 y 2 son de lectura y razonamiento. La descarga, la apertura de Power BI y todas las operaciones técnicas comienzan en la fase 3.

**Uso de la columna de pantallazos:** las tablas de la fase 3 conservan una columna de instrucciones del 25 % y otra del 75 % vacía. Para documentar el procedimiento, edite este archivo en GitHub y pegue las imágenes en la celda derecha del paso correspondiente. Mantenga las etiquetas `<td>` y `</td>`. Puede insertar más de una imagen por paso.

**Fórmulas:** dentro de las tablas, abra **Copiar fórmula DAX** o **Copiar fórmula de Power Query**. Cada medida se crea por separado.

<a id="indice"></a>
## Índice por fases

| Fase | Propósito |
|---|---|
| [Fase 1. Planear](#fase-1) | Definir preguntas y decisiones empresariales. |
| [Fase 2. Analizar](#fase-2) | Comprender datos, indicadores y tipos de visualización. |
| [Fase 3. Construir](#fase-3) | Preparar datos, modelar y construir los gráficos paso a paso. |
| [Fase 4. Ejecutar y explicar](#fase-4) | Validar y comunicar el diagnóstico a la gerencia. |

## Recorrido de las cuatro fases

La **[fase 1. Planear](#fase-1)** establece las decisiones que necesita tomar la gerencia y las preguntas del diagnóstico. El resultado orienta la selección de dimensiones e indicadores de la fase siguiente.

La **[fase 2. Analizar](#fase-2)** distingue ventas, pedidos y clientes; explica las relaciones entre las tablas y fija las reglas de comparación. Estos criterios permiten reconocer errores antes de implementar los cálculos.

La **[fase 3. Construir](#fase-3)** comienza con la [configuración inicial](#paso-3-1), continúa con la [calidad de datos](#paso-3-2), establece el [modelo y las medidas](#paso-3-3), sigue con las [seis vistas empresariales y los nuevos gráficos](#paso-3-4) y desarrolla la [detección y revisión de cambios](#paso-3-5). Cada paso incluye una comprobación y espacio para su captura.

La **[fase 4. Ejecutar y explicar](#fase-4)** comprueba el informe terminado, verifica la cobertura de todos los meses, contrasta las alertas y comunica los hallazgos descubiertos, límites y recomendaciones mediante dos videos dirigidos a audiencias diferentes.

<a id="controles"></a>
## Totales de control del origen

Los controles siguientes se incluyen completos para la versión de referencia de `csv-100k.7z`. La consulta de inicio los calcula desde los CSV y permite verificarlos sin consultar otro ejercicio. Compare los resultados calculados con estas cifras; no las escriba como valores fijos en los visuales.

| Selección | Líneas de venta | Unidades | Pedidos distintos | Ventas |
|---|---:|---:|---:|---:|
| Historial completo, sin filtros | 199.873 | 628.370 | 83.130 | 203.723.865,76 |
| Todo 2020, sin filtros de dimensión | 11.267 | 35.234 | 4.806 | 11.066.206,89 |

**Uso de los controles:** las cifras anteriores verifican la carga del origen y de un subconjunto anual; el análisis empresarial abarca todo el historial y no anticipa meses ni variaciones específicas.

`product.csv` contiene **2.517 productos** en la versión de referencia del paquete. Los conteos de clientes y tiendas se obtendrán durante esta práctica; no se presuponen cifras para esas dimensiones. Los importes conservarán su precisión y se mostrarán con dos decimales. Se mantiene la unidad monetaria del origen, sin rotular los importes como pesos colombianos ni aplicar conversiones por el solo hecho de seleccionar un país.

<a id="fase-1"></a>
## Fase 1. Planear

**SOLO COMPRENSIÓN. No abrir Power BI.** Se define el problema y la audiencia. Estas decisiones orientarán el análisis de las tablas y los indicadores en la fase 2.

<a id="paso-1-1"></a>
### 1.1. Comprender el problema empresarial

La gerencia comercial de Contoso quiere conocer la evolución de su operación y establecer un seguimiento mensual. No dispone de un diagnóstico previo sobre aumentos, caídas o anomalías. Solicita analizar todos los meses disponibles, detectar cuáles requieren atención y examinar el comportamiento de tiendas, clientes, productos, pedidos y ticket promedio antes de recomendar acciones.

**Actividad:** explicar en tres o cuatro líneas qué información necesita la gerencia para decidir qué revisar primero. Distinguir el cambio medido de sus posibles causas.

<a id="paso-1-2"></a>
### 1.2. Definir preguntas, datos y vistas

| Código | Pregunta de negocio | Visual principal | Vista |
|---|---|---|---|
| Q1 | ¿Cómo cambian las ventas y el liderazgo de las categorías? | Tarjetas, cascada y cintas. | Gerencia. |
| Q2 | ¿Qué clientes compran y con qué frecuencia en la selección? | Indicadores y Top 10 de compradores. | Clientes. |
| Q3 | ¿Cómo se compone la venta de cada tienda y cuál es su desempeño? | Barras al 100 % y dispersión con burbujas. | Tiendas. |
| Q4 | ¿Dónde se concentra el ingreso dentro del portafolio? | Treemap y árbol de descomposición. | Productos. |
| Q5 | ¿Qué características se asocian con pedidos de mayor importe en el historial? | Influenciadores clave y segmentos principales. | Factores comerciales. |
| Q6 | ¿Qué meses requieren revisión y cómo se justifican sus alertas? | Líneas con anomalías, tabla de alertas y análisis de aumentos y disminuciones. | Alertas comerciales. |
| Q7 | ¿Qué dos asuntos debería revisar primero la gerencia? | Evidencias seleccionadas entre las vistas. | Presentación ejecutiva. |

**Actividad:** elegir dos preguntas y explicar qué decisión empresarial puede apoyar cada una.

<a id="paso-1-3"></a>
### 1.3. Delimitar el alcance

- Revisar todos los meses del historial, sin elegir de antemano un período problemático. Declarar las fechas inicial y final y verificar la cobertura de los meses de los extremos.
- Mantener el historial completo en el modelo para calcular referencias temporales.
- Evaluar todos los meses mediante variaciones y alertas de ventas y pedidos, y contrastarlas con la detección de anomalías. Registrar cada mes señalado por cualquiera de los métodos y priorizar al menos dos para investigación detallada. El umbral inicial del 30 % es una decisión didáctica.
- Incorporar las dimensiones de clientes y tiendas del mismo paquete cuando estén disponibles.
- Presentar tres hallazgos cuantificados y dos recomendaciones priorizadas.
- Indicar la información adicional necesaria para comprobar las hipótesis.

**Producto de la fase:** un párrafo con el problema, la audiencia y las decisiones que se apoyarán.

<a id="paso-1-4"></a>
### 1.4. Justificar la detección y revisión de cambios

**Justificación empresarial:** la gerencia necesita un criterio reproducible para priorizar revisiones en todos los meses. Una señal puede orientar la comprobación de datos, la revisión de pedidos y la consulta con responsables comerciales. La alerta debe acompañarse de su magnitud absoluta, el segmento afectado y la evidencia necesaria para decidir una acción.

**Justificación pedagógica:** esta actividad se incorpora para que el estudiante distinga la importancia de un cambio para el negocio, su carácter inusual dentro de una serie y la evidencia que puede ayudar a interpretarlo. Comparará una regla transparente, una detección automática y el análisis por dimensiones; deberá justificar coincidencias y diferencias, sin aceptar una salida del programa como una causa demostrada.

El **30 %** se adopta como umbral inicial de práctica para cambios mensuales, tanto positivos como negativos. Se comparará con 20 % y 40 % para discutir cuántos casos se priorizan. En una aplicación empresarial, su elección exigiría considerar volatilidad, estacionalidad, magnitud monetaria y capacidad de respuesta. La sensibilidad del detector y el umbral porcentual son parámetros distintos.

**Aprendizaje esperado:** detectar una señal, verificar datos y filtros, contrastar métodos, localizar contribuciones y proponer una revisión proporcionada a la evidencia. Los períodos investigados se elegirán a partir de los resultados de toda la serie y de su importancia comercial, sin una conclusión anticipada.

**Actividad:** proponer una decisión que podría apoyar una alerta y explicar qué comprobación debería realizarse antes de actuar.

[Volver al índice](#indice)

<a id="fase-2"></a>
## Fase 2. Analizar

**SOLO COMPRENSIÓN. No abrir Power BI ni descargar archivos.** Se estudia cómo se conectan las nuevas dimensiones con las ventas. Lo acordado aquí se implementará en la fase 3.

<a id="paso-2-1"></a>
### 2.1. Comprender las tablas y su granularidad

| Tabla | Una fila representa | Clave utilizada | Papel |
|---|---|---|---|
| `sales` | Una línea de un pedido. | `OrderKey` + `LineNumber`. | Hechos de venta. |
| `product` | Un producto. | `ProductKey`. | Dimensión de producto. |
| `customer` | Un cliente. | `CustomerKey`. | Dimensión de cliente. |
| `store` | Una tienda. | `StoreKey`. | Dimensión de tienda. |
| `Calendario` | Un día. | `Date`. | Dimensión temporal creada en Power BI. |

Un cliente puede realizar varios pedidos y un pedido puede contener varias líneas. Contar filas, pedidos y clientes produce indicadores diferentes. `CustomerKey` y `StoreKey` son identificadores para relacionar tablas; sus sumas no representan indicadores comerciales.

**Actividad:** explicar cómo se contabilizan dos pedidos de un mismo cliente cuando cada pedido contiene tres productos.

<a id="paso-2-2"></a>
### 2.2. Reconocer variables descriptivas y relaciones

| Dimensión | Campos del esquema CSV oficial que se utilizarán | Interpretación |
|---|---|---|
| Cliente | `CustomerKey`, `GivenName`, `Surname`, `CountryFull`, `City`. | Identidad y ubicación del cliente. |
| Tienda | `StoreKey`, `Description`, `CountryName`, `State`. | Identidad y ubicación comercial de la tienda. |
| Producto | `CategoryName`, `SubCategoryName`, `ProductName`, `Brand`. | Jerarquía del portafolio y marca. |
| Tiempo | Año, mes e inicio de mes derivados de `OrderDate`. | Período de la compra. |

El país del cliente y el país de la tienda son variables distintas. Se renombrarán como `CountryCliente` y `CountryTienda` para que los visuales expresen claramente cuál se analiza.

Las dimensiones filtran a `sales` mediante relaciones **uno a varios y de dirección única**. Se conservarán separadas, con una sola fila por clave. El modelo sigue el esquema de estrella descrito por Microsoft en las [fuentes](#fuentes).

**Actividad:** explicar qué visual utilizaría para comparar las ventas por país del cliente y cuál para compararlas por país de la tienda.

<a id="paso-2-3"></a>
### 2.3. Interpretar los indicadores

| Indicador | Definición | Precaución de interpretación |
|---|---|---|
| Clientes compradores | Clientes distintos presentes en `sales` dentro de la selección. | Difiere del total del catálogo `customer`. |
| Ticket promedio | Ventas ÷ pedidos distintos. | Por categoría representa el importe de esa categoría por pedido que la incluye. |
| Pedidos por cliente | Pedidos distintos ÷ clientes compradores. | Se refiere al período y segmento seleccionados. |
| Ventas por cliente | Ventas ÷ clientes compradores. | Es un promedio, no el valor de cada cliente. |
| Clientes recurrentes | Clientes con dos o más pedidos distintos en la selección. | Describe recompra dentro de esa selección; no demuestra retención. |
| Variación absoluta | Ventas actuales − ventas anteriores. | Mide contribución en dinero al cambio. |
| Variación porcentual | Variación absoluta ÷ ventas anteriores. | Una base igual a cero no permite calcular ese porcentaje. |
| Participación de categoría | Ventas de la categoría ÷ ventas del conjunto seleccionado. | Se utilizará en visuales a nivel de categoría. |

El total de un promedio se calcula a partir de sus sumas y conteos. Los clientes y pedidos distintos pueden repetirse entre categorías; el total general se recalcula con `DISTINCTCOUNT`, como explica su [documentación oficial](https://learn.microsoft.com/en-us/dax/distinctcount-function-dax).

**Actividad:** explicar por qué el total del ticket no se obtiene promediando los tickets de las categorías.

<a id="paso-2-4"></a>
### 2.4. Establecer reglas de comparación

1. Comparar un mes completo con el mes completo inmediatamente anterior, conservando los mismos filtros de tienda, cliente y producto.
2. Seleccionar un solo mes para los indicadores de variación. La evolución histórica conservará todos los meses del origen, aunque las vistas de detalle muestren uno solo.
3. Examinar la variación absoluta junto con la porcentual: una caída porcentual grande puede tener poco peso en el resultado total.
4. Considerar cero las ventas de un producto que está ausente en uno de los dos meses para calcular su contribución absoluta. Si la base anterior es cero, el porcentaje quedará en blanco.
5. Interpretar por separado los cambios en pedidos y en ticket. Su relación aritmética con las ventas orienta el diagnóstico; comprobar causas exige más información.

**Actividad:** proponer una hipótesis que pueda explorarse con estas tablas y otra que requiera información de inventarios o campañas.

<a id="paso-2-5"></a>
### 2.5. Comprender los controles de calidad

Al incorporar dimensiones, las ventas, unidades y pedidos del mismo período deben conservar sus valores. Una clave sin correspondencia puede generar categorías en blanco; una combinación que multiplique filas altera los resultados.

Se comprobarán los [totales de referencia](#controles), la unicidad de las claves, la correspondencia entre tablas y la suma de las variaciones absolutas por categoría. Además, cada pedido debe corresponder a un cliente, una tienda y una fecha para analizar influenciadores a ese nivel. Los porcentajes, tickets y conteos distintos se recalcularán en el total.

<a id="paso-2-6"></a>
### 2.6. Elegir el visual según la pregunta

| Visual | Qué permite interpretar | Regla para este taller |
|---|---|---|
| Barras apiladas al 100 % | Composición de las ventas entre categorías dentro de cada tienda. | Cada tienda suma 100 %; comparar también los importes absolutos. |
| Dispersión con burbujas | Relación entre nivel de ventas, variación y cantidad de pedidos por tienda. | Una burbuja por tienda; el tamaño representa pedidos. |
| Treemap | Concentración de las ventas por categoría y subcategoría. | El área representa ventas positivas; conservar etiquetas legibles. |
| Árbol de descomposición | Desglose de una medida por distintas dimensiones. | Empezar por ventas y explorar rutas; el desglose no prueba causas. |
| Cintas | Posición relativa de las categorías en cada mes. | Observar la serie completa; describir cambios o estabilidad del liderazgo. |
| Influenciadores clave | Asociaciones entre características del pedido y su importe. | Evaluar por pedido, con suficiente información; interpretar asociaciones, sin atribuir causalidad. |
| Líneas con anomalías | Valores fuera del rango esperado de una serie. | Conservar varios meses y documentar la sensibilidad utilizada. |
| Tabla de alertas | Meses que cumplen una regla explícita de variación. | Evaluar cada mes frente al anterior; un total de varios meses no es una alerta mensual. |

En el visual de factores se analizará `Ticket Promedio` por `OrderKey`. Al filtrar un pedido completo, el denominador es un pedido y la medida representa su importe. Los países del cliente y la tienda, el año y el mes son atributos únicos de ese pedido; la categoría puede variar entre sus líneas. Por eso los factores se analizarán sin filtros de producto y sin utilizar categoría como un atributo único del pedido.

El identificador del pedido define el nivel de análisis; no se utiliza como factor explicativo. Tampoco se usarán precio, cantidad o importe de línea para explicar una medida construida con esos mismos componentes.

**Actividad:** explicar qué diferencia hay entre localizar dónde se concentra la venta mediante un árbol y explorar asociaciones mediante influenciadores.

<a id="paso-2-7"></a>
### 2.7. Distinguir alerta, anomalía y explicación

| Recurso | Pregunta que responde | Criterio de esta práctica |
|---|---|---|
| Regla de alerta | ¿El cambio alcanza el límite que definimos? | Marcar variaciones mensuales iguales o superiores al 30 % en valor absoluto. |
| Detección de anomalías | ¿El valor se aparta del rango esperado de la serie? | Registrar los puntos señalados y la sensibilidad del detector. |
| Explicar el aumento o la disminución | ¿Qué cambios de composición destacan frente al punto anterior? | Contrastar los resultados con la cascada y las matrices. |

Microsoft documenta la [detección de anomalías](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-anomaly-detection) y el recurso [Explicar la disminución](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-insights). Sus resultados apoyarán la investigación del caso.

**Reglas de interpretación:**

- El detector compara valores con un rango esperado; la regla DAX compara porcentajes mensuales con un límite elegido. Es posible que sus señales difieran.
- Una variación grande puede ser habitual en la serie. Un cambio menor también puede merecer revisión por su importe o contexto. La coincidencia entre métodos no demuestra causalidad.
- Si la venta anterior es cero o falta, el porcentaje queda sin base comparable. No se lo clasificará como estabilidad.
- Revisar meses completos, tipos y filtros. La tabla de alertas y las líneas de seguimiento conservarán todos los meses del historial; cambiar de variable o de filtros cambia el contexto del análisis.
- Identificar el primer y el último mes desde el origen y verificar que todos los meses intermedios figuren en la tabla. Un mes sin registros requiere revisar la cobertura; no demuestra por sí solo una caída comercial. El primer mes puede carecer de referencia anterior y debe quedar Sin base comparable cuando corresponda.
- Verificar si los meses de los extremos están completos. Si no se puede confirmar, mantenerlos visibles con esa limitación y posponer sus conclusiones comerciales. La detección de anomalías se realizará sobre la secuencia continua de meses cuya cobertura se haya validado; registrar cualquier exclusión de un extremo.
- El taller no presupone meses críticos, signos de variación ni anomalías concretas. Se documentará el resultado observado para toda la serie, incluida la ausencia de señales o de explicaciones.

**Actividad:** describir un caso en que una alerta por umbral podría diferir de la detección automática y explicar cómo lo investigaría.

**Criterio para pasar a Construir:** identificar tablas y relaciones, distinguir líneas, pedidos y compradores, y explicar la diferencia entre alerta por umbral, anomalía y contribución al cambio.

[Volver al índice](#indice)

<a id="fase-3"></a>
## Fase 3. Construir en Power BI

**Modalidad: práctica guiada.** Seguir el orden **[3.1 Configuración](#paso-3-1) → [3.2 Calidad de datos](#paso-3-2) → [3.3 Modelo y medidas](#paso-3-3) → [3.4 Visualizaciones](#paso-3-4) → [3.5 Alertas y anomalías](#paso-3-5)**.

Los nombres de los menús pueden variar ligeramente según la versión de Power BI Desktop. Las fórmulas usan comas como separador de argumentos; si su editor exige punto y coma, sustituya los separadores de argumentos. Conserve los nombres de columnas y funciones.

<a id="paso-3-1"></a>
### 3.1. Inicio autónomo y preparación mínima

**Propósito:** iniciar un informe nuevo y utilizar el bloque de preparación suministrado para disponer de ventas, productos y controles. Esta preparación técnica breve permite pasar directamente al diagnóstico y a los gráficos nuevos.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-1"></a>
<p><strong>3.1.1. Crear el archivo de este taller</strong></p>
<ol><li>Abrir <strong>Power BI Desktop → Informe en blanco</strong>.</li><li>Guardar como <code>Taller_02_Contoso_Apellidos.pbix</code> en una carpeta de trabajo propia.</li><li>Mantener por ahora la página inicial vacía; se utilizará después para el control técnico.</li></ol>
<p><strong>Comprobar:</strong> el archivo comienza vacío y todo el desarrollo se realizará dentro de este PBIX.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.1: Crear el archivo de este taller. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-2"></a>
<p><strong>3.1.2. Descargar los datos y organizar la carpeta</strong></p>
<ol><li>Descargar el <a href="https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z">paquete Contoso csv-100k.7z</a> y extraerlo.</li><li>Crear una carpeta <code>datos</code> dentro de la carpeta del taller. Colocar en ella <code>sales.csv</code> y <code>product.csv</code>; ubicar también <code>customer.csv</code> y <code>store.csv</code> si están disponibles.</li><li>Copiar desde el explorador la ruta de la carpeta que contiene los CSV. Ejemplo: <code>C:\Taller_02_Contoso\datos</code>.</li><li>Conservar los CSV originales sin editarlos en Excel. Si faltan clientes o tiendas, utilizar la alternativa del anexo A cuando corresponda.</li></ol>
<p><strong>Comprobar:</strong> se dispone de los archivos de origen y de su ruta; no se requiere un archivo de Power BI previamente construido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.2: Descargar los datos y organizar la carpeta. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-3"></a>
<p><strong>3.1.3. Crear la consulta de inicio BaseContoso</strong></p>
<ol><li>Ir a <strong>Inicio → Obtener datos → Consulta en blanco</strong>. Si no aparece directamente, usar <strong>Más → Otros → Consulta en blanco</strong>.</li><li>En el Editor de Power Query, renombrar la consulta como <code>BaseContoso</code>.</li><li>Abrir <strong>Inicio → Editor avanzado</strong>. En el siguiente paso se reemplazará todo su contenido por el bloque suministrado.</li></ol>
<p><strong>Comprobar:</strong> la consulta BaseContoso está abierta en el Editor avanzado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.3: Crear la consulta de inicio BaseContoso. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-4"></a>
<p><strong>3.1.4. Aplicar el bloque de preparación suministrado</strong></p>
<ol><li>Copiar el bloque siguiente y reemplazar todo el contenido del Editor avanzado.</li><li>Cambiar únicamente <code>CarpetaDatos</code> por la ruta real de sus CSV, entre comillas, sin barra final ni nombre de archivo.</li><li>Pulsar <strong>Listo</strong>. El resultado es un registro con los campos sales, product y controles.</li><li>El bloque interpreta los datos con <code>en-US</code>, asigna tipos, crea ImporteVenta y calcula controles. Conservar la precisión y todo el historial.</li></ol><details><summary>Copiar bloque completo BaseContoso</summary><pre><code class="language-powerquery">let
    // Cambiar solamente esta ruta por la carpeta real.
    CarpetaDatos = &quot;C:\Taller_02_Contoso\datos&quot;,
    LeerCsv = (Nombre as text) as table =&gt;
        Table.PromoteHeaders(
            Csv.Document(
                File.Contents(
                    CarpetaDatos &amp; &quot;\&quot; &amp; Nombre
                ),
                [
                    Delimiter = &quot;,&quot;,
                    Encoding = 65001,
                    QuoteStyle = QuoteStyle.Csv
                ]
            ),
            [PromoteAllScalars = true]
        ),
    VentasTipadas = Table.TransformColumnTypes(
        LeerCsv(&quot;sales.csv&quot;),
        {
            {&quot;OrderKey&quot;, Int64.Type},
            {&quot;LineNumber&quot;, Int64.Type},
            {&quot;OrderDate&quot;, type date},
            {&quot;CustomerKey&quot;, Int64.Type},
            {&quot;StoreKey&quot;, Int64.Type},
            {&quot;ProductKey&quot;, Int64.Type},
            {&quot;Quantity&quot;, Int64.Type},
            {&quot;NetPrice&quot;, type number}
        },
        &quot;en-US&quot;
    ),
    VentasPreparadas = Table.AddColumn(
        VentasTipadas,
        &quot;ImporteVenta&quot;,
        each [Quantity] * [NetPrice],
        type number
    ),
    ProductosPreparados =
        Table.TransformColumnTypes(
            LeerCsv(&quot;product.csv&quot;),
            {
                {&quot;ProductKey&quot;, Int64.Type},
                {&quot;CategoryName&quot;, type text},
                {&quot;SubCategoryName&quot;, type text},
                {&quot;ProductName&quot;, type text},
                {&quot;Brand&quot;, type text}
            },
            &quot;en-US&quot;
        ),
    Controles = [
        Lineas = Table.RowCount(VentasPreparadas),
        Unidades = List.Sum(
            VentasPreparadas[Quantity]
        ),
        Pedidos = List.Count(
            List.Distinct(
                VentasPreparadas[OrderKey]
            )
        ),
        Ventas = List.Sum(
            VentasPreparadas[ImporteVenta]
        ),
        Productos = Table.RowCount(
            ProductosPreparados
        ),
        PrimerMes = Date.StartOfMonth(
            List.Min(VentasPreparadas[OrderDate])
        ),
        UltimoMes = Date.StartOfMonth(
            List.Max(VentasPreparadas[OrderDate])
        )
    ]
in
    [
        sales = VentasPreparadas,
        product = ProductosPreparados,
        controles = Controles
    ]</code></pre></details>
<p><strong>Comprobar:</strong> se muestran sales y product como tablas y controles como registro, sin errores de archivo o de tipo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.4: Aplicar el bloque de preparación suministrado. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-5"></a>
<p><strong>3.1.5. Crear las consultas de salida</strong></p>
<ol><li>En Power Query, usar <strong>Inicio → Nuevo origen → Consulta en blanco</strong>. Nombrarla <code>sales</code>, abrir Editor avanzado y pegar su consulta siguiente.</li><li>Repetir para crear <code>product</code> y <code>ControlBase</code>, utilizando el bloque correspondiente en cada una.</li><li>Mantener habilitada la carga de sales y product. Desactivar <strong>Habilitar carga</strong> en ControlBase y en BaseContoso si esa opción aparece habilitada.</li><li>Consultar ControlBase en Power Query; no se incorpora como tabla de hechos al modelo.</li></ol><details><summary>Copiar consulta sales</summary><pre><code class="language-powerquery">let
    Origen = BaseContoso[sales]
in
    Origen</code></pre></details><details><summary>Copiar consulta product</summary><pre><code class="language-powerquery">let
    Origen = BaseContoso[product]
in
    Origen</code></pre></details><details><summary>Copiar consulta ControlBase</summary><pre><code class="language-powerquery">let
    Origen = Record.ToTable(
        BaseContoso[controles]
    )
in
    Origen</code></pre></details>
<p><strong>Comprobar:</strong> sales y product contienen los datos; ControlBase presenta los campos Name y Value con los controles calculados.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.5: Crear las consultas de salida. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-6"></a>
<p><strong>3.1.6. Verificar el arranque y cargar las tablas</strong></p>
<ol><li>Seleccionar <code>ControlBase</code> y comprobar el historial: <strong>Lineas 199.873</strong>, <strong>Unidades 628.370</strong>, <strong>Pedidos 83.130</strong>, <strong>Productos 2.517</strong> y <strong>Ventas aproximadamente 203.723.865,76</strong>.</li><li>Anotar los valores <code>PrimerMes</code> y <code>UltimoMes</code> calculados en ControlBase. Definen el intervalo mensual de esta práctica. Comprobar en sales las fechas mínima y máxima de OrderDate y registrar la cobertura de sus extremos.</li><li>Si hay diferencias, revisar la versión del paquete, la ruta y los encabezados. Corregir la consulta de este taller antes de avanzar.</li><li>Confirmar que sales incluya <code>ImporteVenta</code> y que product conserve sus columnas de categoría, subcategoría, producto y marca.</li><li>Pulsar <strong>Inicio → Cerrar y aplicar</strong>. Guardar el PBIX. Continuar con clientes y tiendas en 3.2.</li></ol>
<p><strong>Comprobar:</strong> el informe nuevo dispone de su propia base preparada. Los controles completos están incluidos en esta guía.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.6: Verificar el arranque y cargar las tablas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-2"></a>
### 3.2. Calidad de datos – Power Query

**Propósito:** incorporar clientes y tiendas, verificar claves y crear descripciones claras sin multiplicar las líneas de venta. Si utilizó el anexo A, siga sus equivalencias para esta subfase.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-1"></a>
<p><strong>3.2.1. Importar clientes y tiendas</strong></p>
<ol><li>En <strong>Inicio → Obtener datos → Texto/CSV</strong>, seleccionar <code>customer.csv</code> y pulsar <strong>Transformar datos</strong>.</li><li>En Power Query, usar <strong>Inicio → Nuevo origen → Texto/CSV</strong> para incorporar <code>store.csv</code>.</li><li>Nombrar las consultas exactamente <code>customer</code> y <code>store</code>. Conservar <code>sales</code> y <code>product</code> como están.</li><li>Confirmar en <code>customer</code>: <code>CustomerKey</code>, <code>GivenName</code>, <code>Surname</code>, <code>CountryFull</code> y <code>City</code>. En <code>store</code>: <code>StoreKey</code>, <code>Description</code>, <code>CountryName</code> y <code>State</code>.</li></ol>
<p><strong>Comprobar:</strong> aparecen cuatro consultas de negocio. Se importan tablas adicionales del mismo conjunto de datos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.1: Importar clientes y tiendas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-2"></a>
<p><strong>3.2.2. Revisar tipos, calidad y perfil</strong></p>
<ol><li>Activar <strong>Vista → Calidad de columna</strong> y <strong>Perfil de columna</strong>. Cambiar el perfilado de las primeras 1.000 filas a <strong>todo el conjunto de datos</strong>.</li><li>Definir <code>CustomerKey</code>, <code>StoreKey</code> y <code>ProductKey</code> como <strong>Número entero</strong> en sus tablas respectivas.</li><li>Mantener las descripciones como <strong>Texto</strong> y <code>sales[OrderDate]</code> como <strong>Fecha</strong>.</li><li>Revisar que las claves no tengan errores ni vacíos. Registrar el número de filas y de claves distintas de cada dimensión.</li></ol>
<p><strong>Comprobar:</strong> los tipos coinciden entre las tablas relacionadas y cada dimensión tiene una clave válida por fila.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.2: Revisar tipos, calidad y perfil. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-3"></a>
<p><strong>3.2.3. Comprobar la unicidad de las claves</strong></p>
<ol><li>Seleccionar <code>customer[CustomerKey]</code> y comparar <strong>Recuento</strong> con <strong>Distintos</strong>. Deben ser iguales.</li><li>Repetir para <code>store[StoreKey]</code> y <code>product[ProductKey]</code>.</li><li>Si hay duplicados, revisar el origen o el paso que los produjo. Conservar una fila por clave solo cuando se haya comprobado qué registro es correcto.</li><li>Capturar las estadísticas y anotar los conteos de clientes y tiendas obtenidos en su versión.</li></ol>
<p><strong>Comprobar:</strong> cada clave del lado uno es única; el catálogo de productos conserva 2.517 filas en la versión de referencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.3: Comprobar la unicidad de las claves. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-4"></a>
<p><strong>3.2.4. Detectar clientes y tiendas sin correspondencia</strong></p>
<ol><li>Seleccionar <code>sales</code>. Abrir <strong>Inicio → Combinar consultas → Combinar consultas como nuevas</strong>.</li><li>Elegir <code>sales</code> arriba y <code>customer</code> abajo. Seleccionar <code>CustomerKey</code> en ambas tablas.</li><li>Elegir el tipo <strong>Anti izquierda</strong>: filas de ventas sin coincidencia en clientes. Nombrar la consulta <code>Control_ClientesSinFicha</code>.</li><li>Comprobar que quede vacía. Repetir con <code>StoreKey</code> y <code>store</code>; nombrar <code>Control_TiendasSinFicha</code>.</li><li>Desactivar <strong>Habilitar carga</strong> en ambas consultas de control. Si aparecen filas, identificar la clave y corregir el origen o el tipo antes de continuar.</li></ol>
<p><strong>Comprobar:</strong> cero filas sin correspondencia. La combinación se utiliza como control separado; <code>sales</code> conserva su granularidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.4: Detectar clientes y tiendas sin correspondencia. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-5"></a>
<p><strong>3.2.5. Crear la etiqueta del cliente</strong></p>
<ol><li>Seleccionar <code>customer</code> y abrir <strong>Agregar columna → Columna personalizada</strong>.</li><li>Nombre: <code>Cliente</code>. Pegar la fórmula siguiente.</li><li>Establecer el resultado como <strong>Texto</strong>. Revisar varios nombres y claves.</li></ol><details><summary>Copiar fórmula de Power Query</summary><pre><code class="language-powerquery">Text.Trim(Text.Combine(
    {[GivenName], [Surname]}, &quot; &quot;
)) &amp; &quot; [&quot; &amp; Text.From([CustomerKey]) &amp; &quot;]&quot;</code></pre></details>
<p><strong>Comprobar:</strong> la etiqueta incorpora la clave para distinguir clientes que comparten el mismo nombre.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.5: Crear la etiqueta del cliente. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-6"></a>
<p><strong>3.2.6. Crear la etiqueta de la tienda y diferenciar países</strong></p>
<ol><li>Seleccionar <code>store</code>. Crear una columna personalizada llamada <code>Tienda</code> con la fórmula siguiente.</li><li>Definirla como <strong>Texto</strong>. Renombrar <code>CountryName</code> a <code>CountryTienda</code>.</li><li>En <code>customer</code>, renombrar <code>CountryFull</code> a <code>CountryCliente</code> y <code>City</code> a <code>CityCliente</code>.</li></ol><details><summary>Copiar fórmula de Power Query</summary><pre><code class="language-powerquery">Text.Combine(
    {[Description]}, &quot;&quot;
) &amp; &quot; [&quot; &amp; Text.From([StoreKey]) &amp; &quot;]&quot;</code></pre></details>
<p><strong>Comprobar:</strong> cada tienda tiene una etiqueta única y los países de cliente y tienda se distinguen por su nombre.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.6: Crear la etiqueta de la tienda y diferenciar países. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-7"></a>
<p><strong>3.2.7. Verificar atributos únicos por pedido</strong></p>
<ol><li>Crear una <strong>Referencia</strong> de <code>sales</code> y nombrarla <code>Control_AtributosPedido</code>.</li><li>Abrir <strong>Inicio → Agrupar por</strong>. Agrupar por <code>OrderKey</code>, operación <strong>Todas las filas</strong>, columna nueva <code>FilasPedido</code>.</li><li>Agregar una columna personalizada llamada <code>PedidoValido</code> y pegar la fórmula siguiente. Definirla como <strong>Verdadero/Falso</strong>.</li><li>Filtrar <code>PedidoValido = false</code>. Revisar si aparecen pedidos con más de un cliente, tienda o fecha y corregir el origen si corresponde.</li><li>Desactivar <strong>Habilitar carga</strong> en este control. El resultado esperado es una tabla vacía.</li></ol><details><summary>Copiar fórmula de Power Query</summary><pre><code class="language-powerquery">List.Count(List.Distinct(
    [FilasPedido][CustomerKey]
)) = 1
and List.Count(List.Distinct(
    [FilasPedido][StoreKey]
)) = 1
and List.Count(List.Distinct(
    [FilasPedido][OrderDate]
)) = 1</code></pre></details>
<p><strong>Comprobar:</strong> cada pedido tiene un cliente, una tienda y una fecha. Este control justifica el nivel de análisis de los influenciadores.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.7: Verificar atributos únicos por pedido. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-8"></a>
<p><strong>3.2.8. Aplicar cambios y conservar la precisión</strong></p>
<ol><li>Revisar <code>sales[NetPrice]</code> e <code>ImporteVenta</code>: conservar <strong>Número decimal</strong> y la precisión del origen.</li><li>Mantener <code>ImporteVenta = [Quantity] * [NetPrice]</code>. Conservar todo el historial de ventas en Power Query.</li><li>Pulsar <strong>Inicio → Cerrar y aplicar</strong>. Guardar el archivo.</li></ol>
<p><strong>Comprobar:</strong> se cargan las dimensiones; los controles anti izquierda quedan sin carga y los totales de ventas no cambian.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.8: Aplicar cambios y conservar la precisión. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### 3.3. Modelo de datos y medidas DAX

**Propósito:** establecer las relaciones y crear indicadores que respondan correctamente a filtros de mes, tienda, cliente y producto. Las medidas se crean desde Nueva medida; Calendario se crea desde Nueva tabla.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-1"></a>
<p><strong>3.3.1. Crear las relaciones entre las tablas</strong></p>
<ol><li>Abrir <strong>Vista Modelo</strong>. Arrastrar <code>product[ProductKey]</code> hasta <code>sales[ProductKey]</code> para crear la relación. Si Power BI ya la detectó, abrirla para comprobarla.</li><li>Crear de la misma manera <code>customer[CustomerKey]</code> → <code>sales[CustomerKey]</code> y <code>store[StoreKey]</code> → <code>sales[StoreKey]</code>.</li><li>Configurar <strong>uno a varios (1:*)</strong>, dimensión del lado 1, filtro cruzado <strong>Único</strong> y relación <strong>Activa</strong>.</li><li>Conservar una sola conexión directa de cada dimensión con sales. Revisar otras rutas creadas automáticamente y retirar las que dupliquen relaciones o generen ambigüedad.</li></ol>
<p><strong>Comprobar:</strong> el modelo creado en este archivo tiene tres dimensiones que filtran a sales; Calendario se añadirá en los pasos siguientes.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.1: Crear las relaciones entre las tablas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-2"></a>
<p><strong>3.3.2. Crear la tabla Calendario</strong></p>
<ol><li>Seleccionar <strong>Modelado → Nueva tabla</strong>. Pegar la fórmula completa.</li><li>Confirmar que la tabla se llame <code>Calendario</code>. Esta tabla se deriva del historial disponible y cubre años completos.</li><li>Confirmar que sus columnas se hayan creado sin errores antes de continuar.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Calendario =
VAR FechaMin = MIN(sales[OrderDate])
VAR FechaMax = MAX(sales[OrderDate])
RETURN
    ADDCOLUMNS(
        CALENDAR(
            DATE(YEAR(FechaMin), 1, 1),
            DATE(YEAR(FechaMax), 12, 31)
        ),
        &quot;Anio&quot;, YEAR([Date]),
        &quot;MesNumero&quot;, MONTH([Date]),
        &quot;Mes&quot;, FORMAT([Date], &quot;mmm&quot;, &quot;es-ES&quot;),
        &quot;AnioMes&quot;, FORMAT([Date], &quot;yyyy-MM&quot;),
        &quot;AnioMesOrden&quot;, YEAR([Date]) * 100
            + MONTH([Date]),
        &quot;InicioMes&quot;, DATE(
            YEAR([Date]), MONTH([Date]), 1
        )
    )</code></pre></details>
<p><strong>Comprobar:</strong> existe una fila por día, sin huecos ni fechas duplicadas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.2: Crear la tabla Calendario. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-3"></a>
<p><strong>3.3.3. Configurar fechas y orden de meses</strong></p>
<ol><li>En <code>Calendario</code>, definir <code>Date</code> e <code>InicioMes</code> como tipo <strong>Fecha</strong>.</li><li>Seleccionar <strong>Herramientas de tabla → Marcar como tabla de fechas</strong> y elegir <code>Date</code>.</li><li>Seleccionar <code>Mes</code> y usar <strong>Herramientas de columna → Ordenar por columna → MesNumero</strong>.</li><li>Seleccionar <code>AnioMes</code> y ordenar por <code>AnioMesOrden</code>.</li><li>Utilizar estas columnas de Calendario en los filtros, ejes y tablas de este informe.</li></ol>
<p><strong>Comprobar:</strong> los meses aparecen en orden cronológico y Power BI acepta la tabla de fechas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.3: Configurar fechas y orden de meses. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-4"></a>
<p><strong>3.3.4. Relacionar Calendario con ventas</strong></p>
<ol><li>En Vista Modelo, relacionar <code>Calendario[Date]</code> con <code>sales[OrderDate]</code>.</li><li>Configurar <strong>1:*</strong>, filtro <strong>Único</strong> desde Calendario hacia sales y relación <strong>Activa</strong>.</li><li>Usar esta relación para la fecha de compra. Mantener <code>DeliveryDate</code> fuera de esta práctica.</li><li>Capturar el modelo completo con las cuatro dimensiones alrededor de <code>sales</code>.</li></ol>
<p><strong>Comprobar:</strong> el modelo presenta cuatro relaciones activas hacia la tabla de ventas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.4: Relacionar Calendario con ventas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-5"></a>
<p><strong>3.3.5. Crear las tres medidas mínimas del análisis</strong></p>
<ol><li>En el panel Datos, seleccionar <code>sales</code> y abrir <strong>Modelado → Nueva medida</strong> o <strong>Inicio → Nueva medida</strong>.</li><li>Crear cada una de las tres medidas siguientes por separado. ImporteVenta ya fue calculada por BaseContoso en 3.1.</li><li>Continuar después con las medidas de clientes, ticket y variaciones. Todas se construyen dentro de este archivo.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Ventas Totales = SUM(sales[ImporteVenta])</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Unidades Totales = SUM(sales[Quantity])</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Pedidos = DISTINCTCOUNT(sales[OrderKey])</code></pre></details>
<p><strong>Comprobar:</strong> las tres medidas muestran el icono de calculadora y proporcionan la base de los indicadores nuevos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.5: Crear las tres medidas mínimas del análisis. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-6"></a>
<p><strong>3.3.6. Calcular clientes compradores y promedios por cliente</strong></p>
<ol><li>Crear cada una de las tres medidas siguientes mediante <strong>Nueva medida</strong>.</li><li>Leer la explicación: el conteo parte de <code>sales</code> para incluir solo clientes que compraron en el contexto seleccionado.</li><li>Cuando el filtro no tenga ventas, las divisiones quedarán en blanco.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Clientes Compradores =
DISTINCTCOUNT(sales[CustomerKey])</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Pedidos por Cliente =
DIVIDE([Pedidos], [Clientes Compradores])</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Ventas por Cliente =
DIVIDE([Ventas Totales], [Clientes Compradores])</code></pre></details>
<p><strong>Comprobar:</strong> el conteo de compradores cambia al seleccionar otro mes o tienda; difiere del número total del catálogo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.6: Calcular clientes compradores y promedios por cliente. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-7"></a>
<p><strong>3.3.7. Calcular ticket y unidades por pedido</strong></p>
<ol><li>Crear las medidas siguientes.</li><li>Interpretar el ticket y las unidades por pedido dentro del período y las dimensiones seleccionadas.</li><li>En una categoría, interpretar el ticket como el importe de esa categoría por pedido que la incluye.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Ticket Promedio =
DIVIDE([Ventas Totales], [Pedidos])</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Unidades por Pedido =
DIVIDE([Unidades Totales], [Pedidos])</code></pre></details>
<p><strong>Comprobar:</strong> el total se obtiene dividiendo los totales; no promediando las filas visibles.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.7: Calcular ticket y unidades por pedido. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-8"></a>
<p><strong>3.3.8. Identificar clientes recurrentes en la selección</strong></p>
<ol><li>Crear las dos medidas siguientes.</li><li>La primera evalúa los clientes compradores y cuenta los que tienen al menos dos pedidos distintos dentro de la selección.</li><li>Un resultado igual a cero es válido. Con una categoría o tienda filtrada, la recurrencia se refiere a ese segmento.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Clientes Recurrentes =
COALESCE(
    COUNTROWS(
        FILTER(
            VALUES(sales[CustomerKey]),
            CALCULATE([Pedidos]) &gt;= 2
        )
    ),
    0
)</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Clientes Recurrentes % =
DIVIDE(
    [Clientes Recurrentes],
    [Clientes Compradores]
)</code></pre></details>
<p><strong>Comprobar:</strong> los recurrentes no superan a los compradores. El indicador describe la selección, sin presentarlo como tasa de retención.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.8: Identificar clientes recurrentes en la selección. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-9"></a>
<p><strong>3.3.9. Calcular las ventas del mes anterior completo</strong></p>
<ol><li>Crear la medida siguiente.</li><li><code>SELECTEDVALUE</code> identifica un único mes. <code>EDATE</code> obtiene el mes anterior. <code>REMOVEFILTERS</code> retira los filtros temporales antes de aplicar ese mes completo.</li><li>Los filtros de cliente, tienda y producto permanecen vigentes. Con varios meses seleccionados, esta medida queda en blanco por diseño.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Ventas Mes Anterior =
VAR MesActual =
    SELECTEDVALUE(Calendario[InicioMes])
VAR MesAnterior =
    IF(
        NOT ISBLANK(MesActual),
        EDATE(MesActual, -1)
    )
RETURN
    IF(
        NOT ISBLANK(MesActual),
        CALCULATE(
            [Ventas Totales],
            REMOVEFILTERS(Calendario),
            Calendario[InicioMes] = MesAnterior
        )
    )</code></pre></details>
<p><strong>Comprobar:</strong> al elegir un mes con referencia disponible, la medida coincide con las ventas del mes inmediatamente anterior bajo los mismos filtros. El primer mes sin historia previa queda sin base comparable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.9: Calcular las ventas del mes anterior completo. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-10"></a>
<p><strong>3.3.10. Calcular la variación absoluta y porcentual</strong></p>
<ol><li>Crear las dos medidas siguientes.</li><li>La variación absoluta considera cero una ausencia de ventas en uno de los dos meses. Así incluye productos que empiezan o dejan de venderse.</li><li>El porcentaje queda en blanco cuando la referencia es cero o no existe. Aplicar estas medidas a todos los meses del historial. Comprobar la referencia del primer mes y conservar Sin base comparable cuando corresponda.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Variacion Ventas =
VAR Actual = [Ventas Totales]
VAR Anterior = [Ventas Mes Anterior]
RETURN
    IF(
        HASONEVALUE(Calendario[InicioMes])
            &amp;&amp; NOT (
                ISBLANK(Actual)
                &amp;&amp; ISBLANK(Anterior)
            ),
        COALESCE(Actual, 0)
            - COALESCE(Anterior, 0)
    )</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Variacion Ventas % =
DIVIDE(
    [Variacion Ventas],
    [Ventas Mes Anterior]
)</code></pre></details>
<p><strong>Comprobar:</strong> para dos meses consecutivos elegidos como prueba, la variación absoluta coincide con la resta de ventas y la porcentual con su división por la venta anterior. Verificar el signo observado, sin imponer un resultado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.10: Calcular la variación absoluta y porcentual. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-11"></a>
<p><strong>3.3.11. Comparar pedidos y ticket con el mes anterior</strong></p>
<ol><li>Crear las cuatro medidas siguientes.</li><li>La referencia de pedidos utiliza exactamente el mismo mes y filtros que la referencia de ventas.</li><li>El ticket anterior se calcula dividiendo las ventas anteriores entre los pedidos anteriores.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Pedidos Mes Anterior =
VAR MesActual =
    SELECTEDVALUE(Calendario[InicioMes])
VAR MesAnterior =
    IF(
        NOT ISBLANK(MesActual),
        EDATE(MesActual, -1)
    )
RETURN
    IF(
        NOT ISBLANK(MesActual),
        CALCULATE(
            [Pedidos],
            REMOVEFILTERS(Calendario),
            Calendario[InicioMes] = MesAnterior
        )
    )</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Ticket Mes Anterior =
DIVIDE(
    [Ventas Mes Anterior],
    [Pedidos Mes Anterior]
)</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Variacion Pedidos % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Pedidos] - [Pedidos Mes Anterior],
        [Pedidos Mes Anterior]
    )
)</code></pre></details><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Variacion Ticket % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Ticket Promedio] - [Ticket Mes Anterior],
        [Ticket Mes Anterior]
    )
)</code></pre></details>
<p><strong>Comprobar:</strong> se puede interpretar si el cambio observado coincide con una variación de pedidos, de ticket o de ambos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.11: Comparar pedidos y ticket con el mes anterior. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-12"></a>
<p><strong>3.3.12. Calcular la participación de las categorías</strong></p>
<ol><li>Crear la medida siguiente.</li><li>Se utilizará en una matriz de categorías de <strong>Control_T2</strong>, con <code>CategoryName</code> como único nivel de filas.</li><li><code>ALLSELECTED</code> conserva la selección externa y calcula el denominador sobre las categorías seleccionadas. Si el usuario selecciona solo una categoría, su participación dentro de esa selección será 100 %.</li></ol><details><summary>Copiar fórmula DAX</summary><pre><code class="language-dax">Participacion Categoria % =
DIVIDE(
    [Ventas Totales],
    CALCULATE(
        [Ventas Totales],
        ALLSELECTED(product[CategoryName])
    )
)</code></pre></details>
<p><strong>Comprobar:</strong> las participaciones suman aproximadamente 100 % entre las categorías de la selección. Esta medida se conserva para la comprobación de categorías; no se usa en el Treemap con subcategorías ni en el detalle por producto.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.12: Calcular la participación de las categorías. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-13"></a>
<p><strong>3.3.13. Configurar formatos de las medidas</strong></p>
<ol><li>Seleccionar cada medida en el panel Datos y abrir <strong>Herramientas de medición → Formato</strong>.</li><li>Usar <strong>Número entero, 0 decimales</strong> para pedidos, unidades, compradores y recurrentes, incluidos los pedidos anteriores.</li><li>Usar <strong>Número decimal, 2 decimales</strong> para importes, ticket, ventas por cliente, pedidos por cliente y unidades por pedido.</li><li>Usar <strong>Porcentaje, 2 decimales</strong> para todas las medidas que terminan en <code>%</code>. Las fórmulas ya calculan la razón: el formato aplica la presentación porcentual.</li><li>En los visuales de comprobación, seleccionar <strong>Mostrar unidades: Ninguno</strong>.</li></ol>
<p><strong>Comprobar:</strong> los valores se muestran completos y las variaciones negativas conservan su signo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.13: Configurar formatos de las medidas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-14"></a>
<p><strong>3.3.14. Comprobar las medidas y el historial mensual</strong></p>
<ol><li>Renombrar la página inicial vacía como <code>Control_T2</code>. Insertar una tabla con Calendario[AnioMes] y las medidas de ventas, ventas anteriores, variación, pedidos y ticket.</li><li>En Filtros de esta página, agregar <code>Calendario[InicioMes]</code>. Mediante Filtrado avanzado, fijar <strong>es posterior o igual a PrimerMes</strong> y <strong>es anterior o igual a UltimoMes</strong>, usando las fechas anotadas en 3.1.6. Se incluyen ambos límites y todos los meses intermedios.</li><li>Comprobar dos meses consecutivos con datos y sus referencias. Agregar tarjetas de compradores y recurrentes y probar su respuesta por mes y tienda.</li><li>Sin filtros temporales ni de dimensión, comprobar los totales del historial. Como prueba adicional, filtrar temporalmente el año 2020: ventas 11.066.206,89, unidades 35.234 y pedidos 4.806. Después retirar ese filtro anual y restablecer el intervalo completo.</li><li>Guardar y mantener Control_T2 fuera de las segmentaciones sincronizadas.</li></ol>
<p><strong>Comprobar:</strong> los cálculos concilian con el origen, los controles se distinguen del diagnóstico y todos los meses del intervalo están disponibles para revisar.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.14: Comprobar las medidas y el historial mensual. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### 3.4. Visualizaciones y navegación entre vistas

**Propósito:** construir los gráficos nuevos y utilizarlos para responder preguntas distintas. Las cuatro vistas mensuales comparten filtros; Factores comerciales y Alertas comerciales conservan todo el historial con filtros propios. Conservar los controles en páginas técnicas para mantener legibles las vistas ejecutivas.

| Vista | Visualizaciones principales | Período |
|---|---|---|
| Gerencia | Tarjetas, cintas y cascada. | Mes seleccionado; cintas de todo el historial. |
| Clientes | Indicadores, Top 10 y país del cliente. | Mes seleccionado. |
| Tiendas | Barras apiladas al 100 % y dispersión con burbujas. | Mes seleccionado. |
| Productos | Treemap y árbol de descomposición. | Mes seleccionado. |
| Factores comerciales | Influenciadores clave y segmentos principales. | Todo el historial. |
| Alertas comerciales | Líneas con anomalías de ventas y pedidos y tabla de alertas. | Todos los meses del historial; se construye en 3.5. |

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-1"></a>
<p><strong>3.4.1. Crear seis vistas y preparar el lienzo</strong></p>
<ol><li>Crear las páginas <code>Gerencia</code>, <code>Clientes</code>, <code>Tiendas</code>, <code>Productos</code>, <code>Factores comerciales</code> y <code>Alertas comerciales</code>. Conservar <code>Control_T2</code> como página técnica de este archivo.</li><li>En cada página de negocio, abrir <strong>Formato de página → Configuración del lienzo</strong> y elegir <strong>16:9</strong>.</li><li>Reservar la franja superior para título, período y filtros. Ubicar las tarjetas debajo y los visuales principales en la parte inferior.</li><li>Usar colores estables para cada categoría en todos los gráficos. Mantener rojo para variaciones negativas y verde para positivas, junto con sus signos.</li><li>Distribuir los visuales grandes: cintas y cascada en Gerencia; barras al 100 % y dispersión en Tiendas; Treemap y árbol en Productos; influenciadores a mayor tamaño en Factores comerciales. Reservar Alertas comerciales para las líneas y la tabla de seguimiento que se crearán en 3.5.</li></ol>
<p><strong>Comprobar:</strong> cada vista tiene una función clara y los gráficos se leen sin superponerse.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.1: Crear seis vistas y preparar el lienzo. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-2"></a>
<p><strong>3.4.2. Configurar la selección de un mes para explorar</strong></p>
<ol><li>En Gerencia, Clientes, Tiendas y Productos, conservar el intervalo entre PrimerMes y UltimoMes mediante el filtro avanzado de Calendario[InicioMes], como en 3.3.14. Mantener disponibles todos sus años.</li><li>En Gerencia, insertar segmentaciones de Calendario[AnioMes], store[CountryTienda], store[Tienda] y product[CategoryName].</li><li>Activar Selección única en el mes. Elegir un mes con referencia anterior para probar los gráficos; esta selección inicial no lo declara como período crítico.</li><li>Titular los filtros Mes de análisis, País de la tienda, Tienda y Categoría. Mantener todos los segmentos. Los meses de investigación se elegirán después del seguimiento de toda la serie en 3.5.</li></ol>
<p><strong>Comprobar:</strong> se puede elegir cualquier mes del historial; las vistas de detalle muestran uno a la vez y conservan su comparación con el anterior.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.2: Configurar la selección de un mes para explorar. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-3"></a>
<p><strong>3.4.3. Sincronizar solo las cuatro vistas mensuales</strong></p>
<ol><li>Seleccionar el segmentador de mes y abrir <strong>Vista → Sincronizar segmentaciones</strong>.</li><li>Activar Sincronizar y Visible en Gerencia, Clientes, Tiendas y Productos. Repetir con país de la tienda, tienda y categoría.</li><li>Dejar sin sincronizar <code>Factores comerciales</code>, <code>Alertas comerciales</code> y <code>Control_T2</code>. Ajustar la ubicación de los segmentadores visibles.</li><li>Probar un cambio de mes entre las cuatro vistas mensuales y restaurar el mes de prueba.</li></ol>
<p><strong>Comprobar:</strong> el contexto mensual se comparte entre las cuatro vistas; Factores comerciales y Alertas comerciales conservarán su alcance histórico independiente.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.3: Sincronizar solo las cuatro vistas mensuales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-4"></a>
<p><strong>3.4.4. Gerencia: crear los indicadores principales</strong></p>
<ol><li>Crear cinco tarjetas con Ventas Totales, Variacion Ventas %, Pedidos, Clientes Compradores y Ticket Promedio.</li><li>Configurar unidades completas y los decimales definidos en 3.3.13. Alinear y distribuir las tarjetas.</li><li>Agregar un título: <strong>Diagnóstico comercial: mes seleccionado frente al anterior</strong>.</li><li>En <code>Control_T2</code>, conservar una tabla con pedidos actuales y anteriores, sus variaciones y los tickets de ambos meses para comprobar las cifras.</li></ol>
<p><strong>Comprobar:</strong> las tarjetas responden al mes y a las dimensiones seleccionadas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.4: Gerencia: crear los indicadores principales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-5"></a>
<p><strong>3.4.5. Gerencia: construir el gráfico de cintas</strong></p>
<ol><li>Seleccionar <strong>Gráfico de cintas</strong> en Visualizaciones.</li><li>Eje X: <code>Calendario[AnioMes]</code>. Eje Y o Valores: <code>Ventas Totales</code>. Leyenda: <code>product[CategoryName]</code>.</li><li>Ordenar el eje por <code>AnioMes</code> ascendente. Mantener el intervalo histórico completo y todos los años disponibles.</li><li>Seleccionar la segmentación de mes, abrir <strong>Formato → Editar interacciones</strong> y marcar <strong>Ninguno</strong> únicamente sobre las cintas. Salir de Editar interacciones.</li><li>Conservar las interacciones de tienda y categoría sobre este gráfico.</li></ol>
<p><strong>Comprobar:</strong> las cintas muestran todos los meses del historial mientras los indicadores representan el mes elegido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.5: Gerencia: construir el gráfico de cintas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-6"></a>
<p><strong>3.4.6. Gerencia: interpretar el liderazgo con cintas</strong></p>
<ol><li>Titular el gráfico <strong>Liderazgo de categorías en el historial</strong>. Mantener visibles la leyenda y los colores de las categorías.</li><li>En Formato visual, revisar las opciones de Cintas: conservar el color de la serie, ajustar la transparencia y dejar separación suficiente para distinguirlas.</li><li>Pasar el cursor por dos meses consecutivos. Identificar la categoría ubicada arriba y revisar si cambian las posiciones.</li><li>Registrar un cambio de posición o la estabilidad del liderazgo. Indicar los meses y las ventas que respaldan la observación.</li><li>Realizar esta comparación con varias categorías; al seleccionar una sola, el gráfico pierde la comparación de posiciones entre categorías.</li></ol>
<p><strong>Comprobar:</strong> se interpreta la posición relativa y el importe de las categorías, sin presuponer que siempre deben cambiar de puesto.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.6: Gerencia: interpretar el liderazgo con cintas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-7"></a>
<p><strong>3.4.7. Gerencia: crear la cascada de contribuciones</strong></p>
<ol><li>Insertar un <strong>Gráfico de cascada</strong>. Categoría: <code>product[CategoryName]</code>. Eje Y: <code>Variacion Ventas</code>. Dejar Desglose vacío.</li><li>Activar etiquetas de datos y titular <strong>Aportes por categoría a la variación mensual</strong>.</li><li>Mantener la interacción del mes con la cascada: cada mes elegido se compara con el inmediatamente anterior.</li><li>Conservar todas las categorías de la selección. Comparar el total de la cascada con la variación absoluta en <code>Control_T2</code> bajo los mismos filtros.</li></ol>
<p><strong>Comprobar:</strong> las contribuciones positivas y negativas concilian con el cambio mensual de la misma selección.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.7: Gerencia: crear la cascada de contribuciones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-8"></a>
<p><strong>3.4.8. Clientes: presentar compradores y principales clientes</strong></p>
<ol><li>En Clientes, crear tarjetas de Clientes Compradores, Pedidos por Cliente, Ventas por Cliente y Clientes Recurrentes %.</li><li>Insertar una tabla con <code>customer[Cliente]</code>, Ventas Totales, Pedidos y Ticket Promedio.</li><li>En Filtros de este objeto visual, configurar Cliente como <strong>Top N → Superior: 10</strong>, Por valor: Ventas Totales. Pulsar Aplicar filtro y ordenar por ventas descendentes.</li><li>Titular <strong>Diez principales clientes por ventas del mes</strong>. Mantener el filtro Top N limitado a la tabla.</li><li>Agregar una nota: <em>Compradores y recompra dentro del mes y filtros seleccionados.</em></li></ol>
<p><strong>Comprobar:</strong> las tarjetas representan el conjunto seleccionado; la tabla representa sus principales compradores.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.8: Clientes: presentar compradores y principales clientes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-9"></a>
<p><strong>3.4.9. Clientes: distinguir el país del comprador</strong></p>
<ol><li>Insertar barras agrupadas: eje Y <code>customer[CountryCliente]</code>, eje X Ventas Totales.</li><li>Ordenar por ventas descendentes y titular <strong>Ventas por país del cliente</strong>.</li><li>Agregar una segmentación local de CountryCliente. Mantenerla solo en esta vista.</li><li>Comparar el efecto de País del cliente con el de País de la tienda. Limpiar el filtro local al finalizar.</li></ol>
<p><strong>Comprobar:</strong> se distinguen las dos perspectivas geográficas. La ruta alternativa muestra Sin información cuando no hay ficha descriptiva.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.9: Clientes: distinguir el país del comprador. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-10"></a>
<p><strong>3.4.10. Tiendas: crear barras apiladas al 100 %</strong></p>
<ol><li>En Tiendas, seleccionar <strong>Gráfico de barras apiladas 100 %</strong>. Comprobar el icono para elegir la versión normalizada.</li><li>Eje Y: <code>store[Tienda]</code>. Eje X o Valores: <code>Ventas Totales</code>. Leyenda: <code>product[CategoryName]</code>.</li><li>Mantener todas las categorías o varias categorías seleccionadas. Titular <strong>Composición de ventas por tienda y categoría</strong>.</li><li>Si hay muchas tiendas, aplicar Top N de tiendas por Ventas Totales solo a este visual. Conservar todas las categorías dentro de cada tienda mostrada.</li><li>Agregar Ventas Totales y Unidades Totales a Información sobre herramientas, cuando el panel permita añadirlas.</li></ol>
<p><strong>Comprobar:</strong> cada barra representa una tienda y sus segmentos muestran proporciones de las categorías seleccionadas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.10: Tiendas: crear barras apiladas al 100 %. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-11"></a>
<p><strong>3.4.11. Tiendas: formatear e interpretar los porcentajes</strong></p>
<ol><li>Activar las etiquetas de datos y su presentación porcentual cuando esté disponible. Conservar la leyenda y colores consistentes.</li><li>Verificar que el eje de proporciones recorra de 0 % a 100 %. El visual normaliza Ventas Totales automáticamente.</li><li>Comparar dos tiendas y localizar qué categoría tiene mayor peso en cada una. Revisar los importes reales con el cursor o en la matriz de comprobación.</li><li>Verificar que cada barra sume aproximadamente 100 %; puede existir una diferencia mínima por redondeo.</li><li>Explicar que la misma longitud total de las barras no significa iguales ventas. Con una sola categoría seleccionada, cada tienda tendrá un único segmento de 100 %.</li></ol>
<p><strong>Comprobar:</strong> se diferencia composición porcentual de volumen absoluto y se declara qué categorías forman el denominador.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.11: Tiendas: formatear e interpretar los porcentajes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-12"></a>
<p><strong>3.4.12. Tiendas: crear la dispersión con burbujas</strong></p>
<ol><li>Insertar un <strong>Gráfico de dispersión</strong>. Eje X: Ventas Totales. Eje Y: Variacion Ventas %.</li><li>Agregar <code>store[Tienda]</code> al campo que identifica los puntos: <strong>Valores</strong> o <strong>Detalles</strong>, según la versión. Debe quedar una burbuja por tienda.</li><li>Agregar Pedidos a <strong>Tamaño</strong> y <code>store[CountryTienda]</code> a Leyenda.</li><li>En Información sobre herramientas, agregar Ticket Promedio y Ventas Mes Anterior.</li><li>Titular <strong>Tiendas: nivel de ventas y variación mensual</strong>. Dar al eje Y formato de porcentaje.</li></ol>
<p><strong>Comprobar:</strong> la posición horizontal indica ventas, la vertical indica variación y el tamaño representa pedidos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.12: Tiendas: crear la dispersión con burbujas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-13"></a>
<p><strong>3.4.13. Tiendas: agregar referencia y comparar desempeño</strong></p>
<ol><li>Seleccionar la dispersión y abrir el panel <strong>Análisis</strong>. Agregar una <strong>Línea constante del eje Y</strong> con valor <strong>0</strong>.</li><li>Identificar tiendas por encima y por debajo de esa línea. Comparar además sus ventas y el tamaño de las burbujas.</li><li>Seleccionar una burbuja, revisar su etiqueta e información emergente y contrastar el resultado con la tienda elegida en el filtro sincronizado.</li><li>Comprobar en la matriz las tiendas sin base anterior: su variación porcentual queda en blanco y pueden no aparecer en la dispersión.</li><li>Limpiar la selección directa al terminar. Formular una prioridad de revisión sustentada en magnitud y variación.</li></ol>
<p><strong>Comprobar:</strong> se distinguen crecimiento y disminución, conservando el contexto de volumen; se reconoce la ausencia de base comparable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.13: Tiendas: agregar referencia y comparar desempeño. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-14"></a>
<p><strong>3.4.14. Productos: construir el mapa de árbol o Treemap</strong></p>
<ol><li>En Productos, seleccionar <strong>Mapa de árbol</strong> o <strong>Treemap</strong>.</li><li>Categoría o Grupo: <code>product[CategoryName]</code>. Detalles: <code>product[SubCategoryName]</code>. Valores: Ventas Totales.</li><li>Agregar Unidades Totales y Pedidos a Información sobre herramientas. Mantener las ventas como medida del área.</li><li>Titular <strong>Concentración del ingreso por categoría y subcategoría</strong>.</li><li>Activar las etiquetas de categoría y ajustar su tamaño para que las áreas principales se identifiquen con claridad.</li></ol>
<p><strong>Comprobar:</strong> las áreas representan ventas y los rectángulos internos distinguen las subcategorías de cada categoría.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.14: Productos: construir el mapa de árbol o Treemap. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-15"></a>
<p><strong>3.4.15. Productos: explorar la composición del Treemap</strong></p>
<ol><li>Comparar las áreas de las categorías y pasar el cursor sobre sus subcategorías para revisar las cifras.</li><li>Seleccionar una subcategoría y observar el filtrado de los otros visuales de esta página. Comprobar que el árbol de descomposición, cuando esté construido, responda a esa selección.</li><li>Limpiar la selección para restaurar el portafolio. Cambiar de mes y verificar que las áreas respondan.</li><li>Registrar dónde se concentra el ingreso y contrastar los importes en la matriz de detalle.</li></ol>
<p><strong>Comprobar:</strong> la interpretación utiliza la jerarquía y sus importes. Los rectángulos pequeños se consultan con el cursor o en detalle.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.15: Productos: explorar la composición del Treemap. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-16"></a>
<p><strong>3.4.16. Productos: construir el árbol de descomposición</strong></p>
<ol><li>Insertar un <strong>Árbol de descomposición</strong> y darle suficiente ancho. Utilizar Modo de enfoque para explorar rutas extensas.</li><li>En <strong>Analizar</strong>, colocar Ventas Totales.</li><li>En <strong>Explicar por</strong>, colocar <code>store[CountryTienda]</code>, <code>store[Tienda]</code>, <code>product[CategoryName]</code>, <code>product[SubCategoryName]</code> y <code>product[Brand]</code>.</li><li>Titular <strong>Exploración de ventas por tienda y portafolio</strong>.</li><li>Con los demás visuales sin selección, comprobar que el nodo raíz coincida con Ventas Totales en la misma selección de filtros.</li></ol>
<p><strong>Comprobar:</strong> el nodo raíz agrega las ventas y el signo + permite abrir distintas dimensiones.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.16: Productos: construir el árbol de descomposición. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-17"></a>
<p><strong>3.4.17. Productos: explorar rutas manuales y automáticas</strong></p>
<ol><li>Seleccionar el <strong>+</strong> del nodo raíz. Elegir País de la tienda; dentro de un país elegir Tienda; después abrir Categoría y Subcategoría.</li><li>Registrar una ruta y el importe de su último nodo. Contrastar el resultado aplicando los mismos filtros en una matriz.</li><li>Volver a la raíz y probar una ruta distinta, comenzando por Categoría. El árbol permite elegir el orden de las dimensiones.</li><li>Si aparece la bombilla de análisis, probar <strong>Valor alto</strong> o <strong>Valor bajo</strong> y explicar qué dimensión seleccionó el visual.</li><li>Interpretar el resultado como un desglose de ventas. Contrastar con el detalle si el visual limita la cantidad de nodos visibles; los nodos mostrados no constituyen necesariamente el catálogo completo.</li></ol>
<p><strong>Comprobar:</strong> se demuestra una ruta manual y se interpreta la sugerencia automática cuando esté disponible. El árbol localiza contribuciones y no demuestra causas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.17: Productos: explorar rutas manuales y automáticas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-18"></a>
<p><strong>3.4.18. Crear matrices y formato condicional como apoyo</strong></p>
<ol><li>En <code>Control_T2</code>, seleccionar manualmente el mismo <code>Calendario[AnioMes]</code> de la vista que se quiera comprobar. Retirar ese filtro al volver a revisar la serie completa. Igualar también los filtros de dimensiones cuando compare con otra vista. Crear una matriz de categorías con CategoryName, ventas actuales, ventas anteriores, variación absoluta, variación porcentual y Participacion Categoria %.</li><li>Crear una matriz de tiendas con filas CountryTienda → State → Tienda y valores Ventas Totales, Ventas Mes Anterior, Variacion Ventas y Ticket Promedio.</li><li>Crear una matriz de producto con filas CategoryName → SubCategoryName → ProductName y los valores de ventas y variaciones. Activar los iconos +/−.</li><li>Para las variaciones, abrir el menú de la medida en Valores → <strong>Formato condicional → Color de fondo</strong>. Usar mínimo rojo, centro numérico 0 blanco y máximo verde.</li><li>Si los controles no caben con legibilidad, duplicar la página técnica y nombrar los apoyos <code>Control_Tiendas</code> o <code>Control_Productos</code>. Mantenerlos sin segmentadores sincronizados.</li></ol>
<p><strong>Comprobar:</strong> se conservan los detalles verificables y las jerarquías, mientras las vistas ejecutivas priorizan los gráficos nuevos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.18: Crear matrices y formato condicional como apoyo. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-19"></a>
<p><strong>3.4.19. Preparar Factores comerciales con alcance histórico</strong></p>
<ol><li>Abrir Factores comerciales y conservar todos los meses entre PrimerMes y UltimoMes. Retirar filtros de un solo año o mes.</li><li>Confirmar que no existan filtros de producto ni segmentadores sincronizados desde las vistas de detalle mensual.</li><li>Titular <strong>Factores asociados con el importe por pedido: historial completo</strong>.</li><li>Agregar tarjetas de Pedidos, Clientes Compradores y Ticket Promedio. Sin filtros de dimensiones, comprobar los 83.130 pedidos del historial de referencia.</li><li>Si incorpora filtros locales para experimentar, restaurar después todo el historial y todos los segmentos.</li></ol>
<p><strong>Comprobar:</strong> el análisis por pedido declara su alcance histórico. Revisar los recuentos mostrados por el visual: sus resultados pueden basarse en una muestra, como indica la documentación oficial de influenciadores.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.19: Preparar Factores comerciales con alcance histórico. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-20"></a>
<p><strong>3.4.20. Comprobar el importe a nivel de pedido</strong></p>
<ol><li>En la página técnica, insertar una tabla temporal con <code>sales[OrderKey]</code>, <code>sales[OrderDate]</code>, Ventas Totales, Pedidos y Ticket Promedio.</li><li>Usar los campos de identificación como No resumir, mantener el intervalo histórico completo y excluir LineNumber y productos del detalle de esta tabla.</li><li>Seleccionar un pedido. Comprobar que Pedidos = 1 y que Ticket Promedio = Ventas Totales de ese pedido.</li><li>Recordar el control de 3.2.7: cliente, tienda y fecha deben ser únicos para cada pedido.</li></ol>
<p><strong>Comprobar:</strong> el objetivo del análisis representa el importe del pedido completo y no el precio o importe de una línea individual.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.20: Comprobar el importe a nivel de pedido. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-21"></a>
<p><strong>3.4.21. Configurar el objetivo de influenciadores clave</strong></p>
<ol><li>En Factores comerciales, insertar el visual <strong>Influenciadores clave</strong> y reservarle la mayor parte del lienzo.</li><li>En <strong>Analizar</strong>, colocar la medida <code>Ticket Promedio</code>.</li><li>En <strong>Expandir por</strong>, colocar <code>sales[OrderKey]</code>. Este campo establece el nivel del análisis.</li><li>Elegir el análisis de lo que hace <strong>aumentar</strong> el objetivo. Al utilizar una medida numérica, se interpreta un resultado continuo.</li><li>Titular <strong>Características asociadas con pedidos de mayor importe</strong>.</li></ol>
<p><strong>Comprobar:</strong> el visual analiza la medida por pedido y mantiene el alcance histórico declarado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.21: Configurar el objetivo de influenciadores clave. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-22"></a>
<p><strong>3.4.22. Agregar factores explicativos compatibles</strong></p>
<ol><li>En <strong>Explicar por</strong>, agregar <code>customer[CountryCliente]</code>, <code>store[CountryTienda]</code>, <code>Calendario[Anio]</code> y <code>Calendario[Mes]</code>.</li><li>Esperar a que termine el análisis. Mantener las claves de cliente y tienda fuera de Explicar por: son identificadores.</li><li>Conservar OrderKey únicamente en Expandir por. No agregar categoría como un atributo único de pedido, pues un pedido puede incluir varias categorías.</li><li>Mantener fuera de Explicar por precio, cantidad y la propia medida objetivo. Conservar también la página sin filtros de producto para analizar pedidos completos.</li></ol>
<p><strong>Comprobar:</strong> los factores corresponden al mismo nivel de pedido y se distingue el identificador del factor explicativo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.22: Agregar factores explicativos compatibles. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-23"></a>
<p><strong>3.4.23. Interpretar factores y ausencia de patrones</strong></p>
<ol><li>Seleccionar un factor mostrado y leer la comparación del panel derecho. Registrar el grupo, la referencia y la diferencia estimada que aparezca.</li><li>En Formato visual → Análisis, activar los recuentos cuando estén disponibles y revisar el tamaño del grupo.</li><li>Cambiar un filtro local y observar si el análisis se recalcula. Restaurar todo el historial sin filtros locales.</li><li>Si no encuentra influenciadores, revisar contexto y configuración; registrar el resultado cuando no haya asociaciones suficientes para informar.</li><li>Redactar el hallazgo como una asociación dentro de estos datos. Comprobar una causa requeriría evidencia adicional.</li></ol>
<p><strong>Comprobar:</strong> la interpretación informa el contexto y el respaldo observado; no garantiza factores ni atribuye causalidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.23: Interpretar factores y ausencia de patrones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-24"></a>
<p><strong>3.4.24. Explorar segmentos principales</strong></p>
<ol><li>Dentro de Influenciadores clave, abrir la pestaña <strong>Segmentos principales</strong> o <strong>Top segments</strong>.</li><li>Si aparecen segmentos, seleccionar uno y revisar la combinación de condiciones que lo define.</li><li>Registrar el importe medio del segmento, su comparación con el conjunto y el número de observaciones indicado.</li><li>Si no se muestran segmentos, consignar ese resultado sin crear grupos manuales para simular una conclusión.</li></ol>
<p><strong>Comprobar:</strong> se distingue un factor individual de una combinación de características.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.24: Explorar segmentos principales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-25"></a>
<p><strong>3.4.25. Revisar las interacciones de los nuevos gráficos</strong></p>
<ol><li>Abrir <strong>Formato → Editar interacciones</strong>. Confirmar que el mes filtre indicadores y cascada, mientras las cintas conserven todo el historial.</li><li>En Tiendas, probar la selección de una burbuja y de un segmento al 100 %. Revisar qué tienda y categoría está seleccionando cada acción.</li><li>En Productos, seleccionar un rectángulo y comprobar la respuesta del árbol. Limpiar la selección antes de conciliar el nodo raíz.</li><li>Comprobar que las segmentaciones sincronizadas de las cuatro vistas mensuales no afecten Factores comerciales, Alertas comerciales ni los controles técnicos.</li><li>Mantener visibles los títulos del período y limpiar selecciones directas al pasar a una nueva comprobación.</li></ol>
<p><strong>Comprobar:</strong> se explican los cambios de contexto y se distingue el detalle de un mes del seguimiento de toda la serie.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.25: Revisar las interacciones de los nuevos gráficos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-26"></a>
<p><strong>3.4.26. Preparar el procedimiento de investigación por mes</strong></p>
<ol><li>Para el mes de prueba elegido, comprobar la variación general en la cascada y en la matriz bajo los mismos filtros.</li><li>Explorar tiendas con dispersión y barras al 100 %, y categorías y productos con Treemap y árbol de descomposición.</li><li>Consultar Clientes, pedidos y ticket para interpretar los indicadores. En las cintas, ubicar ese mes dentro de la evolución histórica.</li><li>Limpiar los filtros al terminar. Estas comprobaciones preparan las herramientas; la priorización de meses se realizará en 3.5 después de evaluar toda la serie.</li><li>Distinguir los resultados del mes de prueba de las asociaciones por pedido del historial completo.</li></ol>
<p><strong>Comprobar:</strong> las vistas permiten investigar cualquier mes sin una caída o un aumento predeterminados.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.26: Preparar el procedimiento de investigación por mes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-27"></a>
<p><strong>3.4.27. Guardar el avance y capturar las evidencias</strong></p>
<ol><li>En las cuatro vistas mensuales, conservar el mes de prueba y limpiar los filtros de dimensión y las selecciones directas en gráficos.</li><li>En Factores comerciales, mantener todo el historial sin filtros locales. Comprobar los títulos de cada período.</li><li>Capturar el modelo y las configuraciones de los seis gráficos nuevos. Pegar las imágenes en las celdas derechas de los pasos correspondientes.</li><li>Guardar el PBIX en Gerencia. Conservar las seis vistas y los controles técnicos creados en este taller.</li></ol>
<p><strong>Comprobar:</strong> el informe conserva una selección inicial coherente y las evidencias están ubicadas en sus pasos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.27: Guardar el avance y capturar las evidencias. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-5"></a>
### 3.5. Detección de cambios, anomalías y alertas comerciales

**Propósito:** descubrir cambios relevantes mediante un seguimiento de todos los meses, antes de seleccionar los períodos que se investigarán en detalle. Se contrastan una regla de negocio, la detección de anomalías y las contribuciones por dimensión, utilizando el modelo construido en este taller.

**Alcance:** ventas y pedidos de todos los meses entre PrimerMes y UltimoMes, con todos los segmentos en el seguimiento general. La tabla calcula cada mes frente al anterior y conserva los casos sin base comparable. La página histórica de alertas no se sincroniza con la selección mensual de Gerencia.

**Resultado:** página Alertas comerciales con dos líneas, tabla mensual, umbral visible y síntesis de la revisión. Control_Alertas conserva los apoyos técnicos. Las alertas se consultan como señales dentro del informe.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-1"></a>
<p><strong>3.5.1. Preparar el seguimiento de todos los meses</strong></p>
<ol><li>Abrir Alertas comerciales. En Filtros de esta página, agregar Calendario[InicioMes] y fijar el intervalo inclusivo entre PrimerMes y UltimoMes mediante Filtrado avanzado, como en 3.3.14.</li><li>Dejar disponibles todos los años y todos los meses intermedios. Confirmar que la página quede fuera de todas las segmentaciones sincronizadas y retirar filtros ocultos que reduzcan el período.</li><li>Mantener todos los clientes, tiendas y productos. Titular <strong>Seguimiento y alertas de todo el historial mensual</strong> e indicar las fechas de cobertura.</li><li>Distribuir las dos líneas en la franja superior y la tabla mensual debajo. Documentar la cobertura de los meses de los extremos antes de interpretar sus cambios.</li></ol>
<p><strong>Comprobar:</strong> el seguimiento incluye todo el intervalo del origen y no cambia al seleccionar un mes en Gerencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.1: Preparar el seguimiento de todos los meses. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-2"></a>
<p><strong>3.5.2. Crear el umbral y las alertas de ambas variables</strong></p>
<ol><li>Seleccionar sales y crear las cuatro medidas siguientes, cada una desde <strong>Nueva medida</strong>.</li><li>Dar a <code>Umbral Alerta %</code> formato <strong>Porcentaje, 0 decimales</strong>. Mantener Alerta Ventas y Alerta Pedidos como resultados de texto y Color Alerta Ventas como código de color.</li><li>La comparación incluye el límite: una variación de −30 % o +30 % genera revisión. La alerta queda vacía cuando el contexto abarca varios meses.</li><li>Alerta Ventas utiliza Variacion Ventas % y Alerta Pedidos utiliza Variacion Pedidos %. Ambas evalúan cada mes con el mismo umbral.</li></ol><details><summary>Copiar medida Umbral Alerta %</summary><pre><code class="language-dax">Umbral Alerta % = 0.30</code></pre></details><details><summary>Copiar medida Alerta Ventas</summary><pre><code class="language-dax">Alerta Ventas =
VAR MesActual =
    SELECTEDVALUE(Calendario[InicioMes])
VAR Cambio = [Variacion Ventas %]
RETURN
    IF(
        ISBLANK(MesActual),
        BLANK(),
        SWITCH(
            TRUE(),
            ISBLANK(Cambio), &quot;Sin base comparable&quot;,
            Cambio &lt;= -[Umbral Alerta %],
                &quot;Revisar caída&quot;,
            Cambio &gt;= [Umbral Alerta %],
                &quot;Revisar aumento&quot;,
            &quot;Dentro del umbral&quot;
        )
    )</code></pre></details><details><summary>Copiar medida Color Alerta Ventas</summary><pre><code class="language-dax">Color Alerta Ventas =
SWITCH(
    [Alerta Ventas],
    &quot;Revisar caída&quot;, &quot;#B91C1C&quot;,
    &quot;Revisar aumento&quot;, &quot;#166534&quot;,
    &quot;Dentro del umbral&quot;, &quot;#374151&quot;,
    &quot;#6B7280&quot;
)</code></pre></details>
<details><summary>Copiar medida Alerta Pedidos</summary><pre><code class="language-dax">Alerta Pedidos =
VAR MesActual =
    SELECTEDVALUE(Calendario[InicioMes])
VAR Cambio = [Variacion Pedidos %]
RETURN
    IF(
        ISBLANK(MesActual),
        BLANK(),
        SWITCH(
            TRUE(),
            ISBLANK(Cambio), &quot;Sin base comparable&quot;,
            Cambio &lt;= -[Umbral Alerta %],
                &quot;Revisar caída&quot;,
            Cambio &gt;= [Umbral Alerta %],
                &quot;Revisar aumento&quot;,
            &quot;Dentro del umbral&quot;
        )
    )</code></pre></details>
<p><strong>Comprobar:</strong> el umbral inicial es 30 %; una base anterior cero queda como Sin base comparable, sin marcarla como Dentro del umbral.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.2: Crear el umbral y las alertas de ambas variables. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-3"></a>
<p><strong>3.5.3. Evaluar y clasificar todos los meses en la tabla</strong></p>
<ol><li>Insertar una Tabla con Calendario[AnioMes], Ventas Totales, Ventas Mes Anterior, Variacion Ventas, Variacion Ventas %, Alerta Ventas, Pedidos, Variacion Pedidos %, Alerta Pedidos y Variacion Ticket %.</li><li>Ordenar AnioMes ascendente, mostrar importes completos y porcentajes con dos decimales y desactivar la fila de total. Ajustar anchos y desplazamiento para conservar la lectura.</li><li>Aplicar a Alerta Ventas Formato condicional → Color de fuente → Valor de campo → Color Alerta Ventas. Conservar las etiquetas de ambas alertas.</li><li>Agregar la tarjeta Umbral Alerta % y titularla Límite de revisión mensual.</li><li>Verificar que aparezcan todos los meses desde PrimerMes hasta UltimoMes, también los que no generen alertas. Registrar meses sin observaciones, base cero o cobertura pendiente antes de concluir sobre el negocio.</li><li>Comprobar manualmente dos filas elegidas: ventas anteriores, variación, signo y clasificación. Las dos alertas se calculan para cada mes; no se selecciona un período para obtener un resultado esperado.</li></ol>
<p><strong>Comprobar:</strong> la tabla constituye el inventario completo de meses evaluados y distingue alertas, cambios dentro del umbral y casos sin base comparable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.3: Evaluar y clasificar todos los meses en la tabla. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-4"></a>
<p><strong>3.5.4. Crear la serie mensual de ventas</strong></p>
<ol><li>Insertar un <strong>Gráfico de líneas</strong>. Eje X: <code>Calendario[InicioMes]</code> como fecha directa, sin jerarquía automática. Eje Y: <code>Ventas Totales</code>.</li><li>En Formato visual → Eje X, elegir <strong>Continuo</strong>. Ordenar por InicioMes ascendente. Dejar vacíos Leyenda y eje Y secundario; usar una sola medida.</li><li>Conservar toda la secuencia mensual cuya cobertura se haya validado, sin seleccionar meses por el resultado. Titular <strong>Ventas mensuales y anomalías: historial</strong>. Si un extremo está incompleto, excluirlo solo de la detección y documentarlo; mantenerlo visible en el inventario mensual.</li><li>Si se incorpora un segmentador local de mes, usar Editar interacciones → Ninguno sobre las líneas y la tabla de seguimiento para preservar el período completo.</li></ol>
<p><strong>Comprobar:</strong> el eje muestra fechas mensuales y el gráfico tiene una sola serie. No se usa AnioMes como texto en este eje continuo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.4: Crear la serie mensual de ventas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-5"></a>
<p><strong>3.5.5. Activar anomalías y comparar la sensibilidad</strong></p>
<ol><li>Seleccionar la línea de ventas y abrir el panel <strong>Análisis → Anomalías → Agregar</strong>, según la denominación de su versión.</li><li>Mostrar el rango esperado y registrar la sensibilidad inicial. Compararla con un ajuste mayor y anotar cómo cambia el conjunto de señales.</li><li>Elegir una configuración y justificarla por su utilidad para revisar la serie, sin ajustarla para forzar una conclusión sobre un mes.</li><li>Conservar al menos cuatro puntos temporales para la función. Registrar todos los meses señalados en la serie y conservar también el resultado cuando no haya anomalías.</li></ol><p>Consultar las opciones en <a href="https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-analytics-pane">Microsoft Learn: panel Análisis</a>.</p>
<p><strong>Comprobar:</strong> se documentan el período, la sensibilidad y los puntos señalados. La ausencia de anomalías también es un resultado válido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.5: Activar anomalías y comparar la sensibilidad. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-6"></a>
<p><strong>3.5.6. Investigar una anomalía y contrastar sus explicaciones</strong></p>
<ol><li>En <strong>Explicar por</strong>, agregar <code>product[CategoryName]</code>, <code>store[CountryTienda]</code> y <code>customer[CountryCliente]</code>.</li><li>Seleccionar un punto marcado y consultar el panel de explicaciones. Registrar la dimensión, el grupo y la evidencia mostrada.</li><li>Contrastar ese segmento y período con las matrices. Igualar todos los filtros al comparar.</li><li>Si no se señalan puntos o no aparecen explicaciones, registrar ese resultado y continuar con el análisis de cambios del paso siguiente.</li></ol>
<p><strong>Comprobar:</strong> las explicaciones automáticas se presentan como pistas para investigar; se verifican con datos y no se declaran como causas demostradas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.6: Investigar una anomalía y contrastar sus explicaciones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-7"></a>
<p><strong>3.5.7. Investigar los cambios identificados en la serie</strong></p>
<ol><li>Crear Control_Alertas, sin segmentaciones sincronizadas. Copiar allí la línea de ventas, retirar Anomalías de la copia y conservar el intervalo histórico completo validado.</li><li>Para cada mes señalado por alguna regla o detector, registrar la variable y el tipo de señal en el registro de seguimiento. Hacer clic derecho en el punto y usar <strong>Analizar → Explicar la disminución</strong> o <strong>Explicar el aumento</strong>, según el signo observado.</li><li>Confirmar que la comparación corresponda al mes inmediatamente anterior. Si hay un mes sin registros o cobertura pendiente, verificarlo antes de interpretar la comparación automática entre puntos.</li><li>Contrastar los cambios de composición con la cascada y las matrices. Documentar resultados y ausencia de explicaciones relevantes.</li><li>Si el menú no aparece, revisar compatibilidad y filtros según la documentación, registrar la limitación y utilizar cascada y matrices para el contraste.</li></ol><p>Referencia: <a href="https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-insights">Microsoft Learn: explicar fluctuaciones</a>.</p>
<p><strong>Comprobar:</strong> el seguimiento registra todos los meses señalados y admite tanto aumentos como caídas; los meses del análisis detallado se justifican a partir de la evidencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.7: Investigar los cambios identificados en la serie. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-8"></a>
<p><strong>3.5.8. Aplicar la detección a los pedidos</strong></p>
<ol><li>En Alertas comerciales, duplicar la línea de ventas y sustituir únicamente su medida por <code>Pedidos</code>. Mantener el eje continuo y la misma cobertura histórica validada.</li><li>Revisar o volver a agregar Anomalías en la copia. Registrar su sensibilidad y titular <strong>Pedidos mensuales y anomalías: historial</strong>.</li><li>Comparar los meses señalados en ventas y pedidos. Revisar en la tabla Variacion Pedidos % y Variacion Ticket % para los meses estudiados.</li><li>Contrastar Alerta Pedidos con Variacion Pedidos % y registrar todas sus señales. La clasificación utiliza la variación de pedidos, mientras Alerta Ventas utiliza la de ventas.</li><li>Explicar si el cambio de ventas coincide con variaciones de pedidos, de ticket o de ambos. Una diferencia entre las señales de las dos series debe describirse, no corregirse para que coincidan.</li></ol>
<p><strong>Comprobar:</strong> cada variable tiene su propia detección y se conserva su unidad. La relación ventas = pedidos × ticket se usa como comprobación aritmética, sin atribuir causalidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.8: Aplicar la detección a los pedidos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-5-9"></a>
<p><strong>3.5.9. Priorizar los hallazgos y justificar las decisiones</strong></p>
<ol><li>Confirmar el barrido de todos los meses del intervalo y completar el <a href="#registro-alertas">registro de seguimiento de 4.1</a> para cada mes señalado por cualquiera de los métodos. Indicar también cuántos meses se evaluaron, cuántos tienen señales y cuáles requieren validar cobertura o referencia.</li><li>Modificar temporalmente Umbral Alerta % a 0.20 y después a 0.40. Registrar cuántos meses de ventas y de pedidos quedan para revisión en cada caso. Mantener la sensibilidad del detector y restaurar 0.30.</li><li>Priorizar al menos dos meses por magnitud monetaria, variación relativa, coincidencias entre variables y evidencia disponible. Investigar esos meses con Gerencia, Clientes, Tiendas y Productos. Si hay menos de dos meses señalados, incorporar meses de contraste y explicar su elección sin presentarlos como alertas.</li><li>Redactar tres hallazgos cuantificados y dos acciones priorizadas. Incorporar una síntesis en el informe y conservar el registro completo en Control_Alertas.</li><li>Guardar con uno de los meses priorizados seleccionado en las cuatro vistas mensuales, y con todo el historial en Alertas comerciales y Factores comerciales. Limpiar filtros de dimensiones y capturar las evidencias.</li></ol>
<p><strong>Comprobar:</strong> la cobertura es completa, las señales quedan registradas y la elección de meses se puede reproducir; ningún período crítico viene indicado en el enunciado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.5.9: Priorizar los hallazgos y justificar las decisiones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="fase-4"></a>
## Fase 4. Ejecutar y explicar | Storytelling empresarial

**Modalidad: validación, presentación ejecutiva y sustentación técnica.** Se utiliza la solución construida para sostener un diagnóstico, explicar sus límites y proponer acciones evaluables.

<a id="paso-4-1"></a>
### 4.1. Aplicar la comprobación final

En `Control_T2` y en las páginas correspondientes, verificar:

| Prueba | Resultado esperado |
|---|---|
| Todo 2020 sin filtros de dimensión | Ventas 11.066.206,89; unidades 35.234; pedidos 4.806. |
| Historial completo, sin filtros | Ventas 203.723.865,76; unidades 628.370; pedidos 83.130. |
| Referencia temporal | Cada mes se compara con el inmediatamente anterior, con los mismos filtros. |
| Cálculo de variaciones | La resta y la división se comprueban en dos filas elegidas; base cero o ausente queda sin porcentaje. |
| Suma de variaciones absolutas por categoría | Igual a la variación total de la misma selección, conservando precisión. |
| Compradores y recurrentes | Recurrentes ≤ compradores; compradores ≤ pedidos, una vez validadas las claves. |
| Ticket promedio | Ventas ÷ pedidos, tanto para el mes actual como para el anterior. |
| Participación por categoría | Total cercano a 100 % dentro de las categorías seleccionadas. |
| Mes único en las cuatro vistas mensuales | Gerencia, Clientes, Tiendas y Productos representan la misma selección temporal. |
| Cintas históricas | Conservan todos los meses del intervalo y responden a tienda y categoría. |
| Barras al 100 % | Cada tienda suma aproximadamente 100 % de las categorías seleccionadas; se contrastan importes absolutos. |
| Dispersión | Una burbuja por tienda; X ventas, Y variación y tamaño pedidos. |
| Treemap | Las áreas corresponden a ventas por categoría y subcategoría. |
| Árbol de descomposición | El nodo raíz concilia con las ventas de la misma selección. |
| Nivel de pedido | Un cliente, una tienda y una fecha por OrderKey; Pedidos = 1 al filtrar un pedido. |
| Factores comerciales | Todo el historial, sin filtros de producto ni de un mes; Expandir por OrderKey. |
| Alertas comerciales | Todos los meses entre PrimerMes y UltimoMes, independientes del mes sincronizado. |
| Alertas por umbral | Ventas y pedidos clasificados por mes; el total de varios meses queda sin clasificación mensual. |
| Anomalías | Cobertura y sensibilidad registradas; se documentan todos los puntos señalados y las exclusiones justificadas por cobertura. |
| Contraste entre variables | Ventas y pedidos se analizan por separado y se contrastan con ticket. |
| Investigación de períodos | Todas las señales registradas y al menos dos meses priorizados o de contraste, investigados con las vistas de detalle. |
| Inicio autónomo | Archivo nuevo, consulta BaseContoso y controles calculados desde los CSV. |

**Si una comprobación falla:** corregir su origen antes de grabar. Revisar tipos, relaciones, filtros y precisión; conservar los cálculos derivados del origen.

<a id="registro-alertas"></a>
**Registro de cobertura, señales y decisiones**

El inventario de todos los meses queda en la tabla de Alertas comerciales. Completar una fila del registro siguiente por cada mes señalado por una regla o un detector; agregar las filas necesarias e incorporar los meses de contraste elegidos. Un mismo mes puede reunir varias variables o métodos. Conservar la tabla y una síntesis de las decisiones en el PBIX.

| Primer mes y último mes | Meses evaluados | Meses con alguna señal | Meses pendientes de cobertura o base comparable | Meses priorizados y justificación |
|---|---:|---:|---|---|
| | | | | |

| Mes y filtros | Variable | Variación relativa y absoluta pertinente | Alerta por umbral | Anomalía y sensibilidad | Verificación, prioridad y acción |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |

| Umbral temporal | Meses de ventas para revisar | Meses de pedidos para revisar | Utilidad y carga de revisión |
|---|---:|---:|---|
| 20 % | | | |
| 30 % | | | |
| 40 % | | | |

Justificar: ¿qué señales coinciden?, ¿cuáles difieren?, ¿qué muestran las dimensiones?, ¿qué meses se priorizan y por qué?, ¿qué comprobación falta? Si no hay señales, registrar ese resultado y utilizar meses de contraste para demostrar el procedimiento. Restaurar el umbral del 30 % y conservar todo el historial en las vistas de seguimiento.

<a id="paso-4-2"></a>
### 4.2. Video ejecutivo: presentar el diagnóstico a la gerencia

**Duración máxima: 5 minutos. Audiencia: gerencia comercial. Cámara activada y pantalla visible.**

| Secuencia | Tiempo orientativo | Contenido |
|---|---:|---|
| Contexto y mensaje principal | 30 s | Período, magnitud del cambio y asunto prioritario. |
| Evidencia del diagnóstico | 2 min | Tres hallazgos cuantificados; incluir el contraste de alertas y la decisión que apoya. Declarar el período y los filtros de cada evidencia. |
| Interpretación y límites | 1 min | Cambios en pedidos y ticket, alertas, contribuciones y asociaciones; distinguir el detalle mensual del seguimiento histórico y las hipótesis de las causas comprobadas. |
| Acciones y seguimiento | 1 min 30 s | Dos recomendaciones priorizadas, responsable propuesto, indicador y plazo. |

Cada hallazgo debe incluir **qué cambió, cuánto, en qué período y para qué selección**. Seguir la secuencia: **situación → evidencia → interpretación → acción → seguimiento**.

Utilizar lenguaje empresarial y explicar el significado de las cifras. Reservar las fórmulas y los pasos de construcción para el video técnico. Presentar las causas no comprobadas como hipótesis y solicitar datos concretos para contrastarlas.

Para organizar las dos recomendaciones, completar:

| Prioridad | Evidencia observada | Acción propuesta | Área responsable | Indicador y plazo | Información adicional |
|---|---|---|---|---|---|
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |

<a id="paso-4-3"></a>
### 4.3. Video técnico: sustentar la construcción y validación

**Duración máxima: 8 minutos. Audiencia: docente. Cámara activada y pantalla visible.**

1. **Datos y calidad, 2 minutos:** mostrar las nuevas dimensiones, sus claves y los controles de correspondencia. Explicar las etiquetas Cliente y Tienda y la distinción de países.
2. **Modelo y medidas, 3 minutos:** mostrar las cuatro relaciones y Calendario. Explicar compradores, ticket, ventas anteriores y variación. Justificar qué filtros se retiran y cuáles se conservan.
3. **Vistas e interacciones, 2 minutos:** demostrar el mes sincronizado y la configuración de barras al 100 %, árbol e influenciadores. Demostrar Alertas comerciales y justificar el umbral y la sensibilidad; distinguir señales, contribuciones y asociaciones.
4. **Validación, 1 minuto:** presentar los controles del origen, la cobertura de todos los meses y el registro de señales. Justificar los meses priorizados y las coincidencias o diferencias entre métodos. Explicar un posible error y su comprobación.

La sustentación debe evidenciar comprensión de los pasos nuevos y de sus resultados. Partir del informe construido en este taller y mostrar sus consultas, relaciones, medidas y configuración en Power BI.

<a id="paso-4-4"></a>
### 4.4. Requisitos y entrega en Moodle

**Entregables:**

1. Archivo `Taller_02_Contoso_Apellidos.pbix`, con `Control_T2`, los controles de apoyo que haya creado y las seis vistas de negocio. Las seis visualizaciones previstas deben estar configuradas, junto con las líneas de anomalías, la tabla de alertas y la síntesis del contraste de métodos.
2. Enlace al video ejecutivo, máximo cinco minutos.
3. Enlace al video técnico, máximo ocho minutos.

Verificar permisos de visualización de los enlaces y mantener la cámara activada en ambos videos. Pueden trabajar en equipo; **cada estudiante realiza su entrega individual en Moodle**.

La columna de pantallazos queda disponible para documentar la guía; su diligenciamiento no reemplaza la entrega del PBIX ni los videos. Al cambiar de computador, puede ser necesario actualizar las rutas de los CSV desde la configuración del origen de datos.

<a id="paso-4-5"></a>
### 4.5. Criterios de evaluación

| Criterio | Peso |
|---|---:|
| Calidad de datos, modelo, cálculos, configuración de gráficos y validaciones | 25 % |
| Profundidad del diagnóstico, interpretación de indicadores y justificación de alertas | 35 % |
| Recomendaciones sustentadas, priorización y seguimiento | 25 % |
| Storytelling, adaptación a la audiencia y comunicación profesional | 15 % |
| **Total** | **100 %** |

**Resultado esperado:** un diagnóstico que conecte clientes, tiendas, productos y tiempo para detectar señales, identificar prioridades comerciales y sustentar acciones con evidencia.

[Volver al índice](#indice)

<a id="anexo-a"></a>
## Anexo A. Ruta alternativa si faltan customer.csv o store.csv

Esta ruta utiliza las claves de `sales` y mantiene el mismo dataset. Permite estudiar clientes y tiendas por identificador. Las ubicaciones se mostrarán como **Sin información** y las preguntas geográficas quedarán limitadas hasta incorporar las fichas descriptivas del mismo paquete.

Antes de usar Modelado, cerrar y aplicar Power Query para cargar sales y product. Aplicar la alternativa únicamente a la dimensión cuyo CSV falte. Mantener la importación normal para la otra. Si `sales` tampoco contiene su clave, recuperar esa columna del origen; una relación requiere los identificadores correspondientes.

**A.1. Crear customer desde los compradores del historial**

En Power BI, seleccionar **Modelado → Nueva tabla** y pegar:

```dax
customer =
ADDCOLUMNS(
    DISTINCT(
        SELECTCOLUMNS(
            sales,
            "CustomerKey", sales[CustomerKey]
        )
    ),
    "Cliente", "Cliente [" & FORMAT([CustomerKey], "0") & "]",
    "CountryCliente", "Sin información",
    "CityCliente", "Sin información"
)
```

**A.2. Crear store desde las tiendas presentes en ventas**

Seleccionar **Modelado → Nueva tabla** y pegar:

```dax
store =
ADDCOLUMNS(
    DISTINCT(
        SELECTCOLUMNS(
            sales,
            "StoreKey", sales[StoreKey]
        )
    ),
    "Tienda", "Tienda [" & FORMAT([StoreKey], "0") & "]",
    "CountryTienda", "Sin información",
    "State", "Sin información"
)
```

**A.3. Retomar la secuencia principal**

- Estas tablas DAX ya contienen las etiquetas y los nombres que utilizan las vistas. Omitir, solo para ellas, la importación y las transformaciones de Power Query de los pasos 3.2.1 a 3.2.6.
- Verificar en la vista de tabla que las claves sean únicas y no estén vacías. La correspondencia está determinada por las claves tomadas de `sales`.
- Aplicar también el control de atributos por pedido del paso 3.2.7 sobre `sales`. Después continuar en [3.3.1](#paso-3-3-1) con las relaciones y todas las medidas.
- Conservar las vistas por cliente, tienda y producto. En las vistas geográficas, identificar la limitación: **Sin información** no representa un país. En influenciadores, retirar las columnas geográficas constantes y evaluar el mes; registrar si no se detectan asociaciones.
- Explicar en la sustentación qué dimensiones se derivaron de ventas y cuáles se importaron. Este catálogo derivado contiene solo identificadores presentes en el historial de ventas, no la totalidad de clientes o tiendas de la empresa.

<a id="anexo-b"></a>
## Anexo B. Errores frecuentes y correcciones

| Síntoma | Revisar | Corrección |
|---|---|---|
| Compradores no cambian al filtrar el mes | Tabla utilizada en el conteo. | Contar `sales[CustomerKey]`, no las filas de `customer`. |
| Ventas anteriores quedan en blanco | Mes único y filtros de fecha. | Seleccionar un `Calendario[AnioMes]`; usar Calendario en las vistas del informe y conservar el historial. |
| Venta anterior coincide con una referencia inesperada | Filtros directos sobre `sales[OrderDate]`. | Retirar esos filtros de las vistas del informe y usar Calendario. |
| Relación propone muchos a muchos | Claves duplicadas o tipos distintos. | Corregir la dimensión y establecer una clave única del lado 1. |
| Todos los países o tiendas muestran lo mismo | Relación ausente, inactiva o campo de otra tabla. | Revisar la relación de la dimensión con `sales` y el campo usado en el eje. |
| Dos páginas muestran totales diferentes | País del cliente, marca o selecciones locales. | Igualar todos los filtros antes de comparar. |
| La suma de porcentajes de variación no coincide con el total | Agregación aplicada. | Recalcular el porcentaje a partir de la variación y referencia totales. |
| La suma de pedidos o clientes por categoría supera el total | Repetición entre categorías. | Interpretar el conteo distinto del total; un pedido o cliente puede participar en varias. |
| La participación aparece como 100 % en el detalle de productos | Nivel del visual. | Utilizar la medida solo en la matriz de categorías de Control_T2. |
| Un porcentaje de variación queda en blanco | Base anterior igual a cero o sin ventas. | Interpretar junto con la variación absoluta; señalar Sin base comparable. |
| Clientes recurrentes da cero | Número de pedidos en la selección. | Verificar el contexto y aceptar cero cuando ningún cliente tiene dos pedidos. |
| Tiendas con la misma descripción se agrupan | Etiqueta sin clave. | Utilizar `store[Tienda]`, que incorpora `StoreKey`. |
| No coinciden los totales de control | Versión, tipos, filtros o precisión. | Usar el paquete enlazado en 3.1.2 y revisar BaseContoso antes de continuar. |

**Comprobación del arranque:**

| Síntoma | Revisar | Corrección |
|---|---|---|
| BaseContoso no encuentra el archivo | CarpetaDatos y ubicación de los CSV. | Pegar la ruta de la carpeta que contiene sales.csv y product.csv, sin nombre de archivo. |
| BaseContoso muestra un registro y no una tabla | Salida prevista de la consulta. | Crear las consultas sales, product y ControlBase con los bloques de 3.1.5. |
| Una referencia no encuentra BaseContoso | Nombre de la consulta. | Mantener exactamente BaseContoso, respetando mayúsculas y sin espacios. |
| Error de columna en el bloque de inicio | Encabezados y paquete utilizado. | Usar los CSV indicados; comprobar ProductKey, OrderDate, Quantity y NetPrice. |

**Comprobación de los gráficos nuevos:**

| Síntoma | Revisar | Corrección |
|---|---|---|
| Barras al 100 % con un solo color | Solo una categoría seleccionada. | Mantener varias categorías para comparar composición. |
| Barras iguales se interpretan como ventas iguales | Normalización porcentual. | Consultar los importes absolutos en la información emergente o el detalle. |
| La dispersión muestra un punto agregado | Campo que identifica cada tienda. | Agregar Tienda a Valores o Detalles; usar País solo como leyenda. |
| Algunas tiendas no aparecen en dispersión | Base anterior cero y porcentaje en blanco. | Revisarlas en la matriz de ventas y variaciones absolutas. |
| Las cintas muestran un solo mes | Interacción del segmentador de mes. | Marcar Ninguno sobre las cintas y conservar el filtro de año. |
| El árbol no concilia con la tarjeta | Selección activa en Treemap u otro visual. | Limpiar selecciones y comparar bajo los mismos filtros. |
| Influenciadores tiene pocos grupos o no encuentra patrones | Contexto, nivel y observaciones. | Usar todo el historial, revisar Expandir por OrderKey y aceptar la ausencia de asociaciones cuando corresponda. |
| Ticket del análisis refleja una parte del pedido | Filtros de producto. | Retirar filtros de categoría, marca o producto en Factores comerciales. |
| Factores cambia al elegir un mes en otra vista | Sincronización de segmentaciones. | Excluir Factores comerciales de las segmentaciones de las cuatro vistas mensuales. |


**Comprobación de alertas y anomalías:**

| Síntoma | Revisar | Corrección |
|---|---|---|
| No aparece Anomalías | Visual y eje. | Usar líneas, fecha continua y una sola serie; revisar las limitaciones oficiales. |
| La serie muestra solo un mes | Filtros y sincronización. | Retirar la selección mensual de Alertas comerciales y conservar el historial completo. |
| Un mes con alerta no aparece como anomalía | Resultado y sensibilidad. | Documentarlo y contrastarlo con la regla DAX; no imponer una marca esperada. |
| Mes sin base queda como estabilidad | Manejo de blancos. | Comprobar Sin base comparable antes de evaluar los límites porcentuales. |
| Alerta aparece en un total de varios meses | Contexto de la medida. | Mantener la comprobación de un único InicioMes en Alerta Ventas. |
| No aparece Explicar la disminución | Punto y limitaciones. | Seleccionar un mes observado, comprobar su referencia temporal y revisar filtros y compatibilidad; documentar la limitación y usar cascada y matrices. |
| Una alerta de ventas se usa para pedidos | Medida aplicada. | Usar Alerta Pedidos, Variacion Pedidos % y la detección de la serie Pedidos. |

<a id="fuentes"></a>
## Fuentes y correspondencia del esquema

Los controles de referencia del paquete están reproducidos en esta guía y se verifican con sus propias consultas y medidas. Los campos y nombres de archivo de las dimensiones se contrastaron con el código oficial del generador Contoso. Las referencias siguientes permiten ampliar conceptos; no sustituyen pasos ni entregables de este taller.

1. [Documentación de referencia del paquete Contoso](https://github.com/adanbeltran/Labs/blob/main/PowerBI-Taller01-demo/Taller_01_Power_BI_PACE_Contoso.md): procedencia de los controles reproducidos aquí; consulta opcional.
2. [SQLBI. Estructura de Contoso Data Generator](https://docs.sqlbi.com/contoso-data-generator/details): organización de los datos.
3. [SQLBI. Esquema CSV de clientes](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/DataWriter/Csv/CsvCustomer.cs) y [esquema CSV de tiendas](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/DataWriter/Csv/CsvStore.cs): nombres y atributos.
4. [SQLBI. Exportación CSV](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/DataWriter/Csv/CSVWriter.cs) y [nombres de archivos](https://github.com/sql-bi/Contoso-Data-Generator-V2/blob/main/DatabaseGenerator/Consts.cs): encabezados de ventas y archivos del generador.
5. [Microsoft Learn. Relaciones de modelos en Power BI](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-relationships-understand): cardinalidad y propagación de filtros.
6. [Microsoft Learn. Tablas de fechas](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-date-tables): configuración y validaciones temporales.
7. [Microsoft Learn. DISTINCTCOUNT](https://learn.microsoft.com/en-us/dax/distinctcount-function-dax): conteos distintos y totales.
8. [Microsoft Learn. SELECTEDVALUE](https://learn.microsoft.com/en-us/dax/selectedvalue-function-dax), [REMOVEFILTERS](https://learn.microsoft.com/en-us/dax/removefilters-function-dax), [EDATE](https://learn.microsoft.com/en-us/dax/edate-function-dax) y [ALLSELECTED](https://learn.microsoft.com/en-us/dax/allselected-function-dax): funciones empleadas en las nuevas medidas.
9. [Microsoft Learn. Gráficos de cascada](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-waterfall-charts): representación de contribuciones positivas y negativas.
10. [Microsoft Learn. Formato condicional en tablas y matrices](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-conditional-table-formatting): rutas y opciones de formato.

11. [Microsoft Learn. Cintas](https://learn.microsoft.com/en-us/power-bi/visuals/desktop-ribbon-charts): comparación de posiciones por período.
12. [Microsoft Learn. Árbol de descomposición](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-decomposition-tree): medida, dimensiones y exploración manual o automática.
13. [Microsoft Learn. Influenciadores clave](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-influencers): nivel de análisis, factores y segmentos.
14. [Microsoft Learn. Dispersión y burbujas](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-scatter): ejes, puntos y tamaño.
15. [Microsoft Learn. Treemap](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-treemaps): jerarquía, área y detalles.
16. [Microsoft Learn. Series apiladas al 100 %](https://learn.microsoft.com/en-us/power-bi/paginated-reports/report-design/visualizations/multiple-series-on-chart-report-builder): principio de comparación proporcional; las instrucciones de este taller se aplican al visual de Power BI Desktop.

17. [Microsoft Learn. Csv.Document](https://learn.microsoft.com/en-us/powerquery-m/csv-document), [Table.TransformColumnTypes](https://learn.microsoft.com/en-us/powerquery-m/table-transformcolumntypes) y [Record.ToTable](https://learn.microsoft.com/en-us/powerquery-m/record-totable): importación, interpretación regional y controles del bloque de inicio.

18. [Microsoft Learn. Detección de anomalías](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-anomaly-detection): rango esperado, explicaciones y requisitos de la función.
19. [Microsoft Learn. Panel Análisis](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-analytics-pane): activación de anomalías y sensibilidad.
20. [Microsoft Learn. Gráficos de líneas](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-line-chart): eje continuo y serie temporal.
21. [Microsoft Learn. Explicar fluctuaciones](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-insights): comparación con el punto anterior y limitaciones del análisis.
22. [Microsoft Learn. SWITCH](https://learn.microsoft.com/en-us/dax/switch-function-dax): evaluación ordenada de condiciones de las alertas. El umbral del 30 % y las comparaciones con 20 % y 40 % son decisiones didácticas de esta guía.

23. [Microsoft Learn. List.Min](https://learn.microsoft.com/en-us/powerquery-m/list-min), [List.Max](https://learn.microsoft.com/en-us/powerquery-m/list-max) y [Date.StartOfMonth](https://learn.microsoft.com/en-us/powerquery-m/date-startofmonth): cálculo del primer y el último mes desde el origen.

[Volver al índice](#indice)
