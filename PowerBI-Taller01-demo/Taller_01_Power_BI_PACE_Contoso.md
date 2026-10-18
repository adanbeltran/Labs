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

**Mapa visual de la fase:**

<img src="https://github.com/user-attachments/assets/64439d66-7728-4cd0-ad9a-338e2e690e49" width="900" alt="Captura: general">

<a id="paso-3-1"></a>
### 3.1. Configuración inicial

**Propósito.** Descargar los insumos, establecer una carpeta de trabajo y definir la interpretación regional antes de leer los CSV. En esta sección aún no se calculan indicadores.

<table width="100%">
  <thead>
    <tr>
      <th width="25%">Acción en Power BI</th>
      <th width="75%">Captura de pantalla</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-1-1"></a>
      <p><strong>3.1.1. Descargar y organizar los archivos</strong></p>
<p>Descargue el paquete de <a href="https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z">datos Contoso <code>csv-100k.7z</code></a> y extráigalo sin modificar los CSV.</p>
<div><p><strong>Acción 1.</strong> Descargar el paquete <code>.7z</code> y extraerlo en <code>Taller_01_PowerBI/datos</code>. Verificar que aparezcan <code>sales.csv</code> y <code>product.csv</code>. Conservar los nombres originales.</p></div>
<div><p><strong>Acción 2.</strong> Guardar el futuro <code>.pbix</code> en <code>Taller_01_PowerBI</code>. No editar los CSV en Excel ni cambiar sus separadores.</p></div>
<div><strong>Resultado o verificación</strong><p><strong>Resultado esperado:</strong> se localizan dos CSV originales en una carpeta conocida. Al cambiar de computador, las rutas absolutas de las fuentes pueden requerir actualización desde Power Query.</p></div>
    </td>
    <td width="75%" align="center" valign="top">
      <p><img width="1625" height="925" alt="image" src="https://github.com/user-attachments/assets/dbce2484-2d59-4108-821a-53b0fb7df823" /></p>
<p><img width="602" height="575" alt="image" src="https://github.com/user-attachments/assets/2be61f12-a0d6-4bcc-9526-7ea7f3058dc6" /></p>
<p><img width="455" height="445" alt="image" src="https://github.com/user-attachments/assets/213222d5-46c4-476a-b387-c54797280395" /></p> </td>
  </tr>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-1-2"></a>
      <p><strong>3.1.2. Configurar la interpretación regional</strong></p>
<p>La fuente original representa números decimales con punto. Defina la región de importación antes de transformarlos.</p>
<div><p><strong>Acción 1.</strong> Abrir Power BI Ir a <strong>Archivo → Opciones y configuración → Opciones → Archivo actual → Configuración regional</strong>. Seleccionar <strong>Inglés (Estados Unidos)</strong> para la importación y aceptar. </p></div>
    </td>
    <td width="75%" align="center" valign="top">
      <div align="center"><img src="https://github.com/user-attachments/assets/7b18bcd3-0ef3-4d16-8cba-162229f93835" width="700" alt="Captura: regional"></div>
    </td>
</tr>
  
  </tbody>
</table>

<a id="paso-3-2"></a>
### 3.2. Calidad de datos – Power Query

**Propósito.** Importar las dos fuentes (sales.csv y product.csv) controlar tipos, vacíos, duplicados y precisión antes de trabajar en el modelo. 

<table width="100%">
  <thead>
    <tr>
      <th width="25%">Acción en Power BI</th>
      <th width="75%">Captura de pantalla</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-2-1"></a>
      <p><strong>3.2.1. Importar sales.csv y product.csv</strong></p>
<div><p><strong>- </strong> <strong>Ir Inicio → Obtener datos → Texto/CSV</strong>. Localizar <code>sales.csv </code> → Cargar.</p></div>
<div><p><strong>- </strong> <strong>Ir Inicio → Obtener datos → Texto/CSV</strong>; seleccionar <code>product.csv</code> → Cargar.</p></div>
</td>
    <td width="75%" align="center" valign="top">
      <div align="center"><img src="https://github.com/user-attachments/assets/f000ec3e-725d-4090-80d2-c2ecec6bf46a" width="700" alt="Captura: get"></div>
<div align="center"><img src="https://github.com/user-attachments/assets/21beab77-cfd5-42dc-91e6-82d3455f2b73" width="700" alt="Captura: choose"></div>
<div align="center"><img src="https://github.com/user-attachments/assets/53a8bd73-2450-4af3-8f0d-e0491fa8d4f0" width="700" alt="Captura: load"></div>

<div align="center"><img src="https://github.com/user-attachments/assets/9bb4ba05-db4c-4cb9-b958-84230d7cef2c" width="700" alt="Captura: loaded"></div>
    </td>
  </tr>
 
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-2-2"></a>
      <p><strong>3.2.2. Examinar calidad y perfil de columnas</strong></p>
