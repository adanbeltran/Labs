# Taller resuelto 02. Diagnóstico comercial de Contoso en Power BI | Sesión 2

**Metodología PACE | Análisis avanzado de ventas, identificación de anomalías y toma de decisiones**

**Duración:** 3 horas de trabajo guiado; la grabación de los videos y el ajuste final del informe pueden realizarse como trabajo autónomo posterior.  
**Dirigido a:** estudiantes de últimos semestres de Administración de Empresas.  
**Punto de partida:** el archivo `Taller_02_Contoso_Apellidos.pbix` entregado al finalizar la **sesión 1**. No es necesario tener completo el Taller 1.  
**Objetivo:** terminar las vistas comerciales y analizar, en **todos los meses del historial**, variaciones importantes en ventas y pedidos mediante una regla porcentual y la detección automática de anomalías; después investigar los meses que resulten prioritarios y formular recomendaciones.

> **Situación empresarial.** Contoso dispone de ventas históricas, pero la gerencia desconoce cuáles meses presentan cambios significativos y qué segmentos concentran las variaciones. El analista debe descubrir las señales sin recibir una lista previa de meses problemáticos. Las asociaciones detectadas por Power BI son hipótesis para investigar, **no pruebas de causalidad**.

**Lo que ya existe desde la sesión 1:** tablas `sales`, `product`, `customer`, `store` y `Calendario`; cuatro relaciones activas; medidas de ventas, unidades, pedidos, compradores, ticket y variación de ventas; página `Control_T2`; página **Gerencia** con KPI y gráfico de cintas; y página **Tiendas** con barras apiladas al 100 %. También están creadas las páginas **Clientes**, **Productos**, **Factores comerciales** y **Alertas comerciales**. **No se repite la importación ni la construcción de cintas y barras al 100 %.**

**Regla de continuidad:** no sustituir el PBIX de la sesión 1, no cargar datos artificiales y no fijar como anómalo un mes específico. Las nuevas medidas, tablas de comprobación y visuales se incorporan en ese mismo proyecto.

## Organización del trabajo

| Bloque | Actividad | Tiempo orientativo |
|---|---|---:|
| Fase 1. Planear | Preguntas y criterios de priorización. | 5 min |
| Fase 2. Analizar | Diferenciar composición, contribución, alerta y anomalía. | 10 min |
| Fase 3, 3.1 | Abrir avance y comprobar pedidos. | 15 min |
| Fase 3, 3.2 | Medidas complementarias y umbral. | 30 min |
| Fase 3, 3.3 | Completar visualizaciones comerciales. | 55 min |
| Fase 3, 3.4 | Evaluar todo el historial y detectar anomalías. | 55 min |
| Fase 4. Ejecutar y explicar | Verificación y entrega de PBIX. | 10 min |
| **Total de trabajo guiado** | | **180 min** |

Los tiempos corresponden a una práctica **resuelta y guiada**: las expresiones DAX y de Power Query ya están suministradas. Los videos ejecutivos y técnicos se preparan después del desarrollo, sin ocupar tiempo de construcción. Si un equipo encuentra fallos de origen o rendimiento, debe resolverlos y documentar el ajuste.

## Modalidad de trabajo por fase

| Fase PACE | ¿Se utiliza Power BI? | Propósito | Producto |
|---|---|---|---|
| **1. Planear** | **No** | Definir qué hallazgos son útiles para decidir. | Preguntas y criterios. |
| **2. Analizar** | **No** | Distinguir métricas, cambios y métodos de detección. | Reglas de interpretación. |
| **3. Construir** | **Sí** | Ampliar el modelo, crear nuevos gráficos, alertas y anomalías. | PBIX completo con seis vistas empresariales. |
| **4. Ejecutar y explicar** | **Sí** | Comprobar evidencias y presentar el diagnóstico. | PBIX final, hallazgos y dos videos. |

**Regla metodológica:** las fases 1 y 2 son exclusivamente de lectura y razonamiento. Se abre Power BI a partir de la fase 3.

**Columna de pantallazos:** igual que en la sesión 1, cada tabla deja **25 % para instrucciones** y **75 % para capturas**. Para documentar el desarrollo, inserte imágenes en las celdas derechas conservando las etiquetas `<td>` y `</td>`. En GitHub los bloques «Copiar medida DAX» se pueden desplegar; las fórmulas se crean por separado.

<a id="indice"></a>
## Índice por fases

