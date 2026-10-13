# Taller práctico: desplegar una página web con IaaS en OCI Compute

**Tiempo previsto:** 110 minutos, con la cuenta, el repositorio y la capacidad disponibles.

Abre este archivo en Visual Studio Code y pulsa **Ctrl+Shift+V** en Windows o **Cmd+Shift+V** en macOS para seguir el taller en la vista previa. Usa el índice para ir a una fase o a un paso. Consulta [la ayuda de Markdown en VS Code](https://code.visualstudio.com/docs/languages/markdown) si necesitas abrir la vista previa.

<a name="indice"></a>

## Tabla de contenido

- [Fase 1. Preparar el compartimento, la red y el usuario IAM](#fase-1)
  - [Paso 1.1. Crear el compartimento](#paso-1-1)
  - [Paso 1.2. Crear la red y habilitar la conexión SSH](#paso-1-2)
  - [Paso 1.3. Crear el grupo, la política y el usuario IAM](#paso-1-3)
  - [Paso 1.4. Iniciar sesión con el usuario del taller](#paso-1-4)
- [Fase 2. Preparar las claves SSH](#fase-2)
  - [Paso 2.1. Generar el par de claves](#paso-2-1)
  - [Paso 2.2. Copiar la clave pública](#paso-2-2)
- [Fase 3. Crear la máquina virtual](#fase-3)
  - [Paso 3.1. Abrir el formulario de creación](#paso-3-1)
  - [Paso 3.2. Configurar el nombre y la ubicación](#paso-3-2)
  - [Paso 3.3. Seleccionar el sistema y el tamaño](#paso-3-3)
  - [Paso 3.4. Configurar la red, la clave y el disco](#paso-3-4)
- [Fase 4. Conectarse e instalar Nginx](#fase-4)
  - [Paso 4.1. Conectarse mediante SSH](#paso-4-1)
  - [Paso 4.2. Instalar Nginx y abrir el acceso web del servidor](#paso-4-2)
- [Fase 5. Publicar el código y habilitar el acceso público](#fase-5)
  - [Paso 5.1. Descargar y publicar la página del docente](#paso-5-1)
  - [Paso 5.2. Abrir el puerto 80 en OCI](#paso-5-2)
- [Fase 6. Probar la página, revisar costos y cerrar la práctica](#fase-6)
  - [Paso 6.1. Validar la página desde el navegador](#paso-6-1)
  - [Paso 6.2. Revisar costos y configurar una alerta](#paso-6-2)
  - [Paso 6.3. Eliminar la máquina y su disco](#paso-6-3)

---

<a name="fase-1"></a>

## Fase 1. Preparar el compartimento, la red y el usuario IAM

**Tiempo previsto:** 30 minutos.

<a name="paso-1-1"></a>

### Paso 1.1. Crear el compartimento

1. Solicita al docente tu identificador, el nombre de la cuenta OCI, su región principal y el enlace del repositorio público de la página. Usa un repositorio dedicado a archivos estáticos, con `index.html` en la raíz y sus archivos CSS y JavaScript; comprueba que no contenga claves ni archivos `.env`.
2. Usa `est01` como ejemplo y sustitúyelo por tu identificador en todos los nombres. Confirma el turno y el cupo de Compute y almacenamiento. Consulta [los límites de Always Free](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm); aplica los cupos a la cuenta completa, sin multiplicarlos por el número de usuarios.
3. Abre [la consola de OCI](https://cloud.oracle.com/) y selecciona la región principal asignada. Realiza la fase 1 junto al docente, usando una sesión autorizada para administrar usuarios, políticas y recursos. En una cuenta compartida, permite que el docente inicie su sesión sin entregar su contraseña.
4. Abre **Identity & Security > Compartments > Create Compartment**. Completa los campos y pulsa **Create Compartment**.

| Campo | Valor |
| --- | --- |
| Name | `ecored-dev-est01` |
| Description | `Practica web de est01` |
| Parent compartment | La cuenta del curso, identificada como `root` |

5. Crea un archivo local `datos-taller.txt` en VS Code. Guarda el identificador, la cuenta, la región, el compartimento y el enlace del repositorio. No guardes contraseñas ni el contenido de la clave privada.

Consulta [cómo crear un compartimento](https://docs.oracle.com/en-us/iaas/Content/Identity/compartments/To_create_a_compartment.htm) si el formulario muestra los campos en otra posición.

<a name="paso-1-2"></a>

### Paso 1.2. Crear la red y habilitar la conexión SSH

1. Abre **Networking > Virtual cloud networks**, selecciona `ecored-dev-est01` y pulsa **Create VCN**. Usa el formulario de creación manual y completa:

| Campo | Valor |
| --- | --- |
| Name | `vcn-est01` |
| Compartment | `ecored-dev-est01` |
| IPv4 CIDR Blocks | `10.0.0.0/16` |
| Use DNS Hostnames in this VCN | Activado |
| DNS Label | `vcnest01` |
| IPv6 | Sin habilitar |

2. Crea la VCN. Dentro de ella abre **Gateways > Internet gateways > Create Internet Gateway**. Escribe `igw-est01`, selecciona tu compartimento, deja activado **Enable Internet Gateway** y confirma. Crea solamente esta salida a Internet.
3. En la VCN abre **Routing > Route tables > Default Route Table for vcn-est01 > Add Route Rules**. Selecciona **Target Type: Internet Gateway**, **Destination Type: CIDR Block**, **Destination CIDR Block: `0.0.0.0/0`** y **Target: `igw-est01`**. Guarda la regla.
4. Vuelve a la VCN y abre **Subnets > Create Subnet**. Completa la tabla y crea la subred.

| Campo | Valor |
| --- | --- |
| Name / Compartment | `subnet-publica-est01` / `ecored-dev-est01` |
| Subnet Type | **Regional** |
| IPv4 CIDR Block | `10.0.0.0/24` |
| Subnet Access | **Public Subnet** |
| Route Table | **Default Route Table for vcn-est01** |
| DHCP Options | **Default DHCP Options for vcn-est01** |
| Security Lists | **Default Security List for vcn-est01** |
| DNS Label | `web` |

5. Desde el mismo equipo y la misma conexión que usarás para SSH, abre [la consulta de IPv4 pública de ipify](https://api.ipify.org/). Guarda el número mostrado en `datos-taller.txt` como `IP_EQUIPO`. Añade `/32` al final para usarlo como origen de la regla SSH.
6. En la VCN abre **Security > Security Lists > Default Security List for vcn-est01 > Ingress Rules**. Edita la regla del puerto **22**: usa **Source Type: CIDR**, **Source CIDR: tu `IP_EQUIPO/32`**, **IP Protocol: TCP**, **Source Port Range: All** y **Destination Port Range: 22**. Deja **Stateless** desmarcado. Si falta la regla, créala con esos valores.
7. En **Egress Rules**, comprueba una regla hacia `0.0.0.0/0` con **All Protocols** y **Stateless** desmarcado. Si falta, agrégala. Conserva las reglas ICMP existentes. Guarda los nombres de la VCN y la subred en `datos-taller.txt`.

Consulta los formularios oficiales de [VCN](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/create_vcn.htm), [Internet Gateway](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/create-ig.htm), [reglas de ruta](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/update-rules-routetable.htm) y [subred](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/create_subnet.htm) si necesitas localizar un campo.

<a name="paso-1-3"></a>

### Paso 1.3. Crear el grupo, la política y el usuario IAM

1. Abre **Identity & Security > Domains > Default > User management > Groups > Create group**. Si aparece un menú lateral, selecciona **Groups**. Escribe `DesarrolladoresWeb-est01`, agrega una descripción y deja sin marcar **User can request access**. Pulsa **Create**.
2. Abre **Identity & Security > Policies**, selecciona el compartimento raíz de la cuenta y pulsa **Create Policy**. Escribe `Politica-Web-est01`, agrega una descripción y activa **Show manual editor**.
3. Copia el bloque completo. Sustituye `est01` por tu identificador. Si el docente usa otro dominio, reemplaza `Default` por su nombre exacto. Conserva las comillas simples y pulsa **Create**.

```text
Allow group 'Default'/'DesarrolladoresWeb-est01' to inspect compartments in tenancy
Allow group 'Default'/'DesarrolladoresWeb-est01' to manage instance-family in compartment ecored-dev-est01
Allow group 'Default'/'DesarrolladoresWeb-est01' to manage volume-family in compartment ecored-dev-est01
Allow group 'Default'/'DesarrolladoresWeb-est01' to manage virtual-network-family in compartment ecored-dev-est01
Allow group 'Default'/'DesarrolladoresWeb-est01' to read app-catalog-listing in tenancy
Allow group 'Default'/'DesarrolladoresWeb-est01' to use tag-namespaces in tenancy where target.tag-namespace.name='oracle-tags'
```

4. Regresa a **Domains > Default > User management > Users > Create**. Escribe tu nombre, apellido y correo institucional. Desmarca **Use the email address as the username**, escribe `est01` en **Username** y selecciona únicamente `DesarrolladoresWeb-est01` como grupo. Pulsa **Create**.
5. Abre el usuario y verifica su grupo. Mantén esta cuenta fuera de **Administrators** y de los roles de administrador del dominio. Guarda el dominio, el usuario y el grupo en `datos-taller.txt`.

Consulta las guías oficiales para [crear grupos](https://docs.oracle.com/en-us/iaas/Content/Identity/groups/create-groups.htm), [asignar permisos de Compute y red](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm) y [crear usuarios](https://docs.oracle.com/en-us/iaas/Content/Identity/users/create-user-accounts.htm).

<a name="paso-1-4"></a>

### Paso 1.4. Iniciar sesión con el usuario del taller

1. Abre el correo de activación de OCI y establece tu contraseña personal. Si no llega, revisa correo no deseado y solicita al docente reenviar la activación. Completa la verificación adicional que solicite OCI.
2. Cierra la sesión de administración. Abre [la consola de OCI](https://cloud.oracle.com/) en una ventana privada, escribe el nombre de la cuenta y selecciona el dominio registrado.
3. Inicia sesión con tu usuario IAM, selecciona tu región y abre **Compute > Instances**. En el filtro de compartimento elige `ecored-dev-est01`.
4. Comprueba que puedas abrir **Create Instance** y cancela el formulario. Si falta un permiso, solicita al docente revisar la política del [paso 1.3](#paso-1-3); vuelve a iniciar sesión después de corregirla.

[Volver al índice](#indice) · [Continuar con la fase 2: claves SSH](#fase-2)

---

<a name="fase-2"></a>

## Fase 2. Preparar las claves SSH

**Tiempo previsto:** 10 minutos.

<a name="paso-2-1"></a>

### Paso 2.1. Generar el par de claves

1. Abre **Terminal > New Terminal** en VS Code. En Windows selecciona **PowerShell**. Ejecuta `ssh -V` y comprueba que aparezca una versión de OpenSSH. Si el comando no existe, instala el **cliente OpenSSH** siguiendo [la guía de Microsoft](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse).
2. En Windows ejecuta el bloque, sustituyendo `est01` por tu identificador:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.ssh" | Out-Null
ssh-keygen -t rsa -b 3072 -f "$env:USERPROFILE\.ssh\oci-web-est01"
```

3. Si trabajas en macOS, ejecuta este bloque en la terminal integrada en lugar del bloque de Windows:

```bash
mkdir -p ~/.ssh
ssh-keygen -t rsa -b 3072 -f ~/.ssh/oci-web-est01
```

4. Define y confirma una frase de contraseña cuando se solicite. Si aparece la pregunta para sobrescribir una clave existente, responde `n` y conserva esa clave; usa otro nombre para la nueva clave y registra el cambio.

Consulta [la guía de claves SSH de Microsoft](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement) si necesitas identificar los archivos generados.

<a name="paso-2-2"></a>

### Paso 2.2. Copiar la clave pública

1. En Windows ejecuta el bloque para mostrar la clave pública y la ruta del archivo privado:

```powershell
Get-Content "$env:USERPROFILE\.ssh\oci-web-est01.pub"
(Get-Item "$env:USERPROFILE\.ssh\oci-web-est01").FullName
```

2. En macOS ejecuta:

```bash
cat ~/.ssh/oci-web-est01.pub
printf '%s\n' "$HOME/.ssh/oci-web-est01"
```

3. Copia solamente la línea que comienza con `ssh-rsa`, completa y sin saltos intermedios. Conserva ese texto para el [paso 3.4](#paso-3-4).
4. Guarda la ruta del archivo privado en `datos-taller.txt` como `RUTA_CLAVE_PRIVADA`. Conserva el archivo privado en tu equipo y no lo pegues en OCI, GitHub ni en la entrega del taller.

[Volver al índice](#indice) · [Continuar con la fase 3: máquina virtual](#fase-3)

---

<a name="fase-3"></a>

## Fase 3. Crear la máquina virtual

**Tiempo previsto:** 20 minutos.

<a name="paso-3-1"></a>

### Paso 3.1. Abrir el formulario de creación

1. Usa la sesión del usuario IAM del [paso 1.4](#paso-1-4).
2. Abre **Compute > Instances**, selecciona `ecored-dev-est01` y pulsa **Create Instance**.

Consulta [el formulario oficial de creación de instancias](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/launchinginstance.htm) para localizar sus secciones.

<a name="paso-3-2"></a>

### Paso 3.2. Configurar el nombre y la ubicación

Completa los campos siguientes. En **Placement**, selecciona el dominio con capacidad indicado por el docente. Mantén las opciones de seguridad predeterminadas que sean compatibles con la imagen y el tamaño elegidos.

| Campo | Valor |
| --- | --- |
| Name | `servidor-web-est01` |
| Create in compartment | `ecored-dev-est01` |
| Availability domain | El dominio asignado por el docente |
| Capacity type | **On-demand capacity** |

<a name="paso-3-3"></a>

### Paso 3.3. Seleccionar el sistema y el tamaño

1. En **Image and shape > Edit > Change Image**, selecciona la imagen de plataforma **Oracle Linux 9**.
2. En **Change Shape**, selecciona **Virtual Machine** y busca **VM.Standard.E2.1.Micro** entre las unidades AMD o de generación anterior, según el selector. Comprueba **Always Free-eligible**, la región principal y el cupo disponible.
3. Verifica **1 GB de memoria**. Consulta [las especificaciones de E2.1.Micro](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm): esta unidad ofrece **1/8 de OCPU** como base; evita registrarla como un núcleo completo.
4. Si la imagen y la unidad no son compatibles o falta capacidad, solicita al docente una combinación disponible y autorizada antes de continuar. Usa únicamente la alternativa con el cupo y el costo revisados.

<a name="paso-3-4"></a>

### Paso 3.4. Configurar la red, la clave y el disco

1. En **Networking**, selecciona la VCN existente `vcn-est01` y la subred existente `subnet-publica-est01`. Usa `ecored-dev-est01` en los selectores de compartimento.
2. Activa **Assign a public IPv4 address** y conserva la dirección privada automática. Deja sin habilitar **Network security groups** para usar la lista de seguridad de esta práctica.
3. En **Add SSH keys**, selecciona **Paste public keys** y pega la línea pública obtenida en el [paso 2.2](#paso-2-2).
4. En **Boot volume**, conserva el tamaño predeterminado de la imagen, comprueba que quepa en el almacenamiento asignado y deja sin configurar discos adicionales y copias de seguridad.
5. Revisa el resumen y pulsa **Create**. Espera el estado **Running** y dos minutos adicionales para el arranque. Si la creación falla, revisa el mensaje antes de intentar crear otra instancia.
6. Abre la instancia y guarda **Public IP address** en `datos-taller.txt` como `IP_SERVIDOR`. Usa esta dirección para SSH y para abrir la página.

[Volver al índice](#indice) · [Continuar con la fase 4: conexión e instalación](#fase-4)

---

<a name="fase-4"></a>

## Fase 4. Conectarse e instalar Nginx

**Tiempo previsto:** 15 minutos.

<a name="paso-4-1"></a>

### Paso 4.1. Conectarse mediante SSH

1. En la terminal local de VS Code, sustituye `RUTA_CLAVE_PRIVADA` y `IP_SERVIDOR` por los valores reales de `datos-taller.txt`. Conserva las comillas de la ruta y ejecuta:

```text
ssh -i "RUTA_CLAVE_PRIVADA" opc@IP_SERVIDOR
```

2. En la primera conexión confirma con `yes` y escribe la frase de contraseña de tu clave cuando se solicite. Comprueba que la nueva línea de comandos corresponda al usuario `opc` del servidor.
3. Si aparece **Connection timed out**, revisa la IP pública, el estado **Running**, la ruta a Internet y la regla SSH del [paso 1.2](#paso-1-2). Si cambió la conexión de tu equipo, vuelve a consultar su IPv4 y actualiza el origen `/32` de esa regla.
4. Si aparece **Permission denied (publickey)**, comprueba que elegiste el archivo privado asociado a la clave pública del [paso 3.4](#paso-3-4).

<a name="paso-4-2"></a>

### Paso 4.2. Instalar Nginx y abrir el acceso web del servidor

1. Dentro de la sesión del servidor ejecuta el bloque. Espera la instalación y comprueba que la última orden devuelva `active`.

```bash
sudo dnf install -y nginx git
sudo systemctl enable --now nginx
systemctl is-active nginx
```

2. Ejecuta el bloque para permitir HTTP en el servidor. Comprueba `success` en las órdenes del firewall y una respuesta `200 OK` en `curl`.

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
curl -I http://localhost
```

Consulta [la instalación y configuración oficial de Nginx](https://docs.oracle.com/en/learn/ol-nginx/index.html) si falla el servicio. Continúa con las órdenes de la fase 5 en esta misma sesión del servidor.

[Volver al índice](#indice) · [Continuar con la fase 5: publicación](#fase-5)

---

<a name="fase-5"></a>

## Fase 5. Publicar el código y habilitar el acceso público

**Tiempo previsto:** 15 minutos.

<a name="paso-5-1"></a>

### Paso 5.1. Descargar y publicar la página del docente

1. En la sesión del servidor, reemplaza `URL_DEL_REPOSITORIO` por la dirección HTTPS del repositorio público entregado por el docente. Usa la dirección de clonación, por ejemplo la que termina en `.git`, conservando las comillas. Ejecuta:

```bash
git clone --depth 1 "URL_DEL_REPOSITORIO" ~/sitio-docente
ls -la ~/sitio-docente
test -f ~/sitio-docente/index.html && echo "index.html encontrado"
```

2. Comprueba `index.html encontrado` y que estén los archivos CSS y JavaScript referenciados por ese HTML. Si el HTML está dentro de otra carpeta, solicita al docente la ubicación correcta antes de publicar. Si ya existe `sitio-docente` de un intento anterior con el mismo repositorio, actualízalo con `git -C ~/sitio-docente pull --ff-only` en lugar de volver a clonarlo.
3. Publica los archivos del último commit del repositorio y comprueba el resultado:

```bash
git -C ~/sitio-docente archive --format=tar HEAD | sudo tar -x -C /usr/share/nginx/html
sudo restorecon -Rv /usr/share/nginx/html
sudo nginx -t
curl -I http://localhost/index.html
```

4. Comprueba que Nginx indique configuración válida y que `curl` devuelva `200 OK`. Abre `http://localhost/index.html` con `curl` si necesitas verificar que el HTML corresponde al código del docente.

Consulta [el funcionamiento de `git archive`](https://git-scm.com/docs/git-archive) si necesitas revisar el comando que publica los archivos del commit sin copiar la carpeta `.git`.

<a name="paso-5-2"></a>

### Paso 5.2. Abrir el puerto 80 en OCI

1. Regresa a la consola web de OCI, manteniendo la sesión SSH abierta.
2. Abre **Networking > Virtual cloud networks > vcn-est01 > Security > Security Lists > Default Security List for vcn-est01**.
3. En **Ingress Rules**, pulsa **Add Ingress Rules**, completa la tabla y guarda la regla. Conserva la regla SSH del [paso 1.2](#paso-1-2).

| Campo | Valor |
| --- | --- |
| Stateless | Desmarcado |
| Source Type | **CIDR** |
| Source CIDR | `0.0.0.0/0` |
| IP Protocol | **TCP** |
| Source Port Range | **All** |
| Destination Port Range | `80` |
| Description | `Pagina web HTTP` |

4. Abre `subnet-publica-est01` y comprueba que use esta lista de seguridad y la tabla de rutas del [paso 1.2](#paso-1-2).

Consulta [la guía oficial de listas de seguridad](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm) si necesitas identificar la lista asociada a la subred.

[Volver al índice](#indice) · [Continuar con la fase 6: pruebas y cierre](#fase-6)

---

<a name="fase-6"></a>

## Fase 6. Probar la página, revisar costos y cerrar la práctica

**Tiempo previsto:** 20 minutos.

<a name="paso-6-1"></a>

### Paso 6.1. Validar la página desde el navegador

1. En el navegador de tu equipo abre `http://IP_SERVIDOR/index.html`, reemplazando `IP_SERVIDOR` por la dirección pública registrada. Usa `http` en esta práctica.
2. Comprueba el contenido y el diseño del docente. Prueba el botón, el menú u otra interacción incluida en el JavaScript.
3. Abre las herramientas de desarrollo con **F12 > Network**, recarga y verifica estado **200** para el HTML y sus archivos CSS y JavaScript. Revisa **Console** y confirma que no haya errores asociados a esos archivos.
4. Abre la misma dirección en una ventana privada o en un teléfono con datos móviles. Repite una interacción y guarda una captura con la dirección y la página.
5. Si la página no abre, revisa **Running**, la IP pública y el [puerto 80 de OCI](#paso-5-2). Desde la sesión SSH ejecuta `systemctl is-active nginx` y `sudo firewall-cmd --list-services`; comprueba `active` y `http`. Si falla el servicio, vuelve al [paso 4.2](#paso-4-2). Si falta un archivo, revisa el [paso 5.1](#paso-5-1).

<a name="paso-6-2"></a>

### Paso 6.2. Revisar costos y configurar una alerta

1. Realiza este paso junto al docente en una sesión con permisos de facturación. En una cuenta compartida, permite que él opere esa sesión; conserva tus permisos de práctica. Consulta [los permisos de Cost Analysis](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/costanalysisoverview.htm) y [los permisos de Budgets](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/budgetsoverview.htm) si OCI muestra falta de autorización.
2. Abre **Billing & Cost Management > Cost Management > Cost Analysis**. Selecciona el período, filtra por `ecored-dev-est01`, agrupa por **Service** y pulsa **Apply**. Registra el período y el resultado en `datos-taller.txt`. Si aún no hay datos, registra ese resultado y vuelve a consultar después: la información puede tardar hasta 48 horas en aparecer.
3. Abre **Billing & Cost Management > Cost Management > Budgets > Create Budget**. Selecciona **Budget Scope: Compartment**, **Target Compartment: `ecored-dev-est01`**, **Name: `Presupuesto-Web-est01`** y **Schedule: Monthly**. En **Budget Amount**, usa el monto autorizado por el docente y comprueba la moneda de la cuenta. Usa `5` únicamente si ese monto está autorizado. Si el compartimento ya tiene un presupuesto, abre el existente.
4. En la sección de alerta del formulario o, después de crear el presupuesto, en **Budget Alert Rules > Create Budget Alert Rule**, selecciona **Actual Spend**, **Percentage of Budget** y **Threshold %: 80**. Escribe el correo autorizado para recibir la alerta y guarda. Comprueba que la regla aparezca asociada al presupuesto.
5. Registra el monto, la moneda y el umbral. Usa esta alerta para vigilar el gasto; no la tomes como un bloqueo de cobros ni como una orden automática para apagar la máquina. Continúa con el [paso 6.3](#paso-6-3) después de la revisión de la entrega.

Consulta los formularios oficiales de [creación de presupuestos](https://docs.oracle.com/en-us/iaas/Content/Billing/Tasks/create-budget.htm) y [creación de alertas](https://docs.oracle.com/en-us/iaas/Content/Billing/Tasks/create-alert-rule.htm). Comprueba en [cómo funcionan los presupuestos de OCI](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/budgetsoverview.htm) que se usan como límites de aviso.

<a name="paso-6-3"></a>

### Paso 6.3. Eliminar la máquina y su disco

1. Guarda `datos-taller.txt`, la captura y la dirección de la página. Entrega las evidencias por el medio indicado y espera la revisión del docente antes de eliminar los recursos.
2. En la sesión SSH ejecuta `exit` para volver a la terminal de tu equipo.
3. Con tu usuario del taller, abre **Compute > Instances**, selecciona `ecored-dev-est01` y localiza `servidor-web-est01`. En el menú de acciones de esa instancia selecciona **Terminate**.
4. Activa **Permanently delete the attached boot volume** y confirma la terminación. Espera **Terminated**. En **Storage > Block Storage > Boot Volumes**, selecciona tu compartimento y el mismo dominio; verifica que el disco de esta máquina esté eliminado o en **Terminated**.
5. Si el docente indica retirar toda la red, elimina primero `subnet-publica-est01`, luego la regla de ruta hacia `igw-est01`, después `igw-est01` y finalmente `vcn-est01`. Comprueba en cada acción que el nombre y el compartimento correspondan a esta práctica.
6. Cierra la sesión de OCI. Conserva las evidencias y el código del docente.

Consulta [cómo terminar una instancia y eliminar su disco de arranque](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/terminatinginstance.htm) antes de confirmar la acción.

[Volver al índice](#indice) · [Volver a la fase 1](#fase-1)