<p><b>Primera imagen:</b></p>
  <p>En Power BI Desktop, seleccionar <b>Inicio → Transformar datos</b> para abrir Power Query.</p>

  <p><b>Segunda imagen:</b></p>
  <p><b>1.</b> Seleccionar la pestaña <b>Vista</b>.</p>
  <p><b>2.</b> Seleccionar la consulta <b>sales</b>.</p>
  <p><b>3.</b> Activar <b>Calidad de columna</b> y <b>Perfil de columna</b>.</p>
  <p><b>4.</b> Cambiar el perfilado de las primeras 1.000 filas a <b>todo el conjunto de datos</b>.</p>
  <p><b>5.</b> Seleccionar <code>OrderKey</code>, <code>Quantity</code>, <code>NetPrice</code> y <code>LineNumber</code> para revisar sus estadísticas.</p>
  <p><b>6.</b> En <b>Estadísticas de columna</b>, comprobar los totales de <code>OrderKey</code>:</p>
  <p>
    Recuento: <b>199.873</b><br>
    Distintos: <b>83.130</b><br>
    Errores: <b>0</b><br>
    Vacíos: <b>0</b>
  </p>

  <p><b>Resultado esperado:</b> perfilado completo, sin errores ni valores vacíos en las columnas revisadas.</p>
    </td>
    <td width="75%" align="center" valign="top">
<img width="705" height="207" alt="image" src="https://github.com/user-attachments/assets/9c44345a-176d-47bf-81a1-2a564fb8ef58" />

<img width="682" height="732" alt="image" src="https://github.com/user-attachments/assets/adf16f94-8e1b-4cee-b8bb-f260e28c6c9a" />
  </td>
  </tr>
 
  <tr>

<td width="25%">
  <b>3.2.3. Crear ImporteVenta y verificar el total de ventas</b>

  <p><b>1.</b> Seleccionar la consulta <code>sales</code> y abrir <b>Agregar columna</b>.</p>

  <p><b>2.</b> Seleccionar <b>Columna personalizada</b>.</p>

  <p><b>3.</b> Escribir el nombre <code>ImporteVenta</code> y la fórmula:<br>
  <code>[Quantity] * [NetPrice]</code></p>

  <p><b>4.</b> Verificar la sintaxis y pulsar <b>Aceptar</b>.</p>

  <p><b>5.</b> Seleccionar la columna <code>ImporteVenta</code>.</p>

  <p><b>6.</b> Abrir la pestaña <b>Transformar</b>.</p>

  <p><b>7.</b> Cambiar <b>Tipo de datos → Número decimal</b>, conservando la precisión original.</p>

  <p><b>8.</b> Con la columna seleccionada, ir a <b>Transformar</b>.</p>

  <p><b>9.</b> Seleccionar <b>Estadísticas → Suma</b> para calcular el total de ventas.</p>

  <p><b>10.</b> Verificar que la suma sea aproximadamente <b>203.723.865,76</b>.</p>

  <p><b>11.</b> En <b>Pasos aplicados</b>, eliminar únicamente el paso de suma para recuperar la tabla.</p>

  <p><b>Resultado esperado:</b> total de ventas validado y tabla <code>sales</code> recuperada, conservando <code>ImporteVenta</code> como número decimal.</p>
</td>  
    </td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="726" height="412" alt="image" src="https://github.com/user-attachments/assets/5dbdfb2a-5acb-4624-84fc-bf8a4e37a10b" /></em></p>
      <p><em><img width="742" height="318" alt="image" src="https://github.com/user-attachments/assets/9d92b301-a84e-4fe2-bf93-ee3d10c655ba" /></em></p>
      <p><em><img width="737" height="235" alt="image" src="https://github.com/user-attachments/assets/cec8fce3-5892-4b8e-a3bf-8aca3425ce04" /></em></p>
      <p><em><img width="722" height="222" alt="image" src="https://github.com/user-attachments/assets/668ef583-8df3-4abf-864c-ffaa446f14da" /></em></p>
    </td>
  </tr>
  <tr>
   
