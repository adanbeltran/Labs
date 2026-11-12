# Despliegue de Microservicios Cloud Native desde Docker Hub: Entorno Local y Oracle Cloud Infrastructure (OCI) 

<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/0724e797-c56a-4dae-9bc1-0965a7c70d34" />


## Objetivo

Desplegar EcoRed a partir de imágenes previamente publicadas en Docker Hub, primero en el entorno local con imágenes de otra persona y después en Oracle Cloud Infrastructure con imágenes propias, **sin recompilar ni modificar los artefactos**. Configurar los backing services mediante variables de entorno, comprobar los endpoints, la autenticación y la persistencia en ambos entornos, y relacionar la práctica con los doce factores de *The Twelve-Factor App*.

## Tabla de contenido

- [Compatibilidad de terminales y uso de imágenes](#compatibilidad-de-terminales-y-uso-de-imágenes)
- [Arquitectura y resultados esperados](#arquitectura-y-resultados-esperados)
- [Ruta de las imágenes entre entornos](#ruta-de-las-imágenes-entre-entornos)
- [Fase 1. Reproducción y ejecución local desde Docker Hub](#fase-1-reproducción-y-ejecución-local-desde-docker-hub)
  - [1.1. Selección y descarga](#11-selección-y-descarga)
  - [1.2. Servicios externos y variables de entorno](#12-servicios-externos-y-variables-de-entorno)
  - [1.3. Red y ejecución de cuatro contenedores](#13-red-y-ejecución-de-cuatro-contenedores)
  - [1.4. Pruebas independientes e integrales](#14-pruebas-independientes-e-integrales)
- [Fase 2. Despliegue en OCI desde imágenes propias de Docker Hub](#fase-2-despliegue-en-oci-desde-imágenes-propias-de-docker-hub)
  - [2.1. Descargar imágenes propias desde Docker Hub](#21-descargar-imágenes-propias-desde-docker-hub)
  - [2.2. Preparar OCIR](#22-preparar-ocir)
  - [2.3. Etiquetar y publicar en OCIR](#23-etiquetar-y-publicar-en-ocir)
  - [2.4. Red y Container Instance](#24-red-y-container-instance)
  - [2.5. Inyectar variables y probar la IP pública](#25-inyectar-variables-y-probar-la-ip-pública)
  - [2.6. Entregables y verificación de doce factores](#26-entregables-y-verificación-de-doce-factores)
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

### Paso 1. Preparar Docker y una carpeta de trabajo

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

### Paso 2. Elegir el propietario y descargar las cuatro imágenes

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

### Paso 3. Comprobar los backing services propios

Se requiere Firebase Authentication (métodos Email/Password y/o Google), una conexión MongoDB Atlas válida y un Oracle Autonomous Database con tabla `materials`. No utilice claves o contraseñas del docente. En Oracle la tabla y privilegios deben corresponder al usuario que use el microservicio.


**Factor IV:** Firebase, MongoDB y Oracle se usan como recursos externos configurados, no empaquetados en imágenes.

### Paso 4. Crear los cuatro archivos `.env`

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

### Paso 5. Crear la red virtual Docker

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

### Paso 6. Ejecutar Empresas y Materiales

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

### Paso 7. Ejecutar el gateway y el frontend

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

**Nota:** utilice las imágenes elegidas en el paso 2, sin mezclar autores incompatibles.

![Pantallazo original: comando para iniciar el gateway](assets/image-61.png)

![Pantallazo original: comando para iniciar el frontend](assets/image-62.png)

**Opción sin comandos para verificar:** abra **Docker Desktop → Containers** y compruebe que los cuatro aparezcan en ejecución. También puede consultar sus logs desde esa interfaz.

**Importante:** los nombres `ecored-companies` y `ecored-materials` se resuelven solo dentro de la red `ecored-net`. Los puertos 8001, 8002 y 8080 se exponen en la interfaz loopback del computador, no en toda la red del aula. **Factores VI, VII y XI:** procesos independientes, puertos explícitos y logs del contenedor.

## 1.4. Pruebas independientes e integrales

### Paso 8. Revisar salud, aislamiento y logs

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

### Paso 9. Realizar una prueba autenticada de extremo a extremo

Abra `http://localhost:8088`, inicie sesión con un usuario válido en Firebase, registre una empresa, publique un material asociado y compruebe el almacenamiento. También puede ejecutar `tests/02-flujo-firebase.http` mediante REST Client (solicita la API key y un usuario de prueba). En Firebase autorice el dominio local empleado.

![Pantallazo original: formulario de inicio de sesión Firebase](assets/image-63.png)

![Pantallazo original: pantalla de inicio después de autenticarse](assets/image-64.png)

![Pantallazo original: creación de empresa](assets/image-67.png)

![Pantallazo original: publicación de material](assets/image-68.png)

![Pantallazo original: registro de material confirmado](assets/image-69.png)

**Prueba de integración relevante:** Materiales debe validar la empresa a través de la API interna de Empresas antes de persistir el material. El éxito depende de las tres dependencias externas: Firebase, MongoDB y Oracle.

### Paso 10. Finalizar la reproducción

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

**Alternativa sin variables:** utilice los nombres completos de la **opción B** de cada paso, reemplazando los marcadores por valores reales. Nunca publique el Auth Token.

## 2.1. Descargar imágenes propias desde Docker Hub

### Paso 1. Descargar desde Docker Hub las cuatro imágenes propias

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

![Pantallazo original: paso de etiquetado para un registry](assets/image-48.png)

**Factores I, II y V:** código propio versionado, dependencias declaradas y artefactos identificables por versión o digest. Anote SHA256 de las imágenes con `docker image inspect --format '{{.Id}}' ...`.

## 2.2. Preparar OCIR

### Paso 2. Ubicar región, namespace y repositorios

En la consola de OCI seleccione la región adecuada. Vaya a **Developer Services → Containers & Artifacts → Container Registry**, cree repositorios (por ejemplo `ecored/companies`, `ecored/materials`, `ecored/gateway`, `ecored/frontend`) y verifique permisos IAM para publicar y consumir imágenes. Anote el **Object Storage namespace** del tenancy (no confundir con el nombre visible del tenancy).



### Paso 3. Generar un Auth Token e iniciar sesión

En el perfil del usuario de OCI abra **Auth Tokens → Generate Token** y guarde el token de forma segura. Desde la consola identifique: el registro regional (por ejemplo, `gru.ocir.io` si corresponde a su región), el **namespace** de Object Storage y el nombre IAM del usuario autorizado. **No confunda el usuario de Docker Hub con el de OCI.**

**Opción A. PowerShell con variables definidas al inicio:**

```powershell
docker login $REGISTRY -u "${NAMESPACE}/${OCI_USUARIO}"
```

**Opción B. Nombre completo (CMD, PowerShell y Bash):**

```console
docker login gru.ocir.io -u MI_NAMESPACE/MI_USUARIO_OCI
```

La contraseña solicitada por Docker es el **Auth Token de OCI**, no la contraseña de la consola. El formato de usuario puede requerir un prefijo de dominio de identidad; utilice el indicado en OCI. `gru.ocir.io` es un ejemplo, no una región obligatoria.

**Alternativa gráfica:** Docker Desktop permite consultar las imágenes, pero la autenticación con OCIR mediante `docker login` es el método reproducible para este taller.

No incluya el token en capturas ni en `.env` públicos.

## 2.3. Etiquetar y publicar en OCIR

### Paso 4. Asociar las imágenes propias a OCIR

Utilice exclusivamente las imágenes que **usted construyó en el Taller 5**. Los comandos `docker tag` agregan referencias al mismo contenido, sin reconstruirlo. Estos ejemplos usan el registro `gru.ocir.io`, un namespace y un usuario Docker Hub de ejemplo que debe sustituir.

**Opción A. PowerShell con las variables de la fase 2:**

```powershell
docker tag $PROPIA_COMPANIES $OCIR_COMPANIES
docker tag $PROPIA_MATERIALS $OCIR_MATERIALS
docker tag $PROPIA_GATEWAY $OCIR_GATEWAY
docker tag $PROPIA_FRONTEND $OCIR_FRONTEND
docker images
```

**Opción B. Nombres completos (CMD, PowerShell y Bash):**

```console
docker tag MI_USUARIO_DOCKERHUB/ecored-companies:1.0 gru.ocir.io/MI_NAMESPACE/ecored/companies:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-materials:1.0 gru.ocir.io/MI_NAMESPACE/ecored/materials:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-local-gateway:1.0 gru.ocir.io/MI_NAMESPACE/ecored/gateway:1.0
docker tag MI_USUARIO_DOCKERHUB/ecored-frontend:1.0 gru.ocir.io/MI_NAMESPACE/ecored/frontend:1.0
docker images
```

**Alternativa gráfica:** Docker Desktop → Images → Local → Tag (si la versión instalada permite esta opción). Cada etiqueta destino debe coincidir exactamente con la de OCIR.

![Pantallazo original del etiquetado para Docker Hub; la referencia de destino cambia en OCIR](assets/image-49.png)

### Paso 5. Publicar las cuatro imágenes

**Opción A. PowerShell con variables:**

```powershell
docker push $OCIR_COMPANIES
docker push $OCIR_MATERIALS
docker push $OCIR_GATEWAY
docker push $OCIR_FRONTEND
```

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

**Alternativa gráfica:** Docker Desktop → Images → Push, cuando esté disponible y el registro admita la autenticación. Si no, utilice A o B.

![Pantallazo original de `docker push` a Docker Hub; no es una captura de OCIR](assets/image-50.png)

![Pantallazo original de publicación de otras imágenes a Docker Hub](assets/image-51.png)

Verifique **en la consola real de OCIR** repositorio, etiqueta y digest de cada imagen. **Factor V:** separar artefacto, publicación y ejecución. Nunca inserte contraseñas en imágenes.

## 2.4. Red y Container Instance

### Paso 6. Preparar VCN, subred pública y políticas de acceso

En **Networking → Virtual Cloud Networks** cree o seleccione una VCN con subred pública, ruta a Internet Gateway y NSG / Security Lists para permitir **TCP/80** y **TCP/8080** solo desde las redes necesarias. Los puertos internos **8001 y 8002 no deben quedar expuestos a Internet**. Requiere salida DNS/HTTPS para Firebase, Atlas y servicios OCI. Configure en MongoDB Atlas una allowlist de salida (IP pública o NAT de OCI) y conectividad segura con Autonomous Database.

### Paso 7. Crear una Container Instance con cuatro contenedores

En **Developer Services → Containers & Artifacts → Container Instances → Create Container Instance**:

1. Indique nombre `ecored-lab`, compartimento, dominio de disponibilidad y *shape* según cuota/presupuesto.
2. Seleccione la VCN, subred pública, asignación de IP pública y NSG de la etapa anterior.
3. Agregue **cuatro contenedores** utilizando las imágenes privadas OCIR: `frontend`, `gateway`, `companies`, `materials`. Configure las credenciales / políticas necesarias para que la instancia pueda obtener imágenes privadas.
4. Declare los puertos TCP **80, 8080, 8001 y 8002** como puertos de escucha de la instancia según los contenedores; **solo 80 y 8080** deben ser accesibles desde Internet en el NSG.
5. Use política de reinicio adecuada, revisión de logs y recursos de memoria/CPU. Los cuatro contenedores **comparten red, ciclo de vida y recursos**. No se crea ni administra una VM, pero esto **no es Kubernetes ni autoscaling de réplicas**.

![Diagrama propio del despliegue en Container Instances; no corresponde a una captura de la consola OCI](assets/arquitectura-container-instance.svg)

**Factor VIII:** la separación por contenedores no implica escalado horizontal automático; la instancia comparte límites de recursos y reinicio. Para alta disponibilidad y escalado independiente haría falta otro diseño.

## 2.5. Inyectar variables y probar la IP pública

### Paso 8. Configurar las variables en OCI, sin reconstruir imágenes

En la sección de variables de entorno de **cada contenedor** configure lo siguiente, sustituyendo los marcadores por valores reales. **No copie el `.env` local sin adaptar las URLs**. Los contenedores de una misma Container Instance comparten espacio de red, por lo que sus procesos pueden conectarse mediante `127.0.0.1` y sus puertos internos.

| Contenedor | Variables determinantes en OCI |
|---|---|
| `companies` | `DJANGO_SECRET_KEY`, `DJANGO_DEBUG=False`, `DJANGO_ALLOWED_HOSTS=127.0.0.1,localhost`, `MONGODB_URI`, `MONGODB_DB_NAME` |
| `materials` | `HOST=0.0.0.0`, `PORT=8002`, `ORACLE_USER`, `ORACLE_PASSWORD`, `ORACLE_CONNECT_STRING`, `COMPANIES_SERVICE_URL=http://127.0.0.1:8001/api` |
| `gateway` | `HOST=0.0.0.0`, `PORT=8080`, `FIREBASE_PROJECT_ID`, `COMPANIES_URL=http://127.0.0.1:8001`, `MATERIALS_URL=http://127.0.0.1:8002`, `CORS_ORIGINS=http://IP_PUBLICA` |
| `frontend` | `VITE_API_URL=http://IP_PUBLICA:8080/api`, `VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_AUTH_DOMAIN`, `VITE_FIREBASE_PROJECT_ID`, `VITE_FIREBASE_APP_ID` |

**Atención con el acceso público:** cuando el frontend se sirve desde `http://IP_PUBLICA`, el navegador debe poder alcanzar el Gateway en `http://IP_PUBLICA:8080/api`. La IP pública corresponde a la Container Instance; `127.0.0.1` en el navegador del estudiante sería **su propio computador**, no OCI. El usuario puede sustituir `IP_PUBLICA` después de conocer la IP, actualizar variables e iniciar/reiniciar la instancia según lo permita el servicio.

**Limitación de la demostración:** HTTP por IP pública **no ofrece cifrado**; evite introducir contraseñas o tokens reales en una conexión HTTP no protegida. Para demostrar el flujo completo de autenticación desde el navegador, configure **DNS y HTTPS**, junto con el dominio autorizado en Firebase (por ejemplo, mediante un balanceador con TLS). Una IP pública HTTP permite comprobar conectividad, frontend, `/health` y el rechazo `401`, pero **no garantiza** que los proveedores de identidad permitan iniciar sesión: en particular, no suponga que el acceso mediante Google funciona sobre una IP HTTP. Si no dispone de DNS/HTTPS, documente esta limitación y utilice pruebas de conectividad y de backend con usuarios/datos de ensayo, sin exponer credenciales.

### Paso 9. Ejecutar comprobaciones públicas de conectividad

**Opción A. PowerShell con `$IP_PUBLICA` (definida al comienzo de la fase 2):**

```powershell
$IP_PUBLICA = 'CAMBIAR_IP_PUBLICA'   # Actualizarla con la IP mostrada en OCI
Test-NetConnection $IP_PUBLICA -Port 80
Test-NetConnection $IP_PUBLICA -Port 8080
curl.exe -i "http://${IP_PUBLICA}:8080/health"
curl.exe -i "http://${IP_PUBLICA}:8080/api/companies"
Start-Process "http://${IP_PUBLICA}/"
```

**Opción B. IP escrita completa (CMD, PowerShell y Bash):**

```console
curl.exe -i http://IP_PUBLICA:8080/health
curl.exe -i http://IP_PUBLICA:8080/api/companies
```

Sustituya `IP_PUBLICA` por la dirección real. La respuesta esperada es **200** para `/health` y **401** para `/api/companies` sin token.

**Alternativa sin terminal:** abra `http://IP_PUBLICA/` y `http://IP_PUBLICA:8080/health` en el navegador; para comprobar códigos y autenticación utilice REST Client o Postman. **Bash** admite `curl -i` sin el sufijo `.exe`.

Después abra `http://IP_PUBLICA/` y ejecute `tests/03-oci-publica.http`, que utiliza esa IP para verificar el gateway. Si Firebase permite correctamente el origen, pruebe además la autenticación y creación de datos reales. La prueba `401` demuestra que la petición sin token no pasa al microservicio; **no** demuestra por sí sola que el flujo completo funcione.

![Pantallazo original: acceso a aplicación autenticada local. Repita la verificación desde la IP pública, no se trata de una captura OCI](assets/image-64.png)

![Pantallazo original: empresa guardada; reproduzca el resultado desde la IP pública](assets/image-67.png)

![Pantallazo original: material guardado; reproduzca el resultado desde la IP pública](assets/image-69.png)

### Paso 10. Comprobar persistencia, observabilidad y seguridad

- Verifique que Empresas consulta MongoDB Atlas y que Materiales escribe en Oracle Autonomous Database, sin copiar bases de datos dentro de imágenes.
- Consulte logs de cada contenedor desde la consola OCI, sin imprimir tokens ni contraseñas. **Factor XI:** registros como flujos de eventos del proceso.
- Confirme que `http://IP_PUBLICA:8001` y `:8002` **no son accesibles** desde fuera, mientras el Gateway sí responde según las reglas establecidas.
- Si se cambia una variable de entorno, no reconstruya la imagen: realice un nuevo release o actualice la configuración en OCI según corresponda. **Factores III, V y X**.
- Termine o elimine la Container Instance al acabar el taller para evitar cobros; confirme en la consola el estado de los recursos.

## 2.6. Entregables y verificación de doce factores

### Entregables

Un PDF breve con las capturas solicitadas y explicación técnica de cada una, **sin credenciales ni tokens**. Debe contener: imágenes ajenas y su origen, `docker images`, contenedores locales en ejecución, pruebas HTTP y registro de empresa/material; imágenes propias verificadas primero en Docker Hub y después publicadas en los cuatro repositorios OCIR (URL, tag y digest), configuración de OCI Container Instances con cuatro contenedores, reglas NSG, IP pública, pruebas HTTP y evidencia de persistencia. Adjunte los archivos de pruebas `.http` sin claves y enlace a los repositorios propios del Taller 5. No entregue un `.env` con datos reales.

### Tabla de comprobación de los 12 factores

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

**Criterio de aprobación:** no basta con capturas de repositorios; debe demostrarse la reproducción local con imágenes ajenas de Docker Hub y el despliegue en OCI desde imágenes propias de Docker Hub, con evidencias de `docker pull`, `docker tag`, `docker push`, acceso público y respuestas HTTP. La autenticación integral desde el navegador en OCI exige un origen admitido por Firebase y transporte seguro; cuando se utilice únicamente IP HTTP, documente explícitamente esa limitación en vez de presentar un inicio de sesión no verificado como exitoso.

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
- Repositorio de código: https://github.com/adanbeltran/ecored-microservicios
