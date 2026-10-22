# Taller 02. Diagnóstico comercial de Contoso en Power BI

**Metodología PACE | Clientes, tiendas, productos y evolución mensual**

**Objetivo:** construir un informe autónomo de diagnóstico comercial para comparar el desempeño por cliente, tienda y producto, detectar cambios que requieren revisión, identificar sus contribuciones y sustentar recomendaciones ante la gerencia.

**Dirigido a:** estudiantes de último semestre de Administración de Empresas.  

**Caso central:** explorar todos los meses del historial disponible para descubrir variaciones que requieran revisión y sustentar prioridades comerciales.  
**Resultado:** seis vistas de negocio: `Gerencia`, `Clientes`, `Tiendas`, `Productos`, `Factores comerciales` y `Alertas comerciales`, además de la página técnica `Control_T2`.

**Preparación resumida:** el paso 3.1.1 reúne la configuración regional y la carga directa del mismo dataset. Se utilizan los nombres originales de sus columnas y los indicadores ya demostrados. El trabajo nuevo se concentra en las comparaciones, las visualizaciones y su interpretación comercial.

La empresa dispone de registros de ventas, pero todavía no ha identificado qué meses presentan cambios relevantes ni qué segmentos requieren atención. El equipo revisará la serie completa, documentará las señales y localizará sus contribuciones por tiendas, clientes y productos. Las posibles causas se presentarán como hipótesis que requieren evidencia adicional.

**Visualizaciones incorporadas:** barras apiladas al 100 %, dispersión con burbujas, mapa de árbol (Treemap), árbol de descomposición, cintas e influenciadores clave. Se agrega el gráfico de líneas con detección de anomalías y una tabla de alertas por umbral. La cascada permite interpretar contribuciones al cambio y las matrices permiten comprobar el detalle.

**Alcance temporal:** todos los meses comprendidos entre el primer y el último mes registrados en sales. Alertas comerciales y las cintas conservan la serie completa. Gerencia, Clientes, Tiendas y Productos permiten investigar un mes a la vez frente al anterior, después de identificarlo en el seguimiento. Factores comerciales explora los pedidos del historial completo. Las dos vistas históricas quedan fuera de las segmentaciones sincronizadas de las cuatro vistas de detalle mensual.

## Modalidad de trabajo en cada fase

| Fase PACE | ¿Se utiliza Power BI? | Propósito | Producto |
|---|---|---|---|
| **1. Planear** | **No** | Definir preguntas y decisiones empresariales. | Alcance del diagnóstico. |
| **2. Analizar** | **No** | Comprender dimensiones, indicadores y controles. | Diseño del análisis. |
| **3. Construir** | **Sí** | Retomar la base, crear medidas analíticas y configurar visualizaciones. | Informe con seis vistas y gráficos de diagnóstico. |
| **4. Ejecutar y explicar** | **Sí** | Validar, investigar y comunicar resultados. | PBIX y dos videos. |

**Regla metodológica:** las fases 1 y 2 son de lectura y razonamiento. La descarga, la apertura de Power BI y todas las operaciones técnicas comienzan en la fase 3.

**Fórmulas suministradas:** los bloques DAX se copian en Modelado → Nueva medida, salvo Calendario, que se crea con Nueva tabla. Cada bloque explica el indicador que produce. Las tres medidas básicas quedan como referencia breve para iniciar en blanco; no constituyen actividades nuevas.

<a id="indice"></a>
## Índice por fases

