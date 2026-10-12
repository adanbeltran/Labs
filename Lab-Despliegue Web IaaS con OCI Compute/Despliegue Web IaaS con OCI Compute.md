# Taller Práctico: Despliegue Web IaaS con OCI Compute, Nginx y Gestión de Costos

**Objetivo:** Comprender el modelo de Infraestructura como Servicio (IaaS) desplegando una página web estática en Oracle Cloud Infrastructure (OCI). El estudiante aprovisionará una Máquina Virtual (VM), administrará el sistema operativo, configurará las redes y aprenderá a monitorear los costos asociados a los recursos desplegados mediante presupuestos y análisis de facturación.

---

## Índice del Taller

* [Fase 1: Preparación y Generación de Claves](#fase-1-preparacion-y-generacion-de-claves)
* [Fase 2: Aprovisionamiento de la Máquina Virtual (IaaS)](#fase-2-aprovisionamiento-de-la-maquina-virtual-iaas)
* [Fase 3: Conexión SSH y Configuración del Sistema Operativo](#fase-3-conexion-ssh-y-configuracion-del-sistema-operativo)
* [Fase 4: Despliegue del Código y Apertura de Puertos](#fase-4-despliegue-del-codigo-y-apertura-de-puertos)
* [Fase 5: Validación y Pruebas](#fase-5-validacion-y-pruebas)
* [Fase 6: Análisis y Monitoreo de Costos en OCI](#fase-6-analisis-y-monitoreo-de-costos-en-oci)

---

## Fase 1: Preparacion y Generacion de Claves

El acceso seguro a los servidores en IaaS se realiza mediante criptografía asimétrica (Claves SSH).

### 1.1. Generar un par de claves SSH
Abre tu terminal (PowerShell en Windows, o la terminal en macOS/Linux) y ejecuta:
`ssh-keygen -t rsa -b 2048`
* Presiona **Enter** para guardar la clave en la ruta por defecto.
* Deja la contraseña (passphrase) en blanco para este ejercicio académico presionando **Enter** dos veces.

### 1.2. Obtener la clave pública
Ejecuta el siguiente comando para ver el contenido de tu clave pública y cópialo entero (comienza con `ssh-rsa`). Lo necesitarás en la Fase 2:
* Windows: `cat ~/.ssh/id_rsa.pub`
* Mac/Linux: `cat ~/.ssh/id_rsa.pub`

---

## Fase 2: Aprovisionamiento de la Maquina Virtual IaaS

Vamos a aprovisionar nuestra infraestructura, prestando especial atención a elegir recursos gratuitos para no incurrir en costos.

### 2.1. Iniciar el servicio OCI Compute
1. Ingresa a la consola de OCI.
2. En el menú principal (arriba a la izquierda), ve a **Compute** y selecciona **Instances**.
3. Haz clic en **Create Instance**.

### 2.2. Configurar la Instancia y el Modelo de Costo
1. **Name:** `servidor-web-tuapellido`.
2. **Image and Shape:** 
   * Haz clic en *Edit*.
   * Asegúrate de que la imagen sea **Oracle Linux 8 o 9**.
   * En Shape, selecciona una instancia que tenga la etiqueta **Always Free Eligible** (por ejemplo, `VM.Standard.E2.1.Micro` o `VM.Standard.A1.Flex` dependiendo de la disponibilidad de tu región). Elegir esta capa garantiza que el recurso de cómputo no generará facturación.

### 2.3. Configurar la Red (Networking)
1. En **Networking**, selecciona la VCN (Virtual Cloud Network) y la Subred Pública que el docente ha preconfigurado.
2. Asegúrate de que la opción **Assign a public IPv4 address** esté seleccionada (las IPs públicas dinámicas estándar en OCI suelen ser gratuitas).

### 2.4. Inyectar la Clave SSH y Crear
1. En la sección **Add SSH keys**, selecciona **Paste public keys**.
2. Pega el texto de tu clave pública copiada en el paso 1.2.
3. Revisa en la parte inferior derecha que el estimado de costo diga **"0.00 / month"** (o se indique Always Free). 
4. Haz clic en **Create**.

---

## Fase 3: Conexion SSH y Configuracion del Sistema Operativo

### 3.1. Obtener la IP Pública
Espera a que el icono del servidor cambie a verde (**Running**). Copia la dirección **Public IP address**.

### 3.2. Conectarse a la instancia vía SSH
Abre tu terminal local y conéctate usando el usuario administrador de Oracle Linux (`opc`) y tu IP:
`ssh opc@<TU_IP_PUBLICA>`
* Escribe `yes` cuando te pregunte si confías en el host.

### 3.3. Instalar el servidor web Nginx
En IaaS, el SO está vacío. Debemos instalar el software base:
1. Actualiza los repositorios: `sudo dnf update -y`
2. Instala Nginx: `sudo dnf install nginx -y`
3. Inicia el servicio: `sudo systemctl start nginx`
4. Habilita el inicio automático: `sudo systemctl enable nginx`

### 3.4. Configurar el Firewall del Sistema Operativo
Abre el puerto HTTP (80) en el firewall interno de Linux:
```bash
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload