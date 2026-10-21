# Taller resuelto 02. Diagnóstico comercial de Contoso en Power BI | Sesión 1

**Metodología PACE | Construcción del modelo, indicadores comerciales y visualizaciones de composición**

**Duración:** 3 horas de trabajo guiado.  
**Dirigido a:** estudiantes de últimos semestres de Administración de Empresas.  
**Punto de partida:** un archivo `.pbix` completamente nuevo; **no se requiere haber terminado ni entregado el Taller 1**.  
**Caso:** Contoso, empresa ficticia con registros de ventas, clientes, tiendas y productos.  
**Objetivo de esta sesión:** preparar el modelo de datos, calcular indicadores, habilitar la comparación mensual y construir dos visualizaciones avanzadas que permitan examinar el liderazgo de categorías y la composición comercial por tienda.  
**Avance de la sesión:** archivo `Taller_02_Contoso_Apellidos.pbix` con modelo funcional, página de control, página **Gerencia** (indicadores y cintas) y página **Tiendas** (barras apiladas al 100 %); las demás páginas quedan creadas para su desarrollo posterior.

> **Situación empresarial.** La gerencia de Contoso necesita un diagnóstico comercial de todos los meses registrados. Aún no sabe si hubo cambios importantes ni cuáles fueron. Por eso primero se construirá una base confiable y un conjunto de visualizaciones para explorar la operación. **No se atribuirán anomalías ni meses críticos sin analizar la serie completa**, actividad que corresponde a la sesión 2.

Este documento es un **taller demostrativo resuelto**: proporciona rutas de menú, instrucciones, expresiones de Power Query y DAX, verificaciones y campos para documentar pantallazos. Las cifras de control no deben escribirse manualmente en los gráficos. La actividad se enfoca en nuevas vistas y comparaciones, sin repetir como ejercicio principal el tablero de ventas básicas del Taller 1.

## Organización del trabajo

| Bloque | Actividad | Tiempo orientativo |
|---|---|---:|
| Fase 1. Planear | Problema, preguntas y decisiones. | 10 min |
| Fase 2. Analizar | Dimensiones, indicadores y reglas de interpretación. | 15 min |
| Fase 3. Construir: 3.1 | Iniciar e importar con el bloque de preparación. | 30 min |
| Fase 3. Construir: 3.2 | Incorporar y revisar clientes y tiendas. | 30 min |
| Fase 3. Construir: 3.3 | Modelo y medidas DAX. | 50 min |
| Fase 3. Construir: 3.4 | Cintas, barras al 100 % e interacciones. | 30 min |
| Fase 4. Ejecutar y explicar | Verificar, guardar y entregar avance. | 15 min |
| **Total** | | **180 min** |

Los tiempos suponen el uso de las **fórmulas ya suministradas**, sin que el estudiante deba construirlas desde cero. Si aparecen errores de importación o de rutas locales, se deben resolver antes de validar el informe.

## Modalidad de trabajo por fase

| Fase PACE | ¿Se utiliza Power BI? | Propósito | Producto |
|---|---|---|---|
| **1. Planear** | **No** | Formular las preguntas relevantes para la gerencia. | Alcance de la sesión. |
| **2. Analizar** | **No** | Comprender los datos, las métricas y los riesgos de interpretación. | Criterios de validación. |
| **3. Construir** | **Sí** | Importar, modelar, calcular y visualizar. | PBIX con dos páginas funcionales. |
| **4. Ejecutar y explicar** | **Sí** | Verificar interacciones y comunicar hallazgos preliminares. | Entrega parcial evaluable. |

**Regla metodológica:** durante las fases 1 y 2 no se abre Power BI ni se descargan datos. Todas las operaciones técnicas empiezan en la fase 3.