| Fase | Propósito |
|---|---|
| [Fase 1. Planear](#fase-1) | Definir qué se buscará y cómo se priorizará. |
| [Fase 2. Analizar](#fase-2) | Comprender los métodos antes de configurar los gráficos. |
| [Fase 3. Construir](#fase-3) | Completar vistas y encontrar señales en todos los meses. |
| [Fase 4. Ejecutar y explicar](#fase-4) | Validar, interpretar y entregar el informe final. |

<a id="fase-1"></a>
## Fase 1. Planear

**Modalidad: comprensión y razonamiento; todavía no se abre Power BI.**

### 1.1. Delimitar el diagnóstico

La dirección comercial necesita identificar los meses que merecen revisión, cuantificar su cambio frente al mes inmediatamente anterior, averiguar qué categorías o tiendas contribuyeron a ese cambio y distinguir variaciones de **ventas**, **número de pedidos** y **ticket promedio**. El análisis comienza en el **primer mes registrado** y llega al **último**, con todos los meses intermedios. No se preselecciona ningún período.

### 1.2. Formular decisiones y prioridades

| Pregunta | Indicador o técnica | Decisión que podría apoyar |
|---|---|---|
| ¿Qué meses cambiaron considerablemente frente al anterior? | Variación porcentual de ventas y pedidos; umbral de revisión. | Elegir períodos para investigar. |
| ¿Qué puntos se apartan del comportamiento esperado de la serie? | Detección de anomalías en dos gráficos de líneas. | Contrastar las alertas con otra técnica. |
| ¿Dónde se concentra el cambio mensual? | Cascada, matriz, mapa de árbol y árbol de descomposición. | Localizar categorías, tiendas y segmentos. |
| ¿Qué variables se asocian con pedidos de mayor importe? | Influenciadores clave, sobre pedidos del historial. | Formular hipótesis comerciales verificables. |

**Producto de la fase:** definir qué evidencia sería necesaria antes de recomendar una intervención comercial. Evitar afirmaciones causales basadas únicamente en una variación temporal.

<a id="fase-2"></a>
## Fase 2. Analizar

**Modalidad: lectura y razonamiento; todavía no se abre Power BI.**

### 2.1. Distinguir las técnicas

| Herramienta | ¿Qué muestra? | ¿Qué no demuestra? |
|---|---|---|
| **Cascada de variación** | Aportes positivos y negativos de categorías al cambio absoluto de ventas entre dos meses. | La razón causal del cambio. |
| **Dispersión con burbujas** | Ventas, variación porcentual y pedidos de cada tienda en un mismo mes. | Que las tiendas tengan iguales tamaños o condiciones. |
| **Treemap** | Peso relativo de categorías y subcategorías en ventas. | Que un área grande sea rentable o esté creciendo. |
| **Árbol de descomposición** | Desglose de ventas en rutas seleccionadas o sugeridas. | Causas comprobadas. |
| **Influenciadores clave** | Asociaciones y segmentos vinculados al importe de los pedidos. | Relaciones causales. |
| **Umbral DAX de ±30 %** | Meses cuyo cambio relativo supera una regla empresarial explícita. | Anomalías estadísticas automáticamente. |
| **Anomalías en líneas** | Puntos fuera del rango esperado estimado para una serie temporal. | Que todo cambio mayor al 30 % sea anomalía, o viceversa. |

### 2.2. Establecer reglas de comparación

Se conserva la identidad **Ventas = Pedidos × Ticket Promedio** cuando se evalúa la misma selección (ticket = ventas / pedidos). Esta igualdad sirve para verificar cálculos, no para atribuir causas. La variación porcentual de una variable utiliza como denominador su valor en el **mes inmediato anterior**. Si ese valor es cero, no hay porcentaje comparable y la medida debe quedar en blanco.

Los meses primero y último del historial pueden tener **cobertura parcial de días**. Un mes parcial puede parecer una anomalía por una razón de extracción de datos; debe verificarse su cobertura antes de interpretarlo como fenómeno comercial. No se deben borrar esos meses de la tabla histórica. Si se excluyen del **detector**, debe registrarse por qué y conservarse la tabla completa.

**Producto de la fase:** explicar por qué una caída del 30 % no garantiza que Power BI marque una anomalía estadística, y por qué un gráfico de composición no identifica causalidad.

[Volver al índice](#indice)

<a id="fase-3"></a>
## Fase 3. Construir en Power BI

**Modalidad: práctica guiada.** Abrir el PBIX de la sesión 1 y respetar el orden **3.1 → 3.2 → 3.3 → 3.4**. Los nombres de algunas opciones pueden variar según la actualización de Power BI Desktop. Si el equipo usa separadores DAX `;` en vez de `,`, adaptar los separadores de argumentos sin alterar las funciones.


<a id="paso-3-1"></a>
### 3.1. Retomar el avance y validar el nivel de pedido

**Propósito:** partir del PBIX entregado y confirmar que cada pedido se pueda analizar como una observación de negocio, sin rehacer tareas de la sesión 1.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-1"></a>
<p><strong>3.1.1. Abrir el avance y comprobar el modelo</strong></p>
<ol>
<li>Abrir <strong>Archivo → Abrir</strong> y seleccionar <code>Taller_02_Contoso_Apellidos.pbix</code>. Guardar una copia de seguridad local con un nombre auxiliar usando <strong>Archivo → Guardar como</strong>; después reabrir <code>Taller_02_Contoso_Apellidos.pbix</code> para continuar sobre el archivo previsto para la entrega.</li>
<li>Ir a <strong>Vista Modelo</strong> y confirmar las cuatro relaciones activas 1:* desde <code>product</code>, <code>customer</code>, <code>store</code> y <code>Calendario</code> hacia <code>sales</code>, con filtrado cruzado de dirección única.</li>
<li>Abrir <code>Control_T2</code>, retirar selecciones accidentales y comprobar que las medidas <code>Ventas Totales</code>, <code>Pedidos</code>, <code>Ticket Promedio</code> y <code>Variacion Ventas %</code> existan y funcionen.</li>
<li>Corroborar, sin filtros, los controles del paquete de referencia: ventas <strong>203.723.865,76</strong>, pedidos <strong>83.130</strong> y unidades <strong>628.370</strong>; si se usa otra versión documentar los totales calculados.</li>
</ol>
<p><strong>Comprobar:</strong> el trabajo empieza con el PBIX de la sesión 1, mantiene sus filtros y no duplica las medidas existentes.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.1. Abrir el avance y comprobar el modelo. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-1-2"></a>
<p><strong>3.1.2. Comprobar que un pedido pertenece a un cliente, tienda y fecha</strong></p>
<ol>
<li>Ir a <strong>Inicio → Transformar datos</strong>. En Power Query, hacer clic derecho sobre <code>sales</code> → <strong>Referencia</strong>. Renombrar <code>Control_AtributosPedido</code>.</li>
<li>Usar <strong>Inicio → Agrupar por → Avanzado</strong>: agrupar por <code>OrderKey</code>, operación <strong>Todas las filas</strong> y nombre <code>FilasPedido</code>.</li>
<li>Crear <strong>Agregar columna → Columna personalizada</strong>, nombre <code>PedidoValido</code>, y copiar la fórmula M. Filtrar <code>PedidoValido = false</code>.</li>
<li>Si aparecen pedidos incompatibles, registrar la cantidad y revisar los valores de origen antes de analizar influenciadores. Si no hay conflictos, el control estará vacío. Desactivar <strong>Habilitar carga</strong> de esta consulta y cerrar/aplicar.</li>
</ol>
<details><summary>Copiar fórmula de Power Query: Control pedido M</summary>
<pre><code class="language-powerquery">List.Count(List.Distinct(
    [FilasPedido][CustomerKey]
)) = 1
and List.Count(List.Distinct(
    [FilasPedido][StoreKey]
)) = 1
and List.Count(List.Distinct(
    [FilasPedido][OrderDate]
)) = 1</code></pre>
</details>
<p><em>Este control no implica que un pedido tenga un único producto: puede contener varias líneas y categorías.</em></p>
<p><strong>Comprobar:</strong> cada OrderKey tiene un único cliente, tienda y fecha; ninguna medida de ventas cambió.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.1.2. Comprobar que un pedido pertenece a un cliente, tienda y fecha. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-2"></a>
### 3.2. Crear las medidas complementarias

**Propósito:** conservar las medidas de la primera sesión, agregar los cálculos faltantes y establecer una regla de revisión idéntica para ventas y pedidos.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-1"></a>
<p><strong>3.2.1. Medir clientes con dos o más pedidos en la selección</strong></p>
<ol>
<li>En el panel Datos, seleccionar <code>sales</code> y abrir <strong>Modelado → Nueva medida</strong>. Crear separadamente las dos expresiones siguientes.</li>
<li><code>Clientes Recurrentes</code> cuenta clientes con al menos dos <strong>pedidos diferentes dentro de la selección</strong>, no clientes que hayan comprado en cualquier otro mes.</li>
<li>Dar formato de número entero a <code>Clientes Recurrentes</code> y porcentaje con dos decimales a <code>Clientes Recurrentes %</code>. Verificar que recurrentes nunca supere a compradores.</li>
</ol>
<details><summary>Copiar medida DAX: Clientes Recurrentes</summary>
<pre><code class="language-dax">Clientes Recurrentes =
COALESCE(
    COUNTROWS(
        FILTER(
            VALUES(sales[CustomerKey]),
            CALCULATE([Pedidos]) &gt;= 2
        )
    ),
    0
)</code></pre>
</details>
<details><summary>Copiar medida DAX: Clientes Recurrentes %</summary>
<pre><code class="language-dax">Clientes Recurrentes % =
DIVIDE(
    [Clientes Recurrentes],
    [Clientes Compradores]
)</code></pre>
</details>
<p><strong>Comprobar:</strong> la medida cambia al seleccionar mes, tienda o país y no se presenta como tasa de retención longitudinal.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.1. Medir clientes con dos o más pedidos en la selección. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-2"></a>
<p><strong>3.2.2. Calcular pedidos, ticket y sus cambios mensuales</strong></p>
<ol>
<li>Crear por separado las cuatro medidas DAX adjuntas; no reescribir <code>Ventas Mes Anterior</code> ni <code>Variacion Ventas %</code>, ya creadas.</li>
<li>La referencia de pedidos conserva filtros de clientes, tiendas y productos, pero sustituye el mes seleccionado por el anterior completo. El ticket anterior equivale a ventas anteriores dividido entre pedidos anteriores.</li>
<li>Formatear <code>Pedidos Mes Anterior</code> como entero; <code>Ticket Mes Anterior</code> como decimal de dos cifras; y los cambios terminados en <code>%</code> como porcentaje de dos decimales.</li>
<li>En <code>Control_T2</code>, añadir las medidas y revisar dos meses consecutivos; comparar manualmente <code>(Pedidos actual − Pedidos previo) / Pedidos previo</code>. Si la base es 0 o no existe, el porcentaje es blanco.</li>
</ol>
<details><summary>Copiar medida DAX: Pedidos Mes Anterior</summary>
<pre><code class="language-dax">Pedidos Mes Anterior =
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
    )</code></pre>
</details>
<details><summary>Copiar medida DAX: Ticket Mes Anterior</summary>
<pre><code class="language-dax">Ticket Mes Anterior =
DIVIDE(
    [Ventas Mes Anterior],
    [Pedidos Mes Anterior]
)</code></pre>
</details>
<details><summary>Copiar medida DAX: Variacion Pedidos %</summary>
<pre><code class="language-dax">Variacion Pedidos % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Pedidos] - [Pedidos Mes Anterior],
        [Pedidos Mes Anterior]
    )
)</code></pre>
</details>
<details><summary>Copiar medida DAX: Variacion Ticket %</summary>
<pre><code class="language-dax">Variacion Ticket % =
IF(
    HASONEVALUE(Calendario[InicioMes]),
    DIVIDE(
        [Ticket Promedio] - [Ticket Mes Anterior],
        [Ticket Mes Anterior]
    )
)</code></pre>
</details>
<p><strong>Comprobar:</strong> cada mes se compara con el inmediato anterior; el primer mes sin referencia no se transforma artificialmente en una variación porcentual.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.2. Calcular pedidos, ticket y sus cambios mensuales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-3"></a>
<p><strong>3.2.3. Crear la participación de categoría para las matrices</strong></p>
<ol>
<li>Crear la medida <code>Participacion Categoria %</code> mediante <strong>Nueva medida</strong>.</li>
<li>En <code>Control_T2</code> insertar una matriz simple con filas <code>product[CategoryName]</code> y valores <code>Ventas Totales</code>, <code>Participacion Categoria %</code>.</li>
<li>Aplicar un solo mes y comprobar que las participaciones sumen aproximadamente 100 % bajo la misma selección. Evitar esta medida para interpretar subcategorías individuales.</li>
<li>Dar formato Porcentaje con dos decimales y retirar el filtro temporal de la página técnica al terminar.</li>
</ol>
<details><summary>Copiar medida DAX: Participacion Categoria %</summary>
<pre><code class="language-dax">Participacion Categoria % =
DIVIDE(
    [Ventas Totales],
    CALCULATE(
        [Ventas Totales],
        ALLSELECTED(product[CategoryName])
    )
)</code></pre>
</details>
<p><strong>Comprobar:</strong> el denominador corresponde al conjunto de categorías seleccionado, no al valor de cada subcategoría.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.3. Crear la participación de categoría para las matrices. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-2-4"></a>
<p><strong>3.2.4. Establecer alertas reproducibles de ventas y pedidos</strong></p>
<ol>
<li>Crear por separado las cuatro medidas siguientes. La medida <code>Umbral Alerta %</code> comienza en <strong>0.30 = 30 %</strong>, como criterio explícito de revisión.</li>
<li>La regla marca <strong>Revisar caída</strong> cuando el cambio es ≤ −30 % y <strong>Revisar aumento</strong> cuando es ≥ +30 %. En los demás casos devuelve <strong>Dentro del umbral</strong>; si falta base porcentual, <strong>Sin base comparable</strong>.</li>
<li><code>Alerta Ventas</code> utiliza la variación de ventas; <code>Alerta Pedidos</code>, la variación de pedidos. No utilizar <code>Variacion Ventas</code> como sustituto del porcentaje.</li>
<li>Dar a Umbral formato porcentaje con cero decimales; conservar las alertas como texto y <code>Color Alerta Ventas</code> como código de color. Las cuatro medidas se utilizarán en la vista histórica.</li>
</ol>
<details><summary>Copiar medida DAX: Umbral Alerta %</summary>
<pre><code class="language-dax">Umbral Alerta % = 0.30</code></pre>
</details>
<details><summary>Copiar medida DAX: Alerta Ventas</summary>
<pre><code class="language-dax">Alerta Ventas =
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
    )</code></pre>
