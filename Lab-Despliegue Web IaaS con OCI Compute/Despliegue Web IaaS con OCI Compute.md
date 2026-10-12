# Taller Práctico: Fundamentos de OCI - IaaS, Redes, IAM y Gestión de Costos

**Objetivo:** Comprender las bases de Oracle Cloud Infrastructure (OCI) aplicando las mejores prácticas de seguridad. Partiendo desde una cuenta con acceso completo (root), el estudiante preparará un entorno aislado (Compartment y VCN), creará un usuario IAM sin privilegios root, y finalmente desplegará y monitoreará un servidor web (IaaS) detallando la configuración de la instancia.

**Referencia Oficial:** [Documentación de Oracle Cloud Infrastructure](https://docs.oracle.com/es-ww/iaas/Content/home.htm)

---

## Índice del Taller

* [Fase 1: Preparación del Entorno (Compartment, VCN e IAM)](#fase-1)
  * [1.1. Crear un Compartment (Compartimento)](#paso-1-1)
  * [1.2. Crear una Red Virtual en la Nube (VCN)](#paso-1-2)
  * [1.3. Crear un Grupo, Políticas y un Usuario IAM](#paso-1-3)
  * [1.4. Iniciar sesión con el Usuario IAM](#paso-1-4)
* [Fase 2: Generación de Claves SSH](#fase-2)
  * [2.1. Generar un par de claves SSH](#paso-2-1)
  * [2.2. Obtener la clave pública](#paso-2-2)
* [Fase 3: Aprovisionamiento de la Máquina Virtual (IaaS)](#fase-3)
  * [3.1. Iniciar el asistente de creación de instancias](#paso-3-1)
  * [3.2. Configurar la Información Básica y Ubicación (Placement)](#paso-3-2)
  * [3.3. Configurar Imagen y Unidad (Image and Shape)](#paso-3-3)
  * [3.4. Configurar Redes e Inyectar la Clave SSH](#paso-3-4)
* [Fase 4: Conexión SSH y Configuración del Sistema Operativo](#fase-4)
  * [4.1. Conectarse a la instancia vía SSH](#paso-4-1)
  * [4.2. Instalar el servidor web Nginx y configurar el Firewall](#paso-4-2)
* [Fase 5: Despliegue del Código y Apertura de Puertos](#fase-5)
  * [4.1. Desplegar el código vía Git](#paso-5-1)
  * [4.2. Abrir el puerto en la Security List de OCI](#paso-5-2)
* [Fase 6: Validación, Pruebas y Monitoreo de Costos](#fase-6)
  * [6.1. Verificar en el navegador](#paso-6-1)
  * [6.2. Explorar el Análisis de Costos y crear Presupuestos](#paso-6-2)

---

## <a name="fase-1"></a>Fase 1: Preparación del Entorno (Compartment, VCN e IAM)

Trabajar desde el usuario `root` de OCI es una mala práctica de seguridad. Empezaremos creando un entorno de trabajo aislado.

### <a name="paso-1-1"></a>1.1. Crear un Compartment (Compartimento)
Los compartimentos son contenedores lógicos para organizar y aislar recursos en OCI.
1. Ingresa a la consola de OCI con tu cuenta *root*.
2. En el menú principal, ve a **Identity & Security** > **Compartments**.
3. Haz clic en **Create Compartment**.
4. Nómbralo `ecored-dev` (o el nombre sugerido por el docente) y añade una descripción. Haz clic en **Create Compartment**.

### <a name="paso-1-2"></a>1.2. Crear una Red Virtual en la Nube (VCN)
Todo servidor necesita una red para comunicarse.
1. Ve a **Networking** > **Virtual Cloud Networks**.
2. A la izquierda, asegúrate de seleccionar el compartimento recién creado (`ecored-dev`).
3. Haz clic en **Start VCN Wizard** y selecciona **Create VCN with Internet Connectivity**.
4. Nómbrala `VCN-Taller`. El asistente creará automáticamente una subred pública, una privada y los *gateways* necesarios. Haz clic en **Next** y luego en **Create**.

### <a name="paso-1-3"></a>1.3. Crear un Grupo, Políticas y un Usuario IAM
1. **Crear el Grupo:** Ve a **Identity & Security** > **Domains** > **Default domain** > **Groups**. Haz clic en **Create group** y nómbralo `DesarrolladoresWeb`.
2. **Crear la Política:** Ve a **Identity & Security** > **Policies** (asegúrate de estar en el compartimento raíz). Haz clic en **Create Policy**. 
   * Nombre: `Politica-Desarrolladores`.
   * Creador de sentencias manual: Ingresa la regla `Allow group DesarrolladoresWeb to manage all-resources in compartment ecored-dev`. Esto otorga permisos totales, pero **solo** dentro de ese compartimento.
3. **Crear el Usuario:** Ve a **Users** (dentro del dominio). Haz clic en **Create user**. Ingresa tu nombre, apellido y un correo electrónico (esto creará un usuario IAM).
4. **Asignar al Grupo:** Entra a los detalles del usuario creado, baja a **Groups** y asígnalo al grupo `DesarrolladoresWeb`.

### <a name="paso-1-4"></a>1.4. Iniciar sesión con el Usuario IAM
1. Cierra la sesión de tu cuenta *root*.
2. Revisa tu correo electrónico para establecer la contraseña de tu nuevo usuario IAM.
3. Vuelve a iniciar sesión en OCI utilizando estas nuevas credenciales. Ahora trabajarás de forma segura.

---

## <a name="fase-2"></a>Fase 2: Generación de Claves SSH

### <a name="paso-2-1"></a>2.1. Generar un par de claves SSH
Abre tu terminal (PowerShell en Windows, o la terminal en macOS/Linux) y ejecuta:
`ssh-keygen -t rsa -b 2048`
* Presiona **Enter** para guardar la clave en la ruta por defecto y deja la contraseña (passphrase) en blanco.

### <a name="paso-2-2"></a>2.2. Obtener la clave pública
Ejecuta el siguiente comando y copia todo el texto de salida (comienza con `ssh-rsa`):
* Windows: `cat ~/.ssh/id_rsa.pub`
* Mac/Linux: `cat ~/.ssh/id_rsa.pub`

---

## <a name="fase-3"></a>Fase 3: Aprovisionamiento de la Máquina Virtual (IaaS)

### <a name="paso-3-1"></a>3.1. Iniciar el asistente de creación de instancias
1. En el menú principal, ve a **Compute** > **Instances**.
2. Selecciona a la izquierda tu compartimento (`ecored-dev`).
3. Haz clic en **Create Instance**.

### <a name="paso-3-2"></a>3.2. Configurar la Información Básica y Ubicación (Placement)
Según la documentación de OCI, esta sección define dónde residirá físicamente el servidor y su modelo de reserva[cite: 1].
* **Name:** Asigna un nombre descriptivo, como `servidor-web-abeltran`[cite: 1].
* **Create in compartment:** Verifica que esté seleccionado tu compartimento, por ejemplo `ecored-dev`[cite: 1].
* **Placement (Ubicación):** Selecciona el **Availability domain** (Dominio de disponibilidad), que representa un centro de datos físico dentro de tu región (ej. `AD 1` en la región de São Paulo `PmCo:SA-SAOPAULO-1-AD-1`)[cite: 1].
* **Advanced Options - Capacity type:** OCI ofrece varias formas de aprovisionar capacidad[cite: 1]. Las más importantes son:
  * **On-demand capacity:** (Recomendada). Coloca la instancia en un host compartido bajo demanda[cite: 1].
  * **Preemptible capacity:** Capacidad más económica pero Oracle puede reclamarla (apagarla) en cualquier momento si necesita recursos[cite: 1]. Ideal para tareas no críticas, pero no para un servidor web estable[cite: 1].
  * *Otras opciones:* Capacity reservation (para reservar capacidad futura), Dedicated host (servidor físico exclusivo) o Compute cluster[cite: 1].

### <a name="paso-3-3"></a>3.3. Configurar Imagen y Unidad (Image and Shape)
La "Imagen" define el Sistema Operativo, mientras que la "Unidad" (Shape) define los recursos de hardware de la VM[cite: 2].
* **Image (Imagen):** Haz clic en *Change image* y selecciona **Oracle Linux 9**[cite: 2]. (Asegúrate de que la seguridad indique *Shielded instance*)[cite: 2].
* **Shape (Unidad):** Haz clic en *Change shape*. Para no generar costos, selecciona procesadores **AMD** y elige la unidad **VM.Standard.E2.1.Micro** que lleva la etiqueta `Always Free-eligible`[cite: 2]. 
  * *Nota de hardware:* Esta unidad te otorgará 1 core OCPU, 1 GB de memoria RAM y 0.48 Gbps de ancho de banda de red[cite: 2].

### <a name="paso-3-4"></a>3.4. Configurar Redes e Inyectar la Clave SSH
1. En **Networking**, selecciona la **VCN** (`VCN-Taller`) y la **Public Subnet** que creaste en la Fase 1. Asegúrate de asignar una IP pública.
2. En **Add SSH keys**, selecciona **Paste public keys** y pega el texto de tu clave pública.
3. Haz clic en **Create**.

---

## <a name="fase-4"></a>Fase 4: Conexión SSH y Configuración del Sistema Operativo

### <a name="paso-4-1"></a>4.1. Conectarse a la instancia vía SSH
Espera a que el estado de la instancia sea **Running** y copia su **Public IP address**. En tu terminal local ejecuta:
`ssh opc@<TU_IP_PUBLICA>`

### <a name="paso-4-2"></a>4.2. Instalar el servidor web Nginx y configurar el Firewall
Aplica los comandos administrativos para preparar tu sistema operativo:
```bash
sudo dnf update -y
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

# Apertura del puerto 80 en el Firewall de Linux
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload