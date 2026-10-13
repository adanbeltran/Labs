# Taller 1. Preparar la cuenta y la red de la asignatura

## Objetivo

Organizar tu cuenta de OCI (Oracle Cloud Infrastructure, infraestructura de nube de Oracle) mediante el compartimento de la asignatura **Infraestructura y Cloud**, crear un usuario IAM (Identity and Access Management, gestión de identidades y accesos) con permisos para trabajar y preparar la red donde estará la página del taller de publicación. Usarás tu propia cuenta de Oracle Academy Cloud Program, ya activada con **300 dólares estadounidenses de crédito inicial**.

**Tiempo estimado: 50 minutos.**

<a name="indice"></a>

## Tabla de contenido

- [Fase 1. Organizar la cuenta y preparar el acceso](#fase-1)
  - [Paso 1.1. Ingresar y registrar los datos de la cuenta](#paso-1-1)
  - [Paso 1.2. Crear el compartimento de la asignatura](#paso-1-2)
  - [Paso 1.3. Crear el grupo de permisos](#paso-1-3)
  - [Paso 1.4. Crear la política de acceso](#paso-1-4)
  - [Paso 1.5. Crear el usuario de práctica](#paso-1-5)
  - [Paso 1.6. Activar el nuevo usuario](#paso-1-6)
  - [Paso 1.7. Comprobar el acceso y guardar el resultado](#paso-1-7)
- [Fase 2. Crear la red de la asignatura](#fase-2)
  - [Paso 2.1. Crear la red virtual](#paso-2-1)
  - [Paso 2.2. Crear la salida a Internet y su ruta](#paso-2-2)
  - [Paso 2.3. Crear la subred pública](#paso-2-3)
  - [Paso 2.4. Permitir la conexión y el acceso a la página](#paso-2-4)

---

<a name="fase-1"></a>

## Fase 1. Organizar la cuenta y preparar el acceso

Crearás un lugar para agrupar los recursos de la asignatura y un usuario para trabajar con ellos. El compartimento permite identificar esos recursos; el grupo y la política determinan lo que podrá hacer el nuevo usuario. Así podrás publicar la página con permisos definidos y conservar tu cuenta administradora para configurar el acceso.

<a name="paso-1-1"></a>

### Paso 1.1. Ingresar y registrar los datos de la cuenta

Usarás la cuenta que ya activaste para crear la organización y los permisos. Registrar su nombre, región y saldo te permitirá volver a ingresar y controlar el crédito disponible.

1. En el navegador, escribe `cloud.oracle.com` e inicia sesión con tu cuenta administradora de Oracle Academy Cloud Program. Mantén abierta esta sesión durante el taller.
2. En el selector de regiones de la barra superior, elige la región principal de tu cuenta, identificada como **Home region**. Usa esa misma región en los dos talleres.
3. En la página de inicio, revisa el panel **Cost analysis** o **Subscription credits**. Anota el saldo restante; si ya usaste servicios, conserva el saldo real que muestre la cuenta.
4. Crea en Visual Studio Code un archivo `datos-taller.txt`. Guarda el nombre de tu cuenta, la región principal y el saldo disponible. No escribas contraseñas en este archivo.

<img width="295" height="248" alt="image" src="https://github.com/user-attachments/assets/c61451ed-b280-4eba-b3ab-893ca535f38d" />

<img width="582" height="383" alt="image" src="https://github.com/user-attachments/assets/286da5de-c14b-462e-a090-d61bcdf3c8b0" />

<img width="509" height="293" alt="image" src="https://github.com/user-attachments/assets/2c53413c-0af5-4f7a-85bb-44aa7f33499f" />

<img width="943" height="338" alt="image" src="https://github.com/user-attachments/assets/a7d77daf-2abe-4bbd-91d6-9d4182f7d926" />





<a name="paso-1-2"></a>

### Paso 1.2. Crear el compartimento de la asignatura

Crearás el compartimento para reunir los recursos de **Infraestructura y Cloud**. En el campo de nombre usarás guiones bajos porque la consola no admite espacios en ese campo.

1. Abre el menú principal **Identity & Security > Compartments**.
2. Selecciona **Create Compartment** y completa:

| Campo | Valor |
| --- | --- |
| Name | `Infraestructura_y_Cloud` |
| Description | `Recursos de la asignatura Cloud Compunting` |
| Parent compartment | Tu propia cuenta, identificada como `root` |

3. Pulsa **Create Compartment** y comprueba que aparezca `Infraestructura_y_Cloud` en la lista.
4. Guarda ese nombre exacto en `datos-taller.txt`. En los pasos siguientes, selecciona este compartimento cuando la consola solicite dónde crear un recurso.

<img width="428" height="350" alt="image" src="https://github.com/user-attachments/assets/6318bded-5a75-4b98-9075-917b2a5cc0bd" />

<img width="932" height="355" alt="image" src="https://github.com/user-attachments/assets/a9cebd46-cee9-4d41-a9f0-7fa0ebca1f5d" />

<img width="536" height="215" alt="image" src="https://github.com/user-attachments/assets/47c999b3-534f-47e8-80d5-32322f5116e0" />


<a name="paso-1-3"></a>

### Paso 1.3. Crear el grupo de permisos

Crearás un grupo para asignar los permisos al usuario que registrarás después. El grupo reúne usuarios que pueden realizar las mismas acciones; no es necesario darles acceso de administración completa.

1. Abre **Identity & Security > Domains > Default**. El dominio **Default** es el lugar donde administrarás los usuarios de esta cuenta.
2. Selecciona **User management > Groups**; si aparece un menú lateral, selecciona **Groups** desde ese menú.
3. Pulsa **Create group**, escribe `GrupoInfraestructura` en **Name** y `Usuarios de la asignatura Infraestructura y Cloud` en **Description**.
4. Deja sin marcar **User can request access** y pulsa **Create**.
5. Guarda `GrupoInfraestructura` y el dominio `Default` en `datos-taller.txt`. Si tu cuenta tiene otro nombre de dominio, registra y usa ese nombre en los pasos siguientes.


<img width="500" height="266" alt="image" src="https://github.com/user-attachments/assets/ceff1f49-f52b-45cd-8838-12ba0498aeee" />

<img width="373" height="430" alt="image" src="https://github.com/user-attachments/assets/424b627c-cb1e-4983-9161-e3d251d3a46f" />

<img width="931" height="342" alt="image" src="https://github.com/user-attachments/assets/5c9b6c10-965c-4a49-8818-da1e5b37292f" />



<a name="paso-1-4"></a>

### Paso 1.4. Crear la política de acceso

Crearás una política, es decir, una lista de permisos para el grupo. Permitirá manejar la red, el servidor y su disco dentro del compartimento, además de consultar costos y crear una alerta de presupuesto en tu propia cuenta.

1. Abre **Identity & Security > Policies** y selecciona el compartimento raíz de tu cuenta, marcado como `root`.
2. Pulsa **Create Policy**. Escribe `PermisosInfraestructura` en **Name** y `Permisos para publicar y controlar el gasto del taller` en **Description**.
3. Activa **Show manual editor** y pega el bloque completo. Si tu dominio no se llama `Default`, cambia únicamente ese nombre en todas las líneas. Conserva las comillas simples.

```text
Allow group 'Default'/'GrupoInfraestructura' to inspect compartments in tenancy
Allow group 'Default'/'GrupoInfraestructura' to manage instance-family in compartment Infraestructura_y_Cloud
Allow group 'Default'/'GrupoInfraestructura' to manage volume-family in compartment Infraestructura_y_Cloud
Allow group 'Default'/'GrupoInfraestructura' to manage virtual-network-family in compartment Infraestructura_y_Cloud
Allow group 'Default'/'GrupoInfraestructura' to read app-catalog-listing in tenancy
Allow group 'Default'/'GrupoInfraestructura' to use tag-namespaces in tenancy where target.tag-namespace.name='oracle-tags'
Allow group 'Default'/'GrupoInfraestructura' to read usage-report in tenancy
Allow group 'Default'/'GrupoInfraestructura' to use usage-budgets in tenancy
```

4. Pulsa **Create**. Comprueba que la política se guarde sin errores. Si la consola rechaza una línea, verifica el nombre del dominio, del grupo y del compartimento antes de volver a guardarla.
5. Registra `PermisosInfraestructura` en `datos-taller.txt`.

<img width="468" height="633" alt="image" src="https://github.com/user-attachments/assets/5788a7d5-e6be-4ed4-8d53-b36bdd80d059" />

<img width="591" height="616" alt="image" src="https://github.com/user-attachments/assets/90bef9fd-b459-40a5-9b80-eb7b1344193b" />

<img width="636" height="727" alt="image" src="https://github.com/user-attachments/assets/babdcebf-96d3-4693-8732-520f17d4f5e8" />

<img width="905" height="373" alt="image" src="https://github.com/user-attachments/assets/f1a06e2f-22bd-4e75-849f-1c9ebd2aaa1e" />





<a name="paso-1-5"></a>

### Paso 1.5. Crear el usuario de práctica

Crearás un usuario IAM (Identity and Access Management, gestión de identidades y accesos) separado del usuario administrador. Este usuario utilizará los permisos del grupo para completar el taller de publicación.

1. Regresa a **Identity & Security > Domains > Default > User management > Users**. Si aparece un menú lateral, selecciona **Users**.
2. Pulsa **Create** y escribe tu nombre, apellido y correo institucional.
3. Desmarca **Use the email address as the username**. En **Username**, escribe `estudiante-web`.
4. En la selección de grupos, marca únicamente `GrupoInfraestructura`. Pulsa **Create**.
5. Abre el usuario creado y comprueba que su grupo sea `GrupoInfraestructura`. Mantén este usuario fuera del grupo **Administrators** y de los roles de administrador del dominio.
6. Guarda el nombre `estudiante-web` en `datos-taller.txt`.

<img width="543" height="371" alt="image" src="https://github.com/user-attachments/assets/e889f0c9-cf2d-4ff5-9834-cd33453d73d4" />

<img width="705" height="677" alt="image" src="https://github.com/user-attachments/assets/2c92f066-04ce-411c-adff-d40abee0d043" />



<a name="paso-1-6"></a>

### Paso 1.6. Activar el nuevo usuario

Establecerás la contraseña del usuario que acabas de crear para poder iniciar sesión con él. Esta activación corresponde al nuevo usuario; la cuenta del programa académico ya estaba activada antes del taller.

1. Abre el correo de bienvenida del nuevo usuario y utiliza la opción de activación indicada en el mensaje.
2. Establece una contraseña personal y completa la verificación adicional que solicite la plataforma.
3. Si el mensaje no llegó, revisa correo no deseado. Desde tu sesión administradora, vuelve a **Domains > Default > Users**, marca `estudiante-web` y selecciona **Actions** o **More actions > Resend invitation**. Confirma con **Send invitation** y revisa nuevamente tu correo.

<img width="1357" height="787" alt="image" src="https://github.com/user-attachments/assets/e1ecf37f-658d-4b2b-bbe6-efad7949372d" />
<img width="742" height="757" alt="image" src="https://github.com/user-attachments/assets/4dba57a2-d76c-434c-b99c-725b2ee28701" />
<img width="802" height="686" alt="image" src="https://github.com/user-attachments/assets/a652162b-4bae-44e6-a617-3f74de075711" />
<img width="636" height="752" alt="image" src="https://github.com/user-attachments/assets/1f989c1b-e21c-49a9-bf8b-5ba12992f072" />

<img width="738" height="806" alt="image" src="https://github.com/user-attachments/assets/fbe5dc47-ea2b-495a-9072-85e74a75f2dd" />
<img width="767" height="637" alt="image" src="https://github.com/user-attachments/assets/f18f5fd3-dad9-4bb2-91fa-f3d4c5c39842" />
<img width="1880" height="793" alt="image" src="https://github.com/user-attachments/assets/21a51390-adf2-4b99-999e-a21b00971313" />






<a name="paso-1-7"></a>

### Paso 1.7. Comprobar el acceso y guardar el resultado

Comprobarás que el nuevo usuario puede trabajar en el compartimento antes de crear el servidor. Esta prueba permite detectar errores de permisos sin crear recursos que consuman crédito.

1. Conserva abierta tu sesión administradora y abre una ventana privada del navegador.
2. Escribe `cloud.oracle.com`, indica el nombre de tu cuenta, selecciona tu dominio e inicia sesión con `estudiante-web` y su contraseña.
3. Selecciona la región registrada. Abre **Compute > Instances** y elige `Infraestructura_y_Cloud` en el filtro de compartimento.
4. Abre **Create Instance** y cancela el formulario sin crear una máquina. Comprueba también que puedas abrir **Networking > Virtual cloud networks** y seleccionar el mismo compartimento.
5. Si aparece falta de permisos, regresa a la sesión administradora y revisa la política del [paso 1.4](#paso-1-4). Después de corregirla, cierra y vuelve a iniciar la sesión del usuario de práctica.
6. Guarda `datos-taller.txt` y una captura de la lista de instancias donde se vea el compartimento seleccionado. Conserva el archivo para el taller 2.

[Volver a la tabla de contenido](#indice) · [Continuar con la fase 2](#fase-2)

---

<a name="fase-2"></a>

## Fase 2. Crear la red de la asignatura

Continúa con la sesión de `estudiante-web`, la región registrada y el compartimento `Infraestructura_y_Cloud`. Crearás una red con una subred pública para alojar el servidor. La salida a Internet y su ruta permitirán que la máquina se comunique con otros servicios; las reglas de entrada permitirán administrarla y abrir la página desde un navegador.

<a name="paso-2-1"></a>

### Paso 2.1. Crear la red virtual

Crearás una VCN (Virtual Cloud Network, red virtual en la nube) para definir el espacio de direcciones donde estará el servidor. El rango `10.0.0.0/16` delimita las direcciones internas disponibles para la red de esta práctica.

1. Abre **Networking > Virtual cloud networks** y selecciona `Infraestructura_y_Cloud`.
2. Pulsa **Create VCN (Virtual Cloud Network, red virtual en la nube)**, usa el formulario de creación manual y completa:

| Campo | Valor |
| --- | --- |
| Name | `red-infraestructura` |
| Compartment | `Infraestructura_y_Cloud` |
| IPv4 (Internet Protocol version 4, protocolo de Internet versión 4) CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases) Blocks | `10.0.0.0/16` |

3. Deja sin habilitar IPv6 (Internet Protocol version 6, protocolo de Internet versión 6) y conserva los demás valores predeterminados. Pulsa **Create VCN (Virtual Cloud Network, red virtual en la nube)**.
4. Guarda el nombre `red-infraestructura` en `datos-taller.txt`.

<a name="paso-2-2"></a>

### Paso 2.2. Crear la salida a Internet y su ruta

Crearás un **Internet Gateway**, que conecta esta red con Internet. Después indicarás en la tabla de rutas que las conexiones hacia direcciones externas deben usar esa salida.

1. Dentro de `red-infraestructura`, abre **Gateways > Internet gateways > Create Internet Gateway**.
2. Escribe `salida-internet`, selecciona `Infraestructura_y_Cloud` y deja activado **Enable Internet Gateway**. Pulsa **Create Internet Gateway**.
3. Abre **Routing > Route tables > Default Route Table for red-infraestructura > Add Route Rules**.
4. Completa y guarda la regla:

| Campo | Valor |
| --- | --- |
| Target Type | **Internet Gateway** |
| Destination Type | **CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases) Block** |
| Destination CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases) Block | `0.0.0.0/0` |
| Target | `salida-internet` |

El destino `0.0.0.0/0` representa las direcciones externas a las que la máquina podrá enviar tráfico por esta salida.

<a name="paso-2-3"></a>

### Paso 2.3. Crear la subred pública

Crearás una subred, es decir, una parte de la red donde se ubicará la máquina. Será pública para que el servidor pueda tener una dirección accesible desde Internet.

1. Regresa a `red-infraestructura` y abre **Subnets > Create Subnet**.
2. Completa los campos siguientes y conserva los demás valores predeterminados:

| Campo | Valor |
| --- | --- |
| Name / Compartment | `subred-web` / `Infraestructura_y_Cloud` |
| Subnet Type | **Regional** |
| IPv4 (Internet Protocol version 4, protocolo de Internet versión 4) CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases) Block | `10.0.0.0/24` |
| Subnet Access | **Public Subnet** |
| Route Table | **Default Route Table for red-infraestructura** |
| Security Lists | **Default Security List for red-infraestructura** |

3. Pulsa **Create Subnet** y guarda `subred-web` en `datos-taller.txt`.

<a name="paso-2-4"></a>

### Paso 2.4. Permitir la conexión y el acceso a la página

Configurarás la lista de seguridad, que controla las conexiones permitidas a la subred. El puerto **22** permite la conexión SSH (Secure Shell, conexión segura para administrar el servidor) de administración; el puerto **80** permite las solicitudes HTTP (HyperText Transfer Protocol, protocolo de transferencia de hipertexto) de la página. La conexión de administración exigirá la llave privada que guardarás al crear la máquina en el taller 2.

1. Abre **Security > Security Lists > Default Security List for red-infraestructura > Ingress Rules**.
2. Si ya existe la regla del puerto 22, comprueba los valores de la tabla. Si falta, pulsa **Add Ingress Rules** y créala. Agrega también la regla del puerto 80.

| Source CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases) | IP (Internet Protocol, protocolo de Internet) Protocol | Source Port Range | Destination Port Range |
| --- | --- | --- | --- |
| `0.0.0.0/0` | **TCP (Transmission Control Protocol, protocolo de control de transmisión)** | **All** | `22` |
| `0.0.0.0/0` | **TCP (Transmission Control Protocol, protocolo de control de transmisión)** | **All** | `80` |

3. En ambas reglas, usa **Source Type: CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases)** y deja **Stateless** desmarcado para que se reconozcan las respuestas de las conexiones. Guarda con **Add Ingress Rules** cuando hayas creado una regla.
4. En **Egress Rules**, comprueba una regla con **Destination CIDR (Classless Inter-Domain Routing, enrutamiento entre dominios sin clases): `0.0.0.0/0`**, **IP (Internet Protocol, protocolo de Internet) Protocol: All Protocols** y **Stateless** desmarcado. Si no existe, créala con esos valores. Mantén las demás reglas predeterminadas.
5. Abre `subred-web` y verifica que use la tabla de rutas y la lista de seguridad de esta fase.

[Volver a la tabla de contenido](#indice)