</details>
<details><summary>Copiar medida DAX: Alerta Pedidos</summary>
<pre><code class="language-dax">Alerta Pedidos =
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
    )</code></pre>
</details>
<details><summary>Copiar medida DAX: Color Alerta Ventas</summary>
<pre><code class="language-dax">Color Alerta Ventas =
SWITCH(
    [Alerta Ventas],
    &quot;Revisar caída&quot;, &quot;#B91C1C&quot;,
    &quot;Revisar aumento&quot;, &quot;#166534&quot;,
    &quot;Dentro del umbral&quot;, &quot;#374151&quot;,
    &quot;#6B7280&quot;
)</code></pre>
</details>
<p><strong>Comprobar:</strong> la regla se aplica a todos los meses y no clasifica como ausencia de cambio una base porcentual inexistente.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.4. Establecer alertas reproducibles de ventas y pedidos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### 3.3. Completar las vistas de análisis comercial

**Propósito:** construir las visualizaciones que quedaron pendientes, conservando la sincronización del mes solo en **Gerencia**, **Clientes**, **Tiendas** y **Productos**. Estas cuatro vistas se utilizan para analizar posteriormente los meses que se detecten en 3.4.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-1"></a>
<p><strong>3.3.1. Gerencia: cascada de contribuciones al cambio</strong></p>
<ol>
<li>Abrir <strong>Gerencia</strong> y seleccionar el espacio reservado en la sesión 1. Insertar <strong>Gráfico de cascada</strong>.</li>
<li>Colocar <code>product[CategoryName]</code> en <strong>Categoría</strong> y <code>Variacion Ventas</code> en <strong>Eje Y/Valores</strong>; dejar <strong>Desglose</strong> vacío.</li>
<li>Activar etiquetas de datos y titular <strong>Aportes por categoría a la variación mensual</strong>. Mantener todas las categorías del contexto y el filtro de mes <strong>activo</strong> sobre este gráfico.</li>
<li>Comparar la suma de contribuciones con <code>Variacion Ventas</code> de <code>Control_T2</code> bajo filtros idénticos; diferenciar el importe absoluto del cambio porcentual.</li>
</ol>
<p><strong>Comprobar:</strong> las contribuciones de categorías concilian con el cambio absoluto de ventas, sin convertir aportes en explicaciones causales.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.1. Gerencia: cascada de contribuciones al cambio. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-2"></a>
<p><strong>3.3.2. Clientes: tarjetas, clientes principales y país de compra</strong></p>
<ol>
<li>Abrir <strong>Clientes</strong>. Insertar cuatro tarjetas: <code>Clientes Compradores</code>, <code>Pedidos por Cliente</code>, <code>Ventas por Cliente</code> y <code>Clientes Recurrentes %</code>.</li>
<li>Crear una <strong>tabla</strong> con <code>customer[Cliente]</code>, <code>Ventas Totales</code>, <code>Pedidos</code> y <code>Ticket Promedio</code>. En Filtros del visual, usar <strong>Top N: superior 10</strong> con valor <code>Ventas Totales</code> y ordenar de mayor a menor.</li>
<li>Agregar <strong>barras agrupadas</strong>: eje de categorías <code>customer[CountryCliente]</code> y valores <code>Ventas Totales</code>. Titular <strong>Ventas por país del cliente</strong>.</li>
<li>Agregar un segmentador local <code>customer[CountryCliente]</code>; comprobar que no se sincronice con el resto de páginas. Comparar el país del cliente con <code>store[CountryTienda]</code>, que corresponde a otra dimensión.</li>
</ol>
<p><strong>Comprobar:</strong> el mes sincronizado cambia tarjetas y Top 10; clientes recurrentes representa pedidos repetidos en la selección, no fidelización histórica.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.2. Clientes: tarjetas, clientes principales y país de compra. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-3"></a>
<p><strong>3.3.3. Tiendas: dispersión con tamaño de burbuja</strong></p>
<ol>
<li>Abrir <strong>Tiendas</strong>, conservar las barras al 100 % de la sesión 1 y añadir un <strong>gráfico de dispersión</strong>.</li>
<li>Asignar <code>Ventas Totales</code> a <strong>Eje X</strong>, <code>Variacion Ventas %</code> a <strong>Eje Y</strong>, <code>Pedidos</code> a <strong>Tamaño</strong> y <code>store[Tienda]</code> a <strong>Valores/Detalles</strong> (según interfaz): debe haber una burbuja por tienda.</li>
<li>Agregar <code>store[CountryTienda]</code> a <strong>Leyenda</strong>; a información sobre herramientas añadir <code>Ticket Promedio</code> y <code>Ventas Mes Anterior</code>. Titular <strong>Tiendas: nivel de ventas y cambio mensual</strong>.</li>
<li>En <strong>Análisis</strong>, incorporar una línea constante del eje Y en <strong>0</strong>, si está disponible; si no, usar el propio eje como referencia. Comparar dos burbujas y aclarar que tamaños reflejan pedidos, no utilidad.</li>
</ol>
<p><strong>Comprobar:</strong> la posición vertical separa crecimiento y caída; las tiendas sin base porcentual anterior pueden no aparecer en la dispersión.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.3. Tiendas: dispersión con tamaño de burbuja. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-4"></a>
<p><strong>3.3.4. Productos: crear el Treemap de concentración</strong></p>
<ol>
<li>Abrir <strong>Productos</strong> e insertar <strong>Mapa de árbol/Treemap</strong>.</li>
<li>Ubicar <code>product[CategoryName]</code> en <strong>Grupo/Categoría</strong>, <code>product[SubCategoryName]</code> en <strong>Detalles</strong> y <code>Ventas Totales</code> en <strong>Valores</strong>.</li>
<li>Agregar <code>Unidades Totales</code> y <code>Pedidos</code> a información sobre herramientas; activar etiquetas legibles y titular <strong>Concentración de ingresos por categoría y subcategoría</strong>.</li>
<li>Cambiar el mes y observar áreas y jerarquías. Identificar una categoría con mayor participación sin concluir que sea la de mayor margen.</li>
</ol>
<p><strong>Comprobar:</strong> el área se calcula con ventas, no con pedidos, crecimiento, margen o rentabilidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.4. Productos: crear el Treemap de concentración. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-5"></a>
<p><strong>3.3.5. Productos: árbol de descomposición manual y asistido</strong></p>
<ol>
<li>Insertar un <strong>Árbol de descomposición</strong> al lado o debajo del Treemap y darle amplitud suficiente.</li>
<li>Colocar <code>Ventas Totales</code> en <strong>Analizar</strong>. En <strong>Explicar por</strong>, poner <code>store[CountryTienda]</code>, <code>store[Tienda]</code>, <code>product[CategoryName]</code>, <code>product[SubCategoryName]</code> y <code>product[Brand]</code>.</li>
<li>Usar <strong>+</strong> desde el nodo raíz para abrir una ruta <strong>País → Tienda → Categoría → Subcategoría</strong>. Contrastar el importe del último nodo con la tabla o matriz con filtros equivalentes.</li>
<li>Volver a la raíz, probar una ruta distinta que comience por Categoría y, si aparece, probar <strong>Valor alto</strong> (selección asistida por IA). Titular <strong>Explorar ventas por tienda y portafolio</strong>.</li>
</ol>
<p><strong>Comprobar:</strong> el nodo raíz coincide con Ventas Totales sin selecciones directas en otros gráficos; la ruta explora asociaciones y contribuciones, no causalidad.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.5. Productos: árbol de descomposición manual y asistido. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-6"></a>
<p><strong>3.3.6. Crear matrices técnicas para contrastar los gráficos</strong></p>
<ol>
<li>En <code>Control_T2</code> o páginas técnicas duplicadas <code>Control_Tiendas</code>/<code>Control_Productos</code>, crear <strong>matriz por categoría</strong>: filas <code>product[CategoryName]</code>; valores Ventas Totales, Ventas Mes Anterior, Variacion Ventas, Variacion Ventas % y Participacion Categoria %.</li>
<li>Crear <strong>matriz por tienda</strong>: filas <code>store[CountryTienda] → store[State] → store[Tienda]</code>; valores Ventas Totales, Ventas Mes Anterior, Variacion Ventas y Ticket Promedio.</li>
<li>Crear <strong>matriz por producto</strong>: filas <code>product[CategoryName] → product[SubCategoryName] → product[ProductName]</code>; valores Ventas Totales y Variacion Ventas.</li>
<li>Activar expandir/contraer donde corresponda y formato condicional para variación negativa/positiva. Al contrastar, igualar mes, tienda y categoría con la vista comercial y después restaurar controles.</li>
</ol>
<p><strong>Comprobar:</strong> la suma de categorías cuadra con Ventas Totales y las matrices documentan las cifras mostradas en cascada y árbol.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.6. Crear matrices técnicas para contrastar los gráficos. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-7"></a>
<p><strong>3.3.7. Factores comerciales: conservar todos los pedidos del historial</strong></p>
<ol>
<li>Abrir <strong>Factores comerciales</strong>. Confirmar que no tenga sincronización del mes de Gerencia ni filtro local por mes, categoría o tienda. Mantener todos los meses entre <code>PrimerMes</code> y <code>UltimoMes</code>.</li>
<li>Insertar tarjetas <code>Pedidos</code>, <code>Clientes Compradores</code> y <code>Ticket Promedio</code>; sin filtros, comprobar los <strong>83.130 pedidos</strong> de referencia.</li>
<li>En una página técnica insertar temporalmente tabla con <code>sales[OrderKey]</code>, <code>sales[OrderDate]</code>, <code>Ventas Totales</code>, <code>Pedidos</code> y <code>Ticket Promedio</code>. Al filtrar un solo pedido, <code>Pedidos = 1</code> y <code>Ticket Promedio = Ventas Totales</code>.</li>
<li>Recordar el resultado de <code>Control_AtributosPedido</code>: cada OrderKey tiene atributos únicos de fecha, cliente y tienda, condición necesaria para interpretar factores por pedido.</li>
</ol>
<p><strong>Comprobar:</strong> el análisis describe pedidos completos y no confunde importe de línea con ticket de pedido.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.7. Factores comerciales: conservar todos los pedidos del historial. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-8"></a>
<p><strong>3.3.8. Configurar influenciadores clave por pedido</strong></p>
<ol>
<li>Insertar <strong>Influenciadores clave</strong> en Factores comerciales. En <strong>Analizar</strong>, colocar <code>Ticket Promedio</code>.</li>
<li>En <strong>Expandir por</strong> arrastrar <code>sales[OrderKey]</code> para establecer una observación por pedido. Seleccionar la búsqueda de factores asociados con <strong>aumento</strong> del importe, si el menú está disponible.</li>
<li>En <strong>Explicar por</strong>, agregar <code>customer[CountryCliente]</code>, <code>store[CountryTienda]</code>, <code>Calendario[Anio]</code> y <code>Calendario[Mes]</code>. Mantener <code>OrderKey</code> solo en Expandir por.</li>
<li>No usar <code>CategoryName</code> como atributo único del pedido: un pedido puede incluir varias categorías. Tampoco añadir identificadores, cantidad, precio o el mismo Ticket como explicadores.</li>
</ol>
<p><strong>Comprobar:</strong> el modelo compara pedidos del historial con atributos compatibles y no analiza líneas individuales de venta.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.8. Configurar influenciadores clave por pedido. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-9"></a>
<p><strong>3.3.9. Interpretar influenciadores y segmentos principales</strong></p>
<ol>
<li>Abrir <strong>Influenciadores clave</strong> y seleccionar un factor presentado. Registrar grupo, importe asociado, importe de comparación y diferencia que aparezca en el visual.</li>
<li>Comprobar los recuentos de observaciones cuando estén disponibles. El visual puede utilizar una muestra; documentar las limitaciones que informe Power BI.</li>
<li>Ir a la pestaña <strong>Segmentos principales/Top segments</strong>; abrir un segmento y registrar las condiciones conjuntas y su comparación con la población si se muestran.</li>
<li>Si no aparecen influenciadores o segmentos, registrar que no se encontraron patrones suficientes. No inventar cifras ni forzar hallazgos con filtros arbitrarios.</li>
</ol>
<p><strong>Comprobar:</strong> se diferencia un factor individual de una combinación de factores; no se interpreta asociación como causa ni se omiten mensajes de insuficiencia de datos.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.9. Interpretar influenciadores y segmentos principales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-10"></a>
<p><strong>3.3.10. Probar segmentaciones e interacciones entre las vistas</strong></p>
<ol>
<li>Confirmar que el filtro <code>Calendario[AnioMes]</code> de <strong>Gerencia</strong> se sincroniza con <strong>Clientes</strong>, <strong>Tiendas</strong> y <strong>Productos</strong>, pero <strong>no</strong> con Factores comerciales ni Alertas comerciales.</li>
<li>Usar <strong>Formato → Editar interacciones</strong>: el mes debe filtrar KPI, cascada, dispersión, Treemap y árbol, mientras las <strong>cintas</strong> conservan todo el historial (configuración de la sesión 1).</li>
<li>Seleccionar una burbuja, una categoría del Treemap y un nodo del árbol. Observar qué cambia, limpiar las selecciones y contrastar con las matrices.</li>
<li>Restaurar los filtros de dimensiones. El mes usado hasta aquí es una prueba de funcionalidad, <strong>no un mes señalado</strong>; la búsqueda completa comienza en 3.4.</li>
</ol>
<p><strong>Comprobar:</strong> las páginas de detalle comparan un mes; Factores comerciales mantiene todo el historial y no se altera por la sincronización.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.10. Probar segmentaciones e interacciones entre las vistas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### 3.4. Detectar anomalías y revisar todos los meses

