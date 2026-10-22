# Taller. Publicación de un sitio web con Docker y OCI Container Instances

## Objetivo

Publicar el sitio web estático EcoRed en una plataforma de contenedores administrados de OCI. Construirás y probarás una imagen con Docker, la subirás a OCI Container Registry (OCIR) y la desplegarás en Container Instances para que los usuarios accedan desde un navegador mediante HTTP.

**Flujo de publicación:** código del sitio → imagen Docker → OCIR → Container Instances → navegador.

**Tiempo estimado:** 90 a 120 minutos, con Docker Desktop instalado.

**Código del sitio:** [adanbeltran/ecored-estatica](https://github.com/adanbeltran/ecored-estatica).

## Por qué se trabaja con un enfoque PaaS

En PaaS, el usuario despliega su aplicación sobre un entorno proporcionado por el proveedor y conserva el control de la aplicación, mientras el proveedor administra la infraestructura que la soporta. Esta separación de responsabilidades es el criterio central para analizar el modelo. [16]

En este taller, **OCI se utiliza como plataforma para publicar y ejecutar el sitio web**. El estudiante entrega una imagen que contiene EcoRed y Nginx; Oracle se ocupa de los servidores y del entorno que ejecuta el contenedor. El trabajo se concentra en preparar, desplegar y comprobar la aplicación. [1][17]

| Responsabilidad de Oracle | Responsabilidad del estudiante |
| --- | --- |
| Aprovisionar y mantener la infraestructura de ejecución | Preparar los archivos del sitio y el Dockerfile |
| Administrar los servidores y el sistema operativo anfitrión | Construir y actualizar la imagen, incluido Nginx y sus dependencias |
| Proporcionar el servicio de ejecución de contenedores | Elegir recursos, configurar el acceso de red y comprobar la publicación |

**Precisión sobre la clasificación:** Oracle presenta Container Instances como un servicio de **cómputo serverless para contenedores**. El taller aplica principios de PaaS al alojamiento web mediante ese servicio administrado. La publicación de un sitio, por sí sola, no determina el modelo: también importa qué responsabilidades asume el proveedor. La construcción y el mantenimiento de la imagen siguen a cargo del estudiante. [16][17]

## Requisitos

- Cuenta de OCI y usuario administrador.
- Compartimento, VCN y subred pública disponibles en **Brazil East (São Paulo)**. Puedes utilizar infraestructura de red existente.
- Git, Visual Studio Code y [Docker Desktop](https://docs.docker.com/desktop/) instalados. Docker Desktop debe estar iniciado y usar contenedores Linux.
- Conectividad del computador a GitHub, Docker Hub y OCIR.

Los comandos se ejecutan en la terminal local de VS Code. En Windows puedes utilizar PowerShell; en macOS/Linux, una terminal equivalente con Docker disponible.

## Parámetros de configuración

Usa los siguientes nombres o selecciona los recursos equivalentes de tu cuenta. Mantén la misma región para la red, OCIR y la instancia.

| Parámetro | Valor de la guía |
| --- | --- |
| Región | Brazil East (São Paulo), `sa-saopaulo-1` |
| Compartimento | `Infraestructura_y_Cloud` |
| VCN | `red-infraestructura` |
| Subred pública | `subred-web` |
| Internet Gateway | `salida-internet` |
| Carpeta local | `ecored-container` |
| Imagen local | `ecored-web:v1` |
| Endpoint de OCIR | `gru.ocir.io` |
| Repositorio público de OCIR | `ecored/web` |
| Container Instance | `ecored-container` |
| Contenedor | `nginx-ecored` |
| Plataforma de la imagen | `linux/amd64` |
| Shape | `CI.Standard.E4.Flex`, 1 OCPU y 2 GB de memoria |
| Puerto local de prueba | `8080` |
| Puerto del sitio en OCI | `80` |

Reemplaza `NAMESPACE` en los comandos por el namespace real de tu tenancy. Si compartes la cuenta con otros grupos, usa nombres de repositorio e instancia distintos, por ejemplo `grupo01/ecored` y `ecored-grupo01`.

## Índice

1. [Construir y probar la imagen con Docker](#fase-1-construir-y-probar-la-imagen-con-docker).
2. [Subir la imagen a OCI Container Registry](#fase-2-subir-la-imagen-a-oci-container-registry).
3. [Configurar la publicación en Container Instances](#fase-3-configurar-la-publicación-en-container-instances).
4. [Verificar el sitio y consultar los registros](#fase-4-verificar-el-sitio-y-consultar-los-registros).
5. [Revisar costos y cerrar la práctica](#fase-5-revisar-costos-y-cerrar-la-práctica).
6. [Extensión opcional: DuckDNS](#extensión-opcional-duckdns).

## Fase 1. Construir y probar la imagen con Docker

### Paso 1. Comprobar Docker en el computador

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso /th><th width="75%" align="left">Capturas de pantalla</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: Docker Desktop y terminal local de VS Code.</strong></p>
<ol>
<li>Inicia Docker Desktop y espera a que el motor esté disponible.</li>
<li>Abre VS Code y una terminal desde <strong>Terminal &gt; New Terminal</strong>.</li>
<li>Ejecuta:</li>
</ol>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker version
</code></pre>
<p>Debes ver información de <strong>Client</strong> y <strong>Server</strong>. Si solo aparece Client y un error de conexión, inicia o revisa Docker Desktop antes de continuar.</p>
<p>Comprueba también Git:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">git --version
</code></pre>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<img width="946" height="419" alt="image" src="https://github.com/user-attachments/assets/9f4983a3-531b-48b3-820a-01569ea7f0d7" />
<img width="510" height="152" alt="image" src="https://github.com/user-attachments/assets/ec9eb56c-6113-4ac6-add9-f044625f42e9" />


</td>
</tr>
</tbody>
</table>

### Paso 2. Descargar EcoRed

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: terminal local de VS Code.</strong></p>
<p>Ubícate en una carpeta donde guardes tus proyectos y ejecuta los siguientes comandos, uno por uno:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">git clone https://github.com/adanbeltran/ecored-estatica.git ecored-container
cd ecored-container
code .
</code></pre>
<p>En la ventana del proyecto deben aparecer <code>index.html</code>, <code>styles.css</code> y <code>app.js</code>. Estos son los archivos que publicaremos.</p>
<p>Si la terminal de la nueva ventana no está en <code>ecored-container</code>, abre una terminal dentro de esa carpeta. Los siguientes comandos de construcción deben ejecutarse allí.</p>
<p>EcoRed es una aplicación estática: su interactividad se ejecuta en JavaScript en el navegador y sus datos de demostración se guardan en <code>localStorage</code>. El acceso de usuario es simulado. Este despliegue conserva ese funcionamiento.</p>

</td>
<td width="75%" valign="top">

<p>VS Code con index.html, styles.css y app.js en la carpeta del proyecto.</p>
<img width="913" height="158" alt="image" src="https://github.com/user-attachments/assets/5aeb0858-64c3-45d0-993a-ccca0701f0fa" />

</td>
</tr>
</tbody>
</table>

### Paso 3. Crear el Dockerfile

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: editor de VS Code.</strong></p>
<p>En la raíz de <code>ecored-container</code>, crea un archivo llamado exactamente <strong><code>Dockerfile</code></strong>, sin extensión. Pega lo siguiente y guarda:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-dockerfile">FROM docker.io/library/nginx:stable-alpine

COPY index.html styles.css app.js /usr/share/nginx/html/

EXPOSE 80

CMD [&quot;nginx&quot;, &quot;-g&quot;, &quot;daemon off;&quot;]
</code></pre>
<p>La imagen oficial de Nginx aporta el servidor. La instrucción <code>COPY</code> incorpora los tres archivos de EcoRed en su directorio de publicación. El comando final mantiene Nginx ejecutándose en primer plano para que el contenedor permanezca activo. [2]</p>
<p><code>EXPOSE 80</code> describe el puerto que utiliza la imagen. El acceso real dependerá de la publicación local del puerto o, en OCI, de la red y sus reglas.</p>
<p>El Dockerfile copia únicamente esos tres archivos. Si enriqueces la página con una carpeta <code>img</code>, por ejemplo, agrega también:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-dockerfile">COPY img/ /usr/share/nginx/html/img/
</code></pre>
<p>Solo agrega esa instrucción si la carpeta existe y la página la utiliza.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Dockerfile completo abierto en VS Code, sin extensión .txt.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 4. Construir la imagen

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: terminal local de VS Code, dentro de <code>ecored-container</code>.</strong></p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker build --platform linux/amd64 -t ecored-web:v1 .
</code></pre>
<p>El punto final indica que Docker usará la carpeta actual como contexto de construcción. <code>v1</code> identifica esta versión de tu imagen. La plataforma <code>linux/amd64</code> corresponde a la shape E4 que usarás en OCI. [4]</p>
<p>Comprueba que la imagen exista y tenga la plataforma esperada:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker image ls ecored-web
docker image inspect ecored-web:v1 --format &#39;{{.Os}}/{{.Architecture}}&#39;
</code></pre>
<p>El segundo comando debe mostrar:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-text">linux/amd64
</code></pre>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://docs.docker.com/get-started/docker-concepts/the-basics/images/click-image.webp"><img src="https://docs.docker.com/get-started/docker-concepts/the-basics/images/click-image.webp" alt="Vista Images de Docker Desktop" width="100%"></a></p>
<p><em>En Images podrás localizar tu imagen ecored-web con la etiqueta v1. La captura oficial muestra otra imagen como ejemplo. [19]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Construcción completada e imagen ecored-web:v1 con plataforma linux/amd64.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 5. Probar la página con Docker en el computador

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: terminal local y navegador del computador.</strong></p>
<p>Inicia un contenedor de prueba:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker run -d --platform linux/amd64 --name ecored-prueba -p 8080:80 ecored-web:v1
</code></pre>
<p>La relación <code>8080:80</code> publica el puerto 80 del contenedor en el puerto 8080 de tu computador. [4]</p>
<p>Abre <a href="http://localhost:8080">http://localhost:8080</a> y verifica que cargue EcoRed. Comprueba el diseño, la navegación y las acciones disponibles en la página.</p>
<p>Consulta el estado y los registros:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker ps
docker logs ecored-prueba
</code></pre>
<p>Después de comprobar el resultado, detén y elimina únicamente el contenedor de prueba:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker stop ecored-prueba
docker rm ecored-prueba
</code></pre>
<p>La imagen <code>ecored-web:v1</code> permanece en tu computador para subirla a OCIR.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://docs.docker.com/get-started/docker-concepts/the-basics/images/view-your-containers.webp"><img src="https://docs.docker.com/get-started/docker-concepts/the-basics/images/view-your-containers.webp" alt="Docker Desktop: contenedores en ejecución" width="100%"></a></p>
<p><em>Referencia para ubicar el contenedor y el puerto publicado. Tu contenedor debe llamarse ecored-prueba. [20]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>EcoRed en http://localhost:8080 y contenedor ecored-prueba en ejecución.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Fase 2. Subir la imagen a OCI Container Registry

### Paso 6. Crear el repositorio en OCIR

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI.</strong></p>
<ol>
<li>Inicia sesión como administrador y selecciona la misma región de tu red. En el ejemplo, <strong>Brazil East (São Paulo)</strong>.</li>
<li>Busca <strong>Container Registry</strong> en el buscador de la consola. También puedes encontrarlo en <strong>Developer Services &gt; Containers &amp; Artifacts</strong>.</li>
<li>Selecciona el compartimento <code>Infraestructura_y_Cloud</code>.</li>
<li>Pulsa <strong>Create repository</strong> y configura:</li>
</ol>
<ul>
<li><strong>Create in compartment:</strong> <code>Infraestructura_y_Cloud</code>.</li>
<li><strong>Repository name:</strong> <code>ecored/web</code>.</li>
<li><strong>Access:</strong> <code>Public</code>.</li>
</ul>
<ol start="5">
<li>Crea el repositorio.</li>
</ol>
<p>Esta práctica usa un repositorio <strong>público</strong> porque la imagen contiene la página pública de demostración. Cualquier persona que conozca la ruta podrá descargarla. La subida seguirá requiriendo autenticación. Así, la instancia puede descargar esta imagen sin configurar credenciales de lectura de un repositorio privado. [3]</p>
<p>Crear el repositorio antes del primer <code>push</code> permite ubicarlo expresamente en el compartimento de la práctica.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://oracle.github.io/fmw-kubernetes/wcsites-domains/oracle-cloud/ocir/images/ocir-5.png"><img src="https://oracle.github.io/fmw-kubernetes/wcsites-domains/oracle-cloud/ocir/images/ocir-5.png" alt="Oracle Container Registry: Create Repository" width="100%"></a></p>
  <img width="940" height="434" alt="image" src="https://github.com/user-attachments/assets/ecb2cc11-e777-4b7a-a85f-562bfacf303a" />

<p><em>Referencia oficial de una interfaz anterior: localiza Container Registry y Create repository. Usa São Paulo y los valores de la columna izquierda. [18]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Repositorio ecored/web, compartimento Infraestructura_y_Cloud y acceso Public.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 7. Copiar el namespace y generar un Auth Token

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI.</strong></p>
<ol>
<li>Abre el menú de perfil y entra en <strong>Tenancy</strong>, <strong>Tenancy details</strong> o la página de información general de tu tenancy.</li>
<li>Copia el valor <strong>Object Storage namespace</strong>. Ese valor es el <code>NAMESPACE</code> de los siguientes comandos. Es distinto del nombre visible de la cuenta y del nombre del compartimento.</li>
<li>Vuelve al perfil de tu usuario: <strong>User settings</strong>, <strong>My Profile</strong> o el nombre de tu usuario.</li>
<li>En <strong>Tokens and keys &gt; Auth Tokens</strong>, pulsa <strong>Generate token</strong>.</li>
<li>Usa la descripción <code>docker-ecored</code>, genera el token y guárdalo en un lugar privado. Solo se muestra al crearlo.</li>
</ol>
<p>Para iniciar sesión desde Docker utilizarás este <strong>Auth Token</strong>, en lugar de la contraseña de la consola de OCI. [5]</p>
<p>Registra también el nombre de usuario y, cuando corresponda, el dominio de identidad que aparecen en tu perfil.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://oracle.github.io/fmw-kubernetes/wcsites-domains/oracle-cloud/ocir/images/ocir-2.png"><img src="https://oracle.github.io/fmw-kubernetes/wcsites-domains/oracle-cloud/ocir/images/ocir-2.png" alt="Perfil de usuario y sección Auth Tokens de OCI" width="100%"></a></p>
<p><em>Referencia oficial de la sección Auth Tokens. En la interfaz actual puede encontrarse en Tokens and keys. [18]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Ubicación del namespace y descripción del Auth Token, sin mostrar el token.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 8. Iniciar sesión en OCIR desde Docker

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: terminal local de VS Code.</strong></p>
<p>Para São Paulo, ejecuta:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker login gru.ocir.io
</code></pre>
<p>Cuando solicite las credenciales:</p>
<ul>
<li><strong>Username, usuario IAM sin dominio en su identificación:</strong> <code>NAMESPACE/USUARIO</code>.</li>
<li><strong>Username, usuario identificado con dominio de identidad o federación:</strong> <code>NAMESPACE/DOMINIO/USUARIO</code>.</li>
<li><strong>Password:</strong> El <strong>Auth Token</strong> generado en el paso anterior.</li>
</ul>
<p>Usa el dominio y usuario reales que muestra tu perfil. Por ejemplo, si tu dominio es <code>Default</code>, tu usuario es <code>docente@ejemplo.com</code> y tu namespace es <code>abc123ejemplo</code>, el usuario completo será <code>abc123ejemplo/Default/docente@ejemplo.com</code>. No agregues <code>Default</code> ni <code>oracleidentitycloudservice</code> si no corresponden a tu cuenta. [5]</p>
<p>El resultado esperado es <strong>Login Succeeded</strong>. Al pegar el token puede que la terminal no muestre caracteres.</p>
<p><code>gru.ocir.io</code> es un endpoint de OCIR para São Paulo. Si trabajas en otra región, consulta su endpoint y úsalo tanto en el inicio de sesión como en <code>tag</code> y <code>push</code>. [6]</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Terminal con Login Succeeded después de docker login gru.ocir.io.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 9. Etiquetar y subir la imagen

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: terminal local de VS Code.</strong></p>
<p>Reemplaza <code>NAMESPACE</code> por el valor copiado de OCI y ejecuta:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker tag ecored-web:v1 gru.ocir.io/NAMESPACE/ecored/web:v1
docker push gru.ocir.io/NAMESPACE/ecored/web:v1
</code></pre>
<p>Por ejemplo, si el namespace fuera <code>abc123ejemplo</code>, la ruta sería:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-text">gru.ocir.io/abc123ejemplo/ecored/web:v1
</code></pre>
<p>La ruta reúne el registro regional, el namespace de tu tenancy, el repositorio y la etiqueta de versión. Escríbela sin <code>https://</code>. Si elegiste otro nombre de repositorio, sustitúyelo también. [5]</p>
<p>Espera a que termine <code>docker push</code>. Vuelve a <strong>Container Registry &gt; ecored/web</strong> y comprueba que aparezca la imagen con etiqueta <strong><code>v1</code></strong>. Conserva el digest mostrado para identificar la imagen publicada.</p>
<p>El comando <code>push</code> almacena la imagen en OCIR. La ejecución de la página comenzará cuando crees la instancia en la siguiente fase.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Docker push completado y etiqueta v1 visible en el repositorio de OCIR.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Fase 3. Configurar la publicación en Container Instances

### Paso 10. Comprobar la red existente

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI, Networking &gt; Virtual cloud networks.</strong></p>
<p>Abre <code>red-infraestructura</code> y revisa <code>subred-web</code>:</p>
<ul>
<li><strong>Subred:</strong> Es pública y permite asignar direcciones IPv4 públicas.</li>
<li><strong>Tabla de rutas asociada a la subred:</strong> Tiene <code>0.0.0.0/0</code> hacia el Internet Gateway <code>salida-internet</code>.</li>
<li><strong>Lista de seguridad asociada a la subred:</strong> Permite ingreso TCP desde <code>0.0.0.0/0</code> al puerto de destino <code>80</code>.</li>
<li><strong>Regla de ingreso HTTP:</strong> Preferiblemente stateful; puerto de origen sin restricción.</li>
<li><strong>Reglas de salida:</strong> Se conserva la salida general hacia internet, que permite HTTPS saliente hacia los registros.</li>
</ul>
<p>Si la regla TCP 80 ya existe, úsala. Si falta, agrégala a la lista de seguridad de esta subred:</p>
<ul>
<li><strong>Source type:</strong> <code>CIDR</code>.</li>
<li><strong>Source CIDR:</strong> <code>0.0.0.0/0</code>.</li>
<li><strong>IP protocol:</strong> <code>TCP</code>.</li>
<li><strong>Source port range:</strong> Todos, dejar sin restricción.</li>
<li><strong>Destination port range:</strong> <code>80</code>.</li>
<li><strong>Stateless:</strong> Desmarcado.</li>
</ul>
<p>Esta práctica utiliza la lista de seguridad de la subred y no requiere agregar un NSG. Si tu red tiene restricciones adicionales, revisa que también permitan HTTP.</p>
<p>El puerto 8080 solo se usó para la prueba en el computador. En OCI, Nginx atenderá en el puerto 80.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Subred pública, ruta al Internet Gateway y regla de ingreso TCP 80.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 11. Verificar o agregar el Service Gateway

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI, dentro de <code>red-infraestructura</code>.</strong></p>
<p>La documentación de Container Instances solicita una VCN con <strong>Service Gateway</strong> cuando se utiliza una imagen de OCIR. El Internet Gateway permite el acceso público de la página. [7]</p>
<ol>
<li>Abre la sección <strong>Service Gateways</strong> de la VCN.</li>
<li>Si ya existe uno adecuado y habilitado, reutilízalo. En caso contrario, pulsa <strong>Create Service Gateway</strong>.</li>
<li>Usa el nombre <code>servicios-oracle</code>, el compartimento de la práctica y el servicio <strong>All GRU Services in Oracle Services Network</strong> para São Paulo.</li>
<li>Abre la <strong>tabla de rutas realmente asociada a <code>subred-web</code></strong> y agrega la siguiente ruta de servicio:</li>
</ol>
<ul>
<li><strong>Target type:</strong> <code>Service Gateway</code>.</li>
<li><strong>Destination type:</strong> <code>Service</code>.</li>
<li><strong>Destination service:</strong> <code>OCI GRU Object Storage</code>.</li>
<li><strong>Target service gateway:</strong> <code>servicios-oracle</code>, o el existente.</li>
</ul>
<ol start="5">
<li>Conserva la ruta <code>0.0.0.0/0</code> al Internet Gateway.</li>
</ol>
<p>La tabla de rutas de esta subred pública quedará con estas dos rutas relevantes:</p>
<ul>
<li><strong><code>0.0.0.0/0</code>:</strong> Internet Gateway <code>salida-internet</code>.</li>
<li><strong><code>OCI GRU Object Storage</code>:</strong> Service Gateway <code>servicios-oracle</code>.</li>
</ul>
<p>La selección <strong>All GRU Services</strong> habilita servicios en el gateway; la ruta de esta subred pública dirige <strong>Object Storage</strong> hacia ese gateway. Esta combinación es válida. Oracle advierte que la ruta <strong>All GRU Services</strong> y una ruta al Internet Gateway no deben coexistir en la misma tabla de rutas pública. [8][9]</p>
<p>Con la salida general hacia internet se permite el tráfico HTTPS saliente necesario para la descarga. Si limitaste la salida, revisa también TCP 443 hacia los destinos de los registros y la resolución DNS. La descarga de una imagen usa tráfico saliente y no requiere abrir el puerto 443 de ingreso de la página.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Service Gateway habilitado y rutas de la subred pública.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

**Lugar: consola web de OCI.**

Busca **Container Instances**, selecciona `Infraestructura_y_Cloud` y pulsa **Create container instance**. Completa las secciones del asistente. [1][7]

### Paso 12.1. Basic information

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<ul>
<li><strong>Name:</strong> <code>ecored-container</code>.</li>
<li><strong>Create in compartment:</strong> <code>Infraestructura_y_Cloud</code>.</li>
<li><strong>Availability domain:</strong> Uno disponible en tu región.</li>
<li><strong>Shape:</strong> <code>CI.Standard.E4.Flex</code>.</li>
<li><strong>Number of OCPUs:</strong> <code>1</code>.</li>
<li><strong>Amount of memory:</strong> <code>2 GB</code>.</li>
<li><strong>Container restart policy:</strong> <code>Always</code>.</li>
<li><strong>Graceful shutdown timeout:</strong> <code>30</code> segundos.</li>
</ul>
<p>E4 utiliza arquitectura AMD/x86, coherente con <code>linux/amd64</code>. Si no hay capacidad, revisa la disponibilidad de E4 en tu región. Cambiar a una shape Arm requiere construir y subir una imagen compatible con Arm. [10]</p>
<p>Revisa el costo estimado o la tarifa vigente antes de crear el recurso. Pulsa <strong>Next</strong>.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/05-shape-config.png"><img src="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/05-shape-config.png" alt="Configuración de shape de Container Instances" width="100%"></a></p>
<p><em>Ubica la selección de shape y recursos. Configura E4 con 1 OCPU y 2 GB; los valores de la captura son ilustrativos. [1]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Basic information: nombre, compartimento, E4, 1 OCPU, 2 GB y política Always.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 12.2. Networking

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<ul>
<li><strong>Virtual cloud network:</strong> Existente: <code>red-infraestructura</code>.</li>
<li><strong>Subnet:</strong> Existente: <code>subred-web</code>.</li>
<li><strong>Private IPv4 address:</strong> Asignación automática.</li>
<li><strong>Public IPv4 address:</strong> <strong>Assign a public IPv4 address</strong>.</li>
<li><strong>Network security groups:</strong> Sin agregar NSG para esta práctica.</li>
</ul>
<p>Pulsa <strong>Next</strong>.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/06-networking.png"><img src="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/06-networking.png" alt="Configuración de red de Container Instances" width="100%"></a></p>
<p><em>Ubica la VCN, subred y asignación de IP pública. Selecciona los recursos de esta guía. [1]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Networking: VCN, subred y Assign a public IPv4 address.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 12.3. Storage

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p>Continúa sin agregar volúmenes. Los archivos de EcoRed ya están dentro de la imagen.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Storage sin volúmenes adicionales.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 12.4. Containers

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p>Pulsa <strong>Add container</strong> y configura:</p>
<ul>
<li><strong>Name:</strong> <code>nginx-ecored</code>.</li>
<li><strong>Image source:</strong> <code>OCI Container Registry</code>.</li>
<li><strong>Repository compartment:</strong> <code>Infraestructura_y_Cloud</code>.</li>
<li><strong>Repository:</strong> <code>ecored/web</code>.</li>
<li><strong>Image tag:</strong> <code>v1</code>.</li>
<li><strong>Environment variables:</strong> Sin agregar.</li>
<li><strong>Working directory, command y arguments:</strong> Vacíos, para usar los de la imagen.</li>
<li><strong>Security:</strong> Valores predeterminados.</li>
</ul>
<p>Selecciona la imagen y agrega el contenedor. Continúa hasta <strong>Review details</strong>.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Imagen de OCIR seleccionada: ecored/web, etiqueta v1; contenedor nginx-ecored.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 12.5. Review details

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p>Comprueba el nombre, la shape, la red, la asignación de IP pública y la imagen <code>v1</code>. Pulsa <strong>Create</strong>.</p>
<p>Espera a que la instancia esté <strong>Active</strong> y el contenedor esté ejecutándose. Abre los detalles y copia la <strong>IP pública de esta nueva instancia</strong>. Esa IP será la dirección para probar la publicación.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura oficial de referencia</strong></p>
<p><a href="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/15-container-instance-live.png"><img src="https://docs.oracle.com/en/learn/manage-oci-container-instances/images/15-container-instance-live.png" alt="Detalles de Container Instance activa y dirección pública" width="100%"></a></p>
<p><em>Referencia para localizar el estado de la instancia y su dirección pública. Tu instancia se llamará ecored-container. [1]</em></p>
<p><strong>Captura específica del paso</strong></p>
<p>Instancia ecored-container activa y su IP pública.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Fase 4. Verificar el sitio y consultar los registros

### Paso 13. Abrir EcoRed desde internet

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: navegador del computador o de otro dispositivo.</strong></p>
<p>Escribe la dirección siguiente, reemplazando <code>IP_PUBLICA</code> por la IP de la instancia de contenedores:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-text">http://IP_PUBLICA/
</code></pre>
<p>Realiza estas comprobaciones:</p>
<ol>
<li>Verifica que aparezca EcoRed con su diseño completo.</li>
<li>Abre las herramientas del navegador con <strong>F12</strong> y entra en <strong>Network</strong>.</li>
<li>Recarga la página y comprueba respuestas correctas para <code>index.html</code>, <code>styles.css</code> y <code>app.js</code>. Con la caché deshabilitada normalmente verás <code>200</code>.</li>
<li>En <strong>Console</strong>, revisa que no haya errores de carga de archivos.</li>
<li>Prueba las opciones de navegación y las acciones disponibles en EcoRed.</li>
<li>Abre la misma dirección desde un celular, preferiblemente con datos móviles, para verificar el acceso externo.</li>
</ol>
<p>Los datos de <code>localStorage</code> pertenecen al navegador y al origen de la página. Lo que guardaste en <code>localhost:8080</code> no aparecerá automáticamente en la IP pública; tampoco se comparte entre dispositivos.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>EcoRed por IP pública y solicitudes correctas de index.html, styles.css y app.js.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 14. Consultar los registros de Nginx en OCI

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI.</strong></p>
<ol>
<li>Abre <strong>Container Instances &gt; ecored-container</strong>.</li>
<li>Entra en la pestaña <strong>Containers</strong>.</li>
<li>Selecciona <code>nginx-ecored</code>.</li>
<li>En <strong>Actions</strong>, selecciona <strong>View logs</strong>.</li>
<li>Recarga la página en el navegador y vuelve a consultar los registros para identificar solicitudes HTTP a sus archivos. [11]</li>
</ol>
<p>Relaciona al menos una solicitud del navegador con su entrada en el registro de Nginx. Si la página falla, estos registros ayudan a distinguir un problema del servidor de un problema de red.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>View logs con solicitudes HTTP de EcoRed.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Fase 5. Revisar costos y cerrar la práctica

### Paso 15. Revisar el consumo y el presupuesto

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI, Billing &amp; Cost Management.</strong></p>
<ol>
<li>Abre <strong>Cost Analysis</strong> y filtra por <code>Infraestructura_y_Cloud</code> y el período de la práctica.</li>
<li>Revisa el consumo cuando esté disponible. Los datos pueden tardar en reflejarse.</li>
<li>Si ya tienes un presupuesto para ese compartimento, reutilízalo.</li>
<li>Si falta, abre <strong>Budgets &gt; Create budget</strong>, selecciona ese compartimento como objetivo y define un presupuesto mensual de referencia de <code>5</code> en la moneda de tu cuenta, o el valor indicado por el docente.</li>
<li>Agrega una alerta al <code>80 %</code> del gasto real y configura el correo que recibirá la notificación.</li>
</ol>
<p>El presupuesto sirve para generar alertas; no detiene automáticamente los recursos. Los recursos de Container Instances y el almacenamiento de imágenes deben revisarse según las tarifas y condiciones de tu cuenta. [12]</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Presupuesto o consumo y regla de alerta, sin datos personales innecesarios.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 16. Guardar evidencias y decidir cuándo detener el servicio

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p>Registra los datos técnicos de tu despliegue, por ejemplo:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-text">Región: sa-saopaulo-1
Compartimento: Infraestructura_y_Cloud
VCN: red-infraestructura
Subred: subred-web
Repositorio OCIR: ecored/web
Imagen: gru.ocir.io/NAMESPACE/ecored/web:v1
Digest de la imagen: valor mostrado en OCIR
Container Instance: ecored-container
Shape: CI.Standard.E4.Flex
OCPUs: 1
Memoria: 2 GB
Contenedor: nginx-ecored
IP pública: IP_PUBLICA
URL: http://IP_PUBLICA/
</code></pre>
<p>Guarda el Dockerfile y, si elaboras una guía en Git, agrega tus capturas y los resultados de las comprobaciones. Conserva los tokens en un lugar privado.</p>
<p>Si el docente debe verificar la URL, mantén la instancia activa hasta que termine esa revisión.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Registro de datos técnicos con imagen, digest e IP pública.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

### Paso 17. Detener o eliminar los recursos de esta práctica

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p><strong>Lugar: consola web de OCI.</strong></p>
<p>Cuando ya no se requiera el sitio, elige una de estas opciones:</p>
<ul>
<li><strong>Conservar la configuración para otra sesión:</strong> abre la instancia y selecciona <strong>Actions &gt; Stop</strong>. Espera el estado <strong>Inactive</strong>. Podrás usar <strong>Start</strong> posteriormente. El almacenamiento efímero se pierde al detenerla; los archivos de EcoRed se recuperarán de la imagen. [13]</li>
<li><strong>Finalizar la práctica:</strong> abre la instancia y selecciona <strong>Actions &gt; Delete</strong>. La eliminación libera sus recursos de red asociados y termina el servicio web. La imagen permanece en OCIR hasta que la elimines por separado. [14]</li>
</ul>
<p>Si terminaste definitivamente, elimina también el repositorio de imágenes creado para esta práctica cuando ya no lo necesites y revoca el Auth Token <code>docker-ecored</code> si solo se usaba para ella. Revisa los recursos restantes, pues detener la instancia no elimina otros conceptos de consumo.</p>
<p>Conserva la VCN, la subred y los gateways mientras otros recursos los utilicen.</p>
<p>Puedes cerrar también la sesión local de Docker en OCIR:</p>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">docker logout gru.ocir.io
</code></pre>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>Estado Inactive o Deleted una vez finalizada la verificación del sitio.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Extensión opcional: DuckDNS

### Paso opcional. Publicar el acceso por subdominio

<table width="100%" style="width:100%;table-layout:fixed">
<colgroup><col width="25%"><col width="75%"></colgroup>
<thead><tr><th width="25%" align="left">Instrucciones del paso · 25 %</th><th width="75%" align="left">Capturas de pantalla · 75 %</th></tr></thead>
<tbody>
<tr>
<td width="25%" valign="top" style="overflow-wrap:anywhere">
<p>Puedes configurar un subdominio de DuckDNS para acceder al sitio por nombre:</p>
<ol>
<li>Abre <a href="https://www.duckdns.org/">DuckDNS</a> e inicia sesión.</li>
<li>Crea un subdominio disponible, por ejemplo <code>ecored-grupo01.duckdns.org</code>, o selecciona uno que ya controles.</li>
<li>En <strong>current ip</strong>, escribe explícitamente la IP pública de la instancia de contenedores y pulsa <strong>update ip</strong>. Verifica ese dato: la IP detectada desde tu navegador puede ser la de tu conexión doméstica.</li>
<li>Comprueba la resolución DNS desde la terminal:</li>
</ol>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-bash">nslookup ecored-grupo01.duckdns.org
</code></pre>
<ol start="5">
<li>Cuando la respuesta corresponda a la IP de la instancia, abre:</li>
</ol>
<pre style="white-space:pre-wrap;overflow-wrap:anywhere;word-break:break-word"><code class="language-text">http://ecored-grupo01.duckdns.org/
</code></pre>
<p>DuckDNS permite asociar el nombre con una dirección IP. Este despliegue sigue usando HTTP. Para servir HTTPS hace falta configurar TLS y un certificado en una ampliación del taller. [15]</p>
<p>Después de recrear o volver a iniciar la instancia, verifica su IP pública y actualiza el registro de DuckDNS si cambió.</p>

</td>
<td width="75%" valign="top">
<p><strong>Captura por incorporar</strong></p>
<p>DuckDNS apuntando a la IP pública y sitio abierto por http://SUBDOMINIO.duckdns.org/.</p>
<blockquote><p>Espacio para la captura de pantalla de este paso.</p></blockquote>

</td>
</tr>
</tbody>
</table>

## Solución de problemas

| Problema | Qué revisar |
| --- | --- |
| Docker no se conecta al motor | Docker Desktop iniciado; `docker version` debe mostrar Client y Server |
| Dockerfile no encontrado | Terminal ubicada en `ecored-container`, archivo llamado `Dockerfile` y punto al final del comando `docker build` |
| Error al copiar archivos durante la construcción | Existen `index.html`, `styles.css` y `app.js` en la carpeta del Dockerfile; las carpetas adicionales solo se copian si existen |
| El nombre `ecored-prueba` ya está en uso | Revisa `docker ps -a` y elimina únicamente el contenedor de prueba anterior antes de repetir `docker run` |
| Puerto local 8080 ocupado | Cambia la publicación a `-p 8081:80` y abre `http://localhost:8081` |
| `unauthorized` al iniciar sesión o subir | Namespace correcto, formato de usuario y dominio según el perfil, Auth Token vigente y endpoint de la región |
| El `push` funciona, pero no ves la imagen | Región y compartimento seleccionados en OCIR; nombre exacto del repositorio y etiqueta `v1` |
| OCI no logra descargar la imagen | Ruta y etiqueta existentes; repositorio público; VCN con Service Gateway; rutas, DNS y salida HTTPS permitida |
| Error de arquitectura o `exec format error` | Imagen `linux/amd64` y shape `CI.Standard.E4.Flex`; reconstruye y sube una versión nueva si había otra arquitectura |
| La instancia está activa, pero la URL no responde | IP pública de la instancia correcta, subred pública, ruta al Internet Gateway, regla de ingreso TCP 80 y contenedor ejecutándose |
| Aparece la página de bienvenida de Nginx | Se seleccionó la imagen base de Nginx en vez de la imagen de EcoRed, o faltó copiar los archivos al construir |
| La página funciona por IP y falla por nombre | Registro de DuckDNS apuntando a la IP correcta y resolución DNS actualizada; prueba `nslookup` |
| Los datos de prueba parecen haber desaparecido | Se cambió de navegador, de dispositivo o de origen: localhost, IP y subdominio tienen almacenamiento local independiente |

## Resultado esperado

El sitio EcoRed debe responder por HTTP desde la IP pública de Container Instances y, si configuraste DuckDNS, desde tu subdominio. Debes poder identificar qué administra Oracle y qué mantienes tú al publicar una aplicación sobre una plataforma de contenedores administrados.

## Referencias

Documentación consultada el 22 de octubre de 2026. Las etiquetas de la consola pueden variar según el idioma y las actualizaciones del servicio.

1. [Oracle: administrar cargas con Container Instances](https://docs.oracle.com/en/learn/manage-oci-container-instances/).
2. [Docker Hub: imagen oficial de Nginx](https://hub.docker.com/_/nginx).
3. [Oracle: crear un repositorio en Container Registry](https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrycreatingarepository.htm).
4. [Docker: construir y compartir una aplicación en contenedor](https://docs.docker.com/get-started/tutorials/run-an-app/).
5. [Oracle: subir imágenes con Docker CLI](https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrypushingimagesusingthedockercli.htm).
6. [Oracle: requisitos y endpoints regionales de Container Registry](https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryprerequisites.htm).
7. [Oracle: crear una instancia de contenedores](https://docs.oracle.com/en-us/iaas/Content/container-instances/creating-a-container-instance.htm).
8. [Oracle: acceso a servicios mediante Service Gateway](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/servicegateway.htm).
9. [Oracle: problemas conocidos de red y combinación de rutas en subredes públicas](https://docs.oracle.com/en-us/iaas/Content/Network/Reference/known_issues_for_networking.htm).
10. [Oracle: shapes de Container Instances](https://docs.oracle.com/en-us/iaas/Content/container-instances/container-instance-shapes.htm).
11. [Oracle: consultar registros de contenedores](https://docs.oracle.com/en-us/iaas/Content/container-instances/retrieve-logs.htm).
12. [Oracle: presupuestos y alertas](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/budgetsoverview.htm).
13. [Oracle: detener una instancia de contenedores](https://docs.oracle.com/en-us/iaas/Content/container-instances/stopping-a-container-instance.htm).
14. [Oracle: eliminar una instancia de contenedores](https://docs.oracle.com/en-us/iaas/Content/container-instances/terminating-a-container-instance.htm).
15. [DuckDNS: especificación de actualización de DNS](https://www.duckdns.org/spec.jsp).
16. [NIST: definición de cloud computing y modelo PaaS, SP 800-145](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf).
17. [Oracle: descripción de Container Instances como servicio serverless](https://docs.oracle.com/en-us/iaas/Content/container-instances/overview-of-container-instances.htm).
18. [Oracle: capturas de referencia de Container Registry y Auth Tokens](https://oracle.github.io/fmw-kubernetes/wcsites-domains/oracle-cloud/ocir/).
19. [Docker: capturas de la vista Images de Docker Desktop](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/).
20. [Docker: capturas de contenedores y puertos publicados en Docker Desktop](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/).
