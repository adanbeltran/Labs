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
## Índice con navegación por fases y pasos

| Paso | Tema | Modalidad |
| --- | --- | --- |
| [Fase 1. Planear](#fase-1) | Necesidad y preguntas | Comprensión |
| [1.1](#paso-1-1) | Contexto y alcance | Comprensión |
| [1.2](#paso-1-2) | Preguntas, campos y visuales | Comprensión |
| [Fase 2. Analizar](#fase-2) | Significado y calidad de los datos | Comprensión |
| [2.1](#paso-2-1) | Datos y granularidad | Comprensión |
| [2.2](#paso-2-2) | Relaciones entre tablas | Comprensión |
| [2.3](#paso-2-3) | Precisión, separadores y medidas | Comprensión |
| [2.4](#paso-2-4) | Totales de control | Comprensión |
| [Fase 3. Construir](#fase-3) | Trabajo guiado en Power BI | Práctica |
| [3.1](#paso-3-1) | Obtener y extraer los archivos | Preparación |
| [3.2](#paso-3-2) | Configurar Power BI | Práctica |
| [3.3](#paso-3-3) | Importar y seleccionar columnas | Práctica |
| [3.4](#paso-3-4) | Asignar tipos y respetar precisión | Práctica |
| [3.5](#paso-3-5) | Crear columnas de Power Query | Práctica |
| [3.6](#paso-3-6) | Validar calidad de origen | Práctica |
| [3.7](#paso-3-7) | Relacionar tablas | Práctica |
| [3.8](#paso-3-8) | Crear medidas DAX | Práctica |
| [3.9](#paso-3-9) | Crear página, diseño y filtros | Práctica |
| [3.10](#paso-3-10) | Q1: tres tarjetas | Práctica |
| [3.11](#paso-3-11) | Q2: barras por categoría | Práctica |
| [3.12](#paso-3-12) | Q3: tabla comparativa | Práctica |
| [3.13](#paso-3-13) | Q4: línea mensual | Práctica |
| [3.14](#paso-3-14) | Probar interacciones | Práctica |
| [3.15](#paso-3-15) | Página de comprobación de pedidos | Práctica |
| [Fase 4. Ejecutar y explicar](#fase-4) | Presentación y cierre | Práctica + interpretación |
| [4.1](#paso-4-1) | Restaurar filtros y comprobar | Práctica |
| [4.2](#paso-4-2) | Presentar con storytelling | Práctica + interpretación |
| [4.3](#paso-4-3) | Interpretar y proponer acción | Interpretación |
| [4.4](#paso-4-4) | Guardar entrega | Práctica |
| [Dificultades frecuentes](#dificultades) | Solución de errores | Consulta |
| [Fuentes](#fuentes) | Referencias oficiales | Consulta |

## Recorrido de las cuatro fases

La **[fase 1. Planear](#fase-1)** establece el problema comercial, las preguntas que importan y los resultados esperados, sin ejecutar acciones técnicas. Define el sentido del trabajo antes de examinar herramientas.

La **[fase 2. Analizar](#fase-2)** estudia las tablas y su nivel de detalle, qué hace cada campo, qué cálculos son legítimos y cómo reconocer errores de interpretación. Todo sigue siendo conceptual; conocer estos riesgos será indispensable al operar Power BI.

La **[fase 3. Construir](#fase-3)** implementa el análisis en Power BI Desktop. Incluye obtención del archivo, transformación en Power Query, creación de la relación, medidas DAX, cuatro visualizaciones y pruebas técnicas. Cada subpaso especifica la ruta de menú y el resultado esperado.

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
## Fase 3. Construir

**PRÁCTICA GUIADA EN POWER BI DESKTOP.** Esta fase convierte el razonamiento anterior en un informe verificable. Siga los pasos en secuencia. Cada apartado indica qué seleccionar, qué escribir y qué resultado comprobar antes de avanzar. Algunos rótulos de la interfaz pueden variar ligeramente entre versiones.

<a id="paso-3-1"></a>
### 3.1. Obtener los archivos

1. Descargar [csv-100k.7z](https://raw.githubusercontent.com/adanbeltran/Labs/main/PowerBI-Taller01-demo/csv-100k.7z).
2. Extraer su contenido con una herramienta compatible con `.7z` en una carpeta de trabajo, por ejemplo `Taller_01_PowerBI/Contoso`.
3. Identificar `sales.csv` y `product.csv` y mantenerlos en una ubicación estable.
4. No abrir y guardar los CSV con una aplicación que pueda cambiar automáticamente precios o fechas.

**Resultado esperado:** dos archivos CSV listos para importar. **Verificación:** los nombres coinciden exactamente.

<a id="paso-3-2"></a>
### 3.2. Configurar la interpretación regional

1. Abrir **Power BI Desktop** y crear un informe nuevo.
2. Ir a **Archivo → Opciones y configuración → Opciones → Archivo actual → Configuración regional**.
3. Elegir **Inglés (Estados Unidos)** como configuración regional de importación. Confirmar con **Aceptar**.
4. Si está disponible una opción independiente para la región de presentación del informe, usar **Español (Colombia)**; esto cambia la visualización, no corrige datos mal importados.

**Resultado esperado:** los números de origen con punto decimal podrán convertirse correctamente. **Verificación:** todavía no se han importado datos.

<img width="1585" height="1006" alt="image" src="https://github.com/user-attachments/assets/7b18bcd3-0ef3-4d16-8cba-162229f93835" />


<a id="paso-3-3"></a>
### 3.3. Importar los archivos Sales.csv y products.csv

| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1. Seleccionar Obtener datos**. Ir a Inicio → Obtener datos → Texto/CSV. | <img width="1383" height="921" alt="image" src="https://github.com/user-attachments/assets/f000ec3e-725d-4090-80d2-c2ecec6bf46a" /> |
| **2. Seleccionar archivo**. Buscar y seleccionar `sales.csv`. | <img width="470" height="321" alt="image" src="https://github.com/user-attachments/assets/21beab77-cfd5-42dc-91e6-82d3455f2b73" /> |
| **3. Cargar datos**. Seleccionar el botón **Cargar**. |<img width="1112" height="826" alt="image" src="https://github.com/user-attachments/assets/53a8bd73-2450-4af3-8f0d-e0491fa8d4f0" /> |

**Repetir el proceso con el archivo de products.csv**

**Resultado esperado:** los dos archivos cargados**
<img width="1806" height="813" alt="image" src="https://github.com/user-attachments/assets/9bb4ba05-db4c-4cb9-b958-84230d7cef2c" />



<a id="paso-3-4"></a>
### 3.4. Crear columnas calculadas en Power Query
| Acción en Power BI | Captura de pantalla |
|:---|:---:|
| **1. Seleccionar la vista de tabla y seleccionar `sales.csv` | <img width="1890" height="771" alt="image" src="https://github.com/user-attachments/assets/bf020ae3-e5d9-4770-8e6e-60704111f842" /> |
| **2. Seleccionar Nueva Columna** | <img width="1837" height="416" alt="image" src="https://github.com/user-attachments/assets/69f07f68-df39-4f17-93be-bcabcedb2f8a" /> |
| **3. escribir ```ImporteVenta = [Quantity] * [NetPrice]```  Pulsar **Confirmar* |<img width="1447" height="360" alt="image" src="https://github.com/user-attachments/assets/1a5da758-859f-4b46-b624-11a4d3611a5c" /> |
| **4. Agregar una nueva Columna ```InicioMes = EOMONTH('sales'[OrderDate], -1) + 1```  Pulsar **Confirmar* |<img width="1585" height="258" alt="image" src="https://github.com/user-attachments/assets/1907837c-e104-4ca5-b9b2-66d0d040edd8" />|

**Resultado esperado:** `sales` tiene ocho columnas; `product`, tres. **Verificación:** no hay errores en las columnas nuevas. 

<a id="paso-3-6"></a>
### 3.6. Revisar calidad y cargar los datos

1. En Power Query ir a **Ver → Calidad de columna** y **Perfil de columna**.
2. En la barra inferior del Editor, cambiar el perfilado de las primeras 1.000 filas a **todo el conjunto de datos**, si está disponible.
3. Comprobar errores, vacíos y tipos en los campos del modelo.
4. Para contar filas sin alterar `sales`, hacer **clic derecho en sales → Referencia** y renombrar la copia `control_filas_sales`.
5. Sobre esa consulta auxiliar utilizar **Transformar → Contar filas**. Esperar **199.873**.
6. Repetir con una referencia de `product`: esperar **2.517**.
7. Para duplicados de claves de productos, crear otra **Referencia** de `product`, seleccionar `ProductKey` y usar **Inicio → Agrupar por**; agregar **Recuento de filas** y filtrar conteos mayores que 1. El resultado esperado es **cero claves duplicadas**.
8. En las consultas auxiliares, clic derecho y **desmarcar Habilitar carga**. Dejar la carga habilitada en `sales` y `product`.
9. Pulsar **Inicio → Cerrar y aplicar**.

**Resultado esperado:** solo las dos tablas de negocio aparecen en el modelo. **Verificación:** 199.873 líneas de ventas; 2.517 productos; ningún error visible de transformación. Otras comprobaciones específicas (duplicados de pedido-línea, claves huérfanas) pueden hacerse con consultas auxiliares agrupadas, sin modificar las originales.

<a id="paso-3-7"></a>
### 3.7. Conservar y verificar la relación automática

1. En Power BI Desktop, abrir **Vista Modelo** mediante el icono lateral.
2. Localizar las tablas `product` y `sales`.
3. Si existe una relación automática entre `ProductKey`, hacer doble clic en la línea para revisar sus propiedades.
4. Confirmar **cardinalidad uno a varios (1:*)**: `product` en el lado **1**, `sales` en el lado **\***; **relación activa** y **dirección del filtro cruzado: Única** desde productos hacia ventas.
5. Si no existe, abrir **Modelado → Administrar relaciones → Nueva relación**, seleccionar ambos `ProductKey`, fijar las propiedades anteriores y guardar.
6. No aceptar una relación muchos a muchos como solución rápida. Revisar primero unicidad de `product[ProductKey]` y tipos de ambas claves.

**Resultado esperado:** una relación activa correctamente configurada. **Por qué no combinar consultas:** el catálogo describe productos y las ventas son hechos; la relación permite filtrar ventas por categoría sin expandir el catálogo dentro de cada línea.

<a id="paso-3-8"></a>
### 3.8. Crear las tres medidas DAX

1. Volver a la **Vista Informe**.
2. En el panel **Datos**, seleccionar la tabla `sales`.
3. Abrir **Modelado → Nueva medida**; pegar la primera fórmula y presionar **Entrar**.
4. Repetir para la segunda y tercera fórmula; cada medida debe aparecer con icono de calculadora.

```dax
Ventas Totales = SUM('sales'[ImporteVenta])
```

```dax
Unidades Totales = SUM('sales'[Quantity])
```

```dax
Pedidos = DISTINCTCOUNT('sales'[OrderKey])
```

5. Seleccionar `Ventas Totales` y, en **Herramientas de medida**, asignar formato numérico con **dos decimales** y separador de miles. Seleccionar las otras medidas y asignar **cero decimales**.
6. Insertar temporalmente un visual **Tabla** en el informe; arrastrar las tres medidas a **Valores** para comprobar los resultados **sin filtros**.
7. Deben aparecer **203.723.865,76** en ventas, **628.370** unidades y **83.130** pedidos. Retirar el visual temporal después de comprobar.

**Resultado esperado:** tres medidas reutilizables. **Verificación:** si `Pedidos` devuelve 199.873, se están contando filas en lugar de pedidos distintos.

<a id="paso-3-9"></a>
### 3.9. Crear la página de reporte, su diseño y sus filtros

1. Renombrar la pestaña de la primera página (doble clic) a `Resumen comercial`.
2. Seleccionar espacio vacío del lienzo; en **Vista → Temas → Personalizar tema actual**, si su versión lo permite, establecer colores consistentes.
3. Aplicar: fondo `#F4F7FB`; tarjetas y visuales blancos `#FFFFFF`; títulos `#172B4D`; línea mensual `#1F5A85`; categoría destacada `#007F7A`; categorías secundarias `#5F6F84`. Usar fuente legible como Segoe UI.
4. Configurar el lienzo a **16:9** cuando sea adecuado para presentación; insertar título con **Insertar → Cuadro de texto**: `Resumen comercial | Contoso 2020`.
5. En **Visualizaciones**, elegir **Segmentación de datos**. Arrastrar `sales[OrderDate]` a su campo. Si aparece una jerarquía, desplegar el campo y escoger **OrderDate** como fecha individual.
6. Cambiar el estilo del filtro de fecha a **Entre**.
7. Crear otra **Segmentación de datos** para `product[CategoryName]`, con estilo lista o desplegable. Conservar todas las categorías.
8. **Dejar el historial sin filtro inicialmente** para probar las tarjetas del paso siguiente; solo después fijar **01/01/2020** hasta **31/12/2020**.
9. Organizar: encabezado y filtros arriba; tarjetas en franja superior; barras a la izquierda, línea a la derecha y tabla comparativa abajo.

**Resultado esperado:** informe con una página y dos segmentaciones. **Verificación:** el filtro de fecha no debe aplicarse al nivel **Informe**, porque la segunda página necesitará pedidos de 2015.

<a id="paso-3-10"></a>
### 3.10. Responder Q1 con tres tarjetas

1. Hacer clic en espacio vacío; en **Visualizaciones**, elegir **Tarjeta**.
2. Arrastrar la medida `Ventas Totales` a **Datos/Valores**.
3. En **Formato visual** (icono de brocha), habilitar **Título** e introducir `Ventas en moneda base`.
4. En **Valor destacado / Valores**, seleccionar **Unidades de visualización: Ninguno** y **Decimales: 2**. La ubicación del ajuste puede variar según la versión de la tarjeta.
5. Copiar y pegar esta tarjeta dos veces, usando **Ctrl+C** y **Ctrl+V**.
6. En la segunda, sustituir la medida por `Unidades Totales`, cambiar el título a `Unidades` y establecer cero decimales. En la tercera, elegir `Pedidos`, título `Pedidos distintos` y cero decimales.
7. Alinear las tres tarjetas. **Sin fecha filtrada** confirmar los totales históricos del inicio.
8. En el segmentador de fechas, aplicar **01/01/2020–31/12/2020**.

| Tarjeta | Resultado correcto de 2020 |
| --- | ---: |
| Ventas en moneda base | 11.066.206,89 |
| Unidades | 35.234 |
| Pedidos distintos | 4.806 |

**Verificación:** no deben aparecer abreviaturas K o M ni errores por separador decimal.

<a id="paso-3-11"></a>
### 3.11. Responder Q2 con barras horizontales

1. Elegir **Gráfico de barras agrupadas** en **Visualizaciones**.
2. Arrastrar `product[CategoryName]` a **Eje Y** y la medida `Ventas Totales` a **Eje X**.
3. Abrir el menú de los tres puntos **… → Ordenar por → Ventas Totales → Descendente**.
4. En **Formato visual**, activar **Etiquetas de datos**, habilitar **Título** y escribir `Ventas por categoría | 2020`.
5. Usar colores discretos y, si la versión ofrece formato por categoría, destacar Computers en verde azulado `#007F7A`.
6. Verificar que el filtro de fecha sea 2020 y que no haya una categoría aislada en la segmentación.

**Resultado esperado:** **Computers** encabeza el ranking con **5.041.111,59**; la segunda es **Cell phones**, con **1.861.564,14**. **Verificación:** si todas las categorías muestran el mismo valor, revisar la relación de `ProductKey`.

<a id="paso-3-12"></a>
### 3.12. Responder Q3 con una tabla comparativa

1. Seleccionar el visual **Tabla**.
2. Arrastrar a **Columnas/Valores**, en orden: `product[CategoryName]`, `Ventas Totales`, `Unidades Totales`, `Pedidos`.
3. Hacer clic sobre `Ventas Totales` hasta ordenar de mayor a menor.
4. En **Formato visual**, activar totales, ajustar anchos de columna y mostrar todos los números.
5. Comparar las filas con la tabla resuelta de abajo; ordenar temporalmente por `Unidades Totales` para identificar si cambia el líder y luego volver a ordenar por ventas.

| Categoría | Ventas Totales | Unidades Totales | Pedidos |
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

**Resultado esperado:** Computers lidera en ventas y en unidades. **Verificación:** el total de `Pedidos` **no** equivale a sumar pedidos por categoría, porque un pedido puede contener categorías diferentes. Las ventas mostradas por categoría pueden diferir en centavos de la suma de números ya redondeados: conservar precisión original.

<a id="paso-3-13"></a>
### 3.13. Responder Q4 con una línea de evolución mensual

1. Elegir **Gráfico de líneas**.
2. Arrastrar `sales[InicioMes]` a **Eje X** y escoger la fecha individual, **no Jerarquía de fechas**.
3. Arrastrar `Ventas Totales` a **Eje Y**.
4. En **Formato visual → Eje X**, configurar un eje cronológico; usar **Continuo** cuando esté disponible y sea legible. Comprobar que los meses estén ordenados desde enero hasta diciembre.
5. Activar marcadores y un título `Evolución mensual de ventas | 2020`; aplicar color `#1F5A85`.
6. Usar **Insertar → Cuadro de texto** para añadir, cerca del gráfico y sin taparlo, `Marzo: −58,46 % frente a febrero`.
7. Verificar los doce valores mensuales:

| Mes | Ventas Totales |
| --- | ---: |
| Enero | 2.124.215,68 |
| Febrero | 2.658.349,04 |
| Marzo | 1.104.361,28 |
| Abril | 502.924,03 |
| Mayo | 1.160.430,61 |
| Junio | 808.351,16 |
| Julio | 593.685,68 |
| Agosto | 524.873,87 |
| Septiembre | 320.093,34 |
| Octubre | 388.181,27 |
| Noviembre | 341.405,95 |
| Diciembre | 539.334,97 |
| **Total del año (sin redondear líneas)** | **11.066.206,89** |

**Resultado esperado:** febrero es el máximo y septiembre el mínimo. **Verificación:** 12 meses en orden cronológico y una caída febrero-marzo del 58,46 %. La suma de valores mostrados, redondeados a dos decimales, puede diferir del total calculado con precisión original.

<a id="paso-3-14"></a>
### 3.14. Probar filtros y relaciones

1. Confirmar que la segmentación de fecha contiene todo 2020.
2. En `product[CategoryName]`, seleccionar únicamente **Computers**.
3. Comprobar las tarjetas: **ventas 5.041.111,59**, **unidades 8.364** y **pedidos 2.094**.
4. Si un visual no cambia, seleccionar la segmentación y abrir **Formato → Editar interacciones**. Elegir el icono **Filtrar** sobre cada visual que deba responder, no **Ninguno**.
5. Si todas las categorías repiten el total general, volver a **Modelo** y revisar la relación activa por `ProductKey`.
6. Limpiar el filtro Computers o seleccionar todas las categorías.
7. Comprobar que las tarjetas recuperan **11.066.206,89**, **35.234** y **4.806**.

**Resultado esperado:** las selecciones cambian coherentemente el modelo completo y pueden restaurarse.

<a id="paso-3-15"></a>
### 3.15. Crear la página `Comprobacion` y reconstruir dos pedidos

1. Pulsar el símbolo **+** de las pestañas inferiores para crear una página y renombrarla `Comprobacion`.
2. Verificar que esta página no tiene un filtro global de año 2020.
3. Insertar una **Tabla** con los campos, en este orden: `sales[OrderKey]`, `sales[LineNumber]`, `sales[OrderDate]`, `product[CategoryName]`, `sales[Quantity]`, `sales[NetPrice]`, `sales[ImporteVenta]`.
4. Utilizar **No resumir** donde corresponda para mostrar detalle por línea. En el formato de precios e importes mostrar **tres o más decimales**.
5. Insertar una **Segmentación de datos** con `sales[OrderKey]` y seleccionar conjuntamente **1000** y **1001**; activar selección múltiple si hace falta.
6. Crear tres tarjetas usando **las mismas medidas** `Ventas Totales`, `Unidades Totales` y `Pedidos` (ver 3.10).
7. Comparar contra los registros:

| Pedido | Línea | Categoría | Cantidad | NetPrice | ImporteVenta |
| --- | ---: | --- | ---: | ---: | ---: |
| 1000 | 0 | Audio | 1 | 98,967 | 98,967 |
| 1000 | 1 | Computers | 1 | 659,780 | 659,780 |
| 1001 | 0 | Games and Toys | 2 | 54,376 | 108,752 |

El importe exacto suma **867,499** y se presenta como **867,50**. Hay **4 unidades**, **2 pedidos distintos** y **3 líneas**.

**Resultado esperado:** coinciden tanto la tabla de detalle como las tres tarjetas. Si `Comprobacion` queda vacía, quitar filtros de informe/sincronizados de 2020.

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