<td width="25%">
  <b>3.2.4. Crear InicioMes y verificar el resultado</b>

  <p><b>1.</b> Seleccionar la consulta <code>sales</code> y abrir <b>Agregar columna</b>.</p>

  <p><b>2.</b> Seleccionar <b>Agregar Columna</b>.</p>
  <p><b>3.</b> Seleccionar <b>Columna personalizada</b>.</p>

  <p><b>4.</b> Escribir el nombre <code>InicioMes</code> y la fórmula:<br>
  <code>Date.StartOfMonth([OrderDate])</code></p>

  <p><b>5.</b> Verificar que no existan errores de sintaxis y pulsar <b>Aceptar</b>.</p>

  <p><b>6.</b> Seleccionar la columna <code>InicioMes</code>.</p>

  <p><b>7.</b> Abrir la pestaña <b>Transformar</b>.</p>

  <p><b>8.</b> En <b>Tipo de datos</b>, seleccionar <b>Fecha</b>.</p>

  <p><b>9.</b> Verificar que todas las fechas correspondan al <b>primer día del mes</b> de <code>OrderDate</code>. Por ejemplo, 26/02/2015 debe convertirse en 01/02/2015.</p>

  <p><b>10.</b> Seleccionar <b>Inicio → Cerrar y aplicar</b> para guardar las transformaciones y cargar los datos al modelo.</p>

  <p><b>Resultado esperado:</b> columna <code>InicioMes</code> de tipo <b>Fecha</b>, sin errores y con el primer día de cada mes. Las transformaciones quedan aplicadas al modelo.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="873" height="437" alt="image" src="https://github.com/user-attachments/assets/7964aba0-1d74-404a-b759-10743f0b364b" /></em></p>
      <p><em><img width="858" height="482" alt="image" src="https://github.com/user-attachments/assets/92c36a8d-cfa4-4c03-ac76-09812ea0fb43" /></em></p>
      <p><em><img width="848" height="297" alt="image" src="https://github.com/user-attachments/assets/5921980e-fbcd-49a7-bfce-54faf9b98811" />.</em></p>
      <p><em><img width="888" height="300" alt="image" src="https://github.com/user-attachments/assets/3494c052-377c-4408-a677-6dd99e6b6499" /></em></p>
    </td>
  </tr>
  </tbody>
</table>

<a id="paso-3-3"></a>
### 3.3. Modelo de datos

**Propósito.** Configurar la relación de hechos y dimensión y crear indicadores DAX. Aquí las medidas podrán responder dinámicamente a segmentaciones y filtros del informe.

<table width="100%">
  <thead>
    <tr>
      <th width="25%">Acción en Power BI</th>
      <th width="75%">Captura de pantalla</th>
    </tr>
  </thead>
  <tbody>
  <tr>
  <td width="25%">
  <b>3.3.1. Verificar relación por ProductKey</b>

  <p><b>1.</b> Seleccionar la <b>Vista Modelo</b> desde el menú lateral izquierdo de Power BI.</p>

  <p><b>2.</b> Identificar la línea que conecta <code>sales</code> con <code>product</code>. Hacer doble clic sobre ella para revisar la relación.</p>

  <p><b>3.</b> En <b>Editar relación</b>, comprobar:</p>
  <p>
    • <b>Tabla de origen:</b> <code>sales[ProductKey]</code>.<br>
    • <b>Tabla relacionada:</b> <code>product[ProductKey]</code>.<br>
    • <b>Cardinalidad:</b> Varios a uno (*:1).<br>
    • <b>Dirección de filtro cruzado:</b> Único.<br>
    • <b>Activar esta relación:</b> habilitada.
  </p>

  <p><b>Resultado esperado:</b> relación activa entre <code>product</code> (lado 1) y <code>sales</code> (lado *), permitiendo filtrar las ventas por categoría de producto.</p>
  
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="917" height="472" alt="image" src="https://github.com/user-attachments/assets/efe82077-8f15-4b25-a22e-2c0cef4b9303" />.</em></p>
    </td>
  </tr>
  
  <tr>
   
<td width="25%">
  <b>3.3.2. Crear y configurar las tres medidas DAX</b>

  <p><b>1.</b> En el panel <b>Datos</b>, seleccionar la tabla <code>sales</code>.</p>

  <p><b>2.</b> Abrir la <b>Vista Modelo</b> desde el menú lateral izquierdo.</p>

  <p><b>3.</b> Seleccionar <b>Inicio → Nueva medida</b>.</p>

  <p><b>4.</b> Crear la primera medida:</p>
  <p><code>Ventas Totales = SUM('sales'[ImporteVenta])</code></p>
  <p>Pulsar <b>✓</b> para confirmar.</p>

  <p><b>5.</b> Seleccionar nuevamente <b>Nueva medida</b>.</p>

  <p><b>6.</b> Crear la segunda medida:</p>
  <p><code>Unidades Totales = SUM('sales'[Quantity])</code></p>
  <p>Pulsar <b>✓</b>.</p>

  <p><b>7.</b> Seleccionar nuevamente <b>Nueva medida</b>.</p>

  <p><b>8.</b> Crear la tercera medida:</p>
  <p><code>Pedidos = DISTINCTCOUNT('sales'[OrderKey])</code></p>
  <p>Pulsar <b>✓</b>.</p>

  <p><b>9.</b> En el panel <b>Datos</b>, expandir <code>sales</code> y verificar que aparezcan las tres medidas con el icono de calculadora.</p>

  <p><b>10.</b> Seleccionar <b>Vista Informe</b> desde el menú lateral izquierdo.</p>

  <p><b>11.</b> En el panel <b>Datos</b>, seleccionar la medida <code>Ventas Totales</code>.</p>

  <p><b>12.</b> En <b>Herramientas de medición → Formato</b>, configurar las medidas:</p>
  <p>
    • <code>Ventas Totales</code>: número decimal, <b>2 decimales</b>.<br>
    • <code>Unidades Totales</code>: número entero, <b>0 decimales</b>.<br>
    • <code>Pedidos</code>: número entero, <b>0 decimales</b>.
  </p>

  <p><b>Resultado esperado:</b> tres medidas DAX creadas, disponibles en <code>sales</code> y con el formato correcto.</p>

  <p><b>Importante:</b> las medidas se recalculan según los filtros del informe. <code>DISTINCTCOUNT</code> cuenta pedidos diferentes, no líneas de venta.</p>
