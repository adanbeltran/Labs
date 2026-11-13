# Despliegue de Microservicios Cloud Native desde Docker Hub: Entorno Local y Oracle Cloud Infrastructure (OCI) 

<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/0724e797-c56a-4dae-9bc1-0965a7c70d34" />

## Objetivo

Desplegar EcoRed a partir de imágenes previamente publicadas en Docker Hub, primero en el entorno local con imágenes de otra persona y después en Oracle Cloud Infrastructure con imágenes propias, **sin recompilar ni modificar los artefactos**. Configurar los backing services mediante variables de entorno, comprobar los endpoints, la autenticación y la persistencia en ambos entornos, y relacionar la práctica con los doce factores de *The Twelve-Factor App*.

## Tabla de contenido

- [Objetivo](#objetivo)
- [Compatibilidad de terminales y uso de imágenes](#compatibilidad-de-terminales-y-uso-de-imágenes)
- [Arquitectura y resultados esperados](#arquitectura-y-resultados-esperados)
- [Ruta de las imágenes entre entornos](#ruta-de-las-imágenes-entre-entornos)
- [Fase 1. Reproducción y ejecución local desde Docker Hub](#fase-1-reproducción-y-ejecución-local-desde-docker-hub)
  - [Variables PowerShell de la fase 1 (definir una sola vez)](#variables-powershell-de-la-fase-1-definir-una-sola-vez)
  - [1.1. Selección y descarga](#11-selección-y-descarga)
    - [1.1.1. Preparar Docker y una carpeta de trabajo](#111-preparar-docker-y-una-carpeta-de-trabajo)
    - [1.1.2. Elegir el propietario y descargar las cuatro imágenes](#112-elegir-el-propietario-y-descargar-las-cuatro-imágenes)
  - [1.2. Servicios externos y variables de entorno](#12-servicios-externos-y-variables-de-entorno)
    - [1.2.1. Comprobar los backing services propios](#121-comprobar-los-backing-services-propios)
    - [1.2.2. Crear los cuatro archivos `.env`](#122-crear-los-cuatro-archivos-env)
  - [1.3. Red y ejecución de cuatro contenedores](#13-red-y-ejecución-de-cuatro-contenedores)
    - [1.3.1. Crear la red virtual Docker](#131-crear-la-red-virtual-docker)
    - [1.3.2. Ejecutar Empresas y Materiales](#132-ejecutar-empresas-y-materiales)
    - [1.3.3. Ejecutar el gateway y el frontend](#133-ejecutar-el-gateway-y-el-frontend)
  - [1.4. Pruebas independientes e integrales](#14-pruebas-independientes-e-integrales)
    - [1.4.1. Revisar salud, aislamiento y logs](#141-revisar-salud-aislamiento-y-logs)
    - [1.4.2. Realizar una prueba autenticada de extremo a extremo](#142-realizar-una-prueba-autenticada-de-extremo-a-extremo)
    - [1.4.3. Finalizar la reproducción](#143-finalizar-la-reproducción)
- [Fase 2. Despliegue en OCI desde imágenes propias de Docker Hub](#fase-2-despliegue-en-oci-desde-imágenes-propias-de-docker-hub)
  - [Variables PowerShell de la fase 2 (definir una sola vez)](#variables-powershell-de-la-fase-2-definir-una-sola-vez)
  - [2.1. Descargar imágenes propias desde Docker Hub](#21-descargar-imágenes-propias-desde-docker-hub)
    - [2.1.1. Descargar desde Docker Hub las cuatro imágenes propias](#211-descargar-desde-docker-hub-las-cuatro-imágenes-propias)
  - [2.2. Preparar OCIR](#22-preparar-ocir)
    - [2.2.1. Ubicar región, namespace y repositorios](#221-ubicar-región-namespace-y-repositorios)
    - [2.2.2. Generar un Auth Token e iniciar sesión](#222-generar-un-auth-token-e-iniciar-sesión)
  - [2.3. Etiquetar y publicar en OCIR](#23-etiquetar-y-publicar-en-ocir)
    - [2.3.1. Asociar las imágenes propias a OCIR](#231-asociar-las-imágenes-propias-a-ocir)
    - [2.3.2. Publicar las cuatro imágenes](#232-publicar-las-cuatro-imágenes)
  - [2.4. Red y Container Instance](#24-red-y-container-instance)
    - [2.4.1. Preparar VCN, subred pública y políticas de acceso](#241-preparar-vcn-subred-pública-y-políticas-de-acceso)
      - [2.4.1.1. Agregar la regla de entrada TCP/8080](#2411-agregar-la-regla-de-entrada-tcp8080)
      - [2.4.1.2. Verificar las reglas de salida](#2412-verificar-las-reglas-de-salida)
      - [2.4.1.3. Verificar la subred pública](#2413-verificar-la-subred-pública)
      - [2.4.1.4. Verificación final](#2414-verificación-final)
    - [2.4.2. Verificar imágenes y configurar permisos IAM para OCIR](#242-verificar-imágenes-y-configurar-permisos-iam-para-ocir)
      - [2.4.2.1. Confirmar los repositorios y las etiquetas](#2421-confirmar-los-repositorios-y-las-etiquetas)
      - [2.4.2.2. Crear el Dynamic Group para Container Instances](#2422-crear-el-dynamic-group-para-container-instances)
      - [2.4.2.3. Crear la política IAM para leer los repositorios](#2423-crear-la-política-iam-para-leer-los-repositorios)
    - [2.4.3. Preparar un subdominio público con DuckDNS](#243-preparar-un-subdominio-público-con-duckdns)
    - [2.4.4. Verificar OCIR con una instancia mínima (prueba recomendada)](#244-verificar-ocir-con-una-instancia-mínima-prueba-recomendada)
    - [2.4.5. Crear una Container Instance con cuatro contenedores](#245-crear-una-container-instance-con-cuatro-contenedores)
  - [2.5. Inyectar variables y probar la IP pública](#25-inyectar-variables-y-probar-la-ip-pública)
    - [2.5.1. Configurar las variables en OCI, sin reconstruir imágenes](#251-configurar-las-variables-en-oci-sin-reconstruir-imágenes)
    - [2.5.2. Ejecutar comprobaciones públicas de conectividad](#252-ejecutar-comprobaciones-públicas-de-conectividad)
    - [2.5.3. Comprobar persistencia, observabilidad y seguridad](#253-comprobar-persistencia-observabilidad-y-seguridad)
  - [2.6. Entregables y verificación de doce factores](#26-entregables-y-verificación-de-doce-factores)
    - [2.6.1. Entregables](#261-entregables)
    - [2.6.2. Tabla de comprobación de los 12 factores](#262-tabla-de-comprobación-de-los-12-factores)
  - [2.7. Infraestructura como código (IaC): gestionar correcciones de configuración con Terraform](#27-infraestructura-como-código-iac-gestionar-correcciones-de-configuración-con-terraform)
    - [2.7.1. Generar un stack Terraform a partir de la infraestructura existente](#271-generar-un-stack-terraform-a-partir-de-la-infraestructura-existente)
    - [2.7.2. Identificar y verificar el parámetro que se necesita corregir](#272-identificar-y-verificar-el-parámetro-que-se-necesita-corregir)
    - [2.7.3. Editar el código del stack y guardar los cambios](#273-editar-el-código-del-stack-y-guardar-los-cambios)
    - [2.7.4. Ejecutar Plan y revisar si Terraform reemplazará la instancia](#274-ejecutar-plan-y-revisar-si-terraform-reemplazará-la-instancia)
    - [2.7.5. Ejecutar Apply y verificar la nueva versión](#275-ejecutar-apply-y-verificar-la-nueva-versión)
- [Fuentes oficiales](#fuentes-oficiales)

## Compatibilidad de terminales y uso de imágenes

| Procedimiento | CMD Windows | PowerShell | Bash (Git Bash/Linux/macOS) | Sin comandos |
|---|---|---|---|---|
| Descargar imágenes Docker | `docker pull` | `docker pull` o bucle `ForEach-Object` | `docker pull` o `for` | Docker Desktop → Images |
| Crear archivos `.env` | Editor de texto | Editor o comandos | Editor o comandos | VS Code → New File |
| Crear red Docker | `docker network create` | Igual | Igual | Depende de la versión de Docker Desktop |
| Ejecutar contenedores | `docker run` en una línea | Igual o variables `$...` | Igual o scripts shell | Docker Desktop (con limitaciones de configuración) |
| Etiquetar/publicar | `docker tag` / `docker push` | Igual o bucles | Igual o bucles | Docker Desktop, si admite el registro |
| Pruebas HTTP | `curl.exe` o REST Client | `curl.exe` o REST Client | `curl` o REST Client | Navegador / REST Client / Postman |

**Regla didáctica:** elija **una** de las dos formas equivalentes para cada paso: **A. variables PowerShell** o **B. referencias completas**. Las variables se declaran al principio de cada fase y se reutilizan en todos sus comandos; no se necesitan bucles. Puede alternar entre ambas si conserva exactamente los mismos nombres y etiquetas. Una captura de terminal es evidencia del resultado, no una exigencia de utilizar un intérprete determinado. Las capturas de la primera fase se conservan como referencias visuales del taller original y no sustituyen los comandos actuales.

## Arquitectura y resultados esperados

| Elemento | Imagen original de referencia | Tecnología | Puerto del contenedor | Rutas / persistencia |
|---|---|---|---|---|
| Frontend | `adanbeltran/ecored-frontend:1.0` | React + Vite, servido por Nginx | `80` | Interfaz web; configuración `VITE_*` en tiempo de ejecución |
| Gateway | `adanbeltran/ecored-local-gateway:1.0` | Node.js HTTP | `8080` | `/health`, `/api/companies`, `/api/materials` y rutas de detalle |
| Empresas | `adanbeltran/ecored-companies:1.0` | Python, Django REST | `8001` | `/api/health`, `/api/companies`, `/api/companies/{company_id}`; MongoDB Atlas |
| Materiales | `adanbeltran/ecored-materials:1.0` | Node.js, Express | `8002` | `/api/health`, `/api/materials`, `/api/materials/{id}`; Oracle Autonomous Database |

- **Autenticación:** Firebase Authentication entrega un ID token al frontend por HTTPS. Gateway valida su firma y crea encabezados internos `X-User-Id` y `X-User-Email`.
- **Interacción entre servicios:** Materiales solicita `GET /api/companies/{company_id}` a Empresas para validar la propiedad de la empresa antes de guardar el material.
- **Una base de datos por responsabilidad:** Empresas usa colección `companies` de MongoDB Atlas; Materiales usa tabla `materials` de Oracle Autonomous Database.
- **No son imágenes intercambiables:** usar un tag compatible con esta versión del contrato API; si se elige una imagen modificada por un compañero, verificar puertos, rutas y requisitos antes de iniciar.

![Diagrama de despliegue didáctico de EcoRed en OCI Container Instance](assets/arquitectura-container-instance.svg)

**Doce factores (marco de referencia).** I Codebase; II Dependencies; III Config; IV Backing services; V Build, release, run; VI Processes; VII Port binding; VIII Concurrency; IX Disposability; X Dev/prod parity; XI Logs; XII Admin processes. En este laboratorio se comprueban especialmente **III, IV, V, VI, VII, X y XI**; los demás se discuten y no se dan por demostrados automáticamente.

## Ruta de las imágenes entre entornos

| Fase | Registro de origen | Responsable de las imágenes | Operación | Entorno de ejecución |
|---|---|---|---|---|
| 1. Local | Docker Hub | Docente u otro compañero | `docker pull` → `docker run` con `.env` propios | Docker Desktop / motor Docker en Windows |
| 2. OCI | Docker Hub → OCIR | Estudiante, imágenes publicadas previamente | `docker pull` → `docker tag` → `docker push` → despliegue | OCI Container Instances |

**Regla del taller:** la imagen se mantiene sin modificar. La configuración local y la configuración OCI se inyectan en tiempo de ejecución; para cada etapa se conservan los mismos contratos de puertos y endpoints. Las imágenes del docente o compañero son **solo de la Fase 1** y las propias, previamente publicadas en Docker Hub, son **solo de la Fase 2**. Se verifican sus etiquetas y su compatibilidad.

**Diagrama del flujo de artefactos:**

```mermaid
flowchart LR
    A[Docker Hub: imágenes de otra persona] -->|docker pull| B[Docker local: Fase 1]
    B --> C[Pruebas con .env propios]
    D[Docker Hub: imágenes propias] -->|docker pull| E[Docker local: retag]
    E -->|docker tag y docker push| F[OCIR]
    F --> G[OCI Container Instances: 4 contenedores]
    G --> H[Pruebas por IP pública]
```

---

# Fase 1. Reproducción y ejecución local desde Docker Hub

**Meta:** desplegar una versión completa de EcoRed **sin acceso al código fuente y sin `docker build`**, configurada con los servicios externos del estudiante. Los pantallazos de esta fase pertenecen al taller original.

## Variables PowerShell de la fase 1 (definir una sola vez)

Abra una terminal **PowerShell** en VS Code y ejecute este bloque **antes de comenzar la Fase 1**. Sustituya el propietario y, si corresponde, el tag por los que compartió el docente o su compañero. Si la terminal se cierra, vuelva a definir las variables.

```powershell
$ORIGEN = 'adanbeltran'       # Cambiar por el usuario Docker Hub del compañero
$TAG = '1.0'
$COMPANIES = "${ORIGEN}/ecored-companies:${TAG}"
$MATERIALS = "${ORIGEN}/ecored-materials:${TAG}"
$GATEWAY = "${ORIGEN}/ecored-local-gateway:${TAG}"
$FRONTEND = "${ORIGEN}/ecored-frontend:${TAG}"
$RED = 'ecored-net'
$CONT_COMPANIES = 'ecored-companies'
$CONT_MATERIALS = 'ecored-materials'
$CONT_GATEWAY = 'ecored-local-gateway'
$CONT_FRONTEND = 'ecored-frontend'
```

**Alternativa sin PowerShell:** no declare variables; utilice la **opción B con nombres completos** de cada paso. Ambas producen el mismo resultado. En CMD las expresiones `$COMPANIES`, `$RED` y similares **no funcionan**.

## 1.1. Selección y descarga

### 1.1.1. Preparar Docker y una carpeta de trabajo

En los computadores del aula (Windows) abra **VS Code → Terminal → New Terminal**, o utilice **Símbolo del sistema (CMD)** o **Windows PowerShell**. Docker CLI funciona desde cualquiera de las tres. Verifique el motor Docker:

```console
docker version
```

**Procedimiento principal, compatible con CMD y PowerShell:** cree con el Explorador de archivos una carpeta llamada `ecored-imagenes` y dentro una carpeta `evidencias`. Ábrala en VS Code mediante **File → Open Folder**. No necesita conocer comandos de creación de carpetas para realizar el taller.

**Alternativas opcionales para automatizar el paso:**

PowerShell:

```powershell
mkdir ecored-imagenes
cd ecored-imagenes
mkdir evidencias
```

CMD (Windows):

```cmd
mkdir ecored-imagenes
cd ecored-imagenes
mkdir evidencias
```

Bash (Git Bash / Linux / macOS):

```bash
mkdir -p ecored-imagenes/evidencias
cd ecored-imagenes
```

> Estos comandos son equivalentes a crear carpetas manualmente. No son necesarios para utilizar Docker; se presentan para comprender cómo automatizar tareas. **No ejecute bloques con sintaxis de PowerShell en CMD ni Bash sin adaptarlos.**

### 1.1.2. Elegir el propietario y descargar las cuatro imágenes

Solicite al docente o a otro equipo las cuatro referencias completas de Docker Hub: repositorio y etiqueta. Las siguientes corresponden a las imágenes de ejemplo; si usa las de un compañero, sustituya **cada nombre** por el publicado por ese equipo.

**Opción A. PowerShell con variables definidas al inicio (un comando por imagen):**

```powershell
docker pull $COMPANIES
docker pull $MATERIALS
docker pull $GATEWAY
docker pull $FRONTEND
docker images
```

**Opción B. Nombres completos (PowerShell, CMD o Bash):**

```console
docker pull adanbeltran/ecored-companies:1.0
docker pull adanbeltran/ecored-materials:1.0
docker pull adanbeltran/ecored-local-gateway:1.0
docker pull adanbeltran/ecored-frontend:1.0
docker images
```

**Importante:** en la opción B sustituya `adanbeltran` y `1.0` por el usuario y etiqueta compartidos cuando la imagen sea de un compañero.

**Opción gráfica:** Docker Desktop → **Images** → buscar la referencia completa → **Pull**, repitiendo para las cuatro imágenes. La interfaz puede variar por versión.

**Optimización opcional, PowerShell:** el siguiente bucle ejecuta los mismos cuatro `docker pull`; sirve para aprender automatización, pero no es obligatorio:

```powershell
@($COMPANIES,$MATERIALS,$GATEWAY,$FRONTEND) | ForEach-Object { docker pull $_ }
```

**Optimización opcional, Bash:**

```bash
ORIGEN=adanbeltran
TAG=1.0
for servicio in ecored-companies ecored-materials ecored-local-gateway ecored-frontend; do
  docker pull "$ORIGEN/$servicio:$TAG"
done
```

![Referencia real del taller original: imágenes descargadas con sus nombres y tamaños en la terminal](assets/image-56.png)

![Referencia real del taller original: verificación de las imágenes en la terminal](assets/image-57.png)

**Evidencia:** las cuatro referencias, etiquetas y autor del repositorio en Docker Hub; resultado de `docker images`. **Factor V:** reutilizar artefactos construidos sin recompilarlos. El mismo principio se repetirá con imágenes propias en la Fase 2.

## 1.2. Servicios externos y variables de entorno

### 1.2.1. Comprobar los backing services propios

Se requiere Firebase Authentication (métodos Email/Password y/o Google), una conexión MongoDB Atlas válida y un Oracle Autonomous Database con tabla `materials`. No utilice claves o contraseñas del docente. En Oracle la tabla y privilegios deben corresponder al usuario que use el microservicio.

**Factor IV:** Firebase, MongoDB y Oracle se usan como recursos externos configurados, no empaquetados en imágenes.

### 1.2.2. Crear los cuatro archivos `.env`

Desde la carpeta `ecored-imagenes`, cree `companies.docker.env`, `materials.docker.env`, `gateway.docker.env` y `frontend.docker.env`. Son **archivos diferentes**; reemplazar los valores `CAMBIAR_...` por credenciales reales sin subirlos a GitHub.

**`companies.docker.env`**

```dotenv
DJANGO_SECRET_KEY=CAMBIAR_CLAVE_ALEATORIA_LARGA
DJANGO_DEBUG=False
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1,ecored-companies
MONGODB_URI=mongodb+srv://USUARIO:CLAVE@CLUSTER.mongodb.net/?retryWrites=true&w=majority
MONGODB_DB_NAME=ecored_companies
```

**`materials.docker.env`**

```dotenv
HOST=0.0.0.0
PORT=8002
ORACLE_USER=ECORED
ORACLE_PASSWORD=CAMBIAR_CLAVE_ORACLE
ORACLE_CONNECT_STRING=CAMBIAR_CONNECT_STRING_ORACLE
COMPANIES_SERVICE_URL=http://ecored-companies:8001/api
```

**`gateway.docker.env`**

```dotenv
HOST=0.0.0.0
PORT=8080
FIREBASE_PROJECT_ID=CAMBIAR_FIREBASE_PROJECT_ID
COMPANIES_URL=http://ecored-companies:8001
MATERIALS_URL=http://ecored-materials:8002
CORS_ORIGINS=http://localhost:8088,http://127.0.0.1:8088
```

**`frontend.docker.env`**

```dotenv
VITE_API_URL=http://127.0.0.1:8080/api
VITE_FIREBASE_API_KEY=CAMBIAR_API_KEY_WEB
VITE_FIREBASE_AUTH_DOMAIN=CAMBIAR_PROYECTO.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=CAMBIAR_FIREBASE_PROJECT_ID
VITE_FIREBASE_APP_ID=CAMBIAR_APP_ID_WEB
```

> **Precisión:** la API key web de Firebase es un identificador de configuración público; en cambio, `DJANGO_SECRET_KEY`, contraseñas y URI con credenciales deben permanecer confidenciales. Las imágenes del docente pueden usar runtime config de Nginx. Verifique que la imagen del compañero haga lo mismo; si incorpora `VITE_*` durante `npm run build`, no será portable sin reconstrucción.

**Opción sin comandos:** en VS Code use **Explorer → New File** para crear cada `.docker.env`, pegue la plantilla correspondiente y guarde con **Ctrl+S**. No agregue comillas alrededor de los valores ni suba los archivos al repositorio.

![Referencia del taller original: archivo `.http` de pruebas abierto en VS Code, no es una captura de archivos `.env`](assets/image-66.png)

**Factores III y X:** la configuración varía por entorno, mientras el artefacto permanece igual. Nunca adjunte `.env` reales como evidencia.

## 1.3. Red y ejecución de cuatro contenedores

### 1.3.1. Crear la red virtual Docker

**Opción A. PowerShell con variables:**

```powershell
docker network create $RED
```

**Opción B. Nombre completo (CMD, PowerShell y Bash):**

```console
docker network create ecored-net
```

![Referencia del taller original: comandos y verificación de la red Docker en terminal](assets/image-55.png)

**Opción sin comandos:** Docker Desktop permite inspeccionar las redes desde **Networks** (según versión). Para garantizar el nombre exacto de la red personalizada, utilice el comando anterior.

**Opciones equivalentes:** el comando `docker network create ecored-net` es idéntico en CMD, PowerShell y Bash.

### 1.3.2. Ejecutar Empresas y Materiales

**Opción A. PowerShell con variables (dos comandos individuales):**

```powershell
docker run -d --name $CONT_COMPANIES --network $RED --env-file companies.docker.env -p 127.0.0.1:8001:8001 $COMPANIES
docker run -d --name $CONT_MATERIALS --network $RED --env-file materials.docker.env -p 127.0.0.1:8002:8002 $MATERIALS
```

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker run -d --name ecored-companies --network ecored-net --env-file companies.docker.env -p 127.0.0.1:8001:8001 adanbeltran/ecored-companies:1.0
docker run -d --name ecored-materials --network ecored-net --env-file materials.docker.env -p 127.0.0.1:8002:8002 adanbeltran/ecored-materials:1.0
```

Reemplace las referencias de `adanbeltran` por las de la otra persona cuando corresponda. **Use solo una opción**, no ejecute ambos bloques: los nombres de contenedor deben ser únicos.

![Pantallazo original: comando de inicio del servicio Empresas](assets/image-59.png)

![Pantallazo original: comando de inicio del servicio Materiales](assets/image-60.png)

**Alternativa gráfica:** Docker Desktop → Images → Run. Puede que la interfaz no exponga todas las opciones de red o `--env-file`; en tal caso utilice uno de los dos bloques anteriores.

### 1.3.3. Ejecutar el gateway y el frontend

**Opción A. PowerShell con variables:**

```powershell
docker run -d --name $CONT_GATEWAY --network $RED --env-file gateway.docker.env -p 127.0.0.1:8080:8080 $GATEWAY
docker run -d --name $CONT_FRONTEND --network $RED --env-file frontend.docker.env -p 127.0.0.1:8088:80 $FRONTEND
docker ps
```

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker run -d --name ecored-local-gateway --network ecored-net --env-file gateway.docker.env -p 127.0.0.1:8080:8080 adanbeltran/ecored-local-gateway:1.0
docker run -d --name ecored-frontend --network ecored-net --env-file frontend.docker.env -p 127.0.0.1:8088:80 adanbeltran/ecored-frontend:1.0
docker ps
```

**Nota:** utilice las imágenes elegidas en el apartado 1.1.2, sin mezclar autores incompatibles.

![Pantallazo original: comando para iniciar el gateway](assets/image-61.png)

![Pantallazo original: comando para iniciar el frontend](assets/image-62.png)

**Opción sin comandos para verificar:** abra **Docker Desktop → Containers** y compruebe que los cuatro aparezcan en ejecución. También puede consultar sus logs desde esa interfaz.

**Importante:** los nombres `ecored-companies` y `ecored-materials` se resuelven solo dentro de la red `ecored-net`. Los puertos 8001, 8002 y 8080 se exponen en la interfaz loopback del computador, no en toda la red del aula. **Factores VI, VII y XI:** procesos independientes, puertos explícitos y logs del contenedor.

## 1.4. Pruebas independientes e integrales

### 1.4.1. Revisar salud, aislamiento y logs

**Opción A. PowerShell con variables:**

```powershell
docker logs --tail 30 $CONT_COMPANIES
docker logs --tail 30 $CONT_MATERIALS
docker logs --tail 30 $CONT_GATEWAY
docker logs --tail 30 $CONT_FRONTEND
```

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker logs --tail 30 ecored-companies
docker logs --tail 30 ecored-materials
docker logs --tail 30 ecored-local-gateway
docker logs --tail 30 ecored-frontend
```

Abra en Visual Studio Code `tests/01-local.http` e instale la extensión **REST Client**. Las rutas de salud deberán responder 200 y las protegidas sin identidad deberán responder 401. Estas pruebas **no sustituyen la autenticación de Firebase**.

**Alternativa sin comandos:** en Docker Desktop abra **Containers → seleccione un contenedor → Logs**. Repita con los cuatro.

![Pantallazo original: archivo de pruebas HTTP abierto en VS Code](assets/image-66.png)

### 1.4.2. Realizar una prueba autenticada de extremo a extremo

Abra `http://localhost:8088`, inicie sesión con un usuario válido en Firebase, registre una empresa, publique un material asociado y compruebe el almacenamiento. También puede ejecutar `tests/02-flujo-firebase.http` mediante REST Client (solicita la API key y un usuario de prueba). En Firebase autorice el dominio local empleado.

![Pantallazo original: formulario de inicio de sesión Firebase](assets/image-63.png)

![Pantallazo original: pantalla de inicio después de autenticarse](assets/image-64.png)

![Pantallazo original: creación de empresa](assets/image-67.png)

![Pantallazo original: publicación de material](assets/image-68.png)

![Pantallazo original: registro de material confirmado](assets/image-69.png)

**Prueba de integración relevante:** Materiales debe validar la empresa a través de la API interna de Empresas antes de persistir el material. El éxito depende de las tres dependencias externas: Firebase, MongoDB y Oracle.

### 1.4.3. Finalizar la reproducción

**Opción A. PowerShell con variables:**

```powershell
docker stop $CONT_FRONTEND $CONT_GATEWAY $CONT_MATERIALS $CONT_COMPANIES
docker rm $CONT_FRONTEND $CONT_GATEWAY $CONT_MATERIALS $CONT_COMPANIES
```

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker stop ecored-frontend ecored-local-gateway ecored-materials ecored-companies
docker rm ecored-frontend ecored-local-gateway ecored-materials ecored-companies
```

**Opción B, Docker Desktop:** vaya a **Containers**, detenga cada contenedor (**Stop**) y, si ya registró las evidencias, elimínelo (**Delete**). La acción no borra las imágenes locales.

**Resultado de Fase 1:** evidencia de funcionamiento integral en Windows desde **imágenes ajenas publicadas en Docker Hub** y configuración propia. No se publica ninguna imagen nueva ni se reconstruye código.

---

# Fase 2. Despliegue en OCI desde imágenes propias de Docker Hub

**Meta:** partir obligatoriamente de las **cuatro imágenes propias previamente publicadas en Docker Hub**, descargarlas, reetiquetarlas para OCIR, publicarlas allí sin modificar su contenido y ejecutar las mismas cuatro imágenes en una Container Instance con IP pública. Esta parte es nueva: las capturas del ZIP original ilustran Docker Hub, no pantallas de OCIR.

## Variables PowerShell de la fase 2 (definir una sola vez)

La **Fase 2 usa imágenes propias**, distintas de las ajenas de la Fase 1. Abra PowerShell y configure las siguientes variables. Recuerde actualizar la región, el namespace y las referencias de las imágenes propias.

```powershell
$MIO = 'MI_USUARIO_DOCKERHUB'
$TAG_PROPIO = '1.0'
$REGISTRY = 'gru.ocir.io'                # Ejemplo; sustituir por la región OCI real
$NAMESPACE = 'MI_NAMESPACE'
$OCI_USUARIO = 'MI_USUARIO_OCI'         # Según el formato exigido por OCI IAM
$BASE = "${REGISTRY}/${NAMESPACE}/ecored"
$PROPIA_COMPANIES = "${MIO}/ecored-companies:${TAG_PROPIO}"
$PROPIA_MATERIALS = "${MIO}/ecored-materials:${TAG_PROPIO}"
$PROPIA_GATEWAY = "${MIO}/ecored-local-gateway:${TAG_PROPIO}"
$PROPIA_FRONTEND = "${MIO}/ecored-frontend:${TAG_PROPIO}"
$OCIR_COMPANIES = "${BASE}/companies:${TAG_PROPIO}"
$OCIR_MATERIALS = "${BASE}/materials:${TAG_PROPIO}"
$OCIR_GATEWAY = "${BASE}/gateway:${TAG_PROPIO}"
$OCIR_FRONTEND = "${BASE}/frontend:${TAG_PROPIO}"
$IP_PUBLICA = 'CAMBIAR_IP_PUBLICA'       # Asignar después del despliegue
```
<img width="1286" height="360" alt="image" src="https://github.com/user-attachments/assets/cac9f5fa-b259-485a-8b68-1c9e847c34c5" />

**Alternativa sin variables:** utilice los nombres completos de la **opción B** de cada paso, reemplazando los marcadores por valores reales. Nunca publique el Auth Token.

## 2.1. Descargar imágenes propias desde Docker Hub

### 2.1.1. Descargar desde Docker Hub las cuatro imágenes propias

**Comando común, válido tanto en PowerShell como en CMD:**

```console
docker images
```

Para recuperar imágenes propias desde Docker Hub, use **A** o **B**:

```powershell
# A. PowerShell con variables
docker pull $PROPIA_COMPANIES
docker pull $PROPIA_MATERIALS
docker pull $PROPIA_GATEWAY
docker pull $PROPIA_FRONTEND
```

```console
# B. Nombres completos (sustituir MI_USUARIO_DOCKERHUB)
docker pull MI_USUARIO_DOCKERHUB/ecored-companies:1.0
docker pull MI_USUARIO_DOCKERHUB/ecored-materials:1.0
docker pull MI_USUARIO_DOCKERHUB/ecored-local-gateway:1.0
docker pull MI_USUARIO_DOCKERHUB/ecored-frontend:1.0
```

**Comprobación obligatoria:** las cuatro imágenes deben estar previamente publicadas en los repositorios **Docker Hub del propio estudiante**, como resultado del Taller 5, y deben descargarse mediante `docker pull` antes de etiquetarse. Si alguna falta, complete la publicación en Docker Hub como prerrequisito **fuera de este taller**; en este taller no se compila ni se utiliza `docker build`. **No etiquete imágenes del docente o de otros equipos como propias.** Mantenga versiones compatibles de frontend, gateway y microservicios.

![Pantallazo original: listado de imágenes disponibles](assets/image-46.png)

**Factores I, II y V:** código propio versionado, dependencias declaradas y artefactos identificables por versión o digest. Anote SHA256 de las imágenes con `docker image inspect --format '{{.Id}}' ...`.

## 2.2. Preparar OCIR

### 2.2.1. Ubicar región, namespace y repositorios

En la consola de OCI seleccione la región adecuada. Vaya a **Developer Services → Containers & Artifacts → Container Registry**, cree repositorios (por ejemplo `ecored/companies`, `ecored/materials`, `ecored/gateway`, `ecored/frontend`). 

<img width="1127" height="348" alt="image" src="https://github.com/user-attachments/assets/cc89b4b1-5ace-4b7e-8d28-d6025b14dd06" />
<img width="668" height="307" alt="image" src="https://github.com/user-attachments/assets/8af8a5eb-ec97-48f0-b033-b8827625860a" />
<img width="651" height="246" alt="image" src="https://github.com/user-attachments/assets/915e6593-1598-474e-a83e-88ed3aa5d9db" />
<img width="663" height="307" alt="image" src="https://github.com/user-attachments/assets/b4ebe82b-131a-4954-90d4-6507d7d7ce20" />
<img width="597" height="296" alt="image" src="https://github.com/user-attachments/assets/bc7189fd-22e2-42cc-b5cf-719315324c68" />

<img width="1310" height="330" alt="image" src="https://github.com/user-attachments/assets/f0e04932-f10d-4108-9a85-d0cfc9f82eba" />

### 2.2.2. Generar un Auth Token e iniciar sesión

En el perfil del usuario de OCI abra **Auth Tokens → Generate Token** y guarde el token de forma segura. 
<img width="1142" height="197" alt="image" src="https://github.com/user-attachments/assets/163ae2ae-5536-4e6f-9392-c318658511f2" />

**Opción A. PowerShell con variables definidas al inicio:**

```powershell
docker login $REGISTRY -u "${NAMESPACE}/${OCI_USUARIO}"
```
<img width="1431" height="132" alt="image" src="https://github.com/user-attachments/assets/228efaf8-a687-4295-8d42-85a5e1e8076c" />

**Opción B. Nombre completo (CMD, PowerShell y Bash):**

```console
docker login gru.ocir.io -u MI_NAMESPACE/MI_USUARIO_OCI
```

La contraseña solicitada por Docker es el **Auth Token de OCI**, no la contraseña de la consola. 

**Alternativa gráfica:** Docker Desktop permite consultar las imágenes, pero la autenticación con OCIR mediante `docker login` es el método reproducible para este taller.

No incluya el token en capturas ni en `.env` públicos.

## 2.3. Etiquetar y publicar en OCIR

### 2.3.1. Asociar las imágenes propias a OCIR

Utilice exclusivamente las imágenes que **usted construyó en el Taller anterior**. Los comandos `docker tag` agregan referencias al mismo contenido, sin reconstruirlo. Estos ejemplos usan el registro `gru.ocir.io`, un namespace y un usuario Docker Hub de ejemplo que debe sustituir.

**Opción A. PowerShell con las variables de la fase 2:**

```powershell
docker tag $PROPIA_COMPANIES $OCIR_COMPANIES
docker tag $PROPIA_MATERIALS $OCIR_MATERIALS
docker tag $PROPIA_GATEWAY $OCIR_GATEWAY
docker tag $PROPIA_FRONTEND $OCIR_FRONTEND
docker images | Select-String "^gru"
```
<img width="1285" height="92" alt="image" src="https://github.com/user-attachments/assets/0b050c80-540e-482e-aa60-4045266d8533" />
<img width="1247" height="216" alt="image" src="https://github.com/user-attachments/assets/479bef5e-9f65-440b-b6d5-944a2933eb69" />

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker tag MI_USUARIO_DOCKERHUB/ecored-companies:1.0 gru.ocir.io/MI_NAMESPACE/ecored/companies:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-materials:1.0 gru.ocir.io/MI_NAMESPACE/ecored/materials:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-local-gateway:1.0 gru.ocir.io/MI_NAMESPACE/ecored/gateway:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-frontend:1.0 gru.ocir.io/MI_NAMESPACE/ecored/frontend:1.0
docker images
```

### 2.3.2. Publicar las cuatro imágenes

**Opción A. PowerShell con variables:**

```powershell
docker push $OCIR_COMPANIES
docker push $OCIR_MATERIALS
docker push $OCIR_GATEWAY
docker push $OCIR_FRONTEND
```
<img width="1122" height="511" alt="image" src="https://github.com/user-attachments/assets/0fdb548e-7aba-406e-82b6-884dada37ca6" />

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker push gru.ocir.io/MI_NAMESPACE/ecored/companies:1.0
docker push gru.ocir.io/MI_NAMESPACE/ecored/materials:1.0
docker push gru.ocir.io/MI_NAMESPACE/ecored/gateway:1.0
docker push gru.ocir.io/MI_NAMESPACE/ecored/frontend:1.0
```

**Optimización opcional en PowerShell:** después de comprender los cuatro comandos, puede automatizarlos:

```powershell
@($OCIR_COMPANIES,$OCIR_MATERIALS,$OCIR_GATEWAY,$OCIR_FRONTEND) | ForEach-Object { docker push $_ }
```

Verifique **en la consola real de OCIR** repositorio, etiqueta y digest de cada imagen. **Factor V:** separar artefacto, publicación y ejecución. Nunca inserte contraseñas en imágenes.
<img width="902" height="242" alt="image" src="https://github.com/user-attachments/assets/4add4e42-edbd-4c2f-b13a-fec02833e57e" />

## 2.4. Red y Container Instance

### 2.4.1. Preparar VCN, subred pública y políticas de acceso

**Objetivo:** verificar y ajustar la infraestructura de red existente en OCI para desplegar los cuatro microservicios de EcoRed mediante Container Instances.

**Infraestructura existente:**

| Recurso | Configuración |
|---|---|
| Región | São Paulo (`sa-saopaulo-1`) |
| Compartment | `Infraestructura_y_Cloud` |
| VCN | `red-infraestructura` |
| CIDR de VCN | `10.0.0.0/16` |
| Subred pública | `subred-web` |
| CIDR de subred | `10.0.0.0/24` |
| Internet Gateway | `salida-internet` |
| Service Gateway | `servicios-oracle` |
| Security List | `Default Security List for red-infraestructura` |
| Route Table | `Default Route Table for red-infraestructura` |

**Importante:** no es necesario crear otra VCN, subred ni gateways. Se reutilizará la infraestructura existente y únicamente se realizarán los ajustes que se describen a continuación.

#### 2.4.1.1. Agregar la regla de entrada TCP/8080

En la consola de OCI:

1. Ingrese a **Networking → Virtual Cloud Networks**.
2. Seleccione `red-infraestructura`.
3. Abra **Security → Security Lists**.
4. Seleccione `Default Security List for red-infraestructura`.
5. Ingrese a **Security rules → Add Ingress Rules**.
6. Configure los siguientes valores:

| Campo | Valor |
|---|---|
| Stateless | No |
| Source Type | CIDR |
| Source CIDR | `0.0.0.0/0` |
| IP Protocol | TCP |
| Source Port Range | All |
| Destination Port Range | `8080` |
| Description | `EcoRed API Gateway` |

<img width="925" height="362" alt="image" src="https://github.com/user-attachments/assets/00177e06-c3ee-420a-b6fd-f9423147448b" />

7. Seleccione **Add Ingress Rules**.

**Verificación:**

La Security List debe permitir los siguientes puertos:

| Puerto | Servicio | Acceso desde Internet |
|---|---|---|
| 80 | Frontend | Permitido |
| 8080 | API Gateway | Permitido |
| 8001 | Companies | No permitido |
| 8002 | Materials | No permitido |
<img width="1680" height="445" alt="image" src="https://github.com/user-attachments/assets/95f6d7e3-726b-49ea-a6ee-c4f324294515" />

**Nota:** el puerto TCP/80 ya está configurado. No es necesario agregarlo nuevamente.

Para simplificar las pruebas académicas se utiliza `0.0.0.0/0`. En un entorno de producción, el acceso debe limitarse a los orígenes necesarios y protegerse mediante HTTPS y controles de autenticación.

No modifique la regla TCP/22 sin verificar previamente si otros recursos de la VCN utilizan SSH.

#### 2.4.1.2. Verificar las reglas de salida

1. Abra **Security → Security Lists**.
2. Seleccione `Default Security List for red-infraestructura`.
3. Revise **Egress Rules**.
4. Verifique que exista la siguiente configuración:

| Campo | Valor |
|---|---|
| Stateless | No |
| Destination | `0.0.0.0/0` |
| IP Protocol | All Protocols |
| Source Port Range | All |
| Destination Port Range | All |

<img width="1656" height="681" alt="image" src="https://github.com/user-attachments/assets/a8ce26c3-1979-4c0d-a306-42ce8d4ca0e5" />

Esta regla ya existe en la infraestructura actual, por lo que no es necesario modificarla.

Permite establecer conexiones salientes hacia servicios como Firebase y MongoDB Atlas. Para acceder a estos servicios también deben estar configuradas sus respectivas credenciales, reglas de acceso y conexiones seguras.

**Nota:** las reglas anteriores corresponden a la Security List existente. Verifique que `subred-web` tenga asociada esta lista y que no existan restricciones adicionales en algún Network Security Group (NSG).

#### 2.4.1.3. Verificar la subred pública

1. Ingrese a **Networking → Virtual Cloud Networks**.
2. Seleccione `red-infraestructura`.
3. Abra **Subnets**.
4. Seleccione `subred-web`.
5. Verifique:

| Propiedad | Valor |
|---|---|
| Estado | Available |
| Subnet Access | Public Subnet |
| IPv4 CIDR | `10.0.0.0/24` |
| Route Table | `Default Route Table for red-infraestructura` |
<img width="1162" height="301" alt="image" src="https://github.com/user-attachments/assets/2774e429-6e9b-46b7-a957-d3abd74cbeef" />

No es necesario modificar la subred.

#### 2.4.1.4. Verificación final

Antes de continuar con la creación de la Container Instance, confirme:

- [ ] VCN `red-infraestructura` disponible.
- [ ] Subred pública `subred-web` disponible.
- [ ] Internet Gateway `salida-internet` disponible.
- [ ] Ruta `0.0.0.0/0` hacia Internet Gateway.
- [ ] Regla de entrada TCP/80 habilitada.
- [ ] Regla de entrada TCP/8080 habilitada.
- [ ] Puertos TCP/8001 y TCP/8002 sin exposición pública.
- [ ] Regla de salida habilitada.
- [ ] Security List asociada correctamente a `subred-web`.
- [ ] Internet Gateway y tabla de rutas existentes conservados sin cambios.

**Precisión sobre Service Gateway:** `servicios-oracle` existe en la VCN, pero su existencia no garantiza una ruta desde `subred-web`. En la infraestructura de referencia, OCI rechazó agregar `All GRU Services In Oracle Services Network` a la misma tabla que contiene `0.0.0.0/0 → salida-internet`. **No elimine la ruta a Internet ni afirme que OCIR está accesible solo porque el gateway existe.** La prueba del apartado 2.4.4 confirmará si la Container Instance puede descargar una imagen privada con las rutas y los permisos actuales. Si falla por conectividad, revise una topología de rutas compatible según la documentación de OCI, sin modificar a ciegas la red compartida.

**Resultado esperado:** la Security List permite acceso público a TCP/80 y TCP/8080; los microservicios 8001/8002 no cuentan con reglas de ingreso público. La conectividad hacia OCIR y el acceso a sus imágenes privadas se validarán en los siguientes apartados.

**Continuación:** verifique las imágenes y la autorización IAM en 2.4.2, prepare el DNS en 2.4.3 y realice una prueba mínima de descarga en 2.4.4 antes del despliegue completo.

### 2.4.2. Verificar imágenes y configurar permisos IAM para OCIR

Antes de crear la Container Instance definitiva, se comprobará que las imágenes privadas existen y que el servicio OCI tiene autorización para descargarlas. **Iniciar sesión como administrador no transfiere los permisos del usuario a la identidad de la Container Instance.**

#### 2.4.2.1. Confirmar los repositorios y las etiquetas

En **Developer Services → Containers & Artifacts → Container Registry**, seleccione el compartment `Infraestructura_y_Cloud` y compruebe **Image versions** en cada repositorio:

| Repositorio privado | Referencia de imagen en la región GRU |
|---|---|
| `ecored/frontend` | `gru.ocir.io/MI_NAMESPACE/ecored/frontend:1.0` |
| `ecored/gateway` | `gru.ocir.io/MI_NAMESPACE/ecored/gateway:1.0` |
| `ecored/companies` | `gru.ocir.io/MI_NAMESPACE/ecored/companies:1.0` |
| `ecored/materials` | `gru.ocir.io/MI_NAMESPACE/ecored/materials:1.0` |

Reemplace `MI_NAMESPACE` por el **namespace del tenancy** configurado en el apartado 2.2.1. Confirme que la etiqueta Docker `1.0` esté asociada a una versión disponible. La pestaña **Tags** de metadatos OCI no equivale a las etiquetas de versión de una imagen Docker.

#### 2.4.2.2. Crear el Dynamic Group para Container Instances

1. Abra **Identity & Security → Compartments → Infraestructura_y_Cloud** y copie el **OCID del compartimento**.
2. Abra **Identity & Security → Domains → Default → Dynamic Groups**.
3. Seleccione **Create dynamic group**.
4. Nombre: `dg-ecored-container`.
5. Descripción: `Autorizar Container Instances para descargar imágenes privadas desde OCIR`.
6. Seleccione el editor manual de **Matching rules** y escriba:

```text
ALL {resource.type='computecontainerinstance', resource.compartment.id='OCID_COMPARTIMENTO'}
```

7. Sustituya `OCID_COMPARTIMENTO` por el OCID real de `Infraestructura_y_Cloud` y seleccione **Create**.

**Nota:** si el grupo ya existe con esta regla, compruébelo y reutilícelo. No modifique un grupo generado por otros servicios, por ejemplo, `VBStudio-Dynamic-Group-*`.

#### 2.4.2.3. Crear la política IAM para leer los repositorios

1. Abra **Identity & Security → Policies → Create Policy**.
2. Cree la política en el **compartment raíz del tenancy**.
3. Nombre: `policy-ecored-ocir`.
4. Descripción: `Permitir a Container Instances leer imágenes privadas OCIR`.
5. En **Show manual editor**, escriba:

```text
Allow dynamic-group dg-ecored-container to read repos in compartment Infraestructura_y_Cloud
```

6. Guarde la política y confirme que figure en la lista. Si utiliza un dominio de identidad distinto de `Default`, ajuste la referencia del grupo según su dominio.

**Verificación:** el Dynamic Group identifica instancias `computecontainerinstance` del compartment y la política les concede el permiso `read repos`. Si la política ya existe, no la duplique. Puede existir un breve período de propagación de las políticas IAM.

**Referencia oficial:** https://docs.oracle.com/en-us/iaas/Content/container-instances/permissions/policy-reference.htm

### 2.4.3. Preparar un subdominio público con DuckDNS

La dirección IPv4 pública de una Container Instance se conoce **después** de su creación. Por ello, no debe escribirse una IP aún desconocida dentro de las variables de entorno del frontend. Para mantener una URL estable sin reconstruir imágenes se utilizará un subdominio DNS dinámico.

1. Ingrese a https://www.duckdns.org/ e inicie sesión.
2. En **Domains**, registre un subdominio disponible; el ejemplo del taller es `ecored-lab.duckdns.org`.
3. Anote el nombre del subdominio. No comparta ni capture el **token privado de DuckDNS**.
4. Antes de crear los contenedores, configure las variables públicas:

**Frontend:**

```dotenv
VITE_API_URL=http://ecored-lab.duckdns.org:8080/api
```

**Gateway:**

```dotenv
CORS_ORIGINS=http://ecored-lab.duckdns.org
```

5. Cree la Container Instance con **Assign a public IPv4 address**.
6. Una vez finalizada la creación, copie la IP pública asignada en OCI y actualice **Current IP → Update IP** para ese subdominio en DuckDNS.
7. Compruebe en PowerShell:

```powershell
nslookup ecored-lab.duckdns.org
```

**Explicación:** `VITE_API_URL` se ejecuta en el **navegador**, por lo que debe utilizar el nombre DNS o una dirección pública accesible desde el equipo del usuario. En cambio, `COMPANIES_URL=http://127.0.0.1:8001` y `MATERIALS_URL=http://127.0.0.1:8002` son conexiones **internas** entre contenedores de la misma Container Instance. El origen CORS del frontend en HTTP/80 no lleva `:8080`.

**Limitación:** DuckDNS resuelve el nombre de dominio, pero **no proporciona HTTPS a la aplicación automáticamente**. El acceso HTTP permite pruebas de salud y conectividad; para iniciar sesión con credenciales reales mediante Firebase, configure un punto de entrada HTTPS y autorice el dominio en Firebase Authentication. La configuración HTTP del laboratorio no debe utilizarse para transmitir contraseñas o tokens reales.

### 2.4.4. Verificar OCIR con una instancia mínima (prueba recomendada)

Antes de introducir todas las variables de entorno, puede comprobar la descarga de imágenes privadas utilizando **una sola imagen**.

1. Abra **Developer Services → Containers & Artifacts → Container Instances → Create Container Instance**.
2. Indique el nombre `ecored-prueba-ocir`, compartment `Infraestructura_y_Cloud`, una shape admitida y recursos mínimos compatibles con la imagen.
3. En **Networking**, seleccione `red-infraestructura` y `subred-web`, active **Assign a public IPv4 address** y no seleccione NSG adicional si utiliza la Security List existente.
4. Agregue un único contenedor `frontend`, seleccionado desde **OCI Container Registry → ecored/frontend → 1.0**. Utilice los permisos IAM creados en 2.4.2.
5. Cree la instancia y consulte **Work requests → Messages** y **Containers**.

| Resultado | Interpretación |
|---|---|
| Container Instance en `Active` y contenedor en ejecución | OCIR permitió descargar la imagen y el proceso inició |
| `Failed` con error *image could not be pulled* | Revise referencia, IAM y conectividad al registro |
| `Active` pero React muestra variables faltantes | La descarga se logró; faltan variables de configuración de la aplicación |

En esta prueba puede omitirse el `.env`, pues solo se comprueba el acceso a la imagen privada. **El estado `Active` no demuestra que Firebase, Atlas u Oracle Database funcionen.** Conserve las evidencias y elimine la instancia de prueba después de verificarlas para evitar costos.

### 2.4.5. Crear una Container Instance con cuatro contenedores

En **Developer Services → Containers & Artifacts → Container Instances → Create Container Instance**, utilice las pantallas **Basic information → Networking → Storage → Containers → Review**.

1. **Basic information:** nombre `ecored-lab`, compartment `Infraestructura_y_Cloud`, Availability Domain `AD-1` cuando corresponda, shape `CI.Standard.E4.Flex`, OCPU `1` y memoria `16 GB` cuando la cuota y el presupuesto lo permitan. Configure la política de reinicio `Always` y el tiempo de apagado ordenado según el laboratorio.
2. **Networking:** seleccione la VCN `red-infraestructura`, subred pública `subred-web`, dirección IPv4 privada automática y **Assign a public IPv4 address**. La opción **Use network security groups to control traffic** puede quedar desactivada si se emplean las reglas ya existentes de la Security List.
3. **Storage:** para este taller no es obligatorio adjuntar almacenamiento; MongoDB Atlas y Oracle Autonomous Database realizan la persistencia externa.
4. **Containers:** agregue los cuatro contenedores desde los repositorios privados OCIR. Seleccione el tag `1.0` y utilice los permisos IAM del Dynamic Group; no marque **Provide credentials manually** si el acceso mediante IAM ya funciona.

| Nombre | Repositorio OCIR | Puerto interno |
|---|---|---|
| `frontend` | `ecored/frontend:1.0` | TCP/80 |
| `gateway` | `ecored/gateway:1.0` | TCP/8080 |
| `companies` | `ecored/companies:1.0` | TCP/8001 |
| `materials` | `ecored/materials:1.0` | TCP/8002 |

5. En **Environmental variables**, agregue individualmente la configuración de cada contenedor conforme al apartado **2.5.1**, **antes de seleccionar Create**. No incluya el carácter `=` en el campo **Key** de la consola; escriba, por ejemplo, `ORACLE_USER` en **Key** y su valor en **Value**.
6. **Review:** compruebe nombres, repositorios, tags, subred, variables, CPU y memoria; luego seleccione **Create**.
7. En **Container Instances**, confirme que `ecored-lab` esté en estado `Active`; en **Containers**, compruebe el estado de los cuatro procesos y revise sus logs. Si falla la descarga de alguna imagen, examine **Work requests → Error messages** antes de cambiar variables de aplicación.

**Red:** los cuatro contenedores comparten el espacio de red de la Container Instance. Los puertos TCP/80 y TCP/8080 pueden atender solicitudes externas según la Security List; **no** agregue reglas de ingreso público para TCP/8001 ni TCP/8002. Compartir una instancia no implica escalado independiente ni alta disponibilidad.

**Advertencia sobre variables:** OCI Container Instances no ofrece una modificación directa de las variables de entorno mediante su operación estándar de actualización. Para aplicar una corrección en esas variables, revise el procedimiento de **IaC con Terraform y Resource Manager del apartado 2.7**; puede requerir el reemplazo de la instancia. No basta con reiniciarla.

**Capturas de referencia de la creación y configuración en OCI:**

<img width="1140" height="716" alt="image" src="https://github.com/user-attachments/assets/92abb375-c74c-4eeb-bee8-05f2a8f7b7a3" />


<img width="525" height="632" alt="image" src="https://github.com/user-attachments/assets/8f779287-0b17-4806-8035-0047a4fc0743" />

<img width="643" height="372" alt="image" src="https://github.com/user-attachments/assets/3553f65a-eb59-4422-a2bb-c3d6693f706c" />
<img width="995" height="477" alt="image" src="https://github.com/user-attachments/assets/076bb5e1-bfe5-479f-84ac-76359ac43a66" />

<img width="1522" height="802" alt="image" src="https://github.com/user-attachments/assets/bcd0aecb-3a62-4463-9875-c99d96780163" />

<img width="1053" height="522" alt="image" src="https://github.com/user-attachments/assets/ab786b0d-1ef9-4b68-91b5-a0112678bee8" />

<img width="1093" height="692" alt="image" src="https://github.com/user-attachments/assets/39d455fc-99f3-4fb0-a0e4-95705e7e9fd0" />

<img width="1035" height="682" alt="image" src="https://github.com/user-attachments/assets/bdc73690-2d33-48ca-95b2-91d526251658" />

<img width="1082" height="717" alt="image" src="https://github.com/user-attachments/assets/d2ab2500-78a8-4c92-88c8-d4225b72be4e" />

<img width="1121" height="678" alt="image" src="https://github.com/user-attachments/assets/867feb80-9c0f-4d15-8516-b4261b853142" />

<img width="1188" height="513" alt="image" src="https://github.com/user-attachments/assets/52335e6f-09bf-4daf-b982-96a03e6b9175" />



![Diagrama propio del despliegue en Container Instances; no corresponde a una captura de la consola OCI](assets/arquitectura-container-instance.svg)

**Factor VIII:** la separación por contenedores no implica escalado horizontal automático; la instancia comparte límites de recursos y reinicio. Para alta disponibilidad y escalado independiente haría falta otro diseño.

## 2.5. Inyectar variables y probar la IP pública

### 2.5.1. Configurar las variables en OCI, sin reconstruir imágenes

En la sección de variables de entorno de **cada contenedor** configure lo siguiente, sustituyendo los marcadores por valores reales. **No copie el `.env` local sin adaptar las URLs**. Los contenedores de una misma Container Instance comparten espacio de red, por lo que sus procesos pueden conectarse mediante `127.0.0.1` y sus puertos internos.

| Contenedor | Variables determinantes en OCI |
|---|---|
| `companies` | `PORT=8001`, `DJANGO_SECRET_KEY`, `DJANGO_DEBUG=False`, `DJANGO_ALLOWED_HOSTS=127.0.0.1,localhost`, `MONGODB_URI`, `MONGODB_DB_NAME` |
| `materials` | `HOST=0.0.0.0`, `PORT=8002`, `ORACLE_USER`, `ORACLE_PASSWORD`, `ORACLE_CONNECT_STRING`, `COMPANIES_SERVICE_URL=http://127.0.0.1:8001/api` |
| `gateway` | `HOST=0.0.0.0`, `PORT=8080`, `FIREBASE_PROJECT_ID`, `FIREBASE_JWKS_URL` (si aplica), `COMPANIES_URL=http://127.0.0.1:8001`, `MATERIALS_URL=http://127.0.0.1:8002`, `CORS_ORIGINS=http://ecored-lab.duckdns.org` |
| `frontend` | `VITE_API_URL=http://ecored-lab.duckdns.org:8080/api`, `VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_AUTH_DOMAIN`, `VITE_FIREBASE_PROJECT_ID`, `VITE_FIREBASE_APP_ID` |

**Comprobación obligatoria de Firebase:** en **Firebase Console → Project settings → General → Your apps → SDK setup and configuration → Config**, copie la clave web `apiKey` vigente y compruebe que corresponda al mismo `projectId`. En **Google Cloud → APIs & Services → Credentials**, revise que no esté eliminada ni bloqueada por restricciones incompatibles. Un `API_KEY_INVALID` indica que Firebase rechazó la API Key y debe diagnosticarse antes de reconstruir o reemplazar la instancia.

**Atención con el acceso público:** el frontend se ejecuta en el navegador del estudiante, por lo que `VITE_API_URL` **no** debe apuntar a `127.0.0.1`. El script `40-runtime-config.sh` de la imagen EcoRed publica las variables `VITE_*` recibidas al iniciar Nginx en `/runtime-config.js`, sin compilar otra vez. El DNS puede actualizarse después de recibir la IP pública; **las variables de entorno de la Container Instance no pueden corregirse mediante un simple reinicio**. Para modificar sus valores use el flujo de Terraform del apartado 2.7.

**Limitación de la demostración:** HTTP por IP pública **no ofrece cifrado**; evite introducir contraseñas o tokens reales en una conexión HTTP no protegida. Para demostrar el flujo completo de autenticación desde el navegador, configure **DNS y HTTPS**, junto con el dominio autorizado en Firebase (por ejemplo, mediante un balanceador con TLS). Una IP pública HTTP permite comprobar conectividad, frontend, `/health` y el rechazo `401`, pero **no garantiza** que los proveedores de identidad permitan iniciar sesión: en particular, no suponga que el acceso mediante Google funciona sobre una IP HTTP. Si no dispone de DNS/HTTPS, documente esta limitación y utilice pruebas de conectividad y de backend con usuarios/datos de ensayo, sin exponer credenciales.

### 2.5.2. Ejecutar comprobaciones públicas de conectividad

<img width="1557" height="700" alt="image" src="https://github.com/user-attachments/assets/acf49e7f-2863-41a7-8255-fa29f3378224" />

<img width="1325" height="835" alt="image" src="https://github.com/user-attachments/assets/f3e387f7-86c6-4198-bc29-fdf77b2ebe05" />

**Opción A. PowerShell con IP pública (prueba directa de red):**

```powershell
$IP_PUBLICA = 'CAMBIAR_IP_PUBLICA'  # IP real mostrada por OCI
Test-NetConnection $IP_PUBLICA -Port 80
Test-NetConnection $IP_PUBLICA -Port 8080
curl.exe -i "http://${IP_PUBLICA}:8080/health"
curl.exe -i "http://${IP_PUBLICA}:8080/api/companies"
```

**Opción B. PowerShell con DuckDNS (prueba del acceso público previsto):**

```powershell
nslookup ecored-lab.duckdns.org
Test-NetConnection ecored-lab.duckdns.org -Port 80
Test-NetConnection ecored-lab.duckdns.org -Port 8080
curl.exe -i http://ecored-lab.duckdns.org:8080/health
Start-Process http://ecored-lab.duckdns.org/
```

**Resultado esperado:** código HTTP **200** para `/health` y **401** para `/api/companies` sin token. Si DuckDNS resuelve a una IP antigua, actualice **Current IP** desde su cuenta de DuckDNS. En CMD y Bash puede utilizar `curl` con las mismas URLs (en PowerShell se utiliza `curl.exe` para evitar el alias).

Para realizar las pruebas del archivo `tests/03-oci-publica.http`, configure su variable de destino con el host público real. Verificar `401` sin token demuestra que la ruta está protegida, pero **no demuestra autenticación integral ni persistencia**. Para probar la autenticación real con Firebase, utilice un punto de entrada HTTPS y el dominio autorizado en Firebase.

![Pantallazo original: acceso a aplicación autenticada local. Repita la verificación desde la IP pública, no se trata de una captura OCI](assets/image-64.png)

![Pantallazo original: empresa guardada; reproduzca el resultado desde la IP pública](assets/image-67.png)

![Pantallazo original: material guardado; reproduzca el resultado desde la IP pública](assets/image-69.png)

### 2.5.3. Comprobar persistencia, observabilidad y seguridad

- Verifique que Empresas consulta MongoDB Atlas y que Materiales escribe en Oracle Autonomous Database, sin copiar bases de datos dentro de imágenes.
- Consulte logs de cada contenedor desde la consola OCI, sin imprimir tokens ni contraseñas. **Factor XI:** registros como flujos de eventos del proceso.
- Confirme que `http://IP_PUBLICA:8001` y `:8002` **no son accesibles** desde fuera, mientras el Gateway sí responde según las reglas establecidas.
- Si una variable de entorno requiere corrección (por ejemplo, `VITE_FIREBASE_API_KEY`), ajuste la definición declarativa con Terraform en **Resource Manager**, revise el `Plan` y ejecute `Apply` solo si el reemplazo propuesto está controlado. No se soluciona con un simple reinicio. **Factores III, V y X**; consulte el apartado 2.7.
- Termine o elimine la Container Instance al acabar el taller para evitar cobros; confirme en la consola el estado de los recursos.

## 2.6. Entregables y verificación de doce factores

### 2.6.1. Entregables

Un PDF breve con las capturas solicitadas y explicación técnica de cada una, **sin credenciales ni tokens**. Debe contener: imágenes ajenas y su origen, `docker images`, contenedores locales en ejecución, pruebas HTTP y registro de empresa/material; imágenes propias verificadas primero en Docker Hub y después publicadas en los cuatro repositorios OCIR (URL, tag y digest), configuración de OCI Container Instances con cuatro contenedores, reglas de la Security List (o NSG si se utiliza), política IAM de lectura OCIR, DNS de DuckDNS, IP pública, pruebas HTTP y evidencia de persistencia. Adjunte los archivos de pruebas `.http` sin claves y enlace a los repositorios propios del Taller 5. No entregue un `.env` con datos reales.

### 2.6.2. Tabla de comprobación de los 12 factores

| Factor | Aplicación en EcoRed | Estado a demostrar |
|---|---|---|
| I. Codebase | Los componentes propios están versionados en Git | Evidencia del repositorio propio en Taller 5 |
| II. Dependencies | `requirements.txt` y `package.json` con dependencias declaradas | Ya desarrollado en Taller 5 |
| III. Config | `.env` local y variables OCI, fuera de imágenes | **Prueba práctica en ambas fases** |
| IV. Backing services | Firebase, Atlas, Autonomous Database externos | **Prueba con persistencia real** |
| V. Build, release, run | Descargar imágenes ya construidas de Docker Hub, reetiquetar, publicar en OCIR y desplegar | **Prueba práctica** |
| VI. Processes | Gateway, frontend y dos APIs como procesos separados | **Prueba práctica**; revisar estado compartido |
| VII. Port binding | Publicación explícita de 80/8080 y puertos internos 8001/8002 | **Prueba práctica** |
| VIII. Concurrency | Posibilidad de ampliar procesos/instancias; no escalado automático demostrado | **Análisis**, sin atribuir autoscaling |
| IX. Disposability | Arranque, parada y reinicio; comprobar recuperación ante fallos | **Prueba opcional** |
| X. Dev/prod parity | Misma imagen, diferente configuración externa | **Prueba práctica**, diferencias documentadas |
| XI. Logs | `docker logs` local y logs en OCI | **Prueba práctica** |
| XII. Admin processes | Tareas administrativas puntuales y separadas de la aplicación | **Análisis**; no demostrado por el despliegue |

**Criterio de aprobación:** no basta con capturas de repositorios; debe demostrarse la reproducción local con imágenes ajenas de Docker Hub y el despliegue en OCI desde imágenes propias de Docker Hub, con evidencias de `docker pull`, `docker tag`, `docker push`, permisos IAM de acceso a OCIR, resolución DNS, acceso público y respuestas HTTP. La autenticación integral desde el navegador en OCI exige un origen admitido por Firebase y transporte seguro; cuando se utilice únicamente IP HTTP, documente explícitamente esa limitación en vez de presentar un inicio de sesión no verificado como exitoso.

## 2.7. Infraestructura como código (IaC): gestionar correcciones de configuración con Terraform

La **infraestructura como código (*Infrastructure as Code*, IaC)** consiste en describir los recursos tecnológicos mediante archivos declarativos que pueden versionarse, revisarse y ejecutarse de forma reproducible. En este taller, **Terraform** describe la OCI Container Instance, las referencias a las imágenes OCIR, los recursos de CPU y memoria, la subred y las variables de entorno de los cuatro contenedores. **OCI Resource Manager** ejecuta Terraform a través de un *stack* y de trabajos (*jobs*) de tipo **Plan** y **Apply**. Esta práctica facilita corregir configuraciones sin reconstruir las imágenes Docker y permite observar cuándo un cambio exige reemplazar la instancia.

### 2.7.1. Generar un stack Terraform a partir de la infraestructura existente

1. En OCI abra **Developer Services → Resource Manager → Stacks**.
2. Seleccione **Create stack** y, en el origen de la configuración, elija **Existing compartment**.
3. Región: **Brazil East (São Paulo)**; compartimento de origen: `Infraestructura_y_Cloud`.
4. Si aparece **Services to discover**, seleccione los servicios requeridos. Para centrar el ejercicio en el despliegue, priorice **Container Instances**; incorporar red, OCIR u otros servicios puede ampliar innecesariamente el alcance del stack.
5. Nombre sugerido: `stack-ecored-terraform`.
6. Revise las opciones y seleccione **Create**. Al finalizar la generación, abra el stack y compruebe **Terraform configuration**, **View state** y **Jobs**.
7. Descargue una copia de la configuración y consérvela como referencia. **No publique el estado Terraform ni variables confidenciales**: pueden contener contraseñas y URI de bases de datos.

**Importante:** Resource Discovery captura infraestructura existente. Antes de ejecutar `Apply`, confirme si el stack contiene solamente la Container Instance de EcoRed o también otros recursos del compartimento. La configuración generada puede necesitar ajustes y no debe aplicarse automáticamente.

### 2.7.2. Identificar y verificar el parámetro que se necesita corregir

Si aparece `API_KEY_INVALID` al iniciar sesión en EcoRed:

1. En **Firebase Console → Project settings → General → Your apps → Config**, consulte el `apiKey` asociado al proyecto Firebase utilizado por el frontend.
2. En **Google Cloud → APIs & Services → Credentials**, verifique que la clave exista y que sus restricciones sean compatibles con Firebase Authentication.
3. Abra `http://ecored-lab.duckdns.org/runtime-config.js` para consultar la configuración que recibió el frontend desplegado. Compare `VITE_FIREBASE_API_KEY` y el `projectId` con los valores de Firebase.
4. Si la clave es incorrecta, corríjala en el código Terraform según el apartado siguiente. **No suponga que cualquier error de inicio de sesión se soluciona reemplazando la Container Instance**.

Documentación de Firebase: https://firebase.google.com/docs/projects/api-keys

### 2.7.3. Editar el código del stack y guardar los cambios

1. Abra el stack generado en **Resource Manager → Stacks**.
2. Seleccione **Edit → Edit Terraform configuration in code editor**.
3. Busque el recurso `oci_container_instances_container_instance` que representa `ecored-lab` y, dentro de su contenedor `frontend`, localice el mapa `environment_variables`.
4. Modifique únicamente el valor necesario. El siguiente fragmento **ilustra** el cambio; el nombre y la estructura deben coincidir con el HCL realmente generado:

```hcl
containers {
  display_name = "frontend"
  image_url    = "gru.ocir.io/MI_NAMESPACE/ecored/frontend:1.0"

  environment_variables = {
    VITE_API_URL              = "http://ecored-lab.duckdns.org:8080/api"
    VITE_FIREBASE_API_KEY     = "API_KEY_CORREGIDA"
    VITE_FIREBASE_AUTH_DOMAIN = "PROYECTO.firebaseapp.com"
    VITE_FIREBASE_PROJECT_ID  = "PROYECTO"
    VITE_FIREBASE_APP_ID      = "APP_ID"
  }
}
```

5. Verifique que permanecen las variables de los otros tres contenedores y que no se alteran accidentalmente la VCN, la subred, las imágenes o la shape.
6. En el árbol del **Code Editor**, haga clic derecho en el stack y seleccione **Save changes**. Como alternativa, puede descargar la configuración, editarla localmente y cargar el ZIP actualizado mediante **Edit** del stack.

**Seguridad:** utilice valores de ejemplo en los materiales que se suben a GitHub. El estado Terraform puede conservar secretos aunque los parámetros de entrada se marquen como `sensitive`. Si una clave privada se ha expuesto en capturas, rótela antes de utilizarla.

### 2.7.4. Ejecutar Plan y revisar si Terraform reemplazará la instancia

1. Abra el stack actualizado y seleccione **Plan** o, desde Code Editor, **Save changes and run Plan action**.
2. Abra **Jobs → Logs** y revise los recursos que Terraform propone crear, modificar o eliminar.
3. Para cambiar variables de entorno en OCI Container Instances, **espere que el proveedor pueda proponer reemplazar la Container Instance completa** (`-/+`, *must be replaced*). No asuma que reiniciar la instancia actualizará las variables.
4. **No ejecute Apply** si el plan incluye cambios inesperados en otras máquinas, VCN, subredes, repositorios o políticas IAM. Si el stack fue generado desde todo el compartimento, revise especialmente su alcance.

| Resultado de `Plan` | Interpretación | Acción |
|---|---|---|
| `No changes` | Terraform no detecta diferencias con la configuración declarada | Compruebe que guardó la corrección y que no existe un `ignore_changes` que la oculte |
| `-/+` sobre `ecored-lab` | Reemplazo de la Container Instance para aplicar la nueva configuración | Proceda únicamente si acepta interrupción y ha validado el resto del plan |
| Cambios sobre otros recursos | El stack tiene un alcance más amplio que la corrección | Detenga el proceso y depure el código o use un stack independiente |

**No es necesario eliminar manualmente la instancia antes de generar el plan.** Terraform puede planificar su reemplazo, manteniendo trazabilidad del cambio.

### 2.7.5. Ejecutar Apply y verificar la nueva versión

1. Tras revisar el plan y aceptar los cambios, seleccione **Apply** y utilice, cuando esté disponible, el plan aprobado.
2. En **Jobs**, espere el estado `Succeeded`; revise los logs si el despliegue falla.
3. Compruebe que `ecored-lab` se encuentre en `Active` y sus cuatro contenedores estén ejecutándose.
4. **La nueva instancia puede recibir otra IP pública.** En DuckDNS actualice **Current IP** de `ecored-lab.duckdns.org`.
5. Verifique `nslookup ecored-lab.duckdns.org`, `http://ecored-lab.duckdns.org:8080/health` y `/runtime-config.js`. Para demostrar autenticación real con Firebase utilice HTTPS y un dominio autorizado.
6. Conserve la evidencia del Plan anterior, el Apply, el estado de la nueva instancia y la variable corregida, sin mostrar credenciales.

**Precaución:** nunca utilice **Destroy** sobre un stack que también administre infraestructura compartida. Si el objetivo es crear una **segunda Container Instance con otro nombre**, prepare un nuevo stack con **estado Terraform independiente** y verifique que su primer Plan solo agregue el recurso previsto, sin reemplazar `ecored-lab`.

**Referencias oficiales:**

- https://docs.oracle.com/en-us/iaas/Content/ResourceManager/Tasks/create-stack-compartment.htm
- https://docs.oracle.com/en-us/iaas/Content/ResourceManager/Tasks/code-editor.htm
- https://docs.oracle.com/en-us/iaas/Content/ResourceManager/Tasks/update-stack-tf-config.htm
- https://docs.oracle.com/en-us/iaas/tools/terraform-provider-oci/latest/docs/r/container_instances_container_instance.html

---

## Fuentes oficiales

- Wiggins, A. (2017). *The Twelve-Factor App*. https://12factor.net/
- Oracle. *Creating a Container Instance*. https://docs.oracle.com/en-us/iaas/Content/container-instances/creating-a-container-instance.htm
- Oracle. *Preparing for Container Registry*. https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryprerequisites.htm
- Oracle. *Overview of Container Instances*. https://docs.oracle.com/en-us/iaas/Content/container-instances/overview-of-container-instances.htm
- Oracle. *Pushing Images Using the Docker CLI*. https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrypushingimagesusingthedockercli.htm
- Oracle. *Getting an Auth Token*. https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrygettingauthtoken.htm
- Oracle. *Streamline the management of your containerized applications using OCI Container Instances*. https://docs.oracle.com/en/solutions/oci-container-instances-for-containerized-applications/
- Docker. *Docker run reference*. https://docs.docker.com/reference/cli/docker/container/run/
- Oracle. *Container Instances IAM Policies*. https://docs.oracle.com/en-us/iaas/Content/container-instances/permissions/policy-reference.htm
- Oracle. *Creating a Stack from an Existing Compartment*. https://docs.oracle.com/en-us/iaas/Content/ResourceManager/Tasks/create-stack-compartment.htm
- Oracle. *Editing a Configuration Using Code Editor*. https://docs.oracle.com/en-us/iaas/Content/ResourceManager/Tasks/code-editor.htm
- Oracle. *OCI Terraform Provider: Container Instance*. https://docs.oracle.com/en-us/iaas/tools/terraform-provider-oci/latest/docs/r/container_instances_container_instance.html
- Firebase. *Learn about and manage API keys for Firebase*. https://firebase.google.com/docs/projects/api-keys
- DuckDNS. *Dynamic DNS service*. https://www.duckdns.org/
- Repositorio de código: https://github.com/adanbeltran/ecored-microservicios