| Fase | Propósito |
|---|---|
| [Fase 1. Planear](#fase-1) | Definir preguntas y decisiones empresariales. |
| [Fase 2. Analizar](#fase-2) | Comprender datos, indicadores y tipos de visualización. |
| [Fase 3. Construir](#fase-3) | Retomar la base y construir las medidas analíticas y los gráficos. |
| [Fase 4. Ejecutar y explicar](#fase-4) | Validar y comunicar el diagnóstico a la gerencia. |

## Recorrido de las cuatro fases

La **[fase 1. Planear](#fase-1)** establece las decisiones que necesita tomar la gerencia y las preguntas del diagnóstico. El resultado orienta la selección de dimensiones e indicadores de la fase siguiente.

La **[fase 2. Analizar](#fase-2)** distingue ventas, pedidos y clientes; explica las relaciones entre las tablas y fija las reglas de comparación. Estos criterios permiten reconocer errores antes de implementar los cálculos.

La **[fase 3. Construir](#fase-3)** retoma la base en [un único paso de preparación](#paso-3-1-1), añade [tiempo e indicadores analíticos](#paso-3-2), configura [las vistas de negocio](#paso-3-3) y aplica [alertas y anomalías](#paso-3-4). Cada paso incluye una comprobación del resultado y espacio para pantallazos.

La **[fase 4. Ejecutar y explicar](#fase-4)** comprueba el informe terminado, verifica la cobertura de todos los meses, contrasta las alertas y comunica los hallazgos descubiertos, límites y recomendaciones mediante dos videos dirigidos a audiencias diferentes.

<a id="controles"></a>
## Base ya demostrada en el taller 1

Se reutilizan el paquete Contoso, las relaciones por claves y las medidas **Ventas Totales**, **Unidades Totales** y **Pedidos**. La revisión de tipos, vacíos, duplicados, precisión y correspondencia de claves ya fue demostrada; aquí se la nombra como antecedente, sin repetir consultas de perfilado ni recuentos de pedidos.

La referencia de clase es el [Taller 1 publicado en GitHub](https://github.com/adanbeltran/Labs/blob/main/PowerBI-Taller01-demo/Taller_01_Power_BI_PACE_Contoso.md). El presente taller utiliza todos los meses del historial y conserva la unidad monetaria del origen.

<a id="fase-1"></a>
## Fase 1. Planear
<img width="1672" height="941" alt="fase 1" src="https://github.com/user-attachments/assets/0d5a4920-55b8-4633-aafb-32111a167fb2" />



<a id="paso-1-1"></a>
### 1.1. Comprender el problema empresarial

La gerencia comercial de Contoso quiere conocer la evolución de su operación y establecer un seguimiento mensual. No dispone de un diagnóstico previo sobre aumentos, caídas o anomalías. Solicita analizar todos los meses disponibles, detectar cuáles requieren atención y examinar el comportamiento de tiendas, clientes, productos, pedidos y ticket promedio antes de recomendar acciones.


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


<a id="paso-1-3"></a>
### 1.3. Delimitar el alcance

- Revisar todos los meses del historial, sin elegir de antemano un período problemático. Declarar las fechas inicial y final y verificar la cobertura de los meses de los extremos.
- Mantener el historial completo en el modelo para calcular referencias temporales.
- Evaluar todos los meses mediante variaciones y alertas de ventas y pedidos, y contrastarlas con la detección de anomalías. Registrar cada mes señalado por cualquiera de los métodos y priorizar al menos dos para investigación detallada. El umbral inicial del 30 % es una decisión didáctica.
- Utilizar clientes y tiendas del mismo paquete para interpretar segmentos, con sus claves y campos descriptivos originales.
- Presentar tres hallazgos cuantificados y dos recomendaciones priorizadas.
- Indicar la información adicional necesaria para comprobar las hipótesis.


<a id="paso-1-4"></a>
### 1.4. Justificar la detección y revisión de cambios

**Justificación empresarial:** la gerencia necesita un criterio reproducible para priorizar revisiones en todos los meses. Una señal puede orientar la comprobación de datos, la revisión de pedidos y la consulta con responsables comerciales. La alerta debe acompañarse de su magnitud absoluta, el segmento afectado y la evidencia necesaria para decidir una acción.

El **30 %** se adopta como umbral inicial de práctica para cambios mensuales, tanto positivos como negativos. Se comparará con 20 % y 40 % para discutir cuántos casos se priorizan. En una aplicación empresarial, su elección exigiría considerar volatilidad, estacionalidad, magnitud monetaria y capacidad de respuesta. La sensibilidad del detector y el umbral porcentual son parámetros distintos.

[Volver al índice](#indice)

<a id="fase-2"></a>
## Fase 2. Analizar
<img width="1672" height="941" alt="fase 2" src="https://github.com/user-attachments/assets/ad419961-6e7b-4d0e-9f7e-b8a1354bf379" />


<a id="paso-2-1"></a>
### 2.1. Retomar las tablas conocidas y sus dimensiones

| Tabla | Uso en este análisis |
|---|---|
| sales | Ventas y pedidos ya explicados; incluye CustomerKey, StoreKey y ProductKey. |
| product | Categoría, subcategoría, producto y marca. |
| customer | Código, nombre y país del comprador. |
| store | Código, descripción y país de la tienda. |
| Calendario | Meses del historial y referencia para las comparaciones temporales. |



<a id="paso-2-2"></a>
### 2.2. Reconocer variables descriptivas y relaciones

| Dimensión | Campos del esquema CSV oficial que se utilizarán | Interpretación |
|---|---|---|
| Cliente | `CustomerKey`, `GivenName`, `Surname`, `CountryFull`, `City`. | Identidad y ubicación del cliente. |
| Tienda | `StoreKey`, `Description`, `CountryName`, `State`. | Identidad y ubicación comercial de la tienda. |
| Producto | `CategoryName`, `SubCategoryName`, `ProductName`, `Brand`. | Jerarquía del portafolio y marca. |
| Tiempo | Año, mes e inicio de mes derivados de `OrderDate`. | Período de la compra. |

El país del cliente es `customer[CountryFull]` y el país de la tienda es `store[CountryName]`. Se conservan esos nombres del CSV y se usan títulos claros en los gráficos. Para identificar clientes y tiendas se utilizan sus claves; los nombres y descripciones se añaden a las tablas o a la información emergente.

Las dimensiones filtran a `sales` mediante relaciones **uno a varios y de dirección única**. Se conservarán separadas, con una sola fila por clave. Estas relaciones forman un esquema de estrella: las dimensiones permiten explorar la tabla de ventas desde distintas perspectivas.

<a id="paso-2-3"></a>
### 2.3. Interpretar los indicadores

| Indicador | Definición | Precaución de interpretación |
|---|---|---|
| Clientes compradores | Clientes distintos presentes en `sales` dentro de la selección. | Difiere del total del catálogo `customer`. |
| Ticket promedio | Ventas ÷ pedidos distintos. | Por categoría representa el importe de esa categoría por pedido que la incluye. |
| Pedidos por cliente | Pedidos distintos ÷ clientes compradores. | Se refiere al período y segmento seleccionados. |
| Variación absoluta | Ventas actuales − ventas anteriores. | Mide contribución en dinero al cambio. |
| Variación porcentual | Variación absoluta ÷ ventas anteriores. | Una base igual a cero no permite calcular ese porcentaje. |
| Composición porcentual | Peso de cada categoría dentro de una tienda. | Las barras al 100 % realizan la normalización automáticamente. |

El total de un promedio se calcula a partir de sus sumas y conteos. Los clientes y pedidos distintos pueden repetirse entre categorías; el total general se recalcula con `DISTINCTCOUNT`, como explica su [documentación oficial](https://learn.microsoft.com/en-us/dax/distinctcount-function-dax).



<a id="paso-2-4"></a>
### 2.4. Establecer reglas de comparación

1. Comparar un mes completo con el mes completo inmediatamente anterior, conservando los mismos filtros de tienda, cliente y producto.
2. Seleccionar un solo mes para los indicadores de variación. La evolución histórica conservará todos los meses del origen, aunque las vistas de detalle muestren uno solo.
3. Examinar la variación absoluta junto con la porcentual: una caída porcentual grande puede tener poco peso en el resultado total.
4. Considerar cero las ventas de un producto que está ausente en uno de los dos meses para calcular su contribución absoluta. Si la base anterior es cero, el porcentaje quedará en blanco.
5. Interpretar por separado los cambios en pedidos y en ticket. Su relación aritmética con las ventas orienta el diagnóstico; comprobar causas exige más información.

**Actividad:** proponer una hipótesis que pueda explorarse con estas tablas y otra que requiera información de inventarios o campañas.

<a id="paso-2-5"></a>
### 2.5. Mantener los acuerdos de la demostración previa

Se conservan las claves, los tipos y la precisión ya revisados en el taller 1. Las comprobaciones nuevas se centran en el período representado, los filtros de cada vista, la comparación entre meses y la interpretación del nivel de pedido en influenciadores.

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

**Ruta:** 3.1 Preparación resumida → 3.2 Tiempo y medidas analíticas → 3.3 Visualizaciones → 3.4 Alertas y anomalías. La carga se concentra en un paso. No se incluyen rutinas M, perfilado, combinaciones anti izquierda ni tablas auxiliares de limpieza.

Las fórmulas DAX nuevas se suministran para copiar y se interpretan por su resultado empresarial. Utilizar Nueva medida, excepto en la tabla Calendario. Si la configuración de DAX exige punto y coma, adaptar únicamente los separadores de argumentos.

<a id="paso-3-1"></a>
### 3.1. Preparación ya demostrada, en un solo paso

**Propósito:** disponer de la base y los indicadores conocidos con carga directa y configuración regional adecuada.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-1"></a>
<p><strong>3.1.1. Retomar la carga demostrada y configurar el archivo</strong></p>
<ol><li>Abrir un <strong>Informe en blanco</strong> y guardarlo como <code>Taller_02_Contoso_Apellidos.pbix</code>. Si ya dispone de una base de clase, puede trabajar sobre una copia; el archivo del taller 1 no es requisito.</li><li><strong>Antes de importar:</strong> ir a <strong>Archivo → Opciones y configuración → Opciones → Archivo actual → Configuración regional</strong>. Seleccionar <strong>Inglés (Estados Unidos)</strong> para la importación y aceptar.</li><li>Usar la carpeta del mismo <a href="https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z">paquete Contoso</a>. En <strong>Inicio → Obtener datos → Texto/CSV</strong>, cargar sales.csv, product.csv, customer.csv y store.csv, uno por uno. Confirmar delimitador coma y encabezados en la vista previa y pulsar <strong>Cargar</strong>. Mantener la detección automática de tipos; no abrir el Editor avanzado.</li><li>Conservar los nombres sales, product, customer y store. Retomar las relaciones conocidas: product[ProductKey] → sales[ProductKey], customer[CustomerKey] → sales[CustomerKey] y store[StoreKey] → sales[StoreKey], activas 1:* y de dirección única.</p>
</td>
<td width="75%" valign="top">
<img width="1575" height="975" alt="image" src="https://github.com/user-attachments/assets/798387c4-d029-4f1a-b41a-c1ae6cde0788" />
<img width="1282" height="831" alt="image" src="https://github.com/user-attachments/assets/ba8e2190-646e-46bd-9af5-966f1cbc5ba9" />
<img width="1086" height="723" alt="image" src="https://github.com/user-attachments/assets/5bbe7f36-6589-456a-ade3-72299ed5a17e" />
<img width="1806" height="818" alt="image" src="https://github.com/user-attachments/assets/aa26bf55-f674-44c6-b063-bd983d72f97c" />
<img width="1742" height="900" alt="image" src="https://github.com/user-attachments/assets/23d55a96-6d84-4950-bcb4-6a25ca9d3081" />
<img width="1040" height="863" alt="image" src="https://github.com/user-attachments/assets/7f865bef-ffd5-4afa-8f21-a7a8849dfcf0" />




<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-2"></a>
### 3.2. Tiempo e indicadores para el diagnóstico

**Propósito:** añadir solo los cálculos que se utilizarán para comparar e interpretar la operación.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-1"></a>
<p><strong>3.2.1. Preparar el tiempo para comparar todos los meses</strong></p>
<ol><li>Seleccionar <strong>Modelado → Nueva tabla</strong> y copiar la fórmula suministrada.Seleccionar Calendario → <strong>Marcar como tabla de fechas</strong> → Date. Relacionar Calendario[Date] con sales[OrderDate], activa 1:* y de dirección única.</li><li>Ordenar Mes por MesNumero y AnioMes por AnioMesOrden desde <strong>Herramientas de columna → Ordenar por columna</strong>.</li><li>En Vista Datos, consultar OrderDate en orden ascendente y descendente para anotar el primer y el último mes del historial. En la guía se los denomina mes inicial y mes final; utilizar como límites sus fechas de primer día.</li><li>Nombrar la página técnica <code>Control_T2</code> y agregar un cuadro de texto con esos límites. Registrar si la cobertura de un extremo está pendiente de confirmar; los meses se investigan sin una variación predeterminada.</li></ol><details><summary>Copiar Calendario</summary><pre><code class="language-dax">Calendario =
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
<p><strong>Comprobar:</strong> el modelo permite seleccionar un mes y consultar su referencia anterior. La comparación temporal es el propósito de esta tabla; no se agregan transformaciones en Power Query.</p>
</td>
<td width="75%" valign="top">
 <img width="1858" height="890" alt="image" src="https://github.com/user-attachments/assets/c63566a0-1f70-458f-88ba-d377c28e7a37" />
    <img width="1876" height="912" alt="image" src="https://github.com/user-attachments/assets/b97b0d1b-d5e8-4226-8dde-aaaccc1ebc70" />
    <img width="775" height="572" alt="image" src="https://github.com/user-attachments/assets/2f844a67-70db-4f18-9d75-112a717ea8b1" />
    <img width="943" height="963" alt="image" src="https://github.com/user-attachments/assets/309f61a6-cd81-4c62-a292-9a72198d9aa6" />
    <img width="653" height="692" alt="image" src="https://github.com/user-attachments/assets/22a0008e-70bc-420e-aed1-509c817aa1d2" />
    <img width="1870" height="680" alt="image" src="https://github.com/user-attachments/assets/1721a6e1-09d9-46b9-9b24-dd4be440e9f0" />




<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-2"></a>
<p><strong>3.2.2. Añadir los indicadores para interpretar clientes y ticket</strong></p>
<ol><li>Crear cada medida suministrada mediante <strong>Modelado → Nueva medida</strong>.</li><li>Clientes Compradores identifica quiénes compraron en la selección; Pedidos por Cliente resume la frecuencia en ese contexto; Ticket Promedio representa ventas por pedido.</li><li>Interpretar las medidas según el mes y los segmentos seleccionados. En una categoría, el ticket corresponde al importe de esa categoría por pedido que la incluye.</li></ol><details><summary>Copiar Clientes Compradores</summary><pre><code class="language-dax">Clientes Compradores =
DISTINCTCOUNT(sales[CustomerKey])</code></pre></details><details><summary>Copiar Pedidos por Cliente</summary><pre><code class="language-dax">Pedidos por Cliente =
DIVIDE([Pedidos], [Clientes Compradores])</code></pre></details><details><summary>Copiar Ticket Promedio</summary><pre><code class="language-dax">Ticket Promedio =
DIVIDE([Ventas Totales], [Pedidos])</code></pre></details>
<p><strong>Comprobar:</strong> los indicadores cambian con los filtros y sus totales se calculan a partir de las sumas y conteos de la selección.</p>
</td>
<td width="75%" valign="top">
<img width="1856" height="891" alt="image" src="https://github.com/user-attachments/assets/ca1807c7-054c-4339-95b1-c273a833998b" />
<img width="1277" height="106" alt="image" src="https://github.com/user-attachments/assets/ee1e8390-951f-4b21-a19c-8b615b273696" />
<img width="1235" height="117" alt="image" src="https://github.com/user-attachments/assets/89fac197-831e-40aa-86af-8f8834c07479" />
<img width="1286" height="125" alt="image" src="https://github.com/user-attachments/assets/1621cbd2-9c14-4c55-8de0-94bbae4877c7" />
<img width="1160" height="116" alt="image" src="https://github.com/user-attachments/assets/8e1387ee-38ed-4702-bc73-8bbc78de94e2" />
<img width="1168" height="136" alt="image" src="https://github.com/user-attachments/assets/ce77f32c-e2e0-4f03-a833-6166cf421f6e" />







<!-- Captura 3.2.2: Añadir los indicadores para interpretar clientes y ticket. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-3"></a>
<p><strong>3.2.3. Obtener la referencia del mes anterior</strong></p>
<ol><li>Crear las dos medidas siguientes.</li><li>Ambas identifican el mes seleccionado y consultan el inmediatamente anterior. Mantienen los filtros de cliente, tienda y producto.</li><li>La comparación requiere un solo mes. Un total de varios meses no debe interpretarse como una variación mensual.</li></ol><details><summary>Copiar Ventas Mes Anterior</summary><pre><code class="language-dax">Ventas Mes Anterior =
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
    )</code></pre></details><details><summary>Copiar Pedidos Mes Anterior</summary><pre><code class="language-dax">Pedidos Mes Anterior =
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
    )</code></pre></details>
<p><strong>Comprobar:</strong> para cualquier mes elegido con referencia, se obtienen las ventas y los pedidos del mes inmediatamente anterior, con los mismos filtros de dimensiones.</p>
</td>
<td width="75%" valign="top">
<img width="1902" height="602" alt="image" src="https://github.com/user-attachments/assets/61eef6f8-25ee-41b0-9051-36dfd18c55ef" />

<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-4"></a>
<p><strong>3.2.4. Comparar ventas, pedidos y ticket</strong></p>
<ol><li>Crear las medidas suministradas para expresar la diferencia en dinero, su porcentaje y los cambios en pedidos y ticket.</li><li>Interpretar juntas la magnitud absoluta y la relativa: el porcentaje ayuda a comparar, y el importe muestra el peso comercial.</li><li>Si la referencia anterior es cero o falta, el porcentaje queda sin base comparable. Una contribución positiva o negativa señala dónde cambió el resultado; no demuestra la causa.</li></ol><details><summary>Copiar Variacion Ventas</summary><pre><code class="language-dax">Variacion Ventas =
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
    )</code></pre></details><details><summary>Copiar Variacion Ventas %</summary><pre><code class="language-dax">Variacion Ventas % =
DIVIDE(
    [Variacion Ventas],
    [Ventas Mes Anterior]
)</code></pre></details><details><summary>Copiar Ticket Mes Anterior</summary><pre><code class="language-dax">Ticket Mes Anterior =
DIVIDE(
    [Ventas Mes Anterior],
    [Pedidos Mes Anterior]
)</code></pre></details><details><summary>Copiar Variacion Pedidos %</summary><pre><code class="language-dax">Variacion Pedidos % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Pedidos] - [Pedidos Mes Anterior],
        [Pedidos Mes Anterior]
    )
)</code></pre></details><details><summary>Copiar Variacion Ticket %</summary><pre><code class="language-dax">Variacion Ticket % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Ticket Promedio] - [Ticket Mes Anterior],
        [Ticket Mes Anterior]
    )
)</code></pre></details>
<p><strong>Comprobar:</strong> las medidas permiten distinguir el cambio en el número de pedidos del cambio en su importe medio.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.4: Comparar ventas, pedidos y ticket. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-5"></a>
<p><strong>3.2.5. Dar formato y comprobar el contexto del análisis</strong></p>
<ol><li>Dar formato de porcentaje, con dos decimales, a las medidas que terminan en %. Dar dos decimales a importes, ticket y Pedidos por Cliente; conservar los formatos básicos ya demostrados.</li><li>En Control_T2, insertar una tabla mensual con AnioMes, ventas actuales y anteriores, variaciones, pedidos y ticket.</li><li>En Filtros de esta página, agregar Calendario[InicioMes] y utilizar <strong>Filtrado avanzado</strong> para conservar el intervalo inclusivo entre las fechas del mes inicial y el mes final anotadas en 3.2.1.</li><li>Elegir dos filas consecutivas de la tabla y contrastar las referencias temporales y la resta. Después aplicar una tienda o categoría y comprobar que ambos meses mantengan esa selección.</li><li>Retirar los filtros usados para la prueba y conservar esta página fuera de la sincronización de las vistas comerciales.</li></ol>
<p><strong>Comprobar:</strong> se comprueba el comportamiento de las medidas nuevas; no se repite el perfilado ni la conciliación básica del dataset del taller 1.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.5: Dar formato y comprobar el contexto del análisis. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### 3.3. Visualizaciones y navegación entre vistas

**Propósito:** configurar las seis vistas conservando filtros claros y el seguimiento de todos los meses.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-1"></a>
<p><strong>3.3.1. Organizar las vistas y retomar los indicadores conocidos</strong></p>
<ol><li>Crear las páginas Gerencia, Clientes, Tiendas, Productos, Factores comerciales y Alertas comerciales. Conservar Control_T2.</li><li>Elegir lienzo 16:9 y reservar la franja superior para título, período y filtros. Distribuir los gráficos sin superponerlos.</li><li>En Gerencia, reutilizar la construcción de tarjetas demostrada: Ventas Totales, Variacion Ventas %, Pedidos, Clientes Compradores y Ticket Promedio. Mantener las unidades completas y los formatos de 3.2.5.</li><li>Reservar cintas y cascada en Gerencia; barras al 100 % y dispersión en Tiendas; Treemap y árbol en Productos; influenciadores en Factores comerciales; y líneas y tabla mensual en Alertas comerciales.</li></ol>
<p><strong>Comprobar:</strong> el espacio se organiza por preguntas comerciales y las tarjetas básicas se usan como contexto de los gráficos nuevos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.1: Organizar las vistas y retomar los indicadores conocidos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-2"></a>
<p><strong>3.3.2. Configurar la selección de un mes para explorar</strong></p>
<ol><li>En Gerencia, Clientes, Tiendas y Productos, conservar el intervalo entre mes inicial y mes final mediante el filtro avanzado de Calendario[InicioMes], como en 3.2.5. Mantener disponibles todos sus años.</li><li>En Gerencia, insertar segmentaciones de Calendario[AnioMes], store[CountryName], store[StoreKey] y product[CategoryName].</li><li>Activar Selección única en el mes. Elegir un mes con referencia anterior para probar los gráficos; esta selección inicial no lo declara como período crítico.</li><li>Titular los filtros Mes de análisis, País de la tienda, Tienda y Categoría. Mantener todos los segmentos. Los meses de investigación se elegirán después del seguimiento de toda la serie en 3.4.</li></ol>
<p><strong>Comprobar:</strong> se puede elegir cualquier mes del historial; las vistas de detalle muestran uno a la vez y conservan su comparación con el anterior.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.2: Configurar la selección de un mes para explorar. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-3"></a>
<p><strong>3.3.3. Sincronizar solo las cuatro vistas mensuales</strong></p>
<ol><li>Seleccionar el segmentador de mes y abrir <strong>Vista → Sincronizar segmentaciones</strong>.</li><li>Activar Sincronizar y Visible en Gerencia, Clientes, Tiendas y Productos. Repetir con país de la tienda, tienda y categoría.</li><li>Dejar sin sincronizar <code>Factores comerciales</code>, <code>Alertas comerciales</code> y <code>Control_T2</code>. Ajustar la ubicación de los segmentadores visibles.</li><li>Probar un cambio de mes entre las cuatro vistas mensuales y restaurar el mes de prueba.</li></ol>
<p><strong>Comprobar:</strong> el contexto mensual se comparte entre las cuatro vistas; Factores comerciales y Alertas comerciales conservarán su alcance histórico independiente.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.3: Sincronizar solo las cuatro vistas mensuales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-4"></a>
<p><strong>3.3.4. Gerencia: construir el gráfico de cintas</strong></p>
<ol><li>Seleccionar <strong>Gráfico de cintas</strong> en Visualizaciones.</li><li>Eje X: <code>Calendario[AnioMes]</code>. Eje Y o Valores: <code>Ventas Totales</code>. Leyenda: <code>product[CategoryName]</code>.</li><li>Ordenar el eje por <code>AnioMes</code> ascendente. Mantener el intervalo histórico completo y todos los años disponibles.</li><li>Seleccionar la segmentación de mes, abrir <strong>Formato → Editar interacciones</strong> y marcar <strong>Ninguno</strong> únicamente sobre las cintas. Salir de Editar interacciones.</li><li>Conservar las interacciones de tienda y categoría sobre este gráfico.</li></ol>
<p><strong>Comprobar:</strong> las cintas muestran todos los meses del historial mientras los indicadores representan el mes elegido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.4: Gerencia: construir el gráfico de cintas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-5"></a>
<p><strong>3.3.5. Gerencia: interpretar el liderazgo con cintas</strong></p>
<ol><li>Titular el gráfico <strong>Liderazgo de categorías en el historial</strong>. Mantener visibles la leyenda y los colores de las categorías.</li><li>En Formato visual, revisar las opciones de Cintas: conservar el color de la serie, ajustar la transparencia y dejar separación suficiente para distinguirlas.</li><li>Pasar el cursor por dos meses consecutivos. Identificar la categoría ubicada arriba y revisar si cambian las posiciones.</li><li>Registrar un cambio de posición o la estabilidad del liderazgo. Indicar los meses y las ventas que respaldan la observación.</li><li>Realizar esta comparación con varias categorías; al seleccionar una sola, el gráfico pierde la comparación de posiciones entre categorías.</li></ol>
<p><strong>Comprobar:</strong> se interpreta la posición relativa y el importe de las categorías, sin presuponer que siempre deben cambiar de puesto.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.5: Gerencia: interpretar el liderazgo con cintas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-6"></a>
<p><strong>3.3.6. Gerencia: crear la cascada de contribuciones</strong></p>
<ol><li>Insertar un <strong>Gráfico de cascada</strong>. Categoría: <code>product[CategoryName]</code>. Eje Y: <code>Variacion Ventas</code>. Dejar Desglose vacío.</li><li>Activar etiquetas de datos y titular <strong>Aportes por categoría a la variación mensual</strong>.</li><li>Mantener la interacción del mes con la cascada: cada mes elegido se compara con el inmediatamente anterior.</li><li>Conservar todas las categorías de la selección. Comparar el total de la cascada con la variación absoluta en <code>Control_T2</code> bajo los mismos filtros.</li></ol>
<p><strong>Comprobar:</strong> las contribuciones positivas y negativas concilian con el cambio mensual de la misma selección.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.6: Gerencia: crear la cascada de contribuciones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-7"></a>
<p><strong>3.3.7. Clientes: interpretar compradores y principales clientes</strong></p>
<ol><li>En Clientes, presentar Clientes Compradores, Pedidos por Cliente y Ticket Promedio.</li><li>Insertar una tabla con customer[CustomerKey], customer[GivenName], customer[Surname], Ventas Totales, Pedidos y Ticket Promedio. Conservar CustomerKey como identificador.</li><li>En los filtros de esa tabla, aplicar a CustomerKey <strong>Top N → Superior: 10 → Por valor: Ventas Totales</strong>. Aplicar el filtro y ordenar ventas descendentes.</li><li>Titular Diez principales clientes por ventas del mes. El Top N se limita a la tabla; los indicadores mantienen el conjunto de compradores seleccionado.</li></ol>
<p><strong>Comprobar:</strong> los nombres se leen junto con la clave del CSV y se distinguen los principales clientes del total del segmento.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.7: Clientes: interpretar compradores y principales clientes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-8"></a>
<p><strong>3.3.8. Clientes: comparar el país del comprador con el de la tienda</strong></p>
<ol><li>Agregar un gráfico por customer[CountryFull] y Ventas Totales, titulado Ventas por país del cliente.</li><li>Usar CountryFull como segmentación local de Clientes. Mantenerla independiente de los filtros sincronizados de store[CountryName].</li><li>Comparar las perspectivas País del cliente y País de la tienda. Limpiar el filtro local al finalizar.</li></ol>
<p><strong>Comprobar:</strong> se utilizan los campos originales y los títulos aclaran a qué ubicación corresponde cada resultado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.8: Clientes: comparar el país del comprador con el de la tienda. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-9"></a>
<p><strong>3.3.9. Tiendas: crear barras apiladas al 100 %</strong></p>
<ol><li>En Tiendas, seleccionar <strong>Gráfico de barras apiladas 100 %</strong>. Comprobar el icono para elegir la versión normalizada.</li><li>Eje Y: <code>store[StoreKey]</code>. Eje X o Valores: <code>Ventas Totales</code>. Leyenda: <code>product[CategoryName]</code>.</li><li>Mantener todas las categorías o varias categorías seleccionadas. Titular <strong>Composición de ventas por tienda y categoría</strong>.</li><li>Si hay muchas tiendas, aplicar Top N de tiendas por Ventas Totales solo a este visual. Conservar todas las categorías dentro de cada tienda mostrada.</li><li>Agregar store[Description], Ventas Totales y Unidades Totales a Información sobre herramientas, cuando el panel permita añadirlas.</li></ol>
<p><strong>Comprobar:</strong> cada barra representa una tienda y sus segmentos muestran proporciones de las categorías seleccionadas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.9: Tiendas: crear barras apiladas al 100 %. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-10"></a>
<p><strong>3.3.10. Tiendas: formatear e interpretar los porcentajes</strong></p>
<ol><li>Activar las etiquetas de datos y su presentación porcentual cuando esté disponible. Conservar la leyenda y colores consistentes.</li><li>Verificar que el eje de proporciones recorra de 0 % a 100 %. El visual normaliza Ventas Totales automáticamente.</li><li>Comparar dos tiendas y localizar qué categoría tiene mayor peso en cada una. Revisar los importes reales con el cursor o en la matriz de comprobación.</li><li>Verificar que cada barra sume aproximadamente 100 %; puede existir una diferencia mínima por redondeo.</li><li>Explicar que la misma longitud total de las barras no significa iguales ventas. Con una sola categoría seleccionada, cada tienda tendrá un único segmento de 100 %.</li></ol>
<p><strong>Comprobar:</strong> se diferencia composición porcentual de volumen absoluto y se declara qué categorías forman el denominador.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.10: Tiendas: formatear e interpretar los porcentajes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-11"></a>
<p><strong>3.3.11. Tiendas: crear la dispersión con burbujas</strong></p>
<ol><li>Insertar un <strong>Gráfico de dispersión</strong>. Eje X: Ventas Totales. Eje Y: Variacion Ventas %.</li><li>Agregar <code>store[StoreKey]</code> al campo que identifica los puntos: <strong>Valores</strong> o <strong>Detalles</strong>, según la versión. Debe quedar una burbuja por tienda.</li><li>Agregar Pedidos a <strong>Tamaño</strong> y <code>store[CountryName]</code> a Leyenda.</li><li>En Información sobre herramientas, agregar store[Description], Ticket Promedio y Ventas Mes Anterior.</li><li>Titular <strong>Tiendas: nivel de ventas y variación mensual</strong>. Dar al eje Y formato de porcentaje.</li></ol>
<p><strong>Comprobar:</strong> la posición horizontal indica ventas, la vertical indica variación y el tamaño representa pedidos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.11: Tiendas: crear la dispersión con burbujas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-12"></a>
<p><strong>3.3.12. Tiendas: agregar referencia y comparar desempeño</strong></p>
<ol><li>Seleccionar la dispersión y abrir el panel <strong>Análisis</strong>. Agregar una <strong>Línea constante del eje Y</strong> con valor <strong>0</strong>.</li><li>Identificar tiendas por encima y por debajo de esa línea. Comparar además sus ventas y el tamaño de las burbujas.</li><li>Seleccionar una burbuja, revisar su etiqueta e información emergente y contrastar el resultado con la tienda elegida en el filtro sincronizado.</li><li>Comprobar en la matriz las tiendas sin base anterior: su variación porcentual queda en blanco y pueden no aparecer en la dispersión.</li><li>Limpiar la selección directa al terminar. Formular una prioridad de revisión sustentada en magnitud y variación.</li></ol>
<p><strong>Comprobar:</strong> se distinguen crecimiento y disminución, conservando el contexto de volumen; se reconoce la ausencia de base comparable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.12: Tiendas: agregar referencia y comparar desempeño. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-13"></a>
<p><strong>3.3.13. Productos: construir el mapa de árbol o Treemap</strong></p>
<ol><li>En Productos, seleccionar <strong>Mapa de árbol</strong> o <strong>Treemap</strong>.</li><li>Categoría o Grupo: <code>product[CategoryName]</code>. Detalles: <code>product[SubCategoryName]</code>. Valores: Ventas Totales.</li><li>Agregar Unidades Totales y Pedidos a Información sobre herramientas. Mantener las ventas como medida del área.</li><li>Titular <strong>Concentración del ingreso por categoría y subcategoría</strong>.</li><li>Activar las etiquetas de categoría y ajustar su tamaño para que las áreas principales se identifiquen con claridad.</li></ol>
<p><strong>Comprobar:</strong> las áreas representan ventas y los rectángulos internos distinguen las subcategorías de cada categoría.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.13: Productos: construir el mapa de árbol o Treemap. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-14"></a>
<p><strong>3.3.14. Productos: explorar la composición del Treemap</strong></p>
<ol><li>Comparar las áreas de las categorías y pasar el cursor sobre sus subcategorías para revisar las cifras.</li><li>Seleccionar una subcategoría y observar el filtrado de los otros visuales de esta página. Comprobar que el árbol de descomposición, cuando esté construido, responda a esa selección.</li><li>Limpiar la selección para restaurar el portafolio. Cambiar de mes y verificar que las áreas respondan.</li><li>Registrar dónde se concentra el ingreso y contrastar los importes en la matriz de detalle.</li></ol>
<p><strong>Comprobar:</strong> la interpretación utiliza la jerarquía y sus importes. Los rectángulos pequeños se consultan con el cursor o en detalle.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.14: Productos: explorar la composición del Treemap. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-15"></a>
<p><strong>3.3.15. Productos: construir el árbol de descomposición</strong></p>
<ol><li>Insertar un <strong>Árbol de descomposición</strong> y darle suficiente ancho. Utilizar Modo de enfoque para explorar rutas extensas.</li><li>En <strong>Analizar</strong>, colocar Ventas Totales.</li><li>En <strong>Explicar por</strong>, colocar <code>store[CountryName]</code>, <code>store[StoreKey]</code>, <code>product[CategoryName]</code>, <code>product[SubCategoryName]</code> y <code>product[Brand]</code>.</li><li>Titular <strong>Exploración de ventas por tienda y portafolio</strong>.</li><li>Con los demás visuales sin selección, comprobar que el nodo raíz coincida con Ventas Totales en la misma selección de filtros.</li></ol>
<p><strong>Comprobar:</strong> el nodo raíz agrega las ventas y el signo + permite abrir distintas dimensiones.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.15: Productos: construir el árbol de descomposición. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-16"></a>
<p><strong>3.3.16. Productos: explorar rutas manuales y automáticas</strong></p>
<ol><li>Seleccionar el <strong>+</strong> del nodo raíz. Elegir store[CountryName]; dentro de un país elegir store[StoreKey]; después abrir product[CategoryName] y product[SubCategoryName].</li><li>Registrar una ruta y el importe de su último nodo. Contrastar el resultado aplicando los mismos filtros en una matriz.</li><li>Volver a la raíz y probar una ruta distinta, comenzando por Categoría. El árbol permite elegir el orden de las dimensiones.</li><li>Si aparece la bombilla de análisis, probar <strong>Valor alto</strong> o <strong>Valor bajo</strong> y explicar qué dimensión seleccionó el visual.</li><li>Interpretar el resultado como un desglose de ventas. Contrastar con el detalle si el visual limita la cantidad de nodos visibles; los nodos mostrados no constituyen necesariamente el catálogo completo.</li></ol>
<p><strong>Comprobar:</strong> se demuestra una ruta manual y se interpreta la sugerencia automática cuando esté disponible. El árbol localiza contribuciones y no demuestra causas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.16: Productos: explorar rutas manuales y automáticas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-17"></a>
<p><strong>3.3.17. Usar una matriz para comprobar los desgloses</strong></p>
<ol><li>En Control_T2, crear una matriz con product[CategoryName] → product[SubCategoryName] → product[ProductName] en filas y Ventas Totales, Ventas Mes Anterior, Variacion Ventas y Variacion Ventas % en valores.</li><li>Duplicar la matriz y sustituir las filas por store[CountryName] → store[StoreKey] para contrastar las tiendas de las barras y la dispersión. No requiere medidas adicionales.</li><li>Activar los iconos +/− para recorrer los niveles. Seleccionar el mismo mes y los mismos segmentos de la vista que se investiga.</li><li>Aplicar el formato condicional ya conocido a las variaciones: rojo para valores negativos y verde para positivos.</li><li>Contrastar el total con la cascada y los detalles con Treemap y árbol. Retirar el filtro de un mes al volver al seguimiento completo.</li></ol>
<p><strong>Comprobar:</strong> los desgloses se pueden verificar sin crear consultas de control adicionales.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.17: Usar una matriz para comprobar los desgloses. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-18"></a>
<p><strong>3.3.18. Preparar Factores comerciales con alcance histórico</strong></p>
<ol><li>Abrir Factores comerciales y conservar todos los meses entre mes inicial y mes final. Retirar filtros de un solo año o mes.</li><li>Confirmar que no existan filtros de producto ni segmentadores sincronizados desde las vistas de detalle mensual.</li><li>Titular <strong>Factores asociados con el importe por pedido: historial completo</strong>.</li><li>Agregar tarjetas de Pedidos, Clientes Compradores y Ticket Promedio. Declarar que los indicadores corresponden al historial completo y a los segmentos seleccionados.</li><li>Si incorpora filtros locales para experimentar, restaurar después todo el historial y todos los segmentos.</li></ol>
<p><strong>Comprobar:</strong> el análisis por pedido declara su alcance histórico. Revisar los recuentos mostrados por el visual: sus resultados pueden basarse en una muestra, como indica la documentación oficial de influenciadores.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.18: Preparar Factores comerciales con alcance histórico. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-19"></a>
<p><strong>3.3.19. Comprobar el nivel de análisis de influenciadores</strong></p>
<ol><li>En la página técnica, usar una tabla con OrderKey, OrderDate, CustomerKey, StoreKey, Ventas Totales y Ticket Promedio. Mantener los identificadores como No resumir y excluir productos y LineNumber.</li><li>Observar varios pedidos: cliente, tienda y fecha corresponden al mismo pedido; el ticket coincide con su importe cuando el pedido está completo.</li><li>Utilizar esta lectura para justificar OrderKey en Expandir por. No se crea una consulta agrupada ni se vuelve a enseñar el conteo de pedidos.</li></ol>
<p><strong>Comprobar:</strong> se comprende la unidad que analiza el visual antes de interpretar sus asociaciones.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.19: Comprobar el nivel de análisis de influenciadores. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-20"></a>
<p><strong>3.3.20. Configurar el objetivo de influenciadores clave</strong></p>
<ol><li>En Factores comerciales, insertar el visual <strong>Influenciadores clave</strong> y reservarle la mayor parte del lienzo.</li><li>En <strong>Analizar</strong>, colocar la medida <code>Ticket Promedio</code>.</li><li>En <strong>Expandir por</strong>, colocar <code>sales[OrderKey]</code>. Este campo establece el nivel del análisis.</li><li>Elegir el análisis de lo que hace <strong>aumentar</strong> el objetivo. Al utilizar una medida numérica, se interpreta un resultado continuo.</li><li>Titular <strong>Características asociadas con pedidos de mayor importe</strong>.</li></ol>
<p><strong>Comprobar:</strong> el visual analiza la medida por pedido y mantiene el alcance histórico declarado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.20: Configurar el objetivo de influenciadores clave. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-21"></a>
<p><strong>3.3.21. Agregar factores explicativos compatibles</strong></p>
<ol><li>En <strong>Explicar por</strong>, agregar <code>customer[CountryFull]</code>, <code>store[CountryName]</code>, <code>Calendario[Anio]</code> y <code>Calendario[Mes]</code>.</li><li>Esperar a que termine el análisis. Mantener las claves de cliente y tienda fuera de Explicar por: son identificadores.</li><li>Conservar OrderKey únicamente en Expandir por. No agregar categoría como un atributo único de pedido, pues un pedido puede incluir varias categorías.</li><li>Mantener fuera de Explicar por precio, cantidad y la propia medida objetivo. Conservar también la página sin filtros de producto para analizar pedidos completos.</li></ol>
<p><strong>Comprobar:</strong> los factores corresponden al mismo nivel de pedido y se distingue el identificador del factor explicativo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.21: Agregar factores explicativos compatibles. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-22"></a>
<p><strong>3.3.22. Interpretar factores y ausencia de patrones</strong></p>
<ol><li>Seleccionar un factor mostrado y leer la comparación del panel derecho. Registrar el grupo, la referencia y la diferencia estimada que aparezca.</li><li>En Formato visual → Análisis, activar los recuentos cuando estén disponibles y revisar el tamaño del grupo.</li><li>Cambiar un filtro local y observar si el análisis se recalcula. Restaurar todo el historial sin filtros locales.</li><li>Si no encuentra influenciadores, revisar contexto y configuración; registrar el resultado cuando no haya asociaciones suficientes para informar.</li><li>Redactar el hallazgo como una asociación dentro de estos datos. Comprobar una causa requeriría evidencia adicional.</li></ol>
<p><strong>Comprobar:</strong> la interpretación informa el contexto y el respaldo observado; no garantiza factores ni atribuye causalidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.22: Interpretar factores y ausencia de patrones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-23"></a>
<p><strong>3.3.23. Explorar segmentos principales</strong></p>
<ol><li>Dentro de Influenciadores clave, abrir la pestaña <strong>Segmentos principales</strong> o <strong>Top segments</strong>.</li><li>Si aparecen segmentos, seleccionar uno y revisar la combinación de condiciones que lo define.</li><li>Registrar el importe medio del segmento, su comparación con el conjunto y el número de observaciones indicado.</li><li>Si no se muestran segmentos, consignar ese resultado sin crear grupos manuales para simular una conclusión.</li></ol>
<p><strong>Comprobar:</strong> se distingue un factor individual de una combinación de características.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.23: Explorar segmentos principales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-24"></a>
<p><strong>3.3.24. Revisar las interacciones de los nuevos gráficos</strong></p>
<ol><li>Abrir <strong>Formato → Editar interacciones</strong>. Confirmar que el mes filtre indicadores y cascada, mientras las cintas conserven todo el historial.</li><li>En Tiendas, probar la selección de una burbuja y de un segmento al 100 %. Revisar qué tienda y categoría está seleccionando cada acción.</li><li>En Productos, seleccionar un rectángulo y comprobar la respuesta del árbol. Limpiar la selección antes de conciliar el nodo raíz.</li><li>Comprobar que las segmentaciones sincronizadas de las cuatro vistas mensuales no afecten Factores comerciales, Alertas comerciales ni los controles técnicos.</li><li>Mantener visibles los títulos del período y limpiar selecciones directas al pasar a una nueva comprobación.</li></ol>
<p><strong>Comprobar:</strong> se explican los cambios de contexto y se distingue el detalle de un mes del seguimiento de toda la serie.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.24: Revisar las interacciones de los nuevos gráficos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-25"></a>
<p><strong>3.3.25. Preparar el procedimiento de investigación por mes</strong></p>
<ol><li>Para el mes de prueba elegido, comprobar la variación general en la cascada y en la matriz bajo los mismos filtros.</li><li>Explorar tiendas con dispersión y barras al 100 %, y categorías y productos con Treemap y árbol de descomposición.</li><li>Consultar Clientes, pedidos y ticket para interpretar los indicadores. En las cintas, ubicar ese mes dentro de la evolución histórica.</li><li>Limpiar los filtros al terminar. Estas comprobaciones preparan las herramientas; la priorización de meses se realizará en 3.4 después de evaluar toda la serie.</li><li>Distinguir los resultados del mes de prueba de las asociaciones por pedido del historial completo.</li></ol>
<p><strong>Comprobar:</strong> las vistas permiten investigar cualquier mes sin una caída o un aumento predeterminados.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.25: Preparar el procedimiento de investigación por mes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-26"></a>
<p><strong>3.3.26. Guardar el avance y capturar las evidencias</strong></p>
<ol><li>En las cuatro vistas mensuales, conservar el mes de prueba y limpiar los filtros de dimensión y las selecciones directas en gráficos.</li><li>En Factores comerciales, mantener todo el historial sin filtros locales. Comprobar los títulos de cada período.</li><li>Capturar el modelo y las configuraciones de los seis gráficos nuevos. Pegar las imágenes en las celdas derechas de los pasos correspondientes.</li><li>Guardar el PBIX en Gerencia. Conservar las seis vistas y los controles técnicos creados en este taller.</li></ol>
<p><strong>Comprobar:</strong> el informe conserva una selección inicial coherente y las evidencias están ubicadas en sus pasos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.26: Guardar el avance y capturar las evidencias. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### 3.4. Detección de cambios, anomalías y alertas comerciales

**Propósito:** revisar todos los meses del historial antes de priorizar los que se investigarán. Alertas comerciales conserva la serie completa y queda fuera de la sincronización mensual.

Las líneas analizan ventas y pedidos por separado. La tabla mantiene también los casos sin referencia o con cobertura pendiente, para que la selección de meses sea explícita y reproducible.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-1"></a>
<p><strong>3.4.1. Preparar el seguimiento de todos los meses</strong></p>
<ol><li>Abrir Alertas comerciales. En Filtros de esta página, agregar Calendario[InicioMes] y fijar el intervalo inclusivo entre mes inicial y mes final mediante Filtrado avanzado, como en 3.2.5.</li><li>Dejar disponibles todos los años y todos los meses intermedios. Confirmar que la página quede fuera de todas las segmentaciones sincronizadas y retirar filtros ocultos que reduzcan el período.</li><li>Mantener todos los clientes, tiendas y productos. Titular <strong>Seguimiento y alertas de todo el historial mensual</strong> e indicar las fechas de cobertura.</li><li>Distribuir las dos líneas en la franja superior y la tabla mensual debajo. Documentar la cobertura de los meses de los extremos antes de interpretar sus cambios.</li></ol>
<p><strong>Comprobar:</strong> el seguimiento incluye todo el intervalo del origen y no cambia al seleccionar un mes en Gerencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.1: Preparar el seguimiento de todos los meses. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-2"></a>
<p><strong>3.4.2. Crear las alertas de revisión mensual</strong></p>
<ol><li>Crear las tres medidas siguientes, cada una como Nueva medida.</li><li>Dar a Umbral Alerta % formato de porcentaje. El valor inicial es 30 %; las otras dos medidas expresan el resultado de revisión en texto.</li><li>Alerta Ventas usa la variación de ventas y Alerta Pedidos usa la de pedidos. Ambas incluyen los límites de −30 % y +30 % y distinguen los casos sin base comparable.</li></ol><details><summary>Copiar Umbral Alerta %</summary><pre><code class="language-dax">Umbral Alerta % = 0.30</code></pre></details><details><summary>Copiar Alerta Ventas</summary><pre><code class="language-dax">Alerta Ventas =
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
    )</code></pre></details><details><summary>Copiar Alerta Pedidos</summary><pre><code class="language-dax">Alerta Pedidos =
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
<p><strong>Comprobar:</strong> la regla se aplica automáticamente a cada mes y variable, sin anticipar cuáles producirán señales.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.2: Crear las alertas de revisión mensual. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-3"></a>
<p><strong>3.4.3. Evaluar y clasificar todos los meses en la tabla</strong></p>
<ol><li>Insertar una Tabla con Calendario[AnioMes], Ventas Totales, Ventas Mes Anterior, Variacion Ventas, Variacion Ventas %, Alerta Ventas, Pedidos, Variacion Pedidos %, Alerta Pedidos y Variacion Ticket %.</li><li>Ordenar AnioMes ascendente, mostrar importes completos y porcentajes con dos decimales y desactivar la fila de total. Ajustar anchos y desplazamiento para conservar la lectura.</li><li>Conservar visibles Alerta Ventas y Alerta Pedidos como texto. Si se resaltan las variaciones por signo, usar formato condicional de reglas sobre sus valores numéricos; el texto de alerta indica si se alcanza el umbral vigente.</li><li>Agregar la tarjeta Umbral Alerta % y titularla Límite de revisión mensual.</li><li>Verificar que aparezcan todos los meses desde mes inicial hasta mes final, también los que no generen alertas. Registrar meses sin observaciones, base cero o cobertura pendiente antes de concluir sobre el negocio.</li><li>Comprobar manualmente dos filas elegidas: ventas anteriores, variación, signo y clasificación. Las dos alertas se calculan para cada mes; no se selecciona un período para obtener un resultado esperado.</li></ol>
<p><strong>Comprobar:</strong> la tabla constituye el inventario completo de meses evaluados y distingue alertas, cambios dentro del umbral y casos sin base comparable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.3: Evaluar y clasificar todos los meses en la tabla. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-4"></a>
<p><strong>3.4.4. Crear la serie mensual de ventas</strong></p>
<ol><li>Insertar un <strong>Gráfico de líneas</strong>. Eje X: <code>Calendario[InicioMes]</code> como fecha directa, sin jerarquía automática. Eje Y: <code>Ventas Totales</code>.</li><li>En Formato visual → Eje X, elegir <strong>Continuo</strong>. Ordenar por InicioMes ascendente. Dejar vacíos Leyenda y eje Y secundario; usar una sola medida.</li><li>Conservar toda la secuencia mensual cuya cobertura se haya validado, sin seleccionar meses por el resultado. Titular <strong>Ventas mensuales y anomalías: historial</strong>. Si un extremo está incompleto, excluirlo solo de la detección y documentarlo; mantenerlo visible en el inventario mensual.</li><li>Si se incorpora un segmentador local de mes, usar Editar interacciones → Ninguno sobre las líneas y la tabla de seguimiento para preservar el período completo.</li></ol>
<p><strong>Comprobar:</strong> el eje muestra fechas mensuales y el gráfico tiene una sola serie. No se usa AnioMes como texto en este eje continuo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.4: Crear la serie mensual de ventas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-5"></a>
<p><strong>3.4.5. Activar anomalías y comparar la sensibilidad</strong></p>
<ol><li>Seleccionar la línea de ventas y abrir el panel <strong>Análisis → Anomalías → Agregar</strong>, según la denominación de su versión.</li><li>Mostrar el rango esperado y registrar la sensibilidad inicial. Compararla con un ajuste mayor y anotar cómo cambia el conjunto de señales.</li><li>Elegir una configuración y justificarla por su utilidad para revisar la serie, sin ajustarla para forzar una conclusión sobre un mes.</li><li>Conservar al menos cuatro puntos temporales para la función. Registrar todos los meses señalados en la serie y conservar también el resultado cuando no haya anomalías.</li></ol><p>Consultar las opciones en <a href="https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-analytics-pane">Microsoft Learn: panel Análisis</a>.</p>
<p><strong>Comprobar:</strong> se documentan el período, la sensibilidad y los puntos señalados. La ausencia de anomalías también es un resultado válido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.5: Activar anomalías y comparar la sensibilidad. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-6"></a>
<p><strong>3.4.6. Investigar una anomalía y contrastar sus explicaciones</strong></p>
<ol><li>En <strong>Explicar por</strong>, agregar <code>product[CategoryName]</code>, <code>store[CountryName]</code> y <code>customer[CountryFull]</code>.</li><li>Seleccionar un punto marcado y consultar el panel de explicaciones. Registrar la dimensión, el grupo y la evidencia mostrada.</li><li>Contrastar ese segmento y período con las matrices. Igualar todos los filtros al comparar.</li><li>Si no se señalan puntos o no aparecen explicaciones, registrar ese resultado y continuar con el análisis de cambios del paso siguiente.</li></ol>
<p><strong>Comprobar:</strong> las explicaciones automáticas se presentan como pistas para investigar; se verifican con datos y no se declaran como causas demostradas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.6: Investigar una anomalía y contrastar sus explicaciones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-7"></a>
<p><strong>3.4.7. Investigar los cambios identificados en la serie</strong></p>
<ol><li>Crear Control_Alertas, sin segmentaciones sincronizadas. Copiar allí la línea de ventas, retirar Anomalías de la copia y conservar el intervalo histórico completo validado.</li><li>Para cada mes señalado por alguna regla o detector, registrar la variable y el tipo de señal en el registro de seguimiento. Hacer clic derecho en el punto y usar <strong>Analizar → Explicar la disminución</strong> o <strong>Explicar el aumento</strong>, según el signo observado.</li><li>Confirmar que la comparación corresponda al mes inmediatamente anterior. Si hay un mes sin registros o cobertura pendiente, verificarlo antes de interpretar la comparación automática entre puntos.</li><li>Contrastar los cambios de composición con la cascada y las matrices. Documentar resultados y ausencia de explicaciones relevantes.</li><li>Si el menú no aparece, revisar compatibilidad y filtros según la documentación, registrar la limitación y utilizar cascada y matrices para el contraste.</li></ol><p>Referencia: <a href="https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-insights">Microsoft Learn: explicar fluctuaciones</a>.</p>
<p><strong>Comprobar:</strong> el seguimiento registra todos los meses señalados y admite tanto aumentos como caídas; los meses del análisis detallado se justifican a partir de la evidencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.7: Investigar los cambios identificados en la serie. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-8"></a>
<p><strong>3.4.8. Aplicar la detección a los pedidos</strong></p>
<ol><li>En Alertas comerciales, duplicar la línea de ventas y sustituir únicamente su medida por <code>Pedidos</code>. Mantener el eje continuo y la misma cobertura histórica validada.</li><li>Revisar o volver a agregar Anomalías en la copia. Registrar su sensibilidad y titular <strong>Pedidos mensuales y anomalías: historial</strong>.</li><li>Comparar los meses señalados en ventas y pedidos. Revisar en la tabla Variacion Pedidos % y Variacion Ticket % para los meses estudiados.</li><li>Contrastar Alerta Pedidos con Variacion Pedidos % y registrar todas sus señales. La clasificación utiliza la variación de pedidos, mientras Alerta Ventas utiliza la de ventas.</li><li>Explicar si el cambio de ventas coincide con variaciones de pedidos, de ticket o de ambos. Una diferencia entre las señales de las dos series debe describirse, no corregirse para que coincidan.</li></ol>
<p><strong>Comprobar:</strong> cada variable tiene su propia detección y se conserva su unidad. La relación ventas = pedidos × ticket se usa como comprobación aritmética, sin atribuir causalidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.8: Aplicar la detección a los pedidos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-9"></a>
<p><strong>3.4.9. Priorizar los hallazgos y justificar las decisiones</strong></p>
<ol><li>Confirmar el barrido de todos los meses del intervalo y completar el <a href="#registro-alertas">registro de seguimiento de 4.1</a> para cada mes señalado por cualquiera de los métodos. Indicar también cuántos meses se evaluaron, cuántos tienen señales y cuáles requieren validar cobertura o referencia.</li><li>Modificar temporalmente Umbral Alerta % a 0.20 y después a 0.40. Registrar cuántos meses de ventas y de pedidos quedan para revisión en cada caso. Mantener la sensibilidad del detector y restaurar 0.30.</li><li>Priorizar al menos dos meses por magnitud monetaria, variación relativa, coincidencias entre variables y evidencia disponible. Investigar esos meses con Gerencia, Clientes, Tiendas y Productos. Si hay menos de dos meses señalados, incorporar meses de contraste y explicar su elección sin presentarlos como alertas.</li><li>Redactar tres hallazgos cuantificados y dos acciones priorizadas. Incorporar una síntesis en el informe y conservar el registro completo en Control_Alertas.</li><li>Guardar con uno de los meses priorizados seleccionado en las cuatro vistas mensuales, y con todo el historial en Alertas comerciales y Factores comerciales. Limpiar filtros de dimensiones y capturar las evidencias.</li></ol>
<p><strong>Comprobar:</strong> la cobertura es completa, las señales quedan registradas y la elección de meses se puede reproducir; ningún período crítico viene indicado en el enunciado.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.9: Priorizar los hallazgos y justificar las decisiones. Pegue aquí una o varias imágenes. -->
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
| Referencia temporal | Cada mes se compara con el inmediatamente anterior, con los mismos filtros. |
| Cálculo de variaciones | La resta y la división se comprueban en dos filas elegidas; base cero o ausente queda sin porcentaje. |
| Suma de variaciones absolutas por categoría | Igual a la variación total de la misma selección, conservando precisión. |
| Ticket promedio | Ventas ÷ pedidos, tanto para el mes actual como para el anterior. |
| Mes único en las cuatro vistas mensuales | Gerencia, Clientes, Tiendas y Productos representan la misma selección temporal. |
| Cintas históricas | Conservan todos los meses del intervalo y responden a tienda y categoría. |
| Barras al 100 % | Cada tienda suma aproximadamente 100 % de las categorías seleccionadas; se contrastan importes absolutos. |
| Dispersión | Una burbuja por tienda; X ventas, Y variación y tamaño pedidos. |
| Treemap | Las áreas corresponden a ventas por categoría y subcategoría. |
| Árbol de descomposición | El nodo raíz concilia con las ventas de la misma selección. |
| Nivel de pedido | Un cliente, una tienda y una fecha por OrderKey; Pedidos = 1 al filtrar un pedido. |
| Factores comerciales | Todo el historial, sin filtros de producto ni de un mes; Expandir por OrderKey. |
| Alertas comerciales | Todos los meses entre mes inicial y mes final, independientes del mes sincronizado. |
| Alertas por umbral | Ventas y pedidos clasificados por mes; el total de varios meses queda sin clasificación mensual. |
| Anomalías | Cobertura y sensibilidad registradas; se documentan todos los puntos señalados y las exclusiones justificadas por cobertura. |
| Contraste entre variables | Ventas y pedidos se analizan por separado y se contrastan con ticket. |
| Investigación de períodos | Todas las señales registradas y al menos dos meses priorizados o de contraste, investigados con las vistas de detalle. |
| Preparación resumida | Configuración de Estados Unidos y base cargada en el único paso de inicio. |

**Antes de grabar:** comprobar el período y los filtros de las evidencias seleccionadas y conservar los resultados calculados desde los datos.

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

1. **Punto de partida, 30 segundos:** mostrar la configuración regional y nombrar la carga y los indicadores retomados del taller 1.
2. **Comparación e interpretación, 2 minutos:** mostrar Calendario, las medidas analíticas y una comparación mensual con los mismos filtros.
3. **Visualizaciones e interacciones, 3 minutos:** demostrar barras al 100 %, árbol e influenciadores, y explicar qué pregunta responde cada uno.
4. **Seguimiento y decisiones, 2 minutos 30 segundos:** mostrar todos los meses en Alertas comerciales, justificar umbral y sensibilidad y presentar los períodos priorizados. Distinguir señales, contribuciones y asociaciones.


La sustentación debe evidenciar comprensión de los pasos nuevos y de sus resultados. Partir del informe construido en este taller y mostrar las medidas analíticas, las interacciones y la configuración de sus visualizaciones en Power BI.

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
| Medidas analíticas, configuración de gráficos, interacciones y consistencia del informe | 25 % |
| Profundidad del diagnóstico, interpretación de indicadores y justificación de alertas | 35 % |
| Recomendaciones sustentadas, priorización y seguimiento | 25 % |
| Storytelling, adaptación a la audiencia y comunicación profesional | 15 % |
| **Total** | **100 %** |

**Resultado esperado:** un diagnóstico que conecte clientes, tiendas, productos y tiempo para detectar señales, identificar prioridades comerciales y sustentar acciones con evidencia.

[Volver al índice](#indice)

<a id="referencia-base"></a>
## Referencia breve para iniciar en un archivo nuevo

Las tres medidas siguientes ya fueron demostradas en el taller 1. Se incluyen únicamente para que utilizar aquel PBIX no sea requisito. Si ya existen, conservarlas; aquí no se vuelve a desarrollar su enseñanza ni una actividad de conteo.

En Modelado → Nueva medida, copiar por separado:

<details><summary>Medidas básicas ya conocidas</summary>
<pre><code class="language-dax">Ventas Totales =
SUMX(sales, sales[Quantity] * sales[NetPrice])</code></pre>
<pre><code class="language-dax">Unidades Totales = SUM(sales[Quantity])</code></pre>
<pre><code class="language-dax">Pedidos = DISTINCTCOUNT(sales[OrderKey])</code></pre>
</details>

La primera expresión multiplica cantidad por precio y suma el resultado; equivale a sumar ImporteVenta, si esa columna ya existe. Así el archivo nuevo utiliza los CSV originales sin crear una columna calculada en Power Query. Las medidas existentes del taller 1 pueden mantenerse sin modificaciones.

<a id="anexo-b"></a>
## Apoyo breve de lectura y configuración

| Situación | Acción pertinente |
|---|---|
| Los CSV no se interpretan como en la demostración | Comprobar la configuración de importación del archivo antes de cargarlos; no cambiar el idioma de Windows como parte del taller. |
| Falta customer.csv o store.csv | Localizar las dimensiones originales del mismo paquete de clase; solicitar esos archivos al docente si la copia está incompleta. |
| Un eje suma códigos de cliente o tienda | Utilizar No resumir y dar al visual un título empresarial. |
| No se entiende a qué país corresponde un campo | CountryFull pertenece al cliente; CountryName pertenece a la tienda. |
| Las cintas muestran solo un mes | Desactivar la interacción del segmentador de mes sobre las cintas. |
| Los porcentajes y las anomalías no coinciden | Revisar umbral, sensibilidad y contexto; documentar la diferencia como resultado. |

<a id="fuentes"></a>
## Fuentes

1. [Taller 1 de referencia, adanbeltran/Labs](https://github.com/adanbeltran/Labs/blob/main/PowerBI-Taller01-demo/Taller_01_Power_BI_PACE_Contoso.md): procedimientos ya demostrados y fórmulas básicas.
2. [Paquete Contoso](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z) y [estructura del generador SQLBI](https://docs.sqlbi.com/contoso-data-generator/details).
3. [Microsoft Learn: tipos e interpretación regional](https://learn.microsoft.com/en-us/power-query/data-types) y [conector Texto/CSV](https://learn.microsoft.com/en-us/power-query/connectors/text-csv): configuración y opción de carga directa.
4. [Microsoft Learn: SUMX](https://learn.microsoft.com/en-us/dax/sumx-function-dax): cálculo del importe sin columna auxiliar de preparación.
5. [Microsoft Learn: tabla de fechas](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-date-tables), [SELECTEDVALUE](https://learn.microsoft.com/en-us/dax/selectedvalue-function-dax), [REMOVEFILTERS](https://learn.microsoft.com/en-us/dax/removefilters-function-dax) y [EDATE](https://learn.microsoft.com/en-us/dax/edate-function-dax).
6. [Microsoft Learn: cintas](https://learn.microsoft.com/en-us/power-bi/visuals/desktop-ribbon-charts) y [gráficos apilados](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-column-chart-format-settings).
7. [Microsoft Learn: dispersión](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-scatter), [Treemap](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-treemaps) y [árbol de descomposición](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-decomposition-tree).
8. [Microsoft Learn: influenciadores clave](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-influencers).
9. [Microsoft Learn: detección de anomalías](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-anomaly-detection), [panel Análisis](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-analytics-pane) y [explicar fluctuaciones](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-insights).

[Volver al índice](#indice)