</td>
    <td width="75%" align="center" valign="top">
   <p><em> <img width="903" height="435" alt="image" src="https://github.com/user-attachments/assets/efb5de8a-adfc-43e1-b4da-f5f6a9622b88" /></em></p>

<p><em><img width="607" height="405" alt="image" src="https://github.com/user-attachments/assets/965647ea-2472-462b-bca0-dd6b205b641e" />.</em></p>
<p><em><img width="588" height="393" alt="image" src="https://github.com/user-attachments/assets/0e273741-f97f-4ff7-a7d6-97f9ed779cf3" />.</em></p>
      <p><em><img width="622" height="426" alt="image" src="https://github.com/user-attachments/assets/8bb29a6f-19c1-4be5-83aa-5444526cde37" /></em></p>
       <p><em><img width="850" height="470" alt="image" src="https://github.com/user-attachments/assets/9947652c-2694-4171-9c75-f8ae0bca35c1" /></em></p>
       <p><em><img width="895" height="451" alt="image" src="https://github.com/user-attachments/assets/c4f0fec5-02cc-411c-b150-3bcecabc0645" /></em></p>
    </td>
  </tr>

  </tbody>
</table>

<a id="paso-3-4"></a>
### 3.4. Visualizaciones

**Propósito.** Convertir los indicadores validados en cuatro respuestas visuales; luego comprobar interacciones y auditoría por pedido. La página principal se refiere exclusivamente a **2020** y todas las categorías, salvo cuando se realizan pruebas controladas.

<table width="100%">
  <thead>
    <tr>
      <th width="25%">Acción en Power BI</th>
      <th width="75%">Captura de pantalla</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    
<td width="25%"  valign="top">

  <b>3.4.1. Crear la página y definir el diseño</b>

  <p><b>1.</b> Abrir la <b>Vista Informe</b> de Power BI.</p>
  <p><b>2.</b> Renombrar la página como <code>Resumen Comercial</code>.</p>
  <p><b>3.</b> Seleccionar la pestaña <b>Diseño</b>.</p>
  <p><b>4.</b> Hacer clic en <b>Personalización del tema</b>.</p>

  <p><b>5.</b> En el panel <b>Visualizaciones</b>, seleccionar <b>Tema</b>.</p>


  
  <p><b>6.</b> Desplegar <b>Colores → Paleta de colores</b>.</p>

  <p><b>7.</b> Seleccionar el tema <b>Marea accesible</b>.</p>

  <p><b>8.</b> Abrir <b>Formato de página</b> en el panel Visualizaciones.</p>

  <p><b>9.</b> En <b>Configuración del lienzo</b>, seleccionar <b>16:9</b>.</p>

  <p><b>10.</b> Desplegar <b>Fondo del lienzo</b>.</p>

  <p><b>11.</b> Abrir el selector de <b>Color</b>.</p>

  <p><b>12.</b> Seleccionar <b>Más colores</b>.</p>

  <p><b>13.</b> Introducir el código hexadecimal <code>#F4F7FB</code> y establecer <b>Transparencia: 0 %</b>.</p>

  <p><b>14.</b> Seleccionar la pestaña <b>Insertar</b>.</p>

  <p><b>15.</b> Insertar un <b>Cuadro de texto</b> y escribir <code>Resumen comercial - Contoso 2020</code>.</p>

  <p><b>16.</b> Seleccionar el texto, elegir <b>Segoe UI</b> y establecer tamaño <b>24</b>.</p>

  <p><b>17.</b> Abrir el selector de <b>Color de fuente</b>.</p>

  <p><b>18.</b> Activar <b>Negrilla (B)</b>.</p>

  <p><b>19.</b> Aplicar <b>Alineación centrada</b>.</p>

  <p><b>20.</b> Seleccionar <b>Más colores</b> para personalizar el color del título.</p>

  <p><b>21.</b> Introducir el código hexadecimal <code>#172B4D</code>.</p>

  <p><b>Resultado esperado:</b> página <code>Resumen Comercial</code> en formato 16:9, tema <b>Marea accesible</b>, fondo gris claro y título azul oscuro, centrado, en negrilla y tamaño 24.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="902" height="492" alt="image" src="https://github.com/user-attachments/assets/316ea3eb-29b9-426d-bc97-def94756c371" /></em></p>
<p><em><img width="877" height="247" alt="image" src="https://github.com/user-attachments/assets/df3b9520-864f-40ed-a112-befef948e752" />.</em></p>
<p><em><img width="853" height="318" alt="image" src="https://github.com/user-attachments/assets/215c046f-c2a9-4204-bf90-0c4632de6d90" /></em></p>
      <p><em><img width="860" height="395" alt="image" src="https://github.com/user-attachments/assets/75cf119a-675f-431d-b4b6-5a1c8d21a7b1" />.</em></p>
        <p><em><img width="857" height="422" alt="image" src="https://github.com/user-attachments/assets/c0f9ea2e-ef78-481a-8361-5ce780a7508d" /></em></p>
<p><em><img width="492" height="527" alt="image" src="https://github.com/user-attachments/assets/4885db9a-7dcf-40dc-8f43-21d425cdd15e" /></em></p>
<p><em><img width="300" height="596" alt="image" src="https://github.com/user-attachments/assets/e1e837fb-c872-449e-a7c5-6e2cf1ce6e4c" />.</em></p>
      <p><em><img width="272" height="607" alt="image" src="https://github.com/user-attachments/assets/1445641e-6a7c-4add-a5dd-422dd36defd0" />.</em></p>
<p><em><img width="690" height="132" alt="image" src="https://github.com/user-attachments/assets/cb669169-608d-4e42-a34f-d630aa252946" /></em></p>
<p><em><img width="837" height="192" alt="image" src="https://github.com/user-attachments/assets/e8099ad5-2129-4d7f-8696-a9305e01d1a6" /></em></p>
<p><em><img width="827" height="492" alt="image" src="https://github.com/user-attachments/assets/245d9a51-83a4-4d86-bfbf-8c1d3358514d" /></em></p>
<p><em><img width="422" height="472" alt="image" src="https://github.com/user-attachments/assets/7e6c821e-674f-47c7-8802-e8bf17e037dc" /></em></p>
<p><em><img width="1317" height="780" alt="image" src="https://github.com/user-attachments/assets/73be89d8-c504-4113-b431-58cc60d6eab9" /></em></p>


      
    </td>
  </tr>
  <tr>
    
<td width="25%" valign="top">
  <b>3.4.2. Agregar segmentaciones de fecha y categoría</b>

  <p><b>1.</b> Seleccionar la segmentación de fechas <code>OrderDate</code>.</p>

  <p><b>2.</b> En <b>Compilar visual → Valores</b>, abrir el menú desplegable del campo <code>OrderDate</code>.</p>

  <p>Seleccionar la <b>fecha individual</b>, no la jerarquía de fechas.</p>

  <p><b>3.</b> En <b>Formato visual → Configuración de la segmentación → Opciones</b>, seleccionar el estilo <b>Entre</b>.</p>

  <p><b>4.</b> Establecer el intervalo:</p>
  <p>
    Desde: <b>01/01/2020</b><br>
    Hasta: <b>31/12/2020</b>
  </p>

  <p><b>5.</b> Insertar otra <b>Segmentación de datos</b> desde el panel Visualizaciones.</p>

  <p><b>6.</b> En <b>Valores</b>, agregar el campo <code>product[CategoryName]</code>.</p>

  <p><b>7.</b> En <b>Formato visual → Configuración de la segmentación</b>:</p>
  <p>
    • <b>Estilo:</b> Menu Desplegable.<br>
    • <b>Selección única:</b> Desactivada.<br>
    • <b>Selección múltiple:</b> Activada, sin exigir Ctrl, si está disponible.<br>
    • <b>Mostrar opción "Seleccionar todo":</b> Activada.
  </p>

  <p>Quitar cualquier selección individual para incluir todas las categorías.</p>

  <p><b>Resultado esperado:</b> dos segmentaciones en la parte superior del informe: una para el intervalo de fechas de 2020 y otra para seleccionar una o varias categorías.</p>

  <p><b>Importante:</b> ambas segmentaciones deben afectar únicamente la página <code>Resumen Comercial</code>. No aplicar el filtro de 2020 a todo el informe, porque la página <code>Comprobacion</code> utilizará registros de 2015.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="833" height="341" alt="image" src="https://github.com/user-attachments/assets/091485b9-b25c-43ac-89da-d4a084c6f0f1" /></em></p>
      <p><em><img width="870" height="387" alt="image" src="https://github.com/user-attachments/assets/0db93eab-c8e1-481a-9870-b6bb8b57d4b6" />.</em></p>
     <p><em><img width="856" height="495" alt="image" src="https://github.com/user-attachments/assets/b18d49ea-1983-4d67-9e8c-b0413d0adf24" />.</em></p>
     <p><em><img width="673" height="592" alt="image" src="https://github.com/user-attachments/assets/b5c8d763-9a4d-4b03-ae75-7bebf34e8266" />.</em></p>
  

    </td>
  </tr>
  <tr>
    