**Propósito:** combinar alertas transparentes definidas por negocio y detección automática de anomalías en las series mensuales completas de **ventas y pedidos**, sin presuponer qué meses son importantes. Solo después se investigarán en profundidad.

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-1"></a>
<p><strong>3.4.1. Preparar el historial completo de la página Alertas comerciales</strong></p>
<ol>
<li>Abrir <strong>Alertas comerciales</strong>, eliminar cualquier sincronización con las cuatro vistas mensuales y dejar <strong>sin segmentador de mes</strong> esta página.</li>
<li>En los filtros de página, agregar <code>Calendario[InicioMes]</code> y establecer un intervalo inclusivo desde <code>PrimerMes</code> hasta <code>UltimoMes</code> anotados en sesión 1; dejar todos los segmentos de cliente, tienda y producto.</li>
<li>Titular <strong>Seguimiento y alertas de todo el historial mensual</strong> e indicar las fechas de cobertura. Reservar parte superior para dos líneas; mitad inferior para la tabla mensual.</li>
<li>Comprobar fechas mínima y máxima de <code>sales[OrderDate]</code>. Identificar meses extremos parciales y anotarlos, pero mantenerlos en el inventario mensual. Revisar que no falten meses entre los extremos.</li>
</ol>
<p><strong>Comprobar:</strong> el total de meses proviene de Calendario, la página conserva la cobertura histórica y no responde al mes de Gerencia.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.1. Preparar el historial completo de la página Alertas comerciales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-2"></a>
<p><strong>3.4.2. Crear la tabla mensual y las alertas DAX</strong></p>
<ol>
<li>Insertar una <strong>Tabla</strong> con <code>Calendario[AnioMes]</code>, <code>Ventas Totales</code>, <code>Ventas Mes Anterior</code>, <code>Variacion Ventas</code>, <code>Variacion Ventas %</code>, <code>Alerta Ventas</code>, <code>Pedidos</code>, <code>Pedidos Mes Anterior</code>, <code>Variacion Pedidos %</code>, <code>Alerta Pedidos</code> y <code>Variacion Ticket %</code>.</li>
<li>Ordenar <code>AnioMes</code> ascendente y mostrar porcentajes con dos decimales. En Formato, <strong>desactivar fila de total</strong> para los cambios porcentuales. Ampliar ancho o habilitar desplazamiento horizontal.</li>
<li>Aplicar a <code>Alerta Ventas</code> un <strong>Formato condicional → Color de fuente → Valor del campo</strong> usando <code>Color Alerta Ventas</code>. Conservar también las etiquetas de texto. Añadir tarjeta <code>Umbral Alerta %</code> con título <strong>Límite de revisión mensual</strong>.</li>
<li>Revisar manualmente dos filas consecutivas. Confirmar que las alertas aparezcan para <strong>cada mes</strong> (incluidos meses sin cambio relevante y meses sin base comparable), y que la clasificación de ventas y pedidos sea independiente.</li>
</ol>
<p><strong>Comprobar:</strong> la tabla muestra todos los meses y permite auditar qué regla clasificó cada variable.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.2. Crear la tabla mensual y las alertas DAX. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-3"></a>
<p><strong>3.4.3. Construir la serie temporal continua de ventas</strong></p>
<ol>
<li>Insertar <strong>Gráfico de líneas</strong> en Alertas comerciales. En <strong>Eje X</strong> colocar <code>Calendario[InicioMes]</code> como <strong>fecha directa</strong>, no jerarquía automática; en <strong>Eje Y</strong>, solamente <code>Ventas Totales</code>.</li>
<li>En <strong>Formato visual → Eje X</strong>, elegir <strong>Continuo</strong>; ordenar por fecha ascendente. Dejar vacíos <strong>Leyenda</strong>, valores adicionales y <strong>Eje Y secundario</strong>.</li>
<li>Titular <strong>Ventas mensuales y anomalías: historial</strong>. Comprobar que el primer y el último mes aparecen y que la línea muestra el intervalo completo sin filtros de selección de meses.</li>
<li>Usar solo meses con suficiente cobertura para las conclusiones sobre el detector; si un extremo es parcial, documentar y ajustar únicamente el alcance de detección, sin borrar ese mes de la tabla.</li>
</ol>
<p><strong>Comprobar:</strong> el gráfico contiene una serie numérica mensual con eje temporal continuo y suficientes observaciones para detectar anomalías.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.3. Construir la serie temporal continua de ventas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-4"></a>
<p><strong>3.4.4. Habilitar la detección automática de anomalías de ventas</strong></p>
<ol>
<li>Con la línea seleccionada, abrir el panel <strong>Análisis → Anomalías → Agregar</strong>. Activar las marcas y la <strong>banda de valores esperados</strong>, según las opciones de la versión.</li>
<li>Registrar la <strong>sensibilidad inicial</strong> y los meses marcados. Subir la sensibilidad, observar si aparecen más puntos; restaurar una sensibilidad elegida y justificarla sin manipular el resultado deseado.</li>
<li>En <strong>Explicar por</strong>, agregar <code>product[CategoryName]</code>, <code>store[CountryTienda]</code> y <code>customer[CountryCliente]</code> cuando el visual habilite el campo.</li>
<li>Seleccionar un punto marcado y leer la explicación automática: período, valor real, rango esperado y segmentos asociados cuando aparezcan. Conservar el resultado <strong>ninguna anomalía</strong> si así ocurre.</li>
</ol>
<p><em>Según Microsoft Learn, anomalías funciona con gráficos de líneas de series temporales de una sola medida, sin leyenda ni eje secundario y con al menos cuatro puntos. Fuente: <a href="https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-anomaly-detection">Tutorial oficial de anomalías</a>.</em></p>
<p><strong>Comprobar:</strong> el informe registra la sensibilidad, el período y todas las anomalías detectadas; no se confunden con la regla fija del 30 %.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.4. Habilitar la detección automática de anomalías de ventas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-5"></a>
<p><strong>3.4.5. Crear la serie de pedidos y detectar sus anomalías</strong></p>
<ol>
<li>Duplicar la línea de ventas y reemplazar solo <code>Ventas Totales</code> del eje Y por <code>Pedidos</code>. Mantener <code>Calendario[InicioMes]</code>, eje continuo, todos los meses y una única serie.</li>
<li>Revisar si se conservaron las propiedades de Anomalías; de lo contrario, volver a habilitarlas. Registrar sensibilidad, marcas y banda esperada propia de pedidos.</li>
<li>Titular <strong>Pedidos mensuales y anomalías: historial</strong>. Comparar sus meses marcados con los de la línea de ventas sin forzar que coincidan.</li>
<li>Consultar para los meses señalados <code>Variacion Pedidos %</code> y <code>Variacion Ticket %</code>. Explicar diferencias entre señales de ventas y pedidos usando datos, no suposiciones.</li>
</ol>
<p><strong>Comprobar:</strong> la segunda línea analiza pedidos como variable diferente de ventas y conserva el mismo intervalo histórico.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.5. Crear la serie de pedidos y detectar sus anomalías. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-6"></a>
<p><strong>3.4.6. Contrastar alertas, anomalías y explicaciones</strong></p>
<ol>
<li>Crear una página técnica <code>Control_Alertas</code> sin segmentaciones sincronizadas; conservar una copia de las líneas y la tabla histórica para comprobar los datos.</li>
<li>Para cada mes marcado por la regla de negocio o por alguno de los dos detectores, registrar <strong>mes, variable, valor actual, valor previo, variación %, tipo de señal y método</strong> en el registro de la fase 4.</li>
<li>En una copia de la línea de ventas, desactivar Anomalías y, si aparece, probar clic derecho sobre un cambio → <strong>Analizar → Explicar el aumento/la disminución</strong>. Confirmar cuál es el punto anterior que usa la explicación. Si el menú no existe, usar matrices y cascada.</li>
<li>Contrastar con la tabla del mismo mes y los segmentos explicativos. Un detector puede señalar un mes que no supera ±30 %, y la regla puede señalar meses que el detector no considera atípicos.</li>
</ol>
<p><strong>Comprobar:</strong> quedan documentadas coincidencias, desacuerdos y límites; ninguna explicación del visual se declara causal por sí sola.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.6. Contrastar alertas, anomalías y explicaciones. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-7"></a>
<p><strong>3.4.7. Probar la sensibilidad del umbral de negocio</strong></p>
<ol>
<li>Editar <code>Umbral Alerta %</code> y cambiar temporalmente el literal <code>0.30</code> por <code>0.20</code>. Contar en la tabla los meses de <strong>Revisar caída</strong> o <strong>Revisar aumento</strong> en ventas y pedidos.</li>
<li>Repetir con <code>0.40</code>, sin cambiar la sensibilidad del detector estadístico. Anotar cuántos meses quedaron marcados con cada umbral.</li>
<li>Restaurar <code>Umbral Alerta % = 0.30</code> y guardar. Explicar por qué con un umbral más bajo normalmente aparecen más revisiones, sin inventar conteos.</li>
<li>Mantener explícitos los casos <strong>Sin base comparable</strong> y los meses extremos de cobertura parcial, que no deben contarse como ausencia de alerta.</li>
</ol>
<p><strong>Comprobar:</strong> el umbral vuelve al 30 %; la sensibilidad comercial y la estadística se reconocen como parámetros diferentes.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.7. Probar la sensibilidad del umbral de negocio. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-8"></a>
<p><strong>3.4.8. Seleccionar y estudiar meses a partir de las señales</strong></p>
<ol>
<li>Ordenar el registro por gravedad (magnitud del importe, cambio relativo, coincidencia de ventas/pedidos y evidencia adicional). Seleccionar <strong>al menos dos meses</strong> con sustento, no meses fijados de antemano.</li>
<li>Si menos de dos meses resultan señalados, agregar meses de <strong>contraste</strong> y etiquetarlos como tales, sin presentarlos como anomalías. Documentar los meses que exigen verificar la cobertura.</li>
<li>En <strong>Gerencia</strong> seleccionar el primer mes y estudiar tarjetas y cascada; en <strong>Tiendas</strong>, dispersión y barras al 100 %; en <strong>Productos</strong>, Treemap y árbol; en <strong>Clientes</strong>, compradores y ticket. Usar las matrices para comprobar montos.</li>
<li>Repetir con el segundo mes y completar la tabla de hallazgos de la fase 4. No filtrar Alertas comerciales ni Factores comerciales para imitar la selección del mes; cada página conserva su propia finalidad.</li>
</ol>
<p><strong>Comprobar:</strong> los meses estudiados provienen del barrido integral; cada afirmación cuantitativa se corresponde con el período y los filtros usados.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.8. Seleccionar y estudiar meses a partir de las señales. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="fase-4"></a>
## Fase 4. Ejecutar y explicar | Entrega final

