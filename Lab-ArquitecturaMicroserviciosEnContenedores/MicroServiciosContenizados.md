# Arquitectura de microservicios basada en principios Cloud Native: construcción y publicación de imágenes Docker

> **Alcance:** clonar el código, analizar la arquitectura y los doce factores, preparar los servicios externos, configurar el entorno, ejecutar y probar cada microservicio, construir imágenes Docker y publicarlas en Docker Hub. No se requiere desplegar contenedores en un proveedor de nube.

**Repositorio del código:** https://github.com/adanbeltran/ecored-microservicios

## Propósito y resultados de aprendizaje

Al terminar, el estudiante podrá explicar la separación de responsabilidades de Empresas y Materiales; identificar los principios de *The Twelve-Factor App* que se evidencian en el proyecto; configurar Firebase Authentication, MongoDB Atlas y Oracle Autonomous Database; probar APIs individuales e integradas; y construir y publicar imágenes sin incluir credenciales.

## Tabla de contenido

- [0. Clonar el código desde GitHub](#0-clonar-el-código-desde-github)
- [1. Descripción de la arquitectura cloud native y los 12 factores](#1-descripción-de-la-arquitectura-cloud-native-y-los-12-factores)
- [2. Creación de los backing services](#2-creación-de-los-backing-services)
  - [2.1 Firebase Authentication](#21-firebase-authentication)
  - [2.2 MongoDB Atlas](#22-mongodb-atlas)
  - [2.3 Oracle Autonomous Database](#23-oracle-autonomous-database)
- [3. Configuración de variables .env](#3-configuración-de-variables-env)
- [4. Ejecución local y pruebas independientes de endpoints](#4-ejecución-local-y-pruebas-independientes-de-endpoints)
  - [4.1 Iniciar los servicios](#41-iniciar-los-servicios)
  - [4.2 Pruebas independientes con .http](#42-pruebas-independientes-con-http)
  - [4.3 Prueba de integración](#43-prueba-de-integración)
- [5. Creación de imágenes Docker de los microservicios](#5-creación-de-imágenes-docker-de-los-microservicios)
- [6. Publicación de imágenes en Docker Hub](#6-publicación-de-imágenes-en-docker-hub)
- [Verificación final y evidencias](#verificación-final-y-evidencias)
- [Referencias](#referencias)

---

## 0. Preparar el entorno y clonar el proyecto desde GitHub

**Herramientas:** Git, VS Code, Python 3.12+, Node.js 24+, npm, Docker Desktop y extensión REST Client.

### 0.1. Crear y abrir la carpeta de trabajo

1. Abra **Visual Studio Code**.
2. Seleccione **Archivo → Abrir carpeta**.
3. Cree la carpeta `C:\Ecored-Microservicios` y ábrala en VS Code.
4. Abra **Terminal → Nueva terminal** (PowerShell).

### 0.2. Verificar las herramientas

Desde la terminal integrada de VS Code, ejecute:

```powershell
git --version
python --version
node --version
npm --version
```

Verifique que todas las herramientas estén instaladas y que Docker Desktop esté ejecutándose.

### 0.3. Clonar el repositorio

Confirme que la terminal esté ubicada en la carpeta de trabajo y ejecute:

```powershell
cd C:\Ecored-Microservicios
git clone https://github.com/adanbeltran/ecored-microservicios.git .
```

**Nota:** El punto (`.`) permite clonar directamente en la carpeta actual, que debe estar vacía.

### 0.4. Verificar el proyecto

```powershell
git status
Get-ChildItem
```

Compruebe que los archivos del repositorio aparezcan en el **Explorador de VS Code**.

### 0.5. Instalar REST Client

En VS Code, abra **Extensiones** (`Ctrl + Shift + X`), busque **REST Client** de Huachao Mao e instálela para ejecutar las pruebas de los endpoints mediante archivos `.http`.

**Resultado esperado:** Proyecto clonado en `C:\Ecored-Microservicios`, abierto en VS Code y listo para configurar y ejecutar los microservicios.



## 1. Descripción de la arquitectura cloud native y los 12 factores

<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/192df20c-7d75-48a5-b2a3-f4f12573e3d4" />


### 1.1 Componentes y límites de responsabilidad

| Componente | Tecnología | Responsabilidad | Puerto local |
|---|---|---|---:|
| Frontend | React, Vite | Interfaz de usuario e inicio de sesión con Firebase | 5173 |
| Gateway de desarrollo | Node.js, `jose` | Valida ID tokens Firebase, protege las rutas y transmite identidad validada | 8080 |
| Companies Service | Python, Django REST, PyMongo | Gestión de empresas y autorización por propietario | 8001 |
| Materials Service | Node.js, Express, `node-oracledb` | Registro y consulta de materiales, verificación de la empresa | 8002 |
| Firebase Authentication | Servicio administrado | Identificación y emisión de ID tokens | HTTPS |
| MongoDB Atlas | Base de datos externa | Persistencia de Empresas | URI externa |
| Oracle Autonomous Database | Base de datos administrada | Persistencia de Materiales | Conexión TLS |


Los microservicios **no comparten directamente sus bases de datos**. Materiales consulta a Empresas por HTTP para confirmar existencia y pertenencia antes de almacenar un material. El encabezado `X-User-Id` solo es confiable cuando lo establece el gateway tras validar el token. No se debe exponer el acceso directo a los puertos internos a clientes no confiables.

### 1.2 Los doce factores aplicados a EcoRed

La tabla distingue los factores observables en el código de las prácticas que deben comprobarse o completarse durante el taller. No equivale a afirmar que todos los factores están totalmente implementados.

| Factor | Principio | Aplicación concreta y verificación en EcoRed | Estado |
|---|---|---|---|
| I. Codebase | Código versionado | Código fuente en GitHub, clonado en el paso 0; componentes organizados por carpetas | Aplicado |
| II. Dependencies | Dependencias declaradas | `requirements.txt`, `package.json` y `package-lock.json`; instalaciones con `pip` y `npm ci` | Aplicado |
| III. Config | Configuración fuera del código | Archivos `.env` separados por servicio, excluidos del control de versiones y del contexto Docker | Aplicado; verificar exclusiones |
| IV. Backing services | Recursos conectables | Firebase, MongoDB Atlas y Oracle; las URI y credenciales provienen del entorno | Aplicado |
| V. Build, release, run | Separar construcción y ejecución | `docker build` produce imágenes; la configuración se suministra al ejecutar; la publicación crea artefactos versionados | Demostrado parcialmente en este taller |
| VI. Processes | Procesos sin estado | APIs que reciben solicitudes y guardan datos en servicios externos; comprobar que no dependen de archivos locales persistentes | Parcial |
| VII. Port binding | Servicio publicado por puerto | 8001, 8002 y 8080 para backends; Dockerfile documenta `EXPOSE` | Aplicado |
| VIII. Concurrency | Escalar con procesos | Imágenes separadas permiten instancias independientes; **el escalado no se implementa** aquí | Preparado, no demostrado |
| IX. Disposability | Inicio y parada rápidos | Verificación de conexiones al iniciar y terminación del proceso; comprobar reinicio y cierre correcto | Parcial |
| X. Dev/prod parity | Paridad de entornos | Dependencias fijadas e imágenes reproducibles reducen diferencias; no se valida entorno productivo | Parcial |
| XI. Logs | Flujos de eventos | Salidas de consola y `docker logs`; evitar depender solo de archivos internos | Aplicado / verificar |
| XII. Admin processes | Tareas administrativas | SQL de inicialización en `services/materials-service/sql/001_schema.sql` | Parcial: ejecución manual controlada |

**Actividad de análisis:** seleccione tres factores aplicados y dos parciales. Para cada uno cite el archivo, configuración o comando que lo evidencia y explique qué faltaría para considerarlo completamente aplicado.

---

## 2. Creación de los backing services

Los backing services se crean **antes** de ejecutar los microservicios. No almacene contraseñas, cadenas de conexión con secretos ni archivos de cuentas de servicio en GitHub o en imágenes Docker.

### 2.1 Firebase Authentication

1. Abra la [consola de Firebase](https://console.firebase.google.com/) y cree o seleccione un proyecto.
2. En **Build → Authentication → Sign-in method**, habilite **Email/Password**.
3. En **Authentication → Users**, cree un usuario de prueba (correo y contraseña de laboratorio).
4. En **Project settings → Your apps**, registre una **aplicación web** y copie `apiKey`, `authDomain`, `projectId` y `appId`.
5. Verifique que `projectId` sea exactamente el mismo en el frontend y el gateway. Autorice los dominios locales cuando la configuración de Firebase lo requiera.

**Validación:** la API REST de Firebase debe devolver un `idToken` al autenticar el usuario. El archivo `.http` de integración realiza esa solicitud y no requiere guardar la contraseña en el repositorio.

**Precisión:** la `apiKey` de la configuración web no es una contraseña de servidor; no debe confundirse con una clave privada de cuenta de servicio. Las reglas y restricciones de acceso se configuran en Firebase.

### 2.2 MongoDB Atlas

1. Ingrese a [MongoDB Atlas](https://cloud.mongodb.com/) y cree un proyecto y un clúster compatible con los recursos disponibles de su cuenta.
2. En **Database Access**, cree un usuario de base de datos con una contraseña segura y permisos mínimos para el proyecto.
3. En **Network Access**, autorice la dirección IP pública de su equipo para el laboratorio. Evite permitir todas las IP de forma permanente.
4. Seleccione **Connect → Drivers**, copie la cadena de conexión `mongodb+srv://...` y sustituya usuario y contraseña. Codifique caracteres especiales en las credenciales de la URI cuando corresponda.
5. Defina el nombre de base de datos `ecored_companies`. La colección `companies` se creará al insertar la primera empresa.

**Validación:** al arrancar Companies Service, la conexión debe superar la comprobación de MongoDB. Si falla, revise IP autorizada, URI, usuario y contraseña.

### 2.3 Oracle Autonomous Database

1. Ingrese a la consola de Oracle Cloud y abra **Oracle AI Database → Autonomous Database** (el nombre del menú puede variar).

![Seleccionar Autonomous Database](assets/image-10.png)
![Pantalla del servicio de base de datos](assets/image-11.png)

2. Cree una base orientada a procesamiento transaccional y seleccione el nivel de servicio disponible en su cuenta. Si corresponde, active **Always Free**.

![Crear Autonomous Database](assets/image-12.png)
![Configuración de la base de datos](assets/image-13.png)

3. Configure el acceso de red para su equipo. Si utilizará una cadena TLS sin Wallet, confirme que el modo de conexión elegido admite TLS de una vía y que la red permite el acceso.

![Configuración de acceso de red](assets/image-14.png)
![Confirmación de creación](assets/image-15.png)
![Base creada](assets/image-19.png)

4. Abra **Database Actions → SQL** con el usuario administrador y cree un usuario aplicativo. Sustituya `CLAVE_SEGURA_PROPIA` por una contraseña fuerte y única:

```sql
CREATE USER ecored
IDENTIFIED BY "CLAVE_SEGURA_PROPIA"
DEFAULT TABLESPACE DATA
TEMPORARY TABLESPACE TEMP
QUOTA UNLIMITED ON DATA
ACCOUNT UNLOCK;
GRANT CREATE SESSION TO ecored;
GRANT CREATE TABLE TO ecored;
GRANT CREATE SEQUENCE TO ecored;
```

![Abrir Database Actions](assets/image-20.png)
![Editor SQL de Oracle](assets/image-21.png)
![Usuario creado](assets/image-27.png)

5. Conéctese como `ecored`. Ejecute **una sola vez** el archivo `services/materials-service/sql/001_schema.sql` para crear los objetos usados por Materiales. No ejecute indiscriminadamente scripts de inicialización sobre bases que ya contienen datos.

![Abrir sesión SQL](assets/image-24.png)
![Ejecutar script](assets/image-28.png)
![Resultados de ejecución](assets/image-29.png)
![Objetos de la base](assets/image-30.png)

6. Abra **Database Connection → Connection strings**, identifique el servicio `TP` y copie la cadena **TLS** completa a `ORACLE_CONNECT_STRING` sin saltos de línea. No la sustituya por el nombre de la base.

![Opciones de conexión](assets/image-31.png)
![Cadena TLS](assets/image-32.png)
![Detalle del descriptor de conexión](assets/image-33.png)

**Wallet:** es un conjunto de archivos de confianza y, para conexiones mTLS, credenciales/certificados de cliente. Para esta práctica se emplea una conexión TLS de una vía compatible con `node-oracledb` Thin; no se requiere descargar ni insertar un Wallet en la imagen. Si su base exige mTLS, tendrá que ajustar la configuración según la documentación de Oracle.

**Validación:** Materiales crea un pool de conexiones y ejecuta `SELECT 1 FROM dual` al arrancar. Si falla, verifique la lista de acceso de red, credenciales, descriptor y modo TLS.

---

## 3. Configuración de variables .env

Los valores que siguen son **plantillas**, no credenciales reales. En la raíz del proyecto, copie los archivos de ejemplo:

```powershell
Copy-Item services/companies-service/.env.example services/companies-service/.env
Copy-Item services/materials-service/.env.example services/materials-service/.env
Copy-Item local-gateway/.env.example local-gateway/.env
Copy-Item frontend/.env.example frontend/.env
```

**Empresas: `services/companies-service/.env`**

```dotenv
MONGODB_URI=mongodb+srv://USUARIO:CLAVE@HOST/ecored_companies?retryWrites=true&w=majority
MONGODB_DB_NAME=ecored_companies
```

**Materiales: `services/materials-service/.env`**

```dotenv
PORT=8002
HOST=127.0.0.1
ORACLE_USER=ecored
ORACLE_PASSWORD=CLAVE_SEGURA_PROPIA
ORACLE_CONNECT_STRING=(description=(address=(protocol=tcps)(port=1521)(host=HOST_ORACLE))(connect_data=(service_name=SERVICIO_TP))(security=(ssl_server_dn_match=yes)))
COMPANIES_SERVICE_URL=http://127.0.0.1:8001/api
```

**Gateway: `local-gateway/.env`**

```dotenv
PORT=8080
HOST=127.0.0.1
FIREBASE_PROJECT_ID=SU_PROJECT_ID
COMPANIES_URL=http://127.0.0.1:8001
MATERIALS_URL=http://127.0.0.1:8002
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

**Frontend: `frontend/.env`**

```dotenv
VITE_API_URL=http://127.0.0.1:8080/api
VITE_FIREBASE_API_KEY=API_KEY_WEB
VITE_FIREBASE_AUTH_DOMAIN=SU_PROJECT_ID.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=SU_PROJECT_ID
VITE_FIREBASE_APP_ID=APP_ID_WEB
```

Si los archivos `.env.example` incluyen variables adicionales, **consérvelas** y complete sus valores según el ejemplo del repositorio. No escriba valores ficticios para iniciar realmente los servicios.

Compruebe que los `.env` no se publiquen:

```powershell
git status --short
git check-ignore services/companies-service/.env services/materials-service/.env local-gateway/.env frontend/.env
```

La segunda instrucción debe mostrar las rutas ignoradas. Antes de construir imágenes, revise además los archivos `.dockerignore` de cada componente.

---

## 4. Ejecución local y pruebas independientes de endpoints

### 4.1 Iniciar los servicios

**Abra cuatro terminales de VS Code, cada una desde la raíz del repositorio.** Manténgalas abiertas mientras prueba.

**Terminal 1 — Empresas (Django)**

```powershell
cd services/companies-service
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python manage.py runserver 127.0.0.1:8001 --noreload
```

![Ejecución del microservicio Empresas](assets/image-9.png)

**Terminal 2 — Materiales (Node.js)**

```powershell
cd services/materials-service
npm ci
npm start
```

**Terminal 3 — Gateway local (Node.js)**

```powershell
cd local-gateway
npm ci
npm start
```

**Terminal 4 — Frontend (React/Vite)**

```powershell
cd frontend
npm ci
npm run dev
```

Abra `http://localhost:5173` e inicie sesión con el usuario creado en Firebase. Antes de enviar operaciones, confirme que los procesos no reportan errores de MongoDB u Oracle.

### 4.2 Pruebas independientes con .http

Instale la extensión **REST Client** de VS Code y abra los archivos de la carpeta `tests/` entregada junto con este taller. El archivo `tests/ecored-end-to-end.http` **ya existe en el repositorio**; los dos archivos siguientes se incluyen en este ZIP como material de práctica complementario:

- `tests/01-companies-independiente.http`: prueba del microservicio Empresas sin pasar por el gateway.
- `tests/02-materials-independiente.http`: prueba del microservicio Materiales sin pasar por el gateway.

**Seguridad importante:** estas pruebas directas usan `X-User-Id` como identidad **simulada** únicamente en el equipo local. No son una autenticación real: si publicas los microservicios directamente en Internet, un tercero podría falsificar ese encabezado. En una arquitectura expuesta, el gateway debe validar el Bearer token y los backends deben estar aislados de clientes externos.

**Empresas:** primero envíe una solicitud sin encabezado: debe responder `401`. Luego envíe `POST /api/companies` con `X-User-Id: estudiante-demo`, compruebe `201`, liste empresas, consulte el ID creado y pruebe que otro UID no pueda leerlo.

```http
@companiesUrl = http://127.0.0.1:8001
GET {{companiesUrl}}/api/companies

### Crear empresa con identidad simulada (solo entorno local)
# @name createCompany
POST {{companiesUrl}}/api/companies
X-User-Id: estudiante-demo
Content-Type: application/json

{
  "name": "Reciclajes Aula",
  "nit": "NIT-DEMO-{{$timestamp}}",
  "city": "Bogotá",
  "sector": "Reciclaje"
}
```

**Materiales:** primero consulte sin encabezado: debe responder `401`. Luego cree un material usando el ID obtenido en la prueba de Empresas y el **mismo** `X-User-Id`, y verifique `201`. Si cambia el UID o el ID no corresponde a una empresa propia, la operación debe rechazarse. Materiales se comunica con Empresas por HTTP para comprobar la pertenencia.

```http
@materialsUrl = http://127.0.0.1:8002
@companyId = REEMPLAZAR_POR_ID_REAL_DE_EMPRESA

GET {{materialsUrl}}/api/materials

### Crear material con identidad simulada (solo entorno local)
POST {{materialsUrl}}/api/materials
X-User-Id: estudiante-demo
Content-Type: application/json

{
  "company_id": "{{companyId}}",
  "material_type": "Cartón",
  "quantity": 25,
  "unit": "kg",
  "location": "Bogotá"
}
```

**Resultados que deben documentarse:** código HTTP y cuerpo relevante de cada petición; diferencias entre `200`, `201`, `401`, `403` y `404` según lo que realmente devuelva la aplicación. No asuma que una solicitud tuvo éxito sin revisar la respuesta.

### 4.3 Prueba de integración

Una vez comprobados ambos servicios, abra el archivo **original del repositorio** `tests/ecored-end-to-end.http` y ejecute sus solicitudes en orden. El flujo es:

1. `GET /health` del gateway → `200`.
2. Acceso a ruta protegida sin token → `401`.
3. Inicio de sesión Firebase → obtención de `idToken`.
4. Creación de Empresa mediante el gateway → `201`.
5. Listado de Empresas con Bearer token.
6. Creación de Material con la Empresa recién creada → `201`.
7. Consulta del Material y listado general.
8. Acceso directo a los backends **sin identidad** → `401`.

```mermaid
sequenceDiagram
  actor U as Estudiante
  participant A as Firebase Auth
  participant G as Gateway
  participant E as Empresas
  participant M as Materiales
  U->>A: Login
  A-->>U: ID token
  U->>G: POST /api/companies (Bearer)
  G->>E: POST /api/companies (X-User-Id)
  E-->>G: ID de empresa
  G-->>U: 201 + ID
  U->>G: POST /api/materials (Bearer + company_id)
  G->>M: POST /api/materials (X-User-Id)
  M->>E: Validar empresa del usuario
  E-->>M: Empresa autorizada
  M-->>U: 201 + material
```

El archivo original utiliza respuestas nombradas (`firebaseLogin`, `createCompany`, `createMaterial`) para reutilizar token e identificadores. Ejecute primero cada solicitud de la que dependen las siguientes.

#### Autenticación e identidad interna

`X-User-Id` es un encabezado interno que transporta el UID del usuario autenticado. No lo crea el frontend y no se confía en un valor enviado por el navegador.


3. El gateway valida firma, expiración, emisor y audiencia con `jose`.
4. El gateway elimina cualquier `X-User-*` recibido del cliente.
5. El gateway crea `X-User-Id` usando el `sub` ya validado.
6. Los microservicios usan ese UID para separar y autorizar datos.

```mermaid
sequenceDiagram
    participant F as "Frontend · authService + httpClient"
    participant A as "Firebase Auth"
    participant S as "Servicio · authenticate(request) / gatewayIdentity(req)"
    F->>A: loginWithEmail(email, password)
    J-->>G: payload verificado {sub, email}
    G->>S: X-User-Id=sub, X-User-Email=email
    S-->>F: respuesta del dominio
```


#### 3.2 Comunicación Materiales → Empresas

Crear un material requiere una empresa válida, pero Oracle no duplica los datos de MongoDB y Materiales no recibe la URI de MongoDB.

```mermaid
sequenceDiagram
    participant F as "Frontend/Gateway · createMaterial() + proxy()"
    participant M as "app.js · POST /api/materials"
    participant C as "companyClient.js · assertOwnedCompany(baseUrl, companyId, identity)"
    participant D as "Django · CompanyDetailView.get() + get_owned()"
    participant O as "OracleMaterialRepository.create(material, publishedBy)"
    F->>M: material + X-User-Id validado
    M->>M: validateMaterial(req.body)
    M->>C: baseUrl, company_id, req.identity
    C->>D: GET /companies/{id} + X-User-Id
    D->>D: get_owned(company_id, request.user.uid)
    D-->>C: 200 o 404
    C-->>M: empresa autorizada
    M->>O: material, identity.uid
    O-->>F: material creado
```

`createMaterial(material)` llama al gateway. En Express, `validateMaterial()` normaliza la entrada y `assertOwnedCompany()` llama a la API privada de Empresas con el mismo UID. `CompanyDetailView.get()` delega en `MongoCompanyRepository.get_owned()`, que busca a la vez por ID y propietario. Solo después de un `200`, `OracleMaterialRepository.create()` inserta el material y registra `publishedBy`.


#### Prueba opcional con Postman

Para reproducir la secuencia, obtenga el `idToken` mediante Firebase Authentication, configure el Bearer token en las llamadas al gateway, cree una empresa, copie su `id` y úselo al crear un material. Las capturas del taller original muestran el procedimiento:

![Prueba con Postman, paso 1](assets/image-34.png)
![Prueba con Postman, paso 2](assets/image-35.png)
![Prueba con Postman, paso 3](assets/image-36.png)
![Prueba con Postman, paso 4](assets/image-37.png)
![Prueba con Postman, paso 5](assets/image-38.png)
![Prueba con Postman, paso 6](assets/image-39.png)
![Prueba con Postman, paso 7](assets/image-40.png)

---

## 5. Creación de imágenes Docker de los microservicios

Cada servicio tiene su propio `Dockerfile`, contexto de construcción, dependencias y proceso de arranque. El gateway y el frontend también se pueden empaquetar, pero se diferencian de los **dos microservicios de dominio**: Empresas y Materiales.

**Antes de construir:** los archivos `.env`, wallets, claves privadas, directorios de dependencias y entornos virtuales deben quedar fuera de la imagen. Inspeccione `.dockerignore` y evite incluir secretos por instrucciones `COPY` indiscriminadas.

#### Dockerfile de Empresas, línea por línea

| Línea/instrucción | Por qué se usa | Alternativa |
|---|---|---|
| `FROM python:3.12-slim` | Base oficial reducida compatible con el proyecto. | `python:3.12` facilita herramientas de compilación, pero pesa más. Alpine es menor, aunque algunas dependencias Python requieren ajustes. |
| `ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1` | Evita `.pyc` y muestra logs inmediatamente. | Mantener valores predeterminados genera cachés y puede retrasar logs. |
| `WORKDIR /app` | Define un directorio estable para copiar y ejecutar. | Otro directorio funciona si todas las rutas se actualizan. |
| `COPY requirements.txt .` | Separa dependencias para reutilizar la caché. | Copiar todo primero invalida la instalación ante cualquier cambio. |
| `RUN pip install --no-cache-dir -r requirements.txt` | Instala dependencias sin conservar caché de paquetes. | Un entorno multietapa puede reducir más la imagen, con mayor complejidad. |
| `COPY . .` | Copia el servicio después de las dependencias. | Copiar directorios explícitos da más control. |
| `EXPOSE 8001` | Documenta el puerto interno. | No publica el puerto; puede omitirse, pero reduce claridad. |
| `CMD ["gunicorn", ...]` | Ejecuta Django con un servidor apropiado para contenedores. | `runserver` es solo para desarrollo; Uvicorn sería opción con ASGI. |

### 5.2 Dockerfile de Materiales, línea por línea

| Línea/instrucción | Por qué se usa | Alternativa |
|---|---|---|
| `FROM node:24-alpine` | Imagen oficial pequeña para Node. | `node:24-slim` ofrece mayor compatibilidad con librerías nativas a cambio de tamaño. |
| `ENV HOST=0.0.0.0` | Permite recibir tráfico desde fuera del contenedor. | Pasar `HOST` en `docker run`; declararlo aquí documenta el comportamiento seguro del contenedor. |
| `WORKDIR /app` | Unifica rutas de ejecución. | Cualquier ruta consistente es válida. |
| `COPY package*.json ./` | Permite cachear la capa de dependencias. | Copiar todo primero reconstruye más capas. |
| `RUN npm ci --omit=dev` | Instala exactamente el lockfile y omite herramientas de desarrollo. | `npm install` puede modificar resolución; una imagen distroless reduce superficie, pero dificulta diagnóstico. |
| `COPY src ./src` | Incluye solo el código requerido en ejecución. | Copiar todo exige un `.dockerignore` más estricto. |
| `EXPOSE 8002` | Documenta el puerto interno. | Puede omitirse sin impedir `-p`, pero pierde documentación. |
| `CMD ["npm", "start"]` | Usa el punto de entrada declarado en `package.json`. | `node src/server.js` elimina una capa de indirección. |

### 5.3 Dockerfile del gateway local, línea por línea

| Línea/instrucción | Por qué se usa | Alternativa |
|---|---|---|
| `FROM node:24-alpine` | Mantiene la misma versión de Node y una imagen pequeña. | `node:24-slim` si se prioriza compatibilidad. |
| `ENV HOST=0.0.0.0` | Hace visible el gateway fuera del contenedor. | Inyectar `HOST` al ejecutar. |
| `WORKDIR /app` | Fija la ruta de trabajo. | Otra ruta consistente. |
| `COPY package*.json ./` | Aprovecha caché de dependencias. | Copiar todo primero es menos eficiente. |
| `RUN npm ci --omit=dev` | Instala solamente producción de forma reproducible. | `npm install --omit=dev` es menos estricto con el lockfile. |
| `COPY server.js` y `COPY src` | Incluye el proxy y el validador JWT. | Una etapa de empaquetado sería innecesaria para este gateway pequeño. |
| `EXPOSE 8080` | Documenta el puerto del adaptador local. | Puede omitirse, no reemplaza `-p`. |
| `CMD ["npm", "start"]` | Ejecuta el gateway con su script oficial. | Ejecutar Node directamente. |

Esta imagen reproduce la autenticación y el enrutamiento del gateway para las pruebas del taller.

### 5.4 Dockerfile del frontend, línea por línea

| Línea/instrucción | Por qué se usa | Alternativa |
|---|---|---|
| `FROM node:24-alpine AS build` | Primera etapa: compila React. | `node:24-slim` mejora compatibilidad y aumenta tamaño temporal. |
| `WORKDIR /app` | Define la ruta de compilación. | Otra ruta consistente. |
| `COPY package*.json ./` + `RUN npm ci` | Instala versiones exactas y reutiliza caché. | `npm install` es menos reproducible. |
| `COPY . .` + `RUN npm run build` | Produce los archivos estáticos sin valores del estudiante. | Construir fuera de Docker reduce reproducibilidad. |
| `FROM nginx:1.29-alpine` | Segunda etapa pequeña para servir HTML, CSS y JS. | Caddy o Apache también sirven archivos estáticos. Ejecutar Vite preview no es una opción de producción. |
| `COPY nginx.conf ...` | Configura rutas de la SPA y evita cachear la configuración. | Una configuración Nginx montada externamente permite modificarla sin construir. |
| `COPY --from=build ...` | Copia solo el resultado; Node y las fuentes no quedan en producción. | Una sola etapa produciría una imagen mayor. |
| `COPY 40-runtime-config.sh ...` | Genera `runtime-config.js` al iniciar con los valores recibidos. | Montar directamente un archivo `runtime-config.js` mediante un volumen en otros entornos. |
| `RUN chmod +x ...` | Permite que el entrypoint oficial ejecute el generador. | Integrar la lógica en otro entrypoint requiere mantener más código. |
| `EXPOSE 80` | Documenta el puerto HTTP de Nginx. | Puede omitirse sin impedir publicar el puerto. |

El frontend genera su configuración durante el arranque del contenedor, de acuerdo con el Dockerfile del proyecto. `index.html` carga `runtime-config.js`; Nginx lo genera al arrancar el contenedor. Por tanto, una sola imagen sirve para todos los estudiantes y, después, para diferentes entornos.


### Procedimiento de construcción

Inicie Docker Desktop y confirme que el motor esté activo.

![alt text](assets/image-41.png)
Desde la raíz del proyecto:

```powershell
docker build -t ecored-companies:1.0 services/companies-service
```
![alt text](assets/image-42.png)
```powershell
docker build -t ecored-materials:1.0 services/materials-service
```
![alt text](assets/image-43.png)
```powershell

docker build -t ecored-local-gateway:1.0 local-gateway
```
![alt text](assets/image-44.png)

```powershell
docker build -t ecored-frontend:1.0 frontend
```
![alt text](assets/image-45.png)

```powershell
docker image ls ecored-*
```
![alt text](assets/image-46.png)



Tras construir, confirme que existen las imágenes con etiqueta `1.0` y que Docker no mostró errores. `EXPOSE` documenta el puerto: **no** inicia el contenedor ni publica automáticamente el puerto en el host.

---

## 6. Publicación de imágenes en Docker Hub

Cree o utilice una cuenta en [Docker Hub](https://hub.docker.com/). Asegúrese de tener permiso para publicar repositorios bajo el nombre de usuario elegido. Se publican **imágenes**, nunca archivos `.env`, contraseñas ni wallets.

### Procedimiento de publicación

Reemplace `SU_USUARIO` y publique solamente imágenes, nunca `.env` ni Wallets:

```powershell
docker login
```
![alt text](assets/image-47.png)

```powershell
docker tag ecored-companies:1.0 SU_USUARIO/ecored-companies:1.0
```

```powershell
docker tag ecored-materials:1.0 SU_USUARIO/ecored-materials:1.0
```
```powershell
docker tag ecored-local-gateway:1.0 SU_USUARIO/ecored-local-gateway:1.0
```
```powershell
docker tag ecored-frontend:1.0 SU_USUARIO/ecored-frontend:1.0
```
![alt text](assets/image-49.png)

```powershell

docker push SU_USUARIO/ecored-companies:1.0
```
```powershell

docker push SU_USUARIO/ecored-materials:1.0
```
```powershell

docker push SU_USUARIO/ecored-local-gateway:1.0
```
```powershell

docker push SU_USUARIO/ecored-frontend:1.0
```
![alt text](assets/image-50.png)
![alt text](assets/image-51.png)
![alt text](assets/image-52.png)
![alt text](assets/image-53.png)
![alt text](assets/image-54.png)

Use una etiqueta fija como `1.0` para que la clase ejecute exactamente los mismos artefactos. `latest` puede existir, pero no debe ser la única referencia del taller.


**Verificación final de Docker Hub:** compruebe en la interfaz web que cada repositorio publicado contiene la etiqueta `1.0`. Registre los enlaces a los repositorios y la etiqueta, no una captura con credenciales. En este taller **no se descargan ni ejecutan imágenes de terceros**: esa actividad pertenece a otro laboratorio.

---

## Verificación final y evidencias

| Evidencia | Criterio de aceptación |
|---|---|
| Código clonado | Se muestran las carpetas de los dos microservicios y los demás componentes |
| Arquitectura y 12 factores | Diagrama explicado y análisis sustentado de tres factores aplicados y dos parciales |
| Backing services | Firebase autentica, MongoDB conecta y Oracle responde; sin mostrar secretos |
| Configuración | Cuatro `.env` locales, ignorados por Git, con variables correctas |
| Pruebas independientes | `.http` de Empresas y de Materiales con resultados HTTP, incluidos controles de acceso |
| Prueba integral | Firebase → Gateway → Empresas → Materiales, con registros persistidos |
| Imágenes | `docker image ls` muestra cuatro imágenes `1.0` creadas desde código |
| Docker Hub | Enlaces a repositorios publicados y etiquetas `1.0` |

**Criterio pedagógico:** no basta mostrar comandos: explique qué hace cada servicio, por qué se comunica con el otro, qué valida cada prueba y qué evidencias corresponden a los principios cloud native. Oculte tokens, contraseñas, cadenas privadas y direcciones sensibles en capturas.

## Referencias

- [Código EcoRed: repositorio del laboratorio](https://github.com/adanbeltran/ecored-microservicios)
- [The Twelve-Factor App](https://12factor.net/es/)
- [Firebase Authentication](https://firebase.google.com/docs/auth)
- [MongoDB Atlas: documentación](https://www.mongodb.com/docs/atlas/)
- [Oracle Autonomous Database](https://docs.oracle.com/en/cloud/paas/autonomous-database/)
- [Docker: construir y publicar imágenes](https://docs.docker.com/get-started/docker-concepts/building-images/)
- [REST Client para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
