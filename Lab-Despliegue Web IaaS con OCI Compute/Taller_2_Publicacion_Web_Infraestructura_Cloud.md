# Taller 2. Publicar una página web en una máquina virtual

## Objetivo

Publicar la página del repositorio Git suministrado para la práctica en una máquina virtual de OCI (Oracle Cloud Infrastructure, infraestructura de nube de Oracle), usando IaaS (Infrastructure as a Service, infraestructura como servicio). Usarás la red preparada en el taller 1, crearás el servidor, instalarás Nginx para entregar los archivos al navegador y comprobarás el funcionamiento de la página.

**Tiempo estimado: 60 minutos.** Usa tu cuenta académica ya activada, el usuario `estudiante-web` y el compartimento `Infraestructura_y_Cloud` creados en el taller 1, junto con `red-infraestructura` y `subred-web`. Ten a mano `datos-taller.txt` y la dirección de clonación del repositorio Git público suministrado para la práctica. La página del repositorio debe tener `index.html` en su carpeta principal, junto con los archivos o carpetas de diseño e interactividad que utiliza.

<a name="indice"></a>

## Tabla de contenido

- [Fase 1. Crear la máquina y guardar la llave de acceso](#fase-1)
  - [Paso 1.1. Ingresar con el usuario de práctica](#paso-1-1)
  - [Paso 1.2. Elegir el sistema y el tamaño de la máquina](#paso-1-2)
  - [Paso 1.3. Conectar la máquina a la red](#paso-1-3)
  - [Paso 1.4. Guardar la llave y crear la máquina](#paso-1-4)
- [Fase 2. Instalar el servidor y publicar el repositorio](#fase-2)
  - [Paso 2.1. Conectarse e instalar Nginx](#paso-2-1)
  - [Paso 2.2. Descargar la página desde Git](#paso-2-2)
  - [Paso 2.3. Publicar los archivos de la página](#paso-2-3)
  - [Paso 2.4. Probar la página desde el navegador](#paso-2-4)
- [Fase 3. Revisar el gasto y cerrar la práctica](#fase-3)
  - [Paso 3.1. Consultar costos y crear una alerta](#paso-3-1)
  - [Paso 3.2. Guardar las evidencias y eliminar la máquina](#paso-3-2)
  - [Paso 3.3. Eliminar la red de la práctica](#paso-3-3)

---

<a name="fase-1"></a>

## Fase 1. Crear la máquina y guardar la llave de acceso

Crearás la máquina virtual que almacenará y entregará la página. Elegirás su sistema y tamaño, la conectarás a la red y guardarás una llave privada para administrarla sin publicar esa llave en el sitio web.

<a name="paso-1-1"></a>

### Paso 1.1. Ingresar con el usuario de práctica

Trabajarás con el usuario del taller 1 para que los recursos se creen con los permisos definidos para la asignatura. Seleccionar el mismo compartimento y la misma región evita perder de vista los recursos entre las diferentes pantallas.

1. Abre una ventana privada del navegador, escribe `cloud.oracle.com` e inicia sesión con el nombre de tu cuenta, tu dominio y `estudiante-web`.
2. Selecciona la región registrada en `datos-taller.txt`. Usa `Infraestructura_y_Cloud` en los filtros de compartimento de este taller.
3. Conserva disponible tu sesión administradora en la otra ventana para corregir tu política si aparece falta de permisos. En ese caso, revisa en **Identity & Security > Policies > PermisosInfraestructura** los nombres del grupo y del compartimento, y vuelve a iniciar la sesión del usuario de práctica.

<a name="paso-1-2"></a>

### Paso 1.2. Elegir el sistema y el tamaño de la máquina

Definirás el sistema operativo y los recursos de la máquina. Usarás un tamaño pequeño para esta página y revisarás su costo estimado, porque una máquina encendida puede consumir el crédito de tu cuenta.

1. Abre **Compute > Instances**, selecciona `Infraestructura_y_Cloud` y pulsa **Create Instance**.
2. Escribe `servidor-web` en **Name** y comprueba el compartimento. En **Placement**, conserva un dominio de disponibilidad de tu región. Mantén **On-demand capacity**.
3. En **Image and shape > Change Image**, selecciona la imagen de plataforma **Oracle Linux 9**.
4. En **Change Shape**, selecciona **Virtual Machine > AMD (Advanced Micro Devices, fabricante de procesadores)** y la unidad `VM.Standard.E4.Flex` (Virtual Machine, máquina virtual). Si no está disponible, selecciona `VM.Standard.E5.Flex` (Virtual Machine, máquina virtual).
5. Configura **1 OCPU (Oracle Compute Processing Unit, unidad de procesamiento de Oracle)** y **2 GB (gigabytes, unidad de capacidad de memoria) de memoria**. Pulsa **Select Shape** y revisa los valores y el costo estimado antes de continuar.

<img width="757" height="482" alt="image" src="https://github.com/user-attachments/assets/508e6f2f-ae12-42e9-820a-972035922837" />

<img width="855" height="687" alt="image" src="https://github.com/user-attachments/assets/1ada159f-dfbf-4366-890d-fabf5adf3a1d" />

<img width="713" height="642" alt="image" src="https://github.com/user-attachments/assets/ef533f9e-4a05-4fe3-9cf3-51628a92f9b2" />




<a name="paso-1-3"></a>

### Paso 1.3. Conectar la máquina a la red

1. En **Networking**, selecciona la red existente `red-infraestructura` y la subred existente `subred-web`. Usa `Infraestructura_y_Cloud` en los selectores de compartimento.
2. Activa **Assign a public IPv4 (Internet Protocol version 4, protocolo de Internet versión 4) address** y conserva la asignación automática de la dirección privada.
3. Deja sin habilitar **Network security groups** para utilizar la lista de seguridad configurada al final del taller 1.
<img width="408" height="422" alt="image" src="https://github.com/user-attachments/assets/612cf0d8-baa3-4745-96bc-927420e7626a" />


<a name="paso-1-4"></a>

### Paso 1.4. Guardar la llave y crear la máquina

Guardarás el archivo privado que permitirá la conexión SSH (Secure Shell, conexión segura para administrar el servidor). También crearás el disco de arranque que contiene el sistema y los archivos del servidor.

1. En **Add SSH (Secure Shell, conexión segura para administrar el servidor) keys**, selecciona **Generate a key pair for me**. Descarga **Save Private Key** y **Save Public Key**.
2. En tu equipo, renombra el archivo privado como `llave-web.key`, sin agregar `.txt`. Conserva también el archivo público. Guarda la ruta del archivo privado en `datos-taller.txt`; no copies su contenido en ese archivo.

<img width="700" height="517" alt="image" src="https://github.com/user-attachments/assets/32c5c395-f80e-4476-ae67-60e0cee6007b" />

3. En **Boot volume**, conserva el tamaño predeterminado de la imagen y deja sin configurar discos adicionales y copias de seguridad.
4. Revisa el resumen y pulsa **Create**. Espera el estado **Running** y dos minutos adicionales para el inicio del sistema.
5. Si aparece falta de capacidad, vuelve a la creación y cambia el dominio de disponibilidad dentro de la misma región o usa la otra unidad del [paso 1.2](#paso-1-2).
6. En la ficha del servidor, copia **Public IP (Internet Protocol, protocolo de Internet) Address** y guárdala en `datos-taller.txt` como dirección pública del servidor. No uses su dirección privada para las conexiones desde tu equipo.

<img width="1507" height="345" alt="image" src="https://github.com/user-attachments/assets/4c7a8a22-7a68-4ec5-9961-1e7105d37c8b" />


[Volver a la tabla de contenido](#indice) · [Continuar con la fase 2](#fase-2)

---

<a name="fase-2"></a>

## Fase 2. Instalar el servidor y publicar el repositorio

Te conectarás a la máquina, instalarás Nginx y descargarás los archivos del repositorio Git. Nginx entregará esos archivos cuando el navegador solicite la página. Así el código pasará del repositorio a una dirección pública de tu servidor.

<a name="paso-2-1"></a>

### Paso 2.1. Conectarse desde Cloud Shell e instalar Nginx

Usarás Cloud Shell, la terminal integrada en OCI (Oracle Cloud Infrastructure, infraestructura de nube de Oracle), para conectarte a `servidor-web` mediante SSH (Secure Shell, conexión segura para administrar el servidor). Después instalarás Nginx para entregar la página y Git para descargar su código. Cloud Shell y `servidor-web` son equipos diferentes: ejecutarás la instalación después de entrar al servidor.

1. En **Compute > Instances**, selecciona el compartimento `Infraestructura_y_Cloud` y abre `servidor-web`. Comprueba el estado **Running** y copia su **Public IP (Internet Protocol, protocolo de Internet) Address**. Esta dirección permitirá la conexión desde Cloud Shell.
2. En la barra superior de la consola, abre el icono de terminal o **Developer tools > Cloud Shell**. Espera a que aparezca la línea de comandos. Conserva la región donde creaste `servidor-web`.
3. En el selector de red de Cloud Shell, comprueba que se use **Public Network**, la red pública. Si aparece **Service Network**, cámbiala a **Public Network**: la red de servicios permite acceder a servicios de Oracle, pero no a la dirección pública de tu servidor. Si la opción pública no aparece o deniega el acceso, desde tu sesión administradora añade a `PermisosInfraestructura` la línea siguiente y reinicia Cloud Shell desde **Actions > Restart**. Este permiso es adicional al que ya permite abrir la terminal:

```text
Allow group 'Default'/'GrupoInfraestructura' to use cloud-shell-public-network in tenancy
```

4. En el menú de Cloud Shell, selecciona **Upload**. Carga en tu carpeta personal el archivo privado `llave-web.key` guardado en el [paso 1.4](#paso-1-4) y espera la confirmación de carga. Ese archivo permitirá demostrar que tienes el acceso configurado para esta máquina.
5. Ejecuta estas órdenes en **Cloud Shell**. La primera comprueba que el archivo esté en tu carpeta personal; la segunda permite que solamente tu usuario lo lea y modifique, requisito para utilizarlo en la conexión:

```bash
ls -l ~/llave-web.key
chmod 600 ~/llave-web.key
```

6. Sustituye `DIRECCION_PUBLICA` por la dirección que copiaste en el punto 1 y ejecuta en **Cloud Shell**:

```bash
ssh -i ~/llave-web.key opc@DIRECCION_PUBLICA
```

7. En la primera conexión, escribe `yes` cuando se solicite confirmar y pulsa **Enter**. Ahora la terminal debe mostrar una sesión en `servidor-web`. Ejecuta:

```bash
whoami
```

Comprueba que responda `opc`. Ese es el usuario del sistema de la máquina creada con Oracle Linux 9. Mantén abierta esta conexión para ejecutar las órdenes siguientes dentro del servidor.

8. Ejecuta este bloque **dentro de `servidor-web`**. La primera orden instala Nginx y Git; la segunda inicia Nginx y lo deja preparado para iniciarse cuando reinicie el servidor; la tercera comprueba su estado:

```bash
sudo dnf install -y nginx git
sudo systemctl enable --now nginx
systemctl is-active nginx
```

9. Comprueba que la última orden muestre `active`. Continúa con el [paso 2.2](#paso-2-2) desde esta misma conexión: descargarás el repositorio dentro de `servidor-web`.

Si aparece un error, corrige el punto indicado antes de continuar:

- **No such file or directory** o **Identity file ... not accessible**: comprueba el nombre `llave-web.key` y que la carga del archivo haya finalizado en tu carpeta personal de Cloud Shell.
- **Permission denied (publickey)**: comprueba que utilizaste `opc` y la llave privada del par configurado al crear esta máquina.
- **Connection timed out**: verifica la red pública de Cloud Shell, la dirección pública, el estado **Running**, la regla de entrada del puerto 22 en la lista de seguridad de `red-infraestructura` y la ruta hacia `salida-internet`.


<a name="paso-2-2"></a>

### Paso 2.2. Descargar la página desde Git

Descargarás una copia del repositorio para colocar la página en el servidor. El archivo `index.html` contiene HTML (HyperText Markup Language, lenguaje de marcado de hipertexto) para la estructura de la página; los archivos CSS (Cascading Style Sheets, hojas de estilo en cascada) definen su diseño y JavaScript permite sus interacciones.

1. Copia la dirección de clonación del repositorio Git suministrada para el taller. Sustituye `DIRECCION_DEL_REPOSITORIO` por esa dirección en el bloque, conservando las comillas.
2. Ejecuta el bloque en la sesión del servidor:

```bash
git clone --depth 1 "DIRECCION_DEL_REPOSITORIO" ~/pagina-taller
ls -la ~/pagina-taller
test -f ~/pagina-taller/index.html && echo "index.html encontrado"
```

3. Comprueba el mensaje `index.html encontrado` y la presencia de los archivos de diseño e interactividad. Si no aparece el mensaje, verifica que la dirección corresponda a la página estática entregada para el taller y que `index.html` esté en su carpeta principal antes de continuar.
4. Si ya existe `pagina-taller` de un intento anterior con ese mismo repositorio, actualiza esa copia con `git -C ~/pagina-taller pull --ff-only` en lugar de volver a clonarla.

<a name="paso-2-3"></a>

### Paso 2.3. Publicar los archivos de la página

Colocarás la versión guardada en Git dentro de la carpeta que Nginx entrega al navegador. También abrirás el acceso web en el sistema del servidor, porque la regla de la subred y el permiso del sistema son controles diferentes.

1. En la sesión del servidor, ejecuta el bloque. La primera orden copia los archivos de la versión del repositorio sin publicar la carpeta interna `.git`. La segunda ajusta su identificación de seguridad y la tercera verifica la configuración de Nginx.

```bash
git -C ~/pagina-taller archive --format=tar HEAD | sudo tar -x -C /usr/share/nginx/html
sudo restorecon -Rv /usr/share/nginx/html
sudo nginx -t
```

2. Comprueba que la configuración sea válida. Ejecuta el bloque siguiente para permitir HTTP (HyperText Transfer Protocol, protocolo de transferencia de hipertexto) en el servidor. Las primeras dos órdenes habilitan el servicio web en su firewall; la última solicita la página desde la propia máquina.

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
curl -I http://localhost/index.html
```

3. Comprueba `success` en las órdenes del firewall y una respuesta con código `200 OK` en la última orden. Mantén abierta la sesión para las comprobaciones siguientes.

<a name="paso-2-4"></a>

### Paso 2.4. Probar la página desde el navegador

Verificarás el acceso desde fuera del servidor, el diseño y las interacciones. Esta prueba comprueba que los archivos se publicaron y que las conexiones desde Internet están permitidas.

1. En el navegador de tu equipo, escribe `http://DIRECCION_PUBLICA/index.html`, sustituyendo `DIRECCION_PUBLICA` por la dirección real. Usa `http` en esta práctica.
2. Comprueba que aparezca la página suministrada y prueba uno de sus botones, menús o filtros.
3. Abre las herramientas de desarrollo con **F12 > Network**, recarga y revisa que la página y sus archivos de diseño e interactividad respondan con estado **200**. En **Console**, comprueba que no haya errores de esos archivos.
4. Abre la misma dirección en una ventana privada o en un teléfono con datos móviles. Repite una interacción y guarda una captura de la dirección y del resultado.
5. Si la página no abre, revisa la dirección pública, el estado **Running** y las reglas de entrada del puerto 80 en la lista de seguridad de `red-infraestructura`. Dentro del servidor, ejecuta `systemctl is-active nginx` y `sudo firewall-cmd --list-services`; deben mostrar `active` y `http`. Si falta el servicio web, repite el [paso 2.3](#paso-2-3).
6. Guarda la dirección de la página en `datos-taller.txt`.

[Volver a la tabla de contenido](#indice) · [Continuar con la fase 3](#fase-3)

---

<a name="fase-3"></a>

## Fase 3. Revisar el gasto y cerrar la práctica

Consultarás el consumo del compartimento, configurarás un aviso de presupuesto y eliminarás los recursos de esta práctica. El aviso informa del gasto; eliminar la máquina y su disco evita mantener recursos que ya no necesitas.

<a name="paso-3-1"></a>

### Paso 3.1. Consultar costos y crear una alerta

Revisarás el gasto asociado a la asignatura y crearás una alerta para recibir un aviso en tu correo. El presupuesto es una referencia de seguimiento: no detiene la máquina ni bloquea automáticamente el consumo.

1. Con el usuario `estudiante-web`, abre **Billing & Cost Management > Cost Management > Cost Analysis**.
2. Selecciona el período, filtra por `Infraestructura_y_Cloud`, agrupa por **Service** y pulsa **Apply**. Guarda una captura o registra el resultado en `datos-taller.txt`. Si no hay datos, registra ese resultado; la información puede tardar hasta 48 horas en aparecer.
3. Abre **Billing & Cost Management > Cost Management > Budgets > Create Budget** y completa:

| Campo | Valor |
| --- | --- |
| Name | `PresupuestoInfraestructura` |
| Budget Scope | **Compartment** |
| Target Compartment | `Infraestructura_y_Cloud` |
| Schedule | **Monthly** |
| Budget Amount | `5`; registra la moneda que indique la consola |

4. Si la cuenta muestra USD (United States Dollar, dólar estadounidense), el monto corresponde a **5 dólares estadounidenses mensuales**. Si ya existe un presupuesto para este compartimento, abre ese presupuesto en lugar de crear otro.
5. En la sección de alerta del formulario o en **Budget Alert Rules > Create Budget Alert Rule**, selecciona **Actual Spend**, **Percentage of Budget** y **Threshold %: 80**. Escribe tu correo institucional en **Email Recipients** y guarda. Comprueba que la regla quede asociada al presupuesto.
6. Si faltan permisos, verifica desde tu sesión administradora que `PermisosInfraestructura` contenga las líneas `read usage-report` y `use usage-budgets` en `tenancy`. Vuelve a iniciar la sesión del usuario de práctica después de corregirlas.
7. Revisa en tu sesión administradora el saldo restante del crédito y guárdalo en `datos-taller.txt` junto con el monto y el umbral del presupuesto.

<a name="paso-3-2"></a>

### Paso 3.2. Guardar las evidencias y eliminar la máquina

Guardarás la prueba de publicación y eliminarás la máquina con su disco. Terminar solamente la máquina puede conservar el disco; por eso marcarás expresamente su eliminación.

1. Conserva `datos-taller.txt`, la captura de la página y la comprobación del presupuesto. No incluyas el archivo de la llave privada en las evidencias.
2. En la sesión del servidor, escribe `exit` para volver a la terminal de tu equipo.
3. Abre **Compute > Instances > servidor-web**. En su menú de acciones selecciona **Terminate**.
4. Activa **Permanently delete the attached boot volume** y confirma. Espera el estado **Terminated**.
5. Abre **Storage > Block Storage > Boot Volumes**, selecciona `Infraestructura_y_Cloud` y el mismo dominio de disponibilidad. Comprueba que el disco de `servidor-web` esté eliminado o en **Terminated**.

<a name="paso-3-3"></a>

### Paso 3.3. Eliminar la red de la práctica

Retirarás la subred, la salida y la red que creaste para este servidor. Lo harás en orden porque un recurso no puede eliminarse mientras otro lo siga utilizando.

1. Comprueba que `servidor-web` haya terminado. Abre **Networking > Virtual cloud networks > red-infraestructura > Subnets** y elimina `subred-web`.
2. En la tabla de rutas predeterminada de `red-infraestructura`, elimina la regla que apunta a `salida-internet`.
3. Abre **Gateways > Internet gateways** y elimina `salida-internet`.
4. Vuelve a la lista de redes, selecciona `red-infraestructura` y pulsa **Delete**. Comprueba que la eliminación corresponda a esta red de práctica y confirma. Si aparece una dependencia, revisa el recurso indicado y elimina primero esa dependencia de esta práctica.
5. Conserva el compartimento, el grupo, el usuario y el presupuesto para las siguientes actividades de la asignatura. Cierra tus sesiones del usuario de práctica y de administración.

[Volver a la tabla de contenido](#indice)