**Modalidad: verificación del PBIX y comunicación empresarial.** Se presenta lo observado, se explica cómo se comprobó y se separan hechos, asociaciones e hipótesis.

### 4.1. Registro de seguimiento de todos los meses

Antes de priorizar, confirmar cuántos meses contiene el intervalo entre `PrimerMes` y `UltimoMes`. La tabla de **Alertas comerciales** debe contener todas esas filas o permitir verificar explícitamente los meses sin transacciones; no basta inspeccionar dos meses. Tras revisar ambos detectores y las alertas DAX, llenar una fila **por mes señalado por al menos un método**. Agregar también los meses cuyo comparativo o cobertura no es concluyente. No inventar meses ni resultados: las casillas quedan deliberadamente vacías para registrar lo observado.

<a id="registro-alertas"></a>

| Mes (aaaa-mm) | Ventas actuales / previas | Variación ventas % | Variación pedidos % | Umbral ventas / pedidos | Anomalía ventas / pedidos | Cobertura y explicación por revisar |
|---|---|---|---|---|---|---|
| *(Registrar una fila por mes identificado)* | | | | | | |
| | | | | | |
| | | | | | |

**Resumen obligatorio:** total de meses del historial: ___; meses con variación marcada en ventas: ___; meses con variación marcada en pedidos: ___; meses con anomalía estadística en ventas: ___; meses con anomalía estadística en pedidos: ___; meses con base o cobertura que impide una conclusión: ___. La cuenta de meses marcados por un método es independiente de la de los otros métodos; no se deben sumar como si fueran conjuntos disjuntos.

