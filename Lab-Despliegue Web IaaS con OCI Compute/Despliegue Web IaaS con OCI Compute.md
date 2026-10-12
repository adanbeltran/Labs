# Taller Práctico: Despliegue Web IaaS con OCI Compute, Nginx y Gestión de Costos

**Objetivo:** Comprender el modelo de Infraestructura como Servicio (IaaS) desplegando una página web estática en Oracle Cloud Infrastructure (OCI). El estudiante aprovisionará una Máquina Virtual (VM), administrará el sistema operativo, configurará las redes y aprenderá a monitorear los costos asociados a los recursos desplegados mediante presupuestos y análisis de facturación.

---

## Índice del Taller

* [Fase 1: Preparación y Generación de Claves](#fase-1)
  * [1.1. Generar un par de claves SSH](#paso-1-1)
  * [1.2. Obtener la clave pública](#paso-1-2)
* [Fase 2: Aprovisionamiento de la Máquina Virtual (IaaS)](#fase-2)
  * [2.1. Iniciar el servicio OCI Compute](#paso-2-1)
  * [2.2. Configurar la Instancia y el Modelo de Costo](#paso-2-2)
  * [2.3. Configurar la Red (Networking)](#paso-2-3)
  * [2.4. Inyectar la Clave SSH y Crear](#paso-2-4)
* [Fase 3: Conexión SSH y Configuración del Sistema Operativo](#fase-3)
  * [3.1. Obtener la IP Pública](#paso-3-1)
  * [3.2. Conectarse a la instancia vía SSH](#paso-3-2)
  * [3.3. Instalar el servidor web Nginx](#paso-3-3)
  * [3.4. Configurar el Firewall del Sistema Operativo](#paso-3-4)
* [Fase 4: Despliegue del Código y Apertura de Puertos](#fase-4)
  * [4.1. Desplegar el código vía Git](#paso-4-1)
  * [4.2. Abrir el puerto en la Security List de OCI](#paso-4-2)
* [Fase 5: Validación y Pruebas](#fase-5)
  * [5.1. Verificar en el navegador](#paso-5-1)
* [Fase 6: Análisis y Monitoreo de Costos en OCI](#fase-6)
  * [6.1. Explorar el Análisis de Costos (Cost Analysis)](#paso-6-1)
  * [6.2. Crear un Presupuesto (Budget) y una Alerta](#paso-6-2)

---

## <a name="fase-1"></a>Fase 1: Preparación y Generación de Claves

El acceso seguro a los servidores en IaaS se realiza mediante criptografía asimétrica (Claves SSH).

### <a name="paso-1-1"></a>1.1. Generar un par de claves SSH
Abre tu terminal (PowerShell en Windows, o la terminal en macOS/Linux) y ejecuta:
`ssh-keygen -t rsa -b 2048`
* Presiona **Enter** para guardar la clave en la ruta por defecto.
* Deja la contraseña (passphrase) en blanco para este ejercicio académico presionando **Enter** dos veces.

<img width="395" height="215" alt="image" src="https://github.com/user-attachments/assets/e3a1bf5b-6aaf-468c-9bee-855787b9fff7" />


### <a name="paso-1-2"></a>1.2. Obtener la clave pública
Ejecuta el siguiente comando para ver el contenido de tu clave pública y cópialo entero (comienza con `ssh-rsa`). Lo necesitarás en la Fase 2:
* Windows: `cat ~/.ssh/id_rsa.pub`
* Mac/Linux: `cat ~/.ssh/id_rsa.pub`

---

## <a name="fase-2"></a>Fase 2: Aprovisionamiento de la Máquina Virtual (IaaS)

Vamos a aprovisionar nuestra infraestructura, prestando especial atención a elegir recursos gratuitos para no incurrir en costos.

### <a name="paso-2-1"></a>2.1. Iniciar el servicio OCI Compute
1. Ingresa a la consola de OCI.
2. En el menú principal (arriba a la izquierda), ve a **Compute** y selecciona **Instances**.
3. Haz clic en **Create Instance**.

### <a name="paso-2-2"></a>2.2. Configurar la Instancia y el Modelo de Costo
1. **Name:** `servidor-web-tuapellido`.
2. **Image and Shape:** 
   * Haz clic en *Edit*.
   * Asegúrate de que la imagen sea **Oracle Linux 8 o 9**.
   * En Shape, selecciona una instancia que tenga la etiqueta **Always Free Eligible** (por ejemplo, `VM.Standard.E2.1.Micro` o `VM.Standard.A1.Flex` dependiendo de la disponibilidad de tu región). Elegir esta capa garantiza que el recurso de cómputo no generará facturación.

### <a name="paso-2-3"></a>2.3. Configurar la Red (Networking)
1. En **Networking**, selecciona la VCN (Virtual Cloud Network) y la Subred Pública que el docente ha preconfigurado.
2. Asegúrate de que la opción **Assign a public IPv4 address** esté seleccionada (las IPs públicas dinámicas estándar en OCI suelen ser gratuitas).

### <a name="paso-2-4"></a>2.4. Inyectar la Clave SSH y Crear
1. En la sección **Add SSH keys**, selecciona **Paste public keys**.
2. Pega el texto de tu clave pública copiada en el paso 1.2.
3. Revisa en la parte inferior derecha que el estimado de costo diga **"0.00 / month"** (o se indique Always Free). 
4. Haz clic en **Create**.

---

## <a name="fase-3"></a>Fase 3: Conexión SSH y Configuración del Sistema Operativo

### <a name="paso-3-1"></a>3.1. Obtener la IP Pública
Espera a que el icono del servidor cambie a verde (**Running**). Copia la dirección **Public IP address**.

### <a name="paso-3-2"></a>3.2. Conectarse a la instancia vía SSH
Abre tu terminal local y conéctate usando el usuario administrador de Oracle Linux (`opc`) y tu IP:
`ssh opc@<TU_IP_PUBLICA>`
* Escribe `yes` cuando te pregunte si confías en el host.

### <a name="paso-3-3"></a>3.3. Instalar el servidor web Nginx
En IaaS, el SO está vacío. Debemos instalar el software base:
1. Actualiza los repositorios: `sudo dnf update -y`
2. Instala Nginx: `sudo dnf install nginx -y`
3. Inicia el servicio: `sudo systemctl start nginx`
4. Habilita el inicio automático: `sudo systemctl enable nginx`

### <a name="paso-3-4"></a>3.4. Configurar el Firewall del Sistema Operativo
Abre el puerto HTTP (80) en el firewall interno de Linux:
```bash
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
