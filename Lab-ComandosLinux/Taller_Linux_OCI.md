# Taller práctico: comandos Linux y fundamentos de redes en Oracle Cloud Infrastructure (OCI)

**Asignatura:** Infraestructura TI / Cloud Computing  
**Modalidad:** Individual, presencial, con ejecución directa en una instancia Compute de OCI.  
**Entorno:** Oracle Linux o Ubuntu; acceso por SSH desde OCI Cloud Shell.  
**Producto de clase:** un documento PDF con **ocho capturas de pantalla obligatorias, una por reto**, más las capturas adicionales indicadas en el reto de redes cuando se haya autorizado la creación de recursos.  
**Nivel:** básico e intermedio, con énfasis en diagnóstico de redes y conectividad a Internet.

**Progresión didáctica:** cada reto es acumulativo. Para resolver el reto del grupo *n* solo pueden utilizarse comandos, opciones y operadores enseñados en los ejemplos de ese grupo o de los anteriores. Los ejemplos no constituyen la solución literal del reto; se deben adaptar al caso personal.

## Resultados de aprendizaje

Al finalizar, el estudiante podrá interpretar rutas absolutas y relativas, administrar archivos de trabajo, analizar permisos y procesos, reconocer recursos del sistema, **reconstruir el recorrido de un paquete desde una VM hasta Internet**, distinguir la configuración de red de Linux de la red virtual de OCI y consultar o crear recursos de networking de forma controlada.

## Tabla de contenido

1. [Navegación y directorios](#grupo-1)
2. [Creación y administración de archivos](#grupo-2)
3. [Lectura y búsqueda de contenido](#grupo-3)
4. [Usuarios, permisos y privilegios](#grupo-4)
5. [Procesos y trabajos del shell](#grupo-5)
6. [Recursos del sistema](#grupo-6)
7. [Redes, salida a Internet y creación de redes en OCI](#grupo-7)
8. [Servicios, registros y paquetes](#grupo-8)

---

## Conceptos previos: rutas absolutas y relativas

En Linux, el sistema de archivos parte de una **raíz** (`/`). Una **ruta absoluta** comienza en `/` y señala la misma ubicación independientemente del directorio actual. Una **ruta relativa** parte del lugar donde está situado el usuario. El directorio personal del usuario (`~` o `$HOME`) no debe confundirse con `/`.

| Concepto | Definición | Ejemplo | Interpretación |
|---|---|---|---|
| Ruta absoluta | Comienza por `/`. | `/home/opc/taller_oci/datos` | Localiza la carpeta `datos` desde la raíz. |
| Ruta relativa | Depende del directorio actual. | `datos/2026-2` | Desde `/home/opc/taller_oci`, conduce a `/home/opc/taller_oci/datos/2026-2`. |
| Directorio actual | Se representa mediante `.`. | `./datos` | Señala la carpeta `datos` del directorio actual. |
| Directorio padre | Se representa mediante `..`. | `../respaldos` | Desde `datos`, sube un nivel y busca `respaldos`. |
| Directorio personal | Se representa mediante `~` o `$HOME`. | `~/taller_oci` | Equivale a la carpeta de trabajo dentro del HOME del usuario conectado. |

**Ejemplo de interpretación:** si la ubicación actual es `/home/opc/taller_oci`, las rutas `/home/opc/taller_oci/datos` y `datos` llegan a la misma carpeta. Si cambia la ubicación actual a `/home/opc`, la ruta relativa `datos` ya no significa lo mismo. En Ubuntu, el directorio personal puede ser `/home/ubuntu`; la ruta absoluta dependerá de la cuenta utilizada.

**Personalización del taller:** durante las actividades, cada estudiante identificará sus carpetas, inventarios e informes con su primer nombre y primer apellido, escritos en minúsculas, sin tildes ni espacios. Así podrá distinguir sus evidencias y reutilizar los archivos creados en un grupo al avanzar al siguiente. La configuración concreta de este identificador se explicará cuando se utilice por primera vez en el grupo de navegación. No se deben incluir números de documento, contraseñas ni información personal sensible.

## Preparación: de OCI Cloud Shell a la máquina virtual

**Diferencia esencial:** OCI **Cloud Shell** es el entorno de consola administrado desde el que se inicia la conexión SSH. Una vez autenticado, los ejercicios Linux se ejecutan en la **instancia Compute**. Los comandos `oci network ...` del grupo 7 se realizan en **Cloud Shell**, que dispone de un entorno e identidad distintos de los de la VM.

1. En OCI, abra **Compute → Instances**. Verifique que su VM está en estado **Running** e identifique la imagen (Oracle Linux o Ubuntu), su IP pública si existe y su VCN y subred. Si la VM no tiene IP pública, utilice el acceso privado autorizado por el docente.
2. Compruebe que el origen de la conexión tiene permiso para llegar a SSH (TCP/22) y que Cloud Shell dispone de una ruta adecuada hacia la VM. No habilite acceso indiscriminado desde Internet.
3. En Cloud Shell, cargue la clave privada SSH autorizada. **No muestre ni comparta la clave**. Sustituya `mi_clave.key` por el nombre real de ese archivo. `ls -l` inspecciona permisos y `chmod 600` restringe su lectura y escritura al propietario:

   ```bash
   ls -l ~/mi_clave.key
   chmod 600 ~/mi_clave.key
   ```

4. Conéctese con `ssh -i`, que indica qué clave privada usar. Sustituya `IP_PUBLICA` por la dirección real de su VM (estos ejemplos presuponen conectividad pública permitida):

   ```bash
   ssh -i ~/mi_clave.key opc@IP_PUBLICA        # Oracle Linux
   # O bien, si la VM es Ubuntu:
   ssh -i ~/mi_clave.key ubuntu@IP_PUBLICA
   ```

5. Ya dentro de la VM, compruebe la identidad del usuario, el equipo y el directorio actual. `whoami` muestra la cuenta, `hostname` el nombre del equipo y `pwd` la ruta en que se encuentra:

   ```bash
   whoami
   hostname
   pwd
   ```

6. Continúe con el **grupo 1**. El espacio personalizado se creará allí, sin alterar archivos del sistema ni el HOME de otros usuarios.

**Convenciones:** `[opción]` indica un argumento opcional; `<valor>` representa un dato que debe sustituirse, no escribirse literalmente. Las IP y nombres de ejemplo son ilustrativos; registre los valores reales de la VM en las actividades de redes.

**Seguridad:** no modifique la interfaz de red, puerta de enlace, firewall, servicios SSH, NetworkManager ni rutas persistentes durante la práctica, pues puede perder el acceso. Use `sudo` únicamente cuando una operación concreta, autorizada y explicada lo requiera.

---

<a id="grupo-1"></a>
## Grupo 1. Navegación y directorios

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** ubicarse en el sistema de archivos, distinguir rutas absolutas y relativas y crear una estructura personal de directorios.

| Comando o construcción | Sintaxis general | Uso habitual |
|---|---|---|
| `pwd` | `pwd` | Muestra la ruta absoluta actual. |
| `ls` | `ls [-la] [ruta]` | Lista elementos; `-l` muestra detalles y `-a` incluye ocultos. |
| `cd` | `cd [ruta]` | Cambia de directorio; `cd ..` sube al padre. |
| `mkdir` | `mkdir [-p] ruta` | Crea carpetas y, con `-p`, los directorios intermedios. |
| `find` | `find ruta -maxdepth N -type d` | Muestra directorios hasta la profundidad indicada. |
| `whoami` | `whoami` | Muestra la cuenta utilizada en la VM. |
| `hostname` | `hostname` | Muestra el nombre de la VM. |
| `printf` | `printf 'formato\n' valor` | Presenta texto y valores en la terminal. |
| Variable Bash y `export` | `export NOMBRE="valor"` | Guarda un valor y permite que los scripts iniciados desde ese shell también lo reciban; **no** deje espacios alrededor de `=`. |

### 2. Ejemplos guiados para replicar

**Primera utilización del identificador personal.** Sustituya `maria_rojas` por su primer nombre y primer apellido, sin tildes, espacios ni caracteres especiales. `ESTUDIANTE` guarda el identificador y se exporta para que esté disponible en los scripts que se ejecuten después; `LAB` identifica la carpeta principal. Ambas variables corresponden a la sesión actual de la VM.

```bash
export ESTUDIANTE="maria_rojas"
LAB="$HOME/taller_oci/$ESTUDIANTE"
mkdir -p "$LAB"
cd "$LAB"
printf 'Estudiante: %s\n' "$ESTUDIANTE"
whoami
hostname
pwd

# Construcción del ejemplo guiado:
mkdir -p datos/2026-2 documentos/borradores documentos/finales respaldos informes permisos logs .control
ls -la
cd datos/2026-2
pwd                            # Ruta absoluta que muestra el sistema
cd ../..                       # Dos niveles hacia arriba
pwd
cd "$LAB/datos/2026-2"       # Acceso con ruta absoluta
pwd
cd "$LAB"
find . -maxdepth 3 -type d     # Muestra el árbol de directorios del ejemplo
```

**Observe:** compruebe que la navegación con `cd datos/2026-2` y con `cd "$LAB/datos/2026-2"` llega al mismo directorio cuando se inicia desde `$LAB`. `ls -la` hace visible `.control`. **Si se desconecta de SSH, deberá volver a declarar las variables `ESTUDIANTE` y `LAB` como se muestra arriba.**

### 3. Reto del estudiante: estructura de trabajo personalizada

1. En la carpeta `$LAB`, construya una **estructura adicional propia** con directorios de inventario, actividades académicas, documentos, informes y evidencias. Incluya al menos tres actividades con nombres diferentes y dos niveles de subcarpetas en evidencias.
2. Agregue una carpeta oculta para control de su trabajo y demuestre que aparece al usar la opción adecuada de `ls`.
3. Ingrese a una de las actividades mediante una **ruta relativa**, muestre la ubicación con `pwd`, regrese al directorio principal y vuelva al mismo destino mediante una **ruta absoluta**. Compruebe que los dos resultados coinciden.
4. Muestre el árbol de directorios hasta la profundidad necesaria usando `find`; explique cómo interpreta los niveles que creó.
5. **Evidencia 1:** capture el identificador visible mediante `printf`, el nombre de VM (`hostname`), la ruta (`pwd`), la estructura (`find`) y la carpeta oculta (`ls -la`). No utilice comandos de grupos posteriores.

---

<a id="grupo-2"></a>
## Grupo 2. Creación y administración de archivos

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** crear y modificar archivos dentro de la estructura del grupo 1, elaborar un inventario y mantener una copia de seguridad.

| Comando u operador | Sintaxis general | Uso habitual |
|---|---|---|
| `touch` | `touch archivo` | Crea un archivo vacío o actualiza su marca de tiempo. |
| `printf` | `printf 'texto\n'` | Escribe contenido textual estructurado. |
| `>` | `comando > archivo` | Guarda la salida en un archivo **reemplazando** su contenido. |
| `>>` | `comando >> archivo` | **Agrega** contenido al final de un archivo existente. |
| `cat` | `cat archivo` | Muestra el contenido completo de un archivo de texto. |
| `cp` | `cp origen destino` | Copia archivos. |
| `mv` | `mv origen destino` | Mueve o cambia el nombre de un archivo. |
| `rm` | `rm -i archivo` | Solicita confirmación antes de borrar un archivo de prueba. |
| `wc` | `wc -l archivo` | Cuenta líneas; en un CSV con cabecera, reste mentalmente una para contar registros. |

### 2. Ejemplos guiados para replicar

```bash
cd "$LAB"
touch documentos/borradores/nota_demo.txt
printf 'producto,cantidad,precio_unitario,ubicacion\nRouter,2,120000,bodega\nSwitch,3,80000,aula\n' > datos/2026-2/inventario_demo.csv
cat datos/2026-2/inventario_demo.csv
wc -l datos/2026-2/inventario_demo.csv

# Copia antes de la actualización:
cp datos/2026-2/inventario_demo.csv respaldos/inventario_demo_copia.csv
printf 'Cable,5,15000,bodega\n' >> datos/2026-2/inventario_demo.csv
wc -l datos/2026-2/inventario_demo.csv
wc -l respaldos/inventario_demo_copia.csv
cat datos/2026-2/inventario_demo.csv

# Renombrar y retirar exclusivamente un archivo de prueba:
mv documentos/borradores/nota_demo.txt documentos/borradores/nota_renombrada.txt
rm -i documentos/borradores/nota_renombrada.txt
ls -l datos/2026-2 respaldos
```

**Observe:** `>` sobrescribe el archivo indicado; `>>` agrega filas sin perder las anteriores. El archivo de respaldo conserva las líneas existentes al momento de copiarlo. Para los retos utilice nombres distintos de los del ejemplo.

### 3. Reto del estudiante: inventario y control de versiones

1. Dentro de la estructura creada en el grupo 1, elabore `inventario_$ESTUDIANTE.csv` con la cabecera `producto,cantidad,precio_unitario,ubicacion` y **12 registros ficticios** en al menos tres ubicaciones. Incluya dos productos repetidos con diferencias de mayúsculas/minúsculas.
2. Cree `perfil_$ESTUDIANTE.txt` en sus documentos; incluya su nombre académico, grupo, asignatura y tres objetivos del taller. Utilice únicamente `printf` y las redirecciones estudiadas para escribirlo.
3. Antes de modificar el inventario, cree una copia en `respaldos` y renómbrela con el sufijo `_v1`. Verifique con `ls` y `cat` la existencia y el contenido de las dos versiones.
4. Agregue **tres registros nuevos** al inventario original sin perder las 12 filas previas. Compruebe con `wc -l` cuántas líneas tiene ahora cada archivo, explique por qué el respaldo conserva un total diferente y recuerde descontar la cabecera.
5. **Evidencia 2:** capture el contenido del original y del respaldo con `cat`, sus nombres con `ls` y los conteos con `wc -l`. Reutilice la estructura del grupo 1; no emplee `head`, `tail` ni otros comandos que se presentarán después.

---

<a id="grupo-3"></a>
## Grupo 3. Lectura y búsqueda de contenido

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** analizar el inventario del grupo 2, buscar registros y comparar el original con el respaldo.

| Comando | Sintaxis general | Uso habitual |
|---|---|---|
| `cat` | `cat archivo` | Muestra el archivo completo (repaso del grupo 2). |
| `less` | `less archivo` | Permite leer por páginas; se sale con `q`. |
| `head` | `head -n N archivo` | Muestra las primeras `N` líneas. |
| `tail` | `tail -n N archivo` | Muestra las últimas `N` líneas. |
| `grep` | `grep -n 'patrón' archivo` | Busca coincidencias respetando mayúsculas. |
| `grep` | `grep -in 'patrón' archivo` | Ignora mayúsculas y muestra números de línea. |
| `grep` | `grep -ic 'patrón' archivo` | Cuenta líneas coincidentes ignorando mayúsculas. |
| `find` | `find ruta -type f -name '*.csv'` | Localiza archivos CSV por nombre. |
| `diff` | `diff -u archivo1 archivo2` | Compara dos archivos por líneas y presenta diferencias. |

### 2. Ejemplos guiados para replicar

```bash
cd "$LAB"
cat datos/2026-2/inventario_demo.csv
head -n 2 datos/2026-2/inventario_demo.csv
tail -n 2 datos/2026-2/inventario_demo.csv
grep -n 'Router' datos/2026-2/inventario_demo.csv
grep -in 'router' datos/2026-2/inventario_demo.csv
grep -ic 'router' datos/2026-2/inventario_demo.csv
find . -type f -name '*.csv'
diff -u respaldos/inventario_demo_copia.csv datos/2026-2/inventario_demo.csv
# Opcional si less está instalado: less datos/2026-2/inventario_demo.csv
```

**Observe:** `less` puede no estar instalado en imágenes Linux mínimas; es opcional y se cierra con la tecla `q`. Si los archivos son diferentes, `diff -u` imprime las líneas afectadas. Si son idénticos, normalmente no imprime nada. `grep -ic` cuenta **líneas** que contienen el patrón, no el número total de veces que aparece dentro de ellas.

### 3. Reto del estudiante: análisis comparativo del inventario

1. Desde su estructura personal, muestre las primeras cinco y las últimas cuatro líneas del inventario de 15 registros, y verifique con `wc -l` el total, distinguiendo cabecera de datos.
2. Seleccione el producto que registró con distintas mayúsculas. Búsquelo con `grep -n` y luego con `grep -in`; use `grep -ic` para cuantificar las líneas coincidentes y explique cualquier diferencia.
3. Localice todos los CSV de su espacio de trabajo con `find`. Compare el inventario original y el respaldo `_v1` con `diff -u`; explique qué filas fueron incorporadas después de generar la copia.
4. Construya un archivo de resumen en `informes` utilizando `printf` y redirecciones del grupo 2. Registre el patrón examinado, los conteos observados y la diferencia entre versiones (sin inventar resultados).
5. **Evidencia 3:** capture las búsquedas, la comparación `diff -u` y el contenido del resumen mediante `cat`.

---

<a id="grupo-4"></a>
## Grupo 4. Usuarios, permisos y privilegios

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** examinar propietarios, permisos de lectura (`r`), escritura (`w`) y ejecución (`x`) y comprender cuándo se requieren privilegios administrativos.

| Comando o construcción | Sintaxis general | Uso habitual |
|---|---|---|
| `whoami` / `id` | `whoami` / `id` | Consulta usuario efectivo, UID y grupos. |
| `ls -l` | `ls -l archivo` | Muestra permisos, propietario y grupo. |
| `chmod` | `chmod MODO archivo` | Establece permisos de archivos propios. |
| `stat` | `stat -c '%n %a %U:%G' archivo` | Muestra ruta, permisos numéricos y propietario/grupo. |
| `date` | `date` | Muestra fecha y hora configuradas en la VM. |
| `hostname` | `hostname` | Muestra el nombre del equipo (repaso del grupo 1). |
| `sudo` | `sudo comando` | Eleva privilegios cuando existe autorización; **no se necesita para este reto**. |
| Sustitución de comandos | `$(comando)` | Inserta en una cadena la salida de un comando. |
| Ejecución de script | `./ruta/script.sh` | Ejecuta el archivo si dispone del permiso `x` y un intérprete válido. |

**Interpretación:** `600` significa lectura y escritura para el propietario; `644`, lectura y escritura para el propietario y lectura para grupo/otros; `700`, lectura, escritura y acceso/ejecución solo para el propietario. En carpetas, el permiso `x` permite atravesarlas.

### 2. Ejemplos guiados para replicar

```bash
cd "$LAB"
printf 'Solo para practicar permisos\n' > permisos/privado_demo.txt
printf 'Lectura compartida de ejemplo\n' > permisos/publico_demo.txt
chmod 600 permisos/privado_demo.txt
chmod 644 permisos/publico_demo.txt
mkdir -p permisos/zona_privada_demo
chmod 700 permisos/zona_privada_demo
ls -ld permisos/zona_privada_demo
ls -l permisos/privado_demo.txt permisos/publico_demo.txt
stat -c '%n: %a, propietario %U, grupo %G' permisos/privado_demo.txt permisos/publico_demo.txt permisos/zona_privada_demo
id
date
hostname

# Generación y ejecución de un script sin privilegios de administrador:
printf '#!/bin/bash\nprintf "Identificador: %%s\\n" "$ESTUDIANTE"\ndate\nhostname\n' > permisos/estado_demo.sh
chmod 700 permisos/estado_demo.sh
./permisos/estado_demo.sh
```

**Observe:** el script combina `printf`, `date` y `hostname`. `chmod 700` permite ejecutarlo como propietario. El script recibe `$ESTUDIANTE` porque la variable se declaró con `export` en el grupo 1. Si abre otra sesión SSH, debe volver a declararla.

### 3. Reto del estudiante: protección de archivos personales

1. Retome `perfil_$ESTUDIANTE.txt` del grupo 2 y cree en la carpeta `permisos` una copia **privada** y otra de **lectura pública**. Asigne, respectivamente, permisos `600` y `644`; compruébelos con `ls -l` y `stat`.
2. Cree una carpeta personal restringida dentro de `permisos`, establezca el modo `700` y verifique que presenta los permisos esperados. Explique qué permiten los bits `r`, `w` y `x` al propietario de esa carpeta.
3. Cree un script `.sh` distinto al ejemplo que, al ejecutarse, presente su identificador, el nombre de la VM y la fecha/hora. Use únicamente `printf`, `date`, `hostname`, `chmod` y la sintaxis de script ya demostrada.
4. Genere en `informes` una breve justificación de por qué puede realizar estas operaciones sin `sudo`. Indique una operación administrativa que **en principio** sí podría requerir privilegios, sin ejecutarla.
5. **Evidencia 4:** capture permisos numéricos y simbólicos, propietario y salida del script en ejecución.

---

<a id="grupo-5"></a>
## Grupo 5. Procesos y trabajos del shell

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** identificar procesos, observar su estado y terminar exclusivamente procesos propios creados para el taller.

| Comando o construcción | Sintaxis general | Uso habitual |
|---|---|---|
| `ps` | `ps -p PID -o pid,stat,cmd` | Consulta un proceso específico. |
| `ps` | `ps -u USUARIO -o pid,ppid,stat,cmd` | Lista procesos de un usuario. |
| `top` | `top` | Monitorea procesos; se sale con `q`. |
| `jobs` | `jobs -l` | Muestra los trabajos en segundo plano del shell actual. |
| `sleep` | `sleep SEGUNDOS` | Mantiene un proceso de espera durante un tiempo. |
| `&` | `sleep 300 &` | Envía un proceso al segundo plano. |
| `$!` | `PID=$!` | Almacena el PID del último proceso iniciado en segundo plano. |
| `pgrep` | `pgrep -a NOMBRE` | Encuentra procesos coincidentes por nombre. |
| `kill` | `kill PID` | Solicita terminar un proceso propio mediante SIGTERM. |

### 2. Ejemplos guiados para replicar

```bash
cd "$LAB"
ps -u "$(whoami)" -o pid,ppid,stat,cmd
sleep 600 &
PID_DEMO_A=$!
sleep 720 &
PID_DEMO_B=$!
printf 'PID A: %s | PID B: %s\n' "$PID_DEMO_A" "$PID_DEMO_B"
jobs -l
ps -p "$PID_DEMO_A" -o pid,stat,cmd
ps -p "$PID_DEMO_B" -o pid,stat,cmd
pgrep -a sleep
kill "$PID_DEMO_A"
ps -p "$PID_DEMO_A" -o pid,stat,cmd
kill "$PID_DEMO_B"
jobs -l
```

**Observe:** el símbolo `&` inicia una tarea en segundo plano; `$!` permite capturar su PID. Un proceso recién terminado puede aparecer temporalmente como finalizado o zombi hasta que el shell recoja su estado. **No use `kill` sobre procesos de otros usuarios ni sobre servicios del sistema.**

### 3. Reto del estudiante: trazabilidad y control de procesos

1. Conserve el trabajo de los grupos anteriores. Inicie **tres procesos `sleep` propios**, cada uno con un tiempo diferente y suficiente para completar las comprobaciones. Asigne a cada proceso una variable con su PID usando el mecanismo explicado.
2. Consulte los tres procesos mediante `ps` y `jobs -l`. Cree un archivo en `informes` con los tres PID y sus duraciones mediante `printf` y redirección, reutilizando lo aprendido en el grupo 2.
3. Finalice **solo el proceso de duración intermedia**. Compruebe individualmente con `ps` los otros dos y el proceso terminado; explique las diferencias observadas.
4. Termine los otros dos procesos de práctica y compruebe su estado sin modificar procesos ajenos. Conserve el registro creado en el paso 2.
5. **Evidencia 5:** capture el estado inicial, la terminación selectiva, el estado final y el archivo de registro.

---

<a id="grupo-6"></a>
## Grupo 6. Recursos del sistema

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** identificar las características de la VM y relacionar los archivos personales con el almacenamiento, memoria y procesadores disponibles.

| Comando | Sintaxis general | Uso habitual |
|---|---|---|
| `uname` | `uname -a` | Muestra información del kernel y la arquitectura. |
| `free` | `free -h` | Muestra memoria RAM y swap en unidades legibles. |
| `df` | `df -h ruta` | Consulta la capacidad y uso del sistema de archivos que aloja una ruta. |
| `du` | `du -sh ruta` | Mide el espacio ocupado por una carpeta. |
| `lscpu` | `lscpu` | Muestra arquitectura y procesadores visibles. |
| `uptime` | `uptime` | Muestra tiempo desde el arranque y carga media. |
| `cat` | `cat /etc/os-release` | Lee la identificación de la distribución (comando del grupo 2). |
| `date` | `date` | Muestra hora de la VM (grupo 4). |

### 2. Ejemplos guiados para replicar

```bash
cd "$LAB"
cat /etc/os-release
uname -a
free -h
df -h "$HOME"
du -sh "$LAB"
lscpu
uptime
date

# Crear un informe mediante comandos ya estudiados:
printf 'Ficha tecnica de %s\n' "$ESTUDIANTE" > informes/recursos_demo.txt
uname -a >> informes/recursos_demo.txt
free -h >> informes/recursos_demo.txt
df -h "$HOME" >> informes/recursos_demo.txt
du -sh "$LAB" >> informes/recursos_demo.txt
uptime >> informes/recursos_demo.txt
cat informes/recursos_demo.txt
```

**Observe:** `df` informa sobre el sistema de archivos; `du` mide el espacio de una carpeta. Compare los valores, pero no los trate como magnitudes idénticas. La hora y el tiempo de actividad corresponden al entorno donde se ejecutan los comandos: en esta práctica, **la VM**, no su computador personal.

### 3. Reto del estudiante: ficha técnica verificable

1. Consulte la distribución, la versión del kernel, la arquitectura de CPU, los procesadores disponibles, la memoria y el espacio de disco correspondiente a su carpeta de trabajo. Interprete las salidas de `cat`, `uname`, `lscpu`, `free` y `df`.
2. Determine con `du` cuánto ocupa el directorio personal del taller y compárelo con el sistema de archivos mostrado por `df`. Explique por qué un tamaño de carpeta no equivale al espacio usado de todo el disco.
3. Consulte `date`, `hostname` y `uptime`, e identifique por qué la información pertenece a la VM a la que se conectó mediante SSH.
4. En el directorio `informes` que creó anteriormente, genere `recursos_$ESTUDIANTE.txt`. Incluya tanto los datos obtenidos como una interpretación redactada con `printf`, `>` y `>>`, siguiendo la demostración guiada.
5. **Evidencia 6:** capture la consulta de recursos, el informe y su contenido mostrado con `cat`.

---

<a id="grupo-7"></a>
## Grupo 7. Redes, salida a Internet y creación de redes en OCI

**Este grupo es el eje técnico del taller.** Integra diagnóstico de la red Linux, conectividad, reconstrucción de la red OCI, consultas con OCI CLI y creación opcional controlada de una red de laboratorio. Se reutilizan `printf`, `cat` y las redirecciones aprendidas en los grupos 1–6.

### 1. Sintaxis general, objetivo y usos habituales

#### A. Comandos de diagnóstico dentro de la VM Linux

| Comando | Sintaxis general | Qué permite observar |
|---|---|---|
| `ip -br address` | `ip -br address` | Interfaces e IP asignadas; `lo` es loopback. |
| `ip link` | `ip link show` | Estado y dirección MAC de las interfaces. |
| `ip route` | `ip route show` | Tabla de rutas del **sistema operativo**. |
| `ip route get` | `ip route get IP` | Ruta, interfaz y dirección origen prevista para un destino. |
| `cat /etc/resolv.conf` | `cat /etc/resolv.conf` | Configuración visible del resolvedor DNS. |
| `getent` | `getent ahostsv4 DOMINIO` | Resolución de nombres mediante NSS del sistema. |
| `ping` | `ping -c N HOST` | Envía ICMP Echo; puede estar bloqueado aunque exista Internet. |
| `curl` | `curl -I --max-time N URL` | Comprueba respuesta de un servicio HTTP(S). |
| `ss` | `ss -tuln` | Puertos TCP/UDP locales en escucha. |
| `tracepath` | `tracepath IP` | Muestra saltos de red si está instalado y la red responde. |
| `nmcli` | `nmcli device status` | Consulta conexiones administradas por NetworkManager (si existe). |

**Interpretaciones fundamentales:**

- **IP privada:** asignada a la VNIC de la VM dentro de la subred OCI. Por ejemplo, `10.50.1.20/24`.
- **Máscara/prefijo CIDR:** `/24` indica que 24 bits corresponden al prefijo; una subred IPv4 `/24` contiene 256 direcciones totales, aunque OCI reserva algunas y no todas están disponibles para instancias.
- **Gateway o puerta de enlace en Linux:** siguiente salto para salir de la red local. Suele ser el enrutador virtual de la VCN, **no** la IP pública del Internet Gateway.
- **Ruta por defecto:** `default` en `ip route` es la ruta usada para destinos que no coinciden con otras rutas más específicas.
- **DNS:** traduce nombres, por ejemplo `example.com`, a direcciones IP; puede funcionar aunque el acceso HTTP falle, y viceversa.
- **Puertos:** identifican servicios en los extremos (por ejemplo, TCP 22 para SSH, TCP 443 para HTTPS). Un puerto en escucha no implica que sea accesible desde Internet.

#### B. Recursos de OCI: no son comandos de la VM

| Recurso OCI | Función | Ejemplo didáctico |
|---|---|---|
| **VCN** (Virtual Cloud Network) | Red virtual definida por software dentro de una región. | `10.50.0.0/16` |
| **Subred** | Segmento de direcciones dentro de la VCN. | `10.50.1.0/24` |
| **VNIC** (Virtual Network Interface Card) | Tarjeta virtual que conecta la VM a la subred. | IP privada `10.50.1.20` |
| **Route Table** (tabla de rutas OCI) | Establece cómo salen paquetes desde la subred hacia destinos fuera de la VCN. | `0.0.0.0/0 → Internet Gateway` |
| **Internet Gateway (IGW)** | Permite conexión directa con Internet para recursos de subred pública con IP pública y reglas adecuadas. | Ruta de salida pública |
| **NAT Gateway** | Permite iniciar conexiones hacia Internet desde recursos **sin IP pública**, sin aceptar conexiones entrantes no solicitadas. | Subred privada → NAT |
| **NSG** / **Security List** | Reglas virtuales de ingreso y egreso. | SSH solo desde el origen autorizado |
| **DNS de OCI** | Resolución de nombres de Internet y de la VCN, según configuración. | Internet and VCN Resolver |

#### C. Comandos de OCI CLI en Cloud Shell y variables del shell

| Comando u opción | Sintaxis general | Finalidad |
|---|---|---|
| `oci --version` | `oci --version` | Verifica la disponibilidad de OCI CLI. |
| `oci network vcn list` | `oci network vcn list --compartment-id OCID` | Consulta las VCN accesibles. |
| `oci network subnet list` | `oci network subnet list --compartment-id OCID --vcn-id OCID` | Consulta subredes de una VCN. |
| `oci network internet-gateway list` | `oci network internet-gateway list --compartment-id OCID --vcn-id OCID` | Consulta gateways de Internet. |
| `oci network route-table get` | `oci network route-table get --rt-id OCID` | Lee reglas de una tabla de rutas. |
| `oci network security-list get` | `oci network security-list get --security-list-id OCID` | Consulta reglas de una lista de seguridad. |
| `oci network ... create` | `oci network vcn create`, `internet-gateway create`, `subnet create` | Crea recursos, **solo con autorización**. |
| `oci network route-table update` | `oci network route-table update --rt-id OCID --route-rules JSON` | Reemplaza reglas de una tabla, **solo de una VCN aislada autorizada**. |
| Variable y `export` | `export COMPARTMENT_OCID='valor'` | Guarda el identificador en la sesión de Cloud Shell. |
| `exit` | `exit` | Cierra la sesión SSH para regresar a Cloud Shell. |
| Extracción de datos | `--query 'data.id' --raw-output` | Extrae el campo `id` de la respuesta sin formato JSON. |

**Importante:** la tabla de rutas de Linux (`ip route`) y la **tabla de rutas de OCI** son objetos diferentes. `ip` **no crea una VCN ni un Internet Gateway**. Para ello se utiliza la consola OCI, la API o `oci network ...` desde un entorno autenticado, como Cloud Shell. Los comandos `ip link add` e `ip netns add` permiten crear elementos de red **dentro de Linux**, pero no recursos VCN de OCI.

### 2. Ejemplos guiados para replicar y comprender

#### Práctica 7.1. Identificar interfaz, direccionamiento y puerta de enlace (DENTRO DE LA VM)

```bash
hostname
ip -br address
ip link show
ip route show
ip route get 1.1.1.1
```

**Interprete la salida:**

- ¿Cuál interfaz tiene la IP privada de OCI? Su nombre puede ser `ens3`, `enp0s3` u otro; no lo suponga.
- En `ip route`, identifique la línea `default via ... dev ...` cuando exista. El valor después de `via` es el siguiente salto visto por Linux.
- `ip route get 1.1.1.1` indica la ruta prevista, pero **no prueba que se pueda alcanzar Internet**.

**Salida meramente ilustrativa:**

```text
ens3            UP             10.50.1.20/24

default via 10.50.1.1 dev ens3
10.50.1.0/24 dev ens3 proto kernel scope link src 10.50.1.20

1.1.1.1 via 10.50.1.1 dev ens3 src 10.50.1.20
```

#### Práctica 7.2. Diagnóstico por capas: ruta → DNS → Internet (DENTRO DE LA VM)

```bash
ip route get 1.1.1.1
cat /etc/resolv.conf
getent ahostsv4 example.com
ping -c 3 1.1.1.1
ping -c 3 example.com
curl -I --max-time 10 https://example.com
```

**Cómo leer los resultados:**

1. Si `ip route get` falla, revise direccionamiento y tabla de rutas Linux.
2. Si `getent` falla pero la ruta existe, investigue DNS y las reglas aplicables a las consultas DNS.
3. Si `ping` no responde, **no concluya de inmediato que falta Internet**: ICMP podría estar filtrado.
4. Si `curl` devuelve encabezados HTTP (por ejemplo, `200`, `301` o `302`), se ha establecido una conexión al servicio web solicitado. Un error HTTP como `403` también implica que hubo respuesta HTTP; no significa por sí solo fallo de red.
5. Si hay conexión hacia el destino por IP pero no por nombre, la hipótesis principal es un problema de resolución de nombres.

**Comprobaciones adicionales opcionales, si el servicio externo está accesible:**

```bash
curl -sS --max-time 10 https://api.ipify.org
ss -tuln
# Solo si tracepath está instalado:
tracepath 1.1.1.1
```

El primer comando puede mostrar la **IP pública de salida observada por un servicio externo**. No siempre coincide con la IP pública de la VNIC, por ejemplo cuando la VM usa NAT o un proxy.

#### Práctica 7.3. Reconstruir el trayecto hacia Internet en OCI (CONSOLA OCI)

La salida de una **VM con subred pública y acceso directo** puede representarse así:

```text
Proceso (curl) en la VM
      ↓
Kernel Linux: IP privada, interfaz y ruta por defecto
      ↓
VNIC: interfaz virtual conectada a la subred OCI
      ↓
Subred pública dentro de la VCN
      ↓
Tabla de rutas OCI: 0.0.0.0/0 → Internet Gateway
      ↓
Internet Gateway + asignación de IP pública
      ↓
Internet → servidor HTTPS
```

Se aplican además las reglas de **egreso** de NSG/Security List, las reglas del firewall del sistema operativo y las respuestas correspondientes. Las reglas de **ingreso** afectan a nuevas conexiones iniciadas desde fuera; permitir SSH no equivale a habilitar toda la salida a Internet.

**Compare con una subred privada:** `VM sin IP pública → subred privada → tabla OCI 0.0.0.0/0 → NAT Gateway → Internet`. La infraestructura exacta depende de la red configurada y no debe deducirse únicamente de `ip route`.

**Procedimiento de inspección:**

1. En OCI abra **Compute → Instances → su instancia → Attached VNICs**. Registre la IP privada, la IP pública si la hay y el OCID de la subred (sin divulgar OCIDs completos en capturas públicas).
2. Abra **Networking → Virtual Cloud Networks (VCN)** y localice la VCN a la que pertenece la instancia.
3. Abra la **subred** y consulte el CIDR, la tabla de rutas asociada y las listas de seguridad. Compruebe si admite IP públicas.
4. Consulte **Route Tables** y determine adónde envía `0.0.0.0/0` (IGW, NAT u otro destino). Si la ruta no aparece, puede existir una configuración distinta o una ruta más específica que deba analizarse.
5. Consulte los **Network Security Groups (NSG)** asociados a la VNIC y las listas de seguridad pertinentes. Distinga reglas de **ingress** y **egress**.
6. Represente el flujo en una línea y contraste la puerta de enlace mostrada por Linux con el recurso de destino mostrado por la tabla OCI.

#### Práctica 7.4. Consultar recursos de OCI mediante comandos (EN CLOUD SHELL, NO EN LA VM)

Esta práctica es **solo de lectura**. Requiere que la sesión de Cloud Shell tenga habilitada la CLI de OCI y permisos IAM para consultar el compartimento.

```bash
# Ejecutar en Cloud Shell tras salir de SSH con: exit
oci --version

# Copiar el OCID del compartimento desde la consola OCI:
export COMPARTMENT_OCID='OCID_REAL_DEL_COMPARTIMENTO'

# Ver las VCN existentes:
oci network vcn list --compartment-id "$COMPARTMENT_OCID" --output table

# Copiar el OCID de la VCN que contiene su VM:
export VCN_OCID='OCID_REAL_DE_LA_VCN'

# Ver subredes e Internet Gateways de la VCN:
oci network subnet list --compartment-id "$COMPARTMENT_OCID" --vcn-id "$VCN_OCID" --output table
oci network internet-gateway list --compartment-id "$COMPARTMENT_OCID" --vcn-id "$VCN_OCID" --output table
```

Para inspeccionar las rutas de una subred, consulte desde la consola el `route-table-id` correspondiente y ejecute:

```bash
export ROUTE_TABLE_OCID='OCID_REAL_DE_LA_TABLA'
oci network route-table get --rt-id "$ROUTE_TABLE_OCID"
```

Para estudiar reglas de seguridad de la subred:

```bash
export SECURITY_LIST_OCID='OCID_REAL_DE_LA_LISTA'
oci network security-list get --security-list-id "$SECURITY_LIST_OCID"
```

**Observe:** los OCID son identificadores de recursos de la **cuenta OCI**, no direcciones IP. Si `oci ...` devuelve `NotAuthorizedOrNotFound`, el recurso puede no existir en la región/compartimento consultado o faltar un permiso IAM; no altere las políticas para intentar resolverlo.

#### Práctica 7.5. Crear una VCN y una subred de laboratorio mediante OCI CLI (OPCIONAL Y AUTORIZADA)

**Este procedimiento modifica recursos de la cuenta OCI.** Ejecútelo **solo si el docente autorizó la creación** en un compartimento de laboratorio y se verificó la disponibilidad de cuotas, límites y posibles costos. **No modifique la VCN de la VM en la que realiza el taller.** Una red sin instancias no demuestra conectividad externa; el objetivo de esta práctica es reconocer cómo se construye la infraestructura.

La secuencia **no crea servidores ni abre nuevos accesos públicos hacia la VM existente**:

```bash
# CLOUD SHELL tiene variables independientes de las de la VM:
ESTUDIANTE="maria_rojas"  # Reemplace por su propio identificador del grupo 1
export COMPARTMENT_OCID='OCID_REAL_DEL_COMPARTIMENTO_AUTORIZADO'

# Crear una nueva VCN de uso exclusivo. El CIDR 10.73.0.0/16 es
# solo un ejemplo: confirme previamente que no se superpone con otras redes.
export LAB_VCN_OCID=$(oci network vcn create \
  --compartment-id "$COMPARTMENT_OCID" \
  --display-name "vcn-lab-$ESTUDIANTE" \
  --dns-label "vcnredlab" \
  --cidr-blocks '["10.73.0.0/16"]' \
  --query 'data.id' --raw-output)

printf 'VCN creada: %s\n' "$LAB_VCN_OCID"
oci network vcn get --vcn-id "$LAB_VCN_OCID"
```

**Interpretación:** la VCN recién creada incluye una tabla de rutas, lista de seguridad y opciones DHCP predeterminadas, pero **no** incluye automáticamente una conexión funcional a Internet.

```bash
# Obtener la tabla de rutas predeterminada de la VCN recién creada:
export LAB_RT_OCID=$(oci network vcn get --vcn-id "$LAB_VCN_OCID" \
  --query 'data."default-route-table-id"' --raw-output)

# Crear Internet Gateway para esta VCN aislada:
export LAB_IGW_OCID=$(oci network internet-gateway create \
  --compartment-id "$COMPARTMENT_OCID" \
  --vcn-id "$LAB_VCN_OCID" \
  --is-enabled true \
  --display-name 'igw-lab-redes' \
  --query 'data.id' --raw-output)

printf 'IGW creado: %s\n' "$LAB_IGW_OCID"
```

**La siguiente actualización reemplaza todas las reglas de la tabla indicada.** Aquí se ejecuta **exclusivamente** sobre la tabla predeterminada de la **VCN nueva y aislada**; nunca sobre una tabla compartida o de producción.

```bash
# Definir ruta por defecto hacia el IGW del laboratorio:
oci network route-table update \
  --rt-id "$LAB_RT_OCID" \
  --route-rules "[{\"cidrBlock\":\"0.0.0.0/0\",\"networkEntityId\":\"$LAB_IGW_OCID\"}]"

# Crear una subred pública de laboratorio sin instancias:
oci network subnet create \
  --compartment-id "$COMPARTMENT_OCID" \
  --vcn-id "$LAB_VCN_OCID" \
  --cidr-block '10.73.10.0/24' \
  --display-name "subred-publica-$ESTUDIANTE" \
  --dns-label 'publab' \
  --route-table-id "$LAB_RT_OCID" \
  --prohibit-public-ip-on-vnic false

# Verificar la estructura creada:
oci network subnet list --compartment-id "$COMPARTMENT_OCID" \
  --vcn-id "$LAB_VCN_OCID" --output table
oci network route-table get --rt-id "$LAB_RT_OCID"
```

**Limitaciones del ejemplo:** no se despliega una instancia ni se valida tráfico saliente; las reglas de seguridad de la VCN también importan. La conectividad de una VM futura requeriría, además de la ruta al IGW, una IP pública en una subred pública y reglas de salida apropiadas. **No abra SSH/22 ni otros puertos a `0.0.0.0/0` por el solo hecho de completar el taller.**

**Construcción del informe de redes con comandos ya conocidos:** dentro de la **VM** y después de volver a conectarse por SSH, puede conservar evidencia textual como sigue:

```bash
cd "$LAB"
printf 'Diagnostico de red de %s\n' "$ESTUDIANTE" > "informes/red_$ESTUDIANTE.txt"
ip -br address >> "informes/red_$ESTUDIANTE.txt"
ip route show >> "informes/red_$ESTUDIANTE.txt"
ip route get 1.1.1.1 >> "informes/red_$ESTUDIANTE.txt"
getent ahostsv4 example.com >> "informes/red_$ESTUDIANTE.txt"
ss -tuln >> "informes/red_$ESTUDIANTE.txt"
cat "informes/red_$ESTUDIANTE.txt"
```

Si algunas pruebas fallan, agregue con `printf` sus observaciones y capture el error observado. El código anterior no cambia la red ni presupone resultados positivos.

**Cierre del ejercicio opcional:** comunique los recursos creados al docente y elimínelos después de obtener autorización, siguiendo las dependencias de OCI. Nunca elimine redes ajenas. Si no se dispone de permisos, reemplace esta práctica por un **diseño documentado** de la VCN, la subred y la ruta propuesta, sin ejecutar operaciones de creación.

### 3. Reto del estudiante: investigación de la red y diagnóstico de salida a Internet

1. En su VM, identifique el nombre real de la interfaz de red, IPv4 privada, prefijo CIDR, dirección MAC y puerta de enlace usando exclusivamente los comandos `ip` presentados en las prácticas 7.1 y 7.2. Registre los resultados en el archivo personal de redes mediante `printf`, `>` o `>>`.
2. Determine qué interfaz y dirección de origen utilizaría Linux para un destino externo. Compare `ip route show` e `ip route get 1.1.1.1` y explique por qué una ruta prevista **no demuestra** conectividad real.
3. Realice en orden las pruebas de DNS, ICMP y HTTPS con `getent`, `ping` y `curl`. Para cada una, indique el resultado real y explique qué se está comprobando. Si un `ping` falla, no concluya automáticamente que no existe salida a Internet.
4. Consulte los puertos TCP/UDP locales con `ss -tuln`. Identifique si existe un puerto SSH en escucha y justifique por qué escuchar localmente no garantiza que el puerto sea accesible desde Internet.
5. En la **consola OCI**, inspeccione y documente la relación VM → VNIC → subred → VCN → tabla de rutas → IGW o NAT Gateway → Internet. Compare la puerta de enlace mostrada por Linux con el recurso de destino configurado en la tabla de rutas OCI. Consulte también NSG y Security List, tal como se explicó en la práctica 7.3.
6. Si dispone de permisos de lectura, vuelva a **Cloud Shell** y utilice los comandos `oci network ... list` y `... get` de la práctica 7.4 para verificar al menos dos datos observados en la consola OCI. Si IAM impide consultar un recurso, documente el mensaje sin intentar modificar permisos.
7. Genere `informes/red_$ESTUDIANTE.txt` en la VM con los resultados de las pruebas, utilizando únicamente la escritura y consulta de archivos de los grupos anteriores. Añada un diagnóstico de esta situación hipotética: «DNS funciona, pero `curl` no consigue conectarse por HTTPS». Proponga tres verificaciones ya aprendidas y especifique qué aclararía cada una.
8. **Ampliación opcional, exclusivamente con autorización del docente:** adapte la práctica 7.5 y cree una VCN **nueva y aislada**, una subred `/24`, un IGW y una ruta por defecto, usando solo los comandos `oci network` y opciones demostrados en esa práctica. Personalice los nombres. Si no dispone de permisos, presente el diseño de los recursos sin ejecutarlo. No modifique la red de la VM de clase.
9. **Evidencia 7:** capture los resultados de `ip -br address`, `ip route`, `ip route get`, `getent`, `curl` y `ss`, junto con la tabla de rutas de la subred en la consola OCI. Incluya captura adicional únicamente si se autorizó y completó la creación de la nueva red; proteja secretos e identificadores restringidos.

---

<a id="grupo-8"></a>
## Grupo 8. Servicios, registros y paquetes

### 1. Sintaxis general, objetivo y usos habituales

**Objetivo:** examinar servicios y paquetes de la misma VM sin alterar servicios críticos ni instalar software no autorizado.

| Comando | Sintaxis general | Uso habitual |
|---|---|---|
| `systemctl` | `systemctl is-active SERVICIO` | Informa si la unidad está activa. |
| `systemctl` | `systemctl --no-pager status SERVICIO` | Consulta el estado detallado de un servicio. |
| `journalctl` | `journalctl -u SERVICIO -n 10 --no-pager` | Muestra los últimos eventos del servicio (si hay permisos). |
| `command -v` | `command -v programa` | Consulta la ruta de una herramienta presente en `PATH`. |
| `rpm` / `dnf` | `rpm -q PAQUETE` / `dnf list installed` | Consulta paquetes en Oracle Linux. |
| `dpkg` / `apt` | `dpkg -l PAQUETE` / `apt list --installed` | Consulta paquetes en Ubuntu. |

### 2. Ejemplos guiados para replicar

Primero identifique la distribución con `cat /etc/os-release` (grupo 2) y consulte las herramientas y servicios presentes. Si la VM es **Oracle Linux**, ejecute los comandos marcados para esa distribución; si es **Ubuntu**, ejecute solo los correspondientes a Ubuntu. No ejecute todos indistintamente.

```bash
cat /etc/os-release
command -v ip
command -v curl
command -v ss
command -v ssh
```

**Oracle Linux (si su imagen usa el servicio `sshd`):**

```bash
systemctl is-active sshd
systemctl --no-pager status sshd
rpm -q openssh-server
dnf list installed
journalctl -u sshd -n 10 --no-pager
```

**Ubuntu (si su imagen usa el servicio `ssh`):**

```bash
systemctl is-active ssh
systemctl --no-pager status ssh
dpkg -l openssh-server
apt list --installed
journalctl -u ssh -n 10 --no-pager
```

**Observe:** que un ejecutable esté instalado no implica que su servicio esté activo. La visualización de algunos registros puede estar restringida. **No detenga ni reinicie SSH**; perdería su sesión de acceso remoto.

**Ejemplo acumulativo de informe:** reutilice la escritura con `printf`, los comandos de consulta actuales y los archivos del grupo 7:

```bash
cd "$LAB"
printf 'Auditoria de servicios de %s\n' "$ESTUDIANTE" > "informes/servicios_$ESTUDIANTE.txt"
cat /etc/os-release >> "informes/servicios_$ESTUDIANTE.txt"
command -v ssh >> "informes/servicios_$ESTUDIANTE.txt"
cat "informes/red_$ESTUDIANTE.txt"
```

### 3. Reto del estudiante: auditoría acumulativa de la VM

1. Identifique su distribución y seleccione exclusivamente el conjunto de comandos de paquetes explicado para Oracle Linux o para Ubuntu. Consulte si está instalado OpenSSH y muestre los resultados.
2. Verifique con `command -v` las herramientas de red `ip`, `curl` y `ss`, y consulte con `systemctl` el estado de SSH. Explique la diferencia entre **herramienta instalada**, **servicio activo** y **puerto abierto**, relacionando el resultado con `ss -tuln` del grupo 7.
3. Consulte con `journalctl` los últimos eventos de SSH, si los permisos de la cuenta lo permiten. Si no, documente el mensaje observado sin modificar privilegios.
4. Amplíe el archivo `informes/servicios_$ESTUDIANTE.txt` con una interpretación del estado de SSH, los paquetes identificados y los resultados de red del grupo 7. Utilice solamente `printf`, `cat`, redirecciones y los comandos previamente aprendidos.
5. **Evidencia 8:** capture la comprobación de paquetes y servicios, una consulta de red del grupo 7 y el informe final. No instale paquetes, actualice el sistema ni reinicie servicios.

---

## Entrega en clase y criterios de evidencia

**Formato de entrega:** un único archivo `Taller_Linux_OCI_<nombre_apellido>.pdf`, elaborado con capturas obtenidas **durante la sesión de clase**. Se exigen por lo menos **ocho capturas**, una por cada reto; se admiten capturas adicionales en redes para que comandos y configuraciones sean legibles. Entregar en el medio institucional indicado por el docente, **durante la clase**.

Cada captura (o conjunto breve de capturas del mismo reto, cuando no quepa en una sola pantalla) debe permitir verificar: identificador del estudiante, nombre de la VM, comando ejecutado y resultado visible. Antes de tomar la captura puede repetir `printf 'Estudiante: %s\n' "$ESTUDIANTE"` y `hostname`, ya enseñados en el grupo 1. Los comandos se acumulan por grupos: **no se admiten en el reto comandos que todavía no se hayan presentado en la secuencia guiada**. La captura debe mostrar un resultado obtenido, no solo texto escrito. No incluya claves SSH, tokens, contraseñas, contenido de `~/.ssh`, ni OCIDs completos si su institución restringe su divulgación.

| Grupo | Evidencia mínima verificable |
|---|---|
| 1 | Directorios y comprobación de rutas absoluta/relativa. |
| 2 | Inventario personalizado y respaldo, `cat` y recuento con `wc -l`. |
| 3 | Búsquedas `grep`, comparación con `diff` y resumen. |
| 4 | Permisos `600`, `644`, `700` y script ejecutado. |
| 5 | PID, `jobs`, terminación selectiva y registro. |
| 6 | Informe de CPU, memoria, disco y kernel. |
| 7 | Interfaz, rutas, DNS, HTTPS, puertos y ruta de salida en OCI. |
| 8 | Servicios, paquetes, estado de SSH y relación con redes. |

**Criterios de valoración:** continuidad de los retos (solo comandos enseñados hasta el grupo en curso); funcionalidad de los comandos; interpretación correcta de las salidas; trazabilidad y personalización de los archivos; complejidad y autonomía en los retos; seguridad al operar la VM y las redes; calidad y autenticidad de la evidencia. No basta con copiar comandos: el estudiante debe **poder explicar qué hace cada uno y qué demuestra su salida**.

## Fuentes técnicas y alcance

1. Oracle Cloud Infrastructure. *Public IP Addresses*. https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/managingpublicIPs.htm
2. Oracle Cloud Infrastructure. *Internet Gateway*. https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/managingIGs.htm
3. Oracle Cloud Infrastructure. *Creating a VCN*. https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/create_vcn.htm
4. Oracle Cloud Infrastructure. *Virtual Networking Wizards*. https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/quickstartnetworking.htm
5. Oracle Cloud Infrastructure. *OCI CLI: VCN create*. https://docs.oracle.com/en-us/iaas/tools/oci-cli/latest/oci_cli_docs/cmdref/network/vcn/create.html
6. Oracle Cloud Infrastructure. *OCI CLI: route-table update*. https://docs.oracle.com/en-us/iaas/tools/oci-cli/latest/oci_cli_docs/cmdref/network/route-table/update.html
7. Oracle Cloud Infrastructure. *OCI CLI: subnet create*. https://docs.oracle.com/en-us/iaas/tools/oci-cli/latest/oci_cli_docs/cmdref/network/subnet/create.html
8. GNU Project. *Coreutils Manual*. https://www.gnu.org/software/coreutils/manual/
9. GNU Project. *Bash Reference Manual*. https://www.gnu.org/software/bash/manual/
10. Linux man-pages. *ip-route(8)*. https://man7.org/linux/man-pages/man8/ip-route.8.html

> **Nota metodológica:** los ejemplos describen una VM Linux habitual conectada a una VCN de OCI. Las interfaces, direcciones, nombres de servicio, permisos y rutas reales varían. La sección de creación de redes exige IAM y autorización del docente; la presencia de una ruta configurada no prueba por sí misma la conectividad a Internet.