### 4.2. Interpretar y contrastar dos períodos

Para dos meses seleccionados después de analizar todo el historial, registrar en cada caso: **qué cambió, cuánto cambió, con respecto a qué mes, qué tienda/categoría contribuyó y qué dato respalda la interpretación**.

| Período | Cambio cuantificado | Aporte por tienda o categoría | Evidencia en los gráficos | Interpretación y limitación |
|---|---|---|---|---|
| Mes priorizado 1 | | | | |
| Mes priorizado 2 o mes de contraste identificado | | | | |

Presentar **tres hallazgos cuantificados**; para cada uno seguir la secuencia **situación → evidencia → interpretación → acción → seguimiento**. Si la detección automática no encuentra anomalías, esa también es una observación válida y debe expresarse, junto con los resultados de la regla de umbral. Una correlación o coincidencia temporal no demuestra causalidad.

### 4.3. Recomendar dos acciones comerciales verificables

| Prioridad | Evidencia observada | Acción propuesta | Responsable | Indicador y plazo | Datos adicionales para validar |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |

Cada recomendación debe guardar relación con una cifra observada. Ejemplos de *tipo de acción*, sin afirmar que sean necesarias en este conjunto: revisar disponibilidad de producto de una categoría con caída, examinar estrategias comerciales de tiendas con disminución de pedidos o investigar cambios en el ticket medio. La causa de una fluctuación requiere información que no siempre está contenida en el histórico de ventas.