<td width="25%" valign="top">
  <b>3.4.3. Q1: Ventas, unidades y pedidos con tarjetas</b>

  <p>Se utilizarán las tres medidas DAX creadas en la fase 3.3.</p>

  <p><b>1.</b> En <b>Visualizaciones</b>, seleccionar el visual <b>Tarjeta</b>.</p>

  <p><b>2.</b> En el panel <b>Datos → sales</b>, arrastrar la medida <code>Ventas Totales</code> al campo <b>Valores</b> de la tarjeta.</p>

  <p><b>3.</b> Seleccionar la tarjeta y abrir <b>Formato visual</b> (icono de brocha).</p>

  <p><b>4.</b> Seleccionar la pestaña <b>Objeto visual</b>.</p>

  <p><b>5.</b> En <b>Llamada → Valor</b>, configurar el formato del número.</p>

  <p><b>6.</b> Establecer <b>Mostrar unidades: Ninguno</b> y <b>2 posiciones decimales</b>. Esto evita abreviaturas como K o M.</p>

  <p><b>7.</b> En <b>General → Efectos → Fondo</b>, activar el fondo de la tarjeta.</p>

  <p><b>8.</b> Seleccionar el color <code>#FFFFFF</code> y establecer <b>Transparencia: 0 %</b>.</p>

  <p><b>9.</b> Repetir el procedimiento para crear las otras dos tarjetas, utilizando las medidas:</p>

  <p>
    • <code>Unidades Totales</code>: 0 decimales.<br>
    • <code>Pedidos</code>: 0 decimales.
  </p>

  <p>Configurar los nombres visibles de los indicadores como <b>Ventas Totales</b>, <b>Unidades Totales</b> y <b>Pedidos distintos</b>.</p>

  <p><b>10. Verificar los resultados:</b> con el filtro de fechas entre <b>01/01/2020</b> y <b>31/12/2020</b> y todas las categorías incluidas, comprobar:</p>

  <p>
    • <b>Ventas:</b> 11.066.206,89.<br>
    • <b>Unidades:</b> 35.234.<br>
    • <b>Pedidos:</b> 4.806.
  </p>

  <p><b>11. Organizar el tablero:</b></p>

  <p>• Ubicar el título centrado en la parte superior.</p>

  <p>• Colocar las segmentaciones de <b>Período de Análisis</b> y <b>Categoría</b> en una misma fila, debajo del título.</p>

  <p>• Configurar la segmentación de categorías como <b>Desplegable</b> para ahorrar espacio.</p>

  <p>• Situar las tres tarjetas en una fila horizontal, debajo de los filtros, en este orden: <b>Ventas Totales → Unidades Totales → Pedidos</b>.</p>

  <p>• Asignar a las tres tarjetas el mismo ancho y alto, dejando espacios uniformes.</p>

  <p>• Utilizar <b>Formato → Alinear</b> y <b>Distribuir horizontalmente</b>, si están disponibles, para mantener una presentación ordenada.</p>

  <p>• Reservar la zona inferior para los gráficos de barras, evolución mensual y tabla comparativa.</p>

  <p><b>Resultado esperado:</b> tres tarjetas blancas, alineadas horizontalmente, con cifras completas y filtros organizados en la parte superior del informe.</p>

  <p><b>Control de calidad:</b> si las ventas muestran <b>11.066.206,90</b> en lugar de <b>11.066.206,89</b>, revisar la precisión de <code>ImporteVenta</code> y el cálculo antes de continuar. No modificar manualmente los resultados.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="646" height="702" alt="image" src="https://github.com/user-attachments/assets/f733c79b-d707-4807-a39a-933ab1c81d83" />.</em></p>
      <p><em><img width="830" height="430" alt="image" src="https://github.com/user-attachments/assets/fac1e5c7-4ad3-4427-a201-4375a5ea8315" />.</em></p>
    <p><em><img width="815" height="487" alt="image" src="https://github.com/user-attachments/assets/0d0402a1-526f-4a2c-aeb5-0b2f6d662215" />.</em></p>
    </td>
  </tr>
  <tr>
    