**Evidencias:** las tablas de construcción conservan **25 % para instrucciones** y **75 % para pantallazos**. Si esta guía se publica en GitHub, el docente puede editar la celda derecha de cada paso y pegar una o varias imágenes sin eliminar `<td>` o `</td>`. El espacio vacío está reservado intencionalmente para las capturas. Cada bloque plegable incluye fórmulas listas para copiar.

<a id="indice"></a>
## Índice por fases

| Fase | Propósito |
|---|---|
| [Fase 1. Planear](#fase-1) | Definir problema, preguntas y decisiones. |
| [Fase 2. Analizar](#fase-2) | Interpretar indicadores, estructura y visualizaciones. |
| [Fase 3. Construir](#fase-3) | Construir desde cero el avance de Power BI. |
| [Fase 4. Ejecutar y explicar](#fase-4) | Validar, sustentar y entregar el avance. |

<a id="controles"></a>
## Cifras de control del paquete Contoso

Se utilizan los totales de referencia de `csv-100k.7z`. La consulta `ControlBase` calculará estos valores a partir de los archivos originales.

| Selección | Líneas de venta | Unidades | Pedidos distintos | Ventas |
|---|---:|---:|---:|---:|
| Historial completo | 199.873 | 628.370 | 83.130 | 203.723.865,76 |
| Año 2020 | 11.267 | 35.234 | 4.806 | 11.066.206,89 |

En la versión de referencia, `product.csv` contiene **2.517 productos**. La unidad monetaria del dataset se conserva tal como viene en los datos; no se asume que sean pesos colombianos. Las cifras deben obtenerse desde Power BI, **no escribirse manualmente en tarjetas o tablas**.

<a id="fase-1"></a>
## Fase 1. Planear

**Modalidad: comprensión y razonamiento, sin abrir Power BI.**

### 1.1. Comprender la necesidad empresarial

La gerencia solicita conocer el comportamiento de las ventas, los pedidos y los compradores; comprender si las categorías cambian de posición a lo largo del tiempo, y comparar la **mezcla de ventas** de distintas tiendas. Más adelante estudiará automáticamente los meses con variaciones atípicas. En esta primera sesión se implementa la infraestructura y se prueban los gráficos sin elegir de antemano un mes problemático.

**Pregunta orientadora:** ¿por qué una tienda con mayor proporción de una categoría no necesariamente vende más dinero que otra tienda?

### 1.2. Traducir las necesidades en indicadores y gráficos

| Pregunta empresarial | Información | Visual y lectura esperada |
|---|---|---|
| ¿Cuánto vendió Contoso en un mes y cómo se compara con el anterior? | Ventas, pedidos, compradores, ticket y variación. | Tarjetas KPI en **Gerencia**. |
| ¿Cambió la posición de las categorías durante el historial? | Mes, categoría y ventas. | **Gráfico de cintas**, con la serie completa. |
| ¿Qué proporción representan las categorías dentro de cada tienda? | Tienda, categoría y ventas. | **Barras apiladas al 100 %**, con total normalizado por tienda. |
| ¿Son correctas las cifras y el calendario? | Totales del origen, meses y medidas. | Tabla técnica **Control_T2**. |

**Producto de la fase:** responder en dos o tres líneas qué decisiones podrían apoyar el gráfico de cintas y el de barras al 100 %. Todavía no se plantean causas de cambios comerciales.

<a id="fase-2"></a>
## Fase 2. Analizar

**Modalidad: lectura y razonamiento, sin abrir Power BI.**

### 2.1. Identificar las tablas y su granularidad

| Tabla | Nivel de detalle | Clave / atributos principales | Función |
|---|---|---|---|
| `sales` | Una línea de producto vendida dentro de un pedido. | `OrderKey`, `OrderDate`, `CustomerKey`, `StoreKey`, `ProductKey`, `Quantity`, `NetPrice`. | Hechos y medidas. |
| `product` | Una fila por producto. | `ProductKey`, `CategoryName`, `SubCategoryName`, `ProductName`, `Brand`. | Clasificación del portafolio. |
| `customer` | Una fila por cliente. | `CustomerKey`, `GivenName`, `Surname`, `CountryFull`, `City`. | Segmentación de compradores. |
| `store` | Una fila por tienda. | `StoreKey`, `Description`, `CountryName`, `State`. | Segmentación de tiendas. |
| `Calendario` | Un día por fila, calculado desde `sales[OrderDate]`. | `Date`, `InicioMes`, `AnioMes`. | Comparaciones temporales. |

Una línea de venta **no equivale necesariamente a un pedido**. Un mismo `OrderKey` puede aparecer en varias líneas. Por eso los pedidos se cuentan con `DISTINCTCOUNT` y no con el número de filas.

### 2.2. Establecer las reglas del análisis

- **Ventas Totales:** suma de `Quantity × NetPrice` sin eliminar registros del origen.
- **Clientes Compradores:** número de `CustomerKey` distintos presentes en las ventas seleccionadas; no es el total del catálogo de clientes.
- **Ticket Promedio:** ventas divididas por pedidos distintos.
- **Mes anterior:** el mes completo inmediatamente anterior al seleccionado, conservando los filtros de categoría y tienda.
- **Variación mensual absoluta:** ventas del mes seleccionado menos ventas del mes anterior; el porcentaje usa como denominador las ventas anteriores.
- **Cintas:** comparan **posiciones relativas de categorías** a través de todos los meses. El segmentador del mes no debe reducirlas a un solo punto.
- **Barras al 100 %:** comparan la **composición porcentual** de cada tienda. Todas las barras miden 100 %, independientemente de sus ventas absolutas.

Una variación sin referencia anterior puede ser imposible de expresar porcentualmente; una proporción del 100 % no significa que una tienda vendió lo mismo que otra. El informe debe evitar ambas interpretaciones erróneas.

### 2.3. Explicar el modelo esperado

El diseño será de **estrella**. `product`, `customer`, `store` y `Calendario` tendrán relaciones **1:* activas, con dirección única de filtrado hacia `sales`**. Un filtrado por mes debe modificar los indicadores de negocio; una comparación con el mes anterior no debe perder sus filtros de tienda y producto.

**Producto de la fase:** justificar por qué se necesita `Calendario` para comparar meses, por qué `Pedidos` utiliza un conteo distinto y qué representa el 100 % de cada barra.

[Volver al índice](#indice)

<a id="fase-3"></a>
## Fase 3. Construir en Power BI

**Modalidad: práctica guiada.** Iniciar con un archivo nuevo. Respetar el orden **3.1 → 3.2 → 3.3 → 3.4**, sin utilizar el `.pbix` del Taller 1. Los nombres de algunos menús pueden variar según la versión de Power BI Desktop. Si su instalación exige `;` en DAX en lugar de `,`, adapte únicamente los separadores de argumentos.

<a id="paso-3-1"></a>
### 3.1. Inicio autónomo y preparación mínima

**Propósito:** importar los CSV originales y crear la base sin repetir manualmente las transformaciones del Taller 1. **Tiempo sugerido: 30 minutos.**

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
### 3.2. Calidad de datos e incorporación de dimensiones

**Propósito:** preparar clientes y tiendas, validar claves y habilitar una lectura inequívoca de sus nombres. **Tiempo sugerido: 30 minutos.** Si alguno de esos CSV falta en la carpeta del paquete, siga el [Anexo A](#anexo-a), sin crear datos ficticios.

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
<p><strong>3.2.7. Aplicar cambios y conservar la precisión</strong></p>
<ol><li>Revisar <code>sales[NetPrice]</code> e <code>ImporteVenta</code>: conservar <strong>Número decimal</strong> y la precisión del origen.</li><li>Mantener <code>ImporteVenta = [Quantity] * [NetPrice]</code>. Conservar todo el historial de ventas en Power Query.</li><li>Pulsar <strong>Inicio → Cerrar y aplicar</strong>. Guardar el archivo.</li></ol>
<p><strong>Comprobar:</strong> se cargan las dimensiones; los controles anti izquierda quedan sin carga y los totales de ventas no cambian.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.2.7: Aplicar cambios y conservar la precisión. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-3"></a>
### 3.3. Modelo y medidas para la comparación mensual

**Propósito:** relacionar las cuatro dimensiones, construir los indicadores esenciales y validarlos. **Tiempo sugerido: 50 minutos.** Todos los cálculos se crean mediante **Modelado → Nueva medida**, salvo `Calendario`, que se crea con **Modelado → Nueva tabla**.

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
<p><strong>3.3.8. Calcular las ventas del mes anterior completo</strong></p>
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
<!-- Captura 3.3.8: Calcular las ventas del mes anterior completo. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-9"></a>
<p><strong>3.3.9. Calcular la variación absoluta y porcentual</strong></p>
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
<!-- Captura 3.3.9: Calcular la variación absoluta y porcentual. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-10"></a>
<p><strong>3.3.10. Configurar formatos de las medidas</strong></p>
<ol><li>Seleccionar cada medida en el panel Datos y abrir <strong>Herramientas de medición → Formato</strong>.</li><li>Usar <strong>Número entero, 0 decimales</strong> para pedidos, unidades y clientes compradores. Las medidas enteras adicionales de la sesión 2 se formatearán cuando se creen.</li><li>Usar <strong>Número decimal, 2 decimales</strong> para importes, ticket, ventas por cliente, pedidos por cliente y unidades por pedido.</li><li>Usar <strong>Porcentaje, 2 decimales</strong> para todas las medidas que terminan en <code>%</code>. Las fórmulas ya calculan la razón: el formato aplica la presentación porcentual.</li><li>En los visuales de comprobación, seleccionar <strong>Mostrar unidades: Ninguno</strong>.</li></ol>
<p><strong>Comprobar:</strong> los valores se muestran completos y las variaciones negativas conservan su signo.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.10: Configurar formatos de las medidas. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-3-11"></a>
<p><strong>3.3.11. Comprobar las medidas y el historial mensual</strong></p>
<ol><li>Renombrar la página inicial vacía como <code>Control_T2</code>. Insertar una tabla con Calendario[AnioMes] y las medidas de ventas, ventas anteriores, variación, pedidos y ticket.</li><li>En Filtros de esta página, agregar <code>Calendario[InicioMes]</code>. Mediante Filtrado avanzado, fijar <strong>es posterior o igual a la fecha de PrimerMes</strong> y <strong>es anterior o igual a la fecha de UltimoMes</strong>, usando las fechas anotadas en 3.1.6. Se incluyen ambos límites y todos los meses intermedios.</li><li>Comprobar dos meses consecutivos con datos y sus referencias. Agregar una tarjeta de Clientes Compradores y comprobar cómo cambia al filtrar por un mes o una tienda.</li><li>Sin filtros temporales ni de dimensión, comprobar los totales del historial. Como prueba adicional, filtrar temporalmente el año 2020: ventas 11.066.206,89, unidades 35.234 y pedidos 4.806. Después retirar ese filtro anual y restablecer el intervalo completo.</li><li>Guardar y mantener Control_T2 fuera de las segmentaciones sincronizadas.</li></ol>
<p><strong>Comprobar:</strong> los cálculos concilian con el origen, los controles se distinguen del diagnóstico y todos los meses del intervalo están disponibles para revisar.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.3.11: Comprobar las medidas y el historial mensual. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="paso-3-4"></a>
### 3.4. Visualizaciones de la primera entrega

**Propósito:** implementar **Gerencia** y **Tiendas** con visualizaciones que no se trabajaron como eje principal del Taller 1: cintas y barras apiladas al 100 %. **Tiempo sugerido: 30 minutos.** Las páginas **Clientes**, **Productos**, **Factores comerciales** y **Alertas comerciales** se reservan para la sesión 2.

| Página | Estado al final de esta sesión | Período |
|---|---|---|
| `Gerencia` | Indicadores y gráfico de cintas. | Indicadores: mes único; cintas: todo el historial. |
| `Tiendas` | Barras apiladas al 100 % por tienda y categoría. | Mes seleccionado. |
| `Clientes`, `Productos` | Creadas y listas para completar en sesión 2. | Mes sincronizado. |
| `Factores comerciales`, `Alertas comerciales` | Creadas, sin gráficos por ahora. | Historial completo en sesión 2. |
| `Control_T2` | Tabla de meses, cifras y variaciones para validación. | Todos los meses. |

<table width="100%">
<thead><tr><th width="25%">Instrucciones paso a paso</th><th width="75%">Pantallazos</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-1"></a>
<p><strong>3.4.1. Crear seis vistas y preparar el lienzo</strong></p>
<ol><li>Crear las páginas <code>Gerencia</code>, <code>Clientes</code>, <code>Tiendas</code>, <code>Productos</code>, <code>Factores comerciales</code> y <code>Alertas comerciales</code>. Conservar <code>Control_T2</code> como página técnica de este archivo.</li><li>En cada página de negocio, abrir <strong>Formato de página → Configuración del lienzo</strong> y elegir <strong>16:9</strong>.</li><li>Reservar la franja superior para título, período y filtros. Ubicar las tarjetas debajo y los visuales principales en la parte inferior.</li><li>Usar colores estables para cada categoría en todos los gráficos. Mantener rojo para variaciones negativas y verde para positivas, junto con sus signos.</li><li>En esta sesión reservar espacio para el gráfico de cintas en Gerencia y las barras al 100 % en Tiendas. Dejar sin construir la cascada, dispersión, Treemap, árbol de descomposición, influenciadores y alertas, que corresponden a la sesión 2.</li></ol>
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
<ol><li>En Gerencia, Clientes, Tiendas y Productos, conservar el intervalo entre PrimerMes y UltimoMes mediante el filtro avanzado de Calendario[InicioMes], como en 3.3.11. Mantener disponibles todos sus años.</li><li>En Gerencia, insertar segmentaciones de Calendario[AnioMes], store[CountryTienda], store[Tienda] y product[CategoryName].</li><li>Activar Selección única en el mes. Elegir un mes con referencia anterior para probar los gráficos; esta selección inicial no lo declara como período crítico.</li><li>Titular los filtros Mes de análisis, País de la tienda, Tienda y Categoría. Mantener todos los segmentos. Los meses de investigación se elegirán después del seguimiento de toda la serie en 3.5.</li></ol>
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
<ol><li>Crear cinco tarjetas con Ventas Totales, Variacion Ventas %, Pedidos, Clientes Compradores y Ticket Promedio.</li><li>Configurar unidades completas y los decimales definidos en 3.3.10. Alinear y distribuir las tarjetas.</li><li>Agregar un título: <strong>Diagnóstico comercial: mes seleccionado frente al anterior</strong>.</li><li>En <code>Control_T2</code>, conservar una tabla con ventas, ventas del mes anterior, variaciones, pedidos y ticket promedio para comprobar las cifras.</li></ol>
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
<p><strong>3.4.7. Tiendas: crear barras apiladas al 100 %</strong></p>
<ol><li>En Tiendas, seleccionar <strong>Gráfico de barras apiladas 100 %</strong>. Comprobar el icono para elegir la versión normalizada.</li><li>Eje Y: <code>store[Tienda]</code>. Eje X o Valores: <code>Ventas Totales</code>. Leyenda: <code>product[CategoryName]</code>.</li><li>Mantener todas las categorías o varias categorías seleccionadas. Titular <strong>Composición de ventas por tienda y categoría</strong>.</li><li>Si hay muchas tiendas, aplicar Top N de tiendas por Ventas Totales solo a este visual. Conservar todas las categorías dentro de cada tienda mostrada.</li><li>Agregar Ventas Totales y Unidades Totales a Información sobre herramientas, cuando el panel permita añadirlas.</li></ol>
<p><strong>Comprobar:</strong> cada barra representa una tienda y sus segmentos muestran proporciones de las categorías seleccionadas.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.7: Tiendas: crear barras apiladas al 100 %. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-8"></a>
<p><strong>3.4.8. Tiendas: formatear e interpretar los porcentajes</strong></p>
<ol><li>Activar las etiquetas de datos y su presentación porcentual cuando esté disponible. Conservar la leyenda y colores consistentes.</li><li>Verificar que el eje de proporciones recorra de 0 % a 100 %. El visual normaliza Ventas Totales automáticamente.</li><li>Comparar dos tiendas y localizar qué categoría tiene mayor peso en cada una. Revisar los importes reales con el cursor o en la matriz de comprobación.</li><li>Verificar que cada barra sume aproximadamente 100 %; puede existir una diferencia mínima por redondeo.</li><li>Explicar que la misma longitud total de las barras no significa iguales ventas. Con una sola categoría seleccionada, cada tienda tendrá un único segmento de 100 %.</li></ol>
<p><strong>Comprobar:</strong> se diferencia composición porcentual de volumen absoluto y se declara qué categorías forman el denominador.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.8: Tiendas: formatear e interpretar los porcentajes. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
<tr>
<td width="25%" valign="top">
<a id="paso-3-4-9"></a>
<p><strong>3.4.9. Revisar interacciones y preparar el avance</strong></p>
<ol>
<li>Seleccionar un mes cualquiera que tenga ventas en el mes anterior; esta elección es <strong>solo una prueba técnica</strong>, no la identificación de una anomalía.</li>
<li>Verificar que las cinco tarjetas de Gerencia respondan al mes, país de tienda, tienda y categoría.</li>
<li>Comprobar que el gráfico de cintas siga mostrando <strong>todo el historial</strong> al cambiar el segmentador de mes; las barras al 100 % sí deben responder al mes.</li>
<li>Elegir dos tiendas en el gráfico al 100 %, identificar las categorías de mayor peso y consultar los <strong>importes reales</strong> con el cursor. Una barra de 100 % no compara montos absolutos.</li>
<li>Revisar las páginas vacías de la sesión 2: no se deben introducir datos simulados, meses predeterminados ni filtros mensuales en Factores comerciales o Alertas comerciales.</li>
<li>Retirar selecciones directas sobre los gráficos, conservar el mes de prueba en las vistas mensuales y guardar el PBIX.</li>
</ol>
<p><strong>Comprobar:</strong> el informe diferencia el historial de un mes de detalle y permite investigar una tienda sin alterar los controles de origen.</p>
</td>
<td width="75%" valign="top">
<!-- Captura 3.4.9: Revisar interacciones y preparar el avance. Pegue aquí una o varias imágenes. -->
<p><br><br></p>
</td>
</tr>
</tbody>
</table>

[Volver al índice](#indice)

<a id="fase-4"></a>
## Fase 4. Ejecutar y explicar | Primera entrega

**Modalidad: validación y comunicación breve. Tiempo sugerido: 15 minutos.** La primera sesión no exige identificar ni explicar anomalías: esa investigación comienza en la segunda sesión, después de verificar todos los meses.

### 4.1. Lista de comprobación antes de entregar

| Verificación | Resultado esperado | Cumple |
|---|---|:---:|
| Se inició desde un PBIX vacío. | No depende del trabajo del Taller 1. | ☐ |
| `BaseContoso`, `sales` y `product` se crearon correctamente. | Ventas y productos cargados y sin errores. | ☐ |
| Se incorporaron `customer` y `store` o su alternativa documentada. | Claves válidas y campos usados por los gráficos. | ☐ |
| Las cuatro relaciones del modelo son activas. | Cardinalidad 1:* y dirección única. | ☐ |
| Los totales históricos concilian. | 203.723.865,76 en ventas; 83.130 pedidos; 628.370 unidades. | ☐ |
| Al filtrar el año 2020 se obtiene el control conocido. | 11.066.206,89 en ventas; 4.806 pedidos; 35.234 unidades. | ☐ |
| `Control_T2` incluye todos los meses. | No se excluyen meses sin una justificación. | ☐ |
| Las tarjetas muestran indicadores dinámicos. | Responden a la selección mensual y de dimensiones. | ☐ |
| Las cintas conservan el historial. | El segmentador del mes no limita su eje temporal. | ☐ |
| Las barras apiladas muestran composiciones. | Cada tienda equivale al 100 % de las categorías seleccionadas. | ☐ |

Los totales del historial se verifican **sin filtros activos**. El control anual se hace temporalmente y se retira al finalizar. Cuando use la ruta alternativa de los anexos, documente la ausencia de ubicación real.

### 4.2. Interpretación breve del avance

Tomando como evidencia los gráficos y el mes de prueba, diligenciar la siguiente tabla con **cifras observadas en Power BI**, no supuestas:

| Pregunta | Evidencia cuantitativa | Interpretación comercial | Límite del análisis |
|---|---|---|---|
| ¿Qué categoría lideró las ventas en dos meses consecutivos y cambió su posición? | | | |
| ¿Qué categoría representa la mayor proporción de ventas en dos tiendas diferentes? | | | |

**Guía de interpretación:**

- Para las cintas, informar **categoría, meses, posición y ventas**; si no hay cambio de liderazgo, registrar estabilidad.
- Para las barras al 100 %, informar **tienda, categoría y proporción aproximada**, más el importe absoluto consultado.
- Una diferencia de porcentajes o posiciones **no demuestra causas**. Los meses atípicos, las contribuciones al cambio y las asociaciones se evaluarán en la sesión 2.

### 4.3. Entrega parcial en Moodle

**Actividad:** *Taller 02 – Sesión 1. Avance de modelo y visualización comercial*.

**Archivo obligatorio:** `Taller_02_Contoso_Apellidos.pbix`.

El archivo debe contener `Control_T2`, las cuatro relaciones, las medidas implementadas, las seis páginas de negocio creadas y **dos páginas de negocio desarrolladas**: `Gerencia` y `Tiendas`. Las páginas previstas para la sesión 2 pueden estar vacías.

**Evidencia adicional:** dos capturas legibles, una de **Vista Modelo** y otra de los **gráficos construidos**, más la tabla de dos interpretaciones incluida en 4.2 (en la descripción de Moodle o como documento breve, según las instrucciones del curso). Si se usa esta guía como documento de trabajo en GitHub, las capturas pueden insertarse en la columna derecha sin modificar las instrucciones.

**La segunda sesión se continuará sobre el mismo PBIX.** El avance entregado no reemplaza la entrega final del Taller 02. No se exige grabar los dos videos finales en esta primera sesión; su sustentación se realiza con el proyecto completo en la sesión 2.

### 4.4. Criterios de evaluación del avance (40 % de la nota total del Taller 02)

| Criterio | Peso en la calificación total del taller |
|---|---:|
| Modelo de datos, consistencia de claves y medidas DAX. | 15 % |
| Configuración correcta de cintas, barras al 100 % y filtros. | 20 % |
| Validaciones e interpretación inicial sustentada en cifras. | 5 % |
| **Subtotal de la sesión 1** | **40 %** |

El **60 % restante** corresponde a los componentes de análisis avanzado, detección de anomalías y sustentación de la sesión 2. Se evalúa el funcionamiento del archivo y la comprensión de sus resultados, no la copia de las cifras de referencia.

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
- Aplicar también el control de atributos por pedido, que se realizará al comenzar la sesión 2. Después continuar en [3.3.1](#paso-3-3-1) con las relaciones y todas las medidas.
- Conservar las vistas por cliente, tienda y producto. En las vistas geográficas, identificar la limitación: **Sin información** no representa un país. En influenciadores, retirar las columnas geográficas constantes y evaluar el mes; registrar si no se detectan asociaciones.
- Explicar en la sustentación qué dimensiones se derivaron de ventas y cuáles se importaron. Este catálogo derivado contiene solo identificadores presentes en el historial de ventas, no la totalidad de clientes o tiendas de la empresa.

<a id="anexo-b"></a>
## Anexo B. Errores frecuentes de la sesión 1

| Síntoma | Posible causa | Acción correctiva |
|---|---|---|
| `BaseContoso` no encuentra archivos. | Ruta distinta de la carpeta de los CSV. | Corregir `CarpetaDatos`; no incluir el nombre de un CSV. |
| No se ve `sales` como tabla. | Se cargó solo el registro `BaseContoso`. | Crear las consultas de salida `sales` y `product` del paso 3.1.5. |
| Importe muy distinto de los controles. | Tipos o configuración regional incorrecta. | Revisar el decimal con `en-US`, la precisión y la versión del paquete. |
| No se pueden establecer relaciones 1:*. | Claves duplicadas, nulas o tipos distintos. | Revisar unicidad y tipos en las dimensiones. |
| Ventas anteriores dan blanco en todos los meses. | Más de un mes elegido o filtros directos de fechas de `sales`. | Usar `Calendario[AnioMes]` con selección única y relacionar `Calendario[Date]`. |
| Cintas muestran solo un mes. | El segmentador del mes está filtrando el visual histórico. | En Editar interacciones, seleccionar **Ninguno** para ese segmentador sobre las cintas. |
| Las barras parecen representar iguales montos. | Interpretación incorrecta de la normalización. | Consultar Ventas Totales en la información emergente; cada barra suma 100 % de su propia tienda. |
| Barra al 100 % de un solo color. | Solo una categoría seleccionada. | Seleccionar varias categorías o limpiar ese filtro. |
| Las vistas muestran cifras distintas. | Filtros de tienda/categoría o selecciones directas no coinciden. | Igualar los filtros y limpiar selecciones cruzadas antes de comparar. |
| El archivo no abre en otra computadora. | Ruta local del CSV. | En Configuración de origen de datos, actualizar la ruta hacia la carpeta del nuevo equipo. |

<a id="fuentes"></a>
## Fuentes y materiales de referencia

1. [Datos del Taller 02: Contoso csv-100k.7z](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z). Paquete utilizado para reproducir controles.
2. [Contoso Data Generator, SQLBI](https://docs.sqlbi.com/contoso-data-generator/details). Estructura y procedencia de datos.
3. [Microsoft Learn: relaciones de modelos de Power BI](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-relationships-understand). Cardinalidad y filtros.
4. [Microsoft Learn: tablas de fechas](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-date-tables). Estructura temporal.
5. [Microsoft Learn: DISTINCTCOUNT](https://learn.microsoft.com/en-us/dax/distinctcount-function-dax). Conteo de pedidos únicos.
6. [Microsoft Learn: creación de gráficos de cintas](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-ribbon-charts). Comparación de posiciones de categorías.
7. [Microsoft Learn: gráficos de barras y columnas](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-column-charts). Composición y comparación de visuales.

**Continuidad de la sesión 2:** se completarán Clientes, Tiendas (dispersión), Gerencia (cascada), Productos (Treemap y árbol de descomposición), Factores comerciales (influenciadores) y Alertas comerciales (todos los meses, variaciones, detección de anomalías y contraste). La sesión 2 debe partir de este archivo y no del Taller 1.