### 4.4. Lista de comprobación del proyecto

| Control | Resultado esperado | Cumple |
|---|---|:---:|
| El PBIX es continuación de la sesión 1. | Conservar todas las páginas y medidas sin duplicarlas. | ☐ |
| Modelo y controles originales siguen correctos. | Cuatro relaciones activas y totales históricos comprobados. | ☐ |
| Gerencia está completa. | KPI, cintas de historial y cascada mensual. | ☐ |
| Clientes está completa. | Tarjetas, Top 10 y país del cliente. | ☐ |
| Tiendas está completa. | Barras al 100 % y dispersión con burbujas. | ☐ |
| Productos está completa. | Treemap y árbol de descomposición. | ☐ |
| Factores comerciales está completa. | Influenciadores y revisión de segmentos principales o limitación documentada. | ☐ |
| Alertas comerciales está completa. | Dos líneas temporales, anomalías, umbral y tabla de meses. | ☐ |
| No se pierde el historial. | Todos los meses inventariados, incluidos extremos y sin base comparable. | ☐ |
| Hay contrastes comprobables. | Dos meses estudiados, tres hallazgos y dos acciones sustentadas. | ☐ |
| Los filtros se comportan según su alcance. | Mes sincronizado solo en cuatro páginas mensuales. | ☐ |

Los controles del dataset de referencia, sin filtros, son **203.723.865,76** en ventas, **628.370** unidades y **83.130** pedidos; al filtrar temporalmente 2020: **11.066.206,89** en ventas, **35.234** unidades y **4.806** pedidos. Restablecer el historial completo tras las pruebas.

### 4.5. Video ejecutivo: resultados para el área comercial

**Duración máxima: 5 minutos. Destinatario: gerencia comercial. Cámara activada y pantalla de Power BI visibles.**