<td width="25%" valign="top">
  <b>3.4.4. Q2: Ventas por categoría</b>

  <p><b>Objetivo:</b> identificar la categoría con mayores ventas en 2020 mediante un gráfico de barras horizontales.</p>

  <p><b>1.</b> En <b>Visualizaciones</b>, seleccionar <b>Gráfico de barras agrupadas</b>.</p>

  <p><b>2.</b> Asignar los campos:</p>
  <p>
    • <b>Eje Y:</b> <code>product[CategoryName]</code>.<br>
    • <b>Eje X:</b> <code>Ventas Totales</code>.<br>
    • <b>Leyenda:</b> dejar vacía.
  </p>

  <p>En el menú <b>...</b> del gráfico, seleccionar <b>Ordenar por → Ventas Totales → Descendente</b>.</p>

  <p><b>3.</b> Seleccionar el gráfico y abrir <b>Formato visual</b>.</p>

  <p><b>4.</b> Seleccionar la pestaña <b>General</b>.</p>

  <p><b>5.</b> En <b>Título → Texto</b>, escribir <b>Ventas por Categoría</b>.</p>

  <p><b>6.</b> Establecer la <b>alineación centrada</b> del título.</p>

  <p><b>7.</b> Desactivar <b>Subtítulo</b>.</p>

  <p><b>8.</b> Cambiar a la pestaña <b>Objeto visual</b>.</p>

  <p><b>9.</b> Desplegar <b>Barras</b> para configurar los colores.</p>

  <p><b>10.</b> En <b>Aplicar configuración a → Categorías</b>, seleccionar <b>Computers</b>.</p>

  <p><b>11.</b> En <b>Color</b>, elegir un verde del tema <b>Marea accesible</b> para destacar Computers. Mantener las demás categorías en azul.</p>

  <p><b>12.</b> Activar <b>Etiquetas de datos → Valor</b> y configurar <b>Mostrar unidades: Ninguno</b> para presentar los importes completos.</p>

  <p><b>Resultado esperado:</b> gráfico de barras horizontales, ordenado de mayor a menor, con Computers destacada y los valores de ventas visibles.</p>

  <p><b>Totales de control:</b></p>
  <p>
    • <b>Computers:</b> 5.041.111,59.<br>
    • <b>Cell phones:</b> 1.861.564,14.
  </p>

  <p><b>Verificación:</b> Computers debe ocupar la primera posición y presentar la barra más larga. Las demás categorías deben mostrar barras proporcionales a sus ventas.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="755" height="737" alt="image" src="https://github.com/user-attachments/assets/714ea952-1ce9-494a-abd1-add2180462b4" /></p>
<p><em><img width="520" height="302" alt="image" src="https://github.com/user-attachments/assets/3b55d620-c4af-4755-843a-960cd6ae36be" />o.</em></p>
    </td>
  </tr>
  <tr>
   
<td width="25%" valign="top">
  <b>3.4.5. Q3: Comparar ventas, unidades y pedidos por categoría</b>

  <p><b>Objetivo:</b> comparar los indicadores comerciales para identificar si la categoría con mayores ventas también lidera en unidades vendidas.</p>

  <p><b>1.</b> En <b>Visualizaciones</b>, seleccionar el objeto visual <b>Tabla</b>.</p>

  <p><b>2.</b> En <b>Compilar visual → Columnas</b>, agregar los campos en este orden:</p>
  <p>
    • <code>product[CategoryName]</code>.<br>
    • <code>Ventas Totales</code>.<br>
    • <code>Unidades Totales</code>.<br>
    • <code>Pedidos</code>.
  </p>

  <p><b>3.</b> Hacer clic en el encabezado <b>Ventas Totales</b> hasta ordenar los importes de <b>mayor a menor</b>. Verificar que Computers aparezca primero.</p>

  <p><b>4.</b> Hacer clic en <b>Unidades Totales</b> para ordenar temporalmente las categorías por cantidad de unidades vendidas, de mayor a menor.</p>

  <p>Comparar ambos ordenamientos y determinar si la categoría líder cambia.</p>

  <p>Finalmente, volver a ordenar por <b>Ventas Totales → Descendente</b>.</p>

  <p><b>Resultado esperado:</b> tabla ordenada de mayor a menor venta, con los tres indicadores y la fila de totales.</p>

  <p><b>Totales de control de 2020:</b></p>
  <p>
    • <b>Ventas:</b> 11.066.206,89.<br>
    • <b>Unidades:</b> 35.234.<br>
    • <b>Pedidos distintos:</b> 4.806.
  </p>

  <p><b>Análisis:</b> Computers lidera tanto en ventas como en unidades vendidas. La cantidad de pedidos por categoría no debe sumarse para obtener el total general, porque un pedido puede incluir productos de varias categorías.</p>

  <p><b>Verificación:</b> comprobar que la tabla responda a las segmentaciones de fecha y categoría.</p>
</td>
    <td width="75%" align="center" valign="top">
      <p><em><img width="726" height="753" alt="image" src="https://github.com/user-attachments/assets/ec18deea-3fd1-464d-9754-062d4b6794af" />.</em></p>
    </td>
  </tr>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-4-6"></a>
      <p><strong>3.4.6. Q4: línea de ventas mensuales</strong></p>