1. Presentar brevemente las preguntas y el período completo examinado.
2. Mostrar los meses señalados en **Alertas comerciales**, con porcentajes y anomalías de las dos series.
3. Investigar visualmente **dos meses** mediante cascada, dispersión, mapa de árbol o árbol de descomposición, con las mismas selecciones al comprobar cifras.
4. Presentar **tres hallazgos y dos recomendaciones** con sus indicadores de seguimiento, diferenciando dato observado e hipótesis.

Usar lenguaje empresarial. No dedicar este video a leer fórmulas DAX o explicar menús técnicos.

### 4.6. Video técnico: sustentación para el docente

**Duración máxima: 8 minutos. Destinatario: docente. Cámara activada y pantalla visibles.**

1. **Modelo y continuidad (1 min):** mostrar cuatro relaciones, calendario y control del pedido; indicar qué se recibió de la sesión 1.
2. **Medidas (2 min):** mostrar y explicar referencias mensuales, variación de pedidos y alertas del 30 %; comprobar una fila de tabla.
3. **Gráficos avanzados (2 min):** mostrar cascada, dispersión, Treemap, árbol e influenciadores; justificar la unidad de análisis del pedido.
4. **Anomalías (2 min):** enseñar cómo se configura una línea temporal sin leyenda, el ajuste de sensibilidad y el contraste con el umbral en todo el historial.
5. **Validación (1 min):** evidenciar meses priorizados, conclusiones y limitaciones, con el PBIX ejecutándose.

La sustentación debe demostrar comprensión; no basta recorrer los gráficos terminados. En el caso de no encontrar factores o anomalías, explicar qué se configuró, qué se verificó y el resultado observado.

### 4.7. Entrega en Moodle

**Actividad:** *Taller 02 – Sesión 2. Diagnóstico comercial y detección de anomalías (entrega final)*.

**Entregables obligatorios:**

1. Archivo **`Taller_02_Contoso_Apellidos.pbix`** actualizado con `Control_T2`, controles técnicos necesarios y las seis páginas de negocio **Gerencia**, **Clientes**, **Tiendas**, **Productos**, **Factores comerciales** y **Alertas comerciales**. La primera entrega no se reemplaza: este archivo contiene ambas sesiones.
2. **Enlace del video ejecutivo**, máximo 5 minutos, con cámara activada y resultados comerciales.
3. **Enlace del video técnico**, máximo 8 minutos, con cámara activada y explicación de construcción y validación.
4. **Registro de seguimiento**, análisis de dos períodos, tres hallazgos y dos acciones, incorporados en la evidencia del informe o como documento breve anexo; el docente debe poder identificar los meses y cifras utilizados.

Las capturas de la guía sirven para documentar el procedimiento y **no reemplazan** el archivo `.pbix` ni los videos. Los dos videos pueden grabarse fuera de las tres horas guiadas y entregarse en el plazo que establezca el docente. Si se trabaja en equipo, **cada estudiante entrega individualmente en Moodle** y asegura permisos de visualización de sus enlaces.

### 4.8. Criterios de evaluación

| Componente | Porcentaje de la nota final del Taller 02 |
|---|---:|
| **Sesión 1:** modelo, medidas, cintas y barras al 100 % (avance ya evaluado). | 40 % |
| Detección de cambios: umbrales, dos series y evidencia de cobertura mensual. | 20 % |
| Visualizaciones de diagnóstico avanzado e interacciones comprobables. | 25 % |
| Interpretación, contraste, recomendaciones y sustentación. | 15 % |
| **Total** | **100 %** |

**Nota:** la sesión 2 representa el **60 % de la calificación total**. No se recalifica como nueva entrega lo ya construido y evaluado en la sesión 1, salvo que se hayan realizado correcciones que afectan el funcionamiento del producto final.

## Anexo A. Errores frecuentes y solución

| Síntoma | Posible causa | Verificación o solución |
|---|---|---|
| El mes anterior devuelve blanco en todas las filas. | Más de un mes en el contexto, relación de Calendario ausente o filtros directos sobre `sales[OrderDate]`. | Comprobar selección única por mes, relación activa y usar `Calendario[InicioMes]`. |
| El porcentaje de un mes inicial es blanco. | No existe base del mes anterior o vale cero. | Registrar «Sin base comparable», no inventar 0 %. |
| La cascada no cuadra con la tarjeta. | Distintos filtros de mes, producto o tienda. | Restablecer selecciones y comparar ambas vistas bajo el mismo contexto. |
| Aparece un solo punto en dispersión. | `store[Tienda]` quedó como segmentador y no como identificador del punto. | Colocar el campo en Valores/Detalles y retirar el filtro de tienda si restringe a una sola. |
| No hay influenciadores o aparece un error de relaciones. | `OrderKey` no se usó en Expandir por, atributos no son propios del pedido o información insuficiente. | Ejecutar `Control_AtributosPedido`, revisar la medida objetivo y evitar categorías de producto por línea. |
| No aparece Anomalías. | Eje X como texto, leyenda, varias medidas, eje secundario o menos de cuatro puntos. | Usar `Calendario[InicioMes]` como fecha directa, eje Continuo y una única serie. |
| Faltan meses en Alertas comerciales. | Filtros de mes heredados, segmentador sincronizado o eje de otra granularidad. | Quitar sincronización, revisar filtros ocultos y usar Calendario completo. |
| Un mes extremo aparece como anomalía. | Cobertura parcial de días en el origen. | Revisar fecha mínima/máxima, documentar el sesgo y no presentar causalidad comercial. |
| Cambian las cintas cuando se selecciona un mes. | Se alteró la interacción configurada en sesión 1. | Volver a Editar interacciones → Ninguno desde el segmentador de mes hacia las cintas. |
| Los totales no coinciden con la referencia. | CSV distinto, tipos regionales o relaciones incorrectas. | Revisar versión del dataset, `en-US`, precisión y relaciones 1:*. |

## Anexo B. Fuentes y recursos

1. [Datos utilizados en este taller: Contoso csv-100k.7z](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z). Archivo de base para reproducir los controles del taller.
2. [Microsoft Learn: tutorial de detección de anomalías en Power BI](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-anomaly-detection). Requisitos de gráfico de líneas, eje temporal, sensibilidad y explicaciones.
3. [Microsoft Learn: utilización del panel Análisis](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-analytics-pane). Funciones disponibles en visuales, incluida la detección de anomalías.
4. [Microsoft Learn: visualización de influenciadores clave](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-influencers). Nivel de análisis, expansión por pedido, segmentos y limitaciones.
5. [Microsoft Learn: árbol de descomposición](https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-decomposition-tree). Desglose manual y sugerencias automáticas.
6. [Guía de sesión 1](Taller_02_Power_BI_PACE_Contoso_Sesion_1.md). Punto de partida y estructura del archivo `.pbix`.

**Criterio de interpretación:** alertas DAX, anomalías estadísticas, desgloses y factores asociados ofrecen **perspectivas complementarias**. El diagnóstico se fundamenta en coincidencias verificables, medidas correctas y limitaciones explícitas; no en un mes impuesto por el enunciado.