<p>El eje debe seguir la fecha real de inicio de mes.</p>
<div><p><strong>Acción 1.</strong> Insertar <strong>Gráfico de líneas</strong>. Eje X: <code>sales[InicioMes]</code> como <strong>Fecha</strong> individual, no jerarquía. Eje Y: <code>Ventas Totales</code>.</p></div>
<div><p><strong>Acción 2.</strong> Ordenar cronológicamente, activar marcadores, aplicar color <code>#1F5A85</code> y título <code>Evolución mensual de ventas - 2020</code>.</p></div>
<div><p><strong>Acción 3.</strong> Agregar anotación visual <code>Marzo: −58,46 % frente a febrero</code> y contrastar meses de máximo y mínimo.</p></div>
<div><strong>Resultado o verificación</strong><p><strong>Verificación:</strong> febrero <strong>2.658.349,04</strong> (máximo); marzo <strong>1.104.361,28</strong>; septiembre <strong>320.093,34</strong> (mínimo). Deben mostrarse 12 meses ordenados.</p></div>
    </td>
    <td width="75%" align="center" valign="top">
      <p><em>Captura 3.4.6a pendiente. Pegar aquí la imagen de este paso.</em></p>
<p><em>Captura 3.4.6b pendiente. Pegar aquí la imagen de este paso.</em></p>
<p><em>Captura 3.4.6c pendiente. Pegar aquí la imagen de este paso.</em></p>
    </td>
  </tr>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-4-7"></a>
      <p><strong>3.4.7. Probar interacciones entre filtros y visuales</strong></p>
<p>Compruebe el filtrado antes de utilizar el tablero en la fase 4.</p>
<div><p><strong>Acción 1.</strong> Con 2020 activo, seleccionar únicamente <code>Computers</code> en la segmentación. Verificar ventas <strong>5.041.111,59</strong>, unidades <strong>8.364</strong>, pedidos <strong>2.094</strong>.</p></div>
<div><p><strong>Acción 2.</strong> Si algún visual no responde, seleccionar la segmentación → <strong>Formato → Editar interacciones</strong> → <strong>Filtrar</strong>. Limpiar la selección para regresar a los totales anuales.</p></div>
<div><strong>Resultado o verificación</strong><p><strong>Verificación:</strong> todas las categorías restauradas: 11.066.206,89; 35.234; 4.806.</p></div>
    </td>
    <td width="75%" align="center" valign="top">
      <p><em>Captura 3.4.7a pendiente. Pegar aquí la imagen de este paso.</em></p>
<p><em>Captura 3.4.7b pendiente. Pegar aquí la imagen de este paso.</em></p>
    </td>
  </tr>
  <tr>
    <td width="25%" valign="top">
      <a id="paso-3-4-8"></a>
      <p><strong>3.4.8. Crear una página de comprobación con dos pedidos</strong></p>
<p>Esta página verifica el resultado del modelo a nivel de línea de pedido y no lleva filtro global de año.</p>
<div><p><strong>Acción 1.</strong> Añadir página <code>Comprobacion</code>. Insertar <strong>Tabla</strong> con <code>OrderKey</code>, <code>LineNumber</code>, <code>OrderDate</code>, <code>CategoryName</code>, <code>Quantity</code>, <code>NetPrice</code>, <code>ImporteVenta</code>. En campos de detalle usar <strong>No resumir</strong> cuando aplique.</p></div>
<div><p><strong>Acción 2.</strong> Insertar segmentación de <code>OrderKey</code>; seleccionar simultáneamente <strong>1000</strong> y <strong>1001</strong>. Añadir tarjetas con las tres medidas originales.</p></div>
<div><p><strong>Acción 3.</strong> Verificar 3 líneas: pedido 1000 (Audio 1 × 98,967; Computers 1 × 659,780); pedido 1001 (Games and Toys 2 × 54,376).</p></div>
<div><strong>Resultado o verificación</strong><p><strong>Control manual:</strong> 98,967 + 659,780 + 108,752 = <strong>867,499</strong> (visualizado como <strong>867,50</strong>); <strong>4 unidades</strong> y <strong>2 pedidos distintos</strong>.</p>
<p><strong>Cierre de la fase 3.</strong> Antes de pasar a la fase 4, comprobar la relación activa, las tres medidas, las dos páginas, los filtros y la página de auditoría. Se conserva una cifra completa de control sin forzar redondeos.</p>
<p><a href="#indice">Volver al índice</a></p></div>
    </td>
    <td width="75%" align="center" valign="top">
      <p><em>Captura 3.4.8a pendiente. Pegar aquí la imagen de este paso.</em></p>
<p><em>Captura 3.4.8b pendiente. Pegar aquí la imagen de este paso.</em></p>
<p><em>Captura 3.4.8c pendiente. Pegar aquí la imagen de este paso.</em></p>
    </td>
  </tr>
  </tbody>
</table>

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
