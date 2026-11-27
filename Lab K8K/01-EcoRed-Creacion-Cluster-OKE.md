# Taller 1. Creación y configuración de un clúster Kubernetes en OCI

<a id="inicio"></a>

## Tabla de contenido

- [Fase 1. Fundamentos de Kubernetes y arquitectura EcoRed](#fase-1)
  - [1.1. De OCI Container Instances a Kubernetes](#1-1)
  - [1.2. Componentes de un clúster Kubernetes](#1-2)
  - [1.3. Red del clúster en OCI](#1-3)
  - [1.4. Arquitectura objetivo de EcoRed](#1-4)
  - [1.5. Ruta de los tres talleres](#1-5)
- [Fase 2. Creación y verificación del clúster OKE](#fase-2)
  - [2.1. Identificar los recursos e imágenes existentes](#2-1)
  - [2.2. Verificar la arquitectura de las imágenes](#2-2)
  - [2.3. Abrir OKE y seleccionar Quick Create](#2-3)
  - [2.4. Configurar el clúster y los dos Worker Nodes](#2-4)
  - [2.5. Crear el clúster y comprobar su estado](#2-5)
  - [2.6. Identificar las dos máquinas virtuales](#2-6)
  - [2.7. Identificar la VCN y las subredes](#2-7)
  - [2.8. Conectar Cloud Shell a la red privada](#2-8)
  - [2.9. Configurar kubectl y acceder al clúster](#2-9)
  - [2.10. Verificar nodos y recursos del sistema](#2-10)
  - [2.11. Crear el namespace ecored](#2-11)
  - [2.12. Comprobar la ejecución de Pods (prueba temporal)](#2-12)
  - [2.13. Guardar la información para el Taller 2](#2-13)
  - [2.14. Verificación final y diagnóstico](#2-14)
- [Entregable y criterios de aceptación](#entregable)
- [Referencias](#referencias)

---

**Caso de estudio:** EcoRed Circular.  
**Duración sugerida:** 2 horas de trabajo académico, sin contar demoras de aprovisionamiento de OCI.  
**Objetivo:** crear un clúster OKE con dos Worker Nodes privados y administrarlo desde Cloud Shell mediante `kubectl`, preparando la infraestructura para desplegar EcoRed en el siguiente taller.

**Pregunta detonante:** ¿qué cambia cuando pasamos de ejecutar cuatro contenedores en una Container Instance a disponer de un clúster que administra los contenedores sobre dos servidores?

**Punto de partida:** ya se han realizado los talleres de Docker, OCIR, microservicios y servicios externos. Este grupo aún no ha creado el clúster OKE. Se conservarán las imágenes publicadas en OCIR, la configuración de Firebase, MongoDB Atlas y Oracle Autonomous Database. No se requiere reconstruir las imágenes ni crear nuevamente las bases de datos.

<a id="fase-1"></a>

## Fase 1. Fundamentos de Kubernetes y arquitectura EcoRed

<a id="1-1"></a>

### 1.1. De OCI Container Instances a Kubernetes


<img width="1600" height="900" alt="01-evolucion-contenedores-oke" src="https://github.com/user-attachments/assets/7e1ad5b4-6ef5-4cb4-b503-9a5dd2a4b46d" />


**Kubernetes** administra aplicaciones contenerizadas sobre uno o varios servidores. **Oracle Kubernetes Engine (OKE)** es el servicio de OCI que permite crear y operar clústeres Kubernetes con el plano de control administrado por Oracle.

En EcoRed se conservarán las imágenes Docker de Frontend, Companies y Materials. En el Taller 2, OCI API Gateway reemplazará el gateway Node.js del laboratorio anterior. La aplicación se distribuirá sobre dos servidores, en lugar de concentrarse en una única Container Instance.

<a id="1-2"></a>

### 1.2. Componentes de un clúster Kubernetes

<img width="1600" height="900" alt="02-componentes-kubernetes" src="https://github.com/user-attachments/assets/4579b286-dabe-48e4-83b9-f044c898f3a7" />



| Concepto | Qué representa en EcoRed |
|---|---|
| Clúster | Conjunto formado por el plano de control y los nodos que ejecutan las aplicaciones. |
| Control Plane | Coordina el estado del clúster y asigna Pods a los nodos. Oracle lo administra en OKE. |
| Worker Node | Máquina virtual que aporta CPU y memoria para ejecutar Pods. |
| Node Pool | Grupo de Worker Nodes administrados con una configuración común. |
| Pod | Unidad que Kubernetes programa en un nodo; contiene uno o más contenedores. |
| Namespace | Agrupación lógica de recursos. Utilizaremos `ecored`. |
| kubectl | Comando para consultar y administrar Kubernetes. |

En la figura aparecen seis Pods de EcoRed como referencia del Taller 2. **En el Taller 1 todavía no se desplegarán los microservicios.** Los Pods del sistema que crea Kubernetes sí estarán presentes.

<a id="1-3"></a>

### 1.3. Red del clúster en OCI

<img width="1600" height="900" alt="03-red-oke" src="https://github.com/user-attachments/assets/567fa0f4-0e47-43ac-a93c-4d61725be6f8" />

| Recurso | Función |
|---|---|
| VCN | Red virtual que contiene los recursos del clúster. |
| Subred del API de Kubernetes | Aloja el punto de acceso administrativo al clúster (TCP 6443). No confundir con OCI API Gateway. |
| Subred de Worker Nodes | Aloja las dos máquinas virtuales, sin IP pública. |
| Subred pública de Load Balancers | Permitirá publicar el frontend en el Taller 2. |
| NAT Gateway | Permite conexiones salientes a Internet desde recursos privados sin publicar los nodos. |
| Service Gateway | Permite acceso a servicios OCI compatibles por la red de Oracle. |
| Security Lists / NSG | Controlan los flujos de red permitidos. |

Quick Create aprovisionará una VCN nueva dedicada al clúster. Esta decisión simplifica la primera creación de OKE y evita alterar la red de una Container Instance que ya funciona. Si la configuración elegida utiliza red nativa de OCI para Pods, se identificará también la subred o red que se les asigna. Las direcciones reales se consultarán después de crear el clúster, no se copiarán de las figuras.

<a id="1-4"></a>

### 1.4. Arquitectura objetivo de EcoRed

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/a0876c00-69ac-46fd-b0be-73c4ab5eb877" />


El objetivo de la ruta completa es alcanzar una arquitectura con dos Worker Nodes, un frontend público, dos microservicios privados, tres balanceadores, OCI API Gateway y Firebase. Las bases de datos continuarán fuera de Kubernetes. El escalamiento horizontal se incorporará en el Taller 3.

<a id="1-5"></a>

### 1.5. Ruta de los tres talleres
<img width="1600" height="900" alt="04-ruta-tres-talleres" src="https://github.com/user-attachments/assets/a196ec13-b4e0-49d5-a790-f1f18d924107" />


| Taller | Qué se construirá | Resultado |
|---|---|---|
| 1. Infraestructura | Clúster OKE, dos nodos, red, acceso con kubectl y namespace. | Clúster preparado. |
| 2. Despliegue | Frontend y dos microservicios, imágenes de OCIR, configuración, Services, Load Balancers, OCI API Gateway y Firebase. | EcoRed funcional. |
| 3. Operación | Metrics Server, HPA, recuperación de Pods, mantenimiento de un nodo y actualización progresiva. | EcoRed con escalamiento y pruebas de disponibilidad. |

[↑ Volver a la tabla de contenido](#inicio)

---

<a id="fase-2"></a>

## Fase 2. Creación y verificación del clúster OKE

En esta fase se construirán los componentes explicados en 1.2 y 1.3. Las actividades en Docker Desktop se realizarán en la terminal local; las actividades de Kubernetes se realizarán en **OCI Cloud Shell**.

<a id="2-1"></a>

### 2.1. Identificar los recursos e imágenes existentes

Ingrese a [OCI Console](https://cloud.oracle.com/) con la sesión que utiliza en los talleres. Seleccione la región de trabajo, preferiblemente **Brazil East (São Paulo)** (`sa-saopaulo-1`, clave OCIR `gru`), y el compartimento de sus recursos anteriores.

1. Abra el menú principal y seleccione **Developer Services → Container Registry**. Si no encuentra la opción, búsquela por su nombre en la barra de búsqueda superior.
2. Seleccione el compartimento donde publicó las imágenes.
3. Abra los repositorios de Companies, Materials y Frontend. Verifique que existe la etiqueta utilizada en el taller anterior.
4. Registre la ruta completa de las tres imágenes. No vuelva a publicarlas si ya están disponibles.

En el taller de referencia del semestre anterior se utilizaron rutas como las siguientes:

```text
gru.ocir.io/MI_NAMESPACE/ecored/ecored-companies:1.0
gru.ocir.io/MI_NAMESPACE/ecored/ecored-materials:1.0
gru.ocir.io/MI_NAMESPACE/ecored/ecored-frontend:1.0
```

Si sus repositorios se llaman `ecored/companies`, `ecored/materials` y `ecored/frontend`, conserve esos nombres. La ruta real es la que aparece en OCIR; no renombre ni duplique los repositorios para ajustarlos al ejemplo.

**Resultado esperado:** las tres imágenes están localizadas. MongoDB, Oracle y Firebase se reutilizarán en el Taller 2; no se conectarán desde OKE en este taller.

<a id="2-2"></a>

### 2.2. Verificar la arquitectura de las imágenes

En la terminal local de VS Code o Docker Desktop, consulte las imágenes que construyó y publicó anteriormente. Los siguientes comandos usan las imágenes públicas de referencia; cambie el usuario y la etiqueta por los de sus imágenes reales cuando corresponda.

```bash
docker buildx imagetools inspect adanbeltran/ecored-companies:1.0
docker buildx imagetools inspect adanbeltran/ecored-materials:1.0
docker buildx imagetools inspect adanbeltran/ecored-frontend:1.0
```

Si la salida no muestra la arquitectura de una imagen de plataforma única, descárguela (solo si no está localmente) y consulte sus propiedades. Ejemplo:

```bash
docker pull adanbeltran/ecored-companies:1.0
docker image inspect adanbeltran/ecored-companies:1.0 --format "{{.Os}}/{{.Architecture}}"
```

Repita la comprobación con las otras dos imágenes. Si las publicadas en OCIR difieren de las de Docker Hub, compruebe las referencias efectivamente publicadas en OCIR (use la autenticación de registro del taller anterior cuando sea necesario).

| Resultado | Selección en OKE |
|---|---|
| `linux/amd64` en las tres imágenes | Shape x86-64 AMD o Intel. |
| `linux/arm64` en las tres imágenes | Shape Ampere compatible. |
| Imágenes con ambas plataformas | Seleccione una plataforma común y verifique que las tres la soportan. |

El taller de referencia del semestre anterior descargaba las imágenes con `--platform linux/amd64`. Por ello, la opción principal de esta guía será un shape AMD x86-64; **no seleccione Ampere A1 si alguna imagen solo admite AMD64**. Si no existe una plataforma común, deténgase y revise las imágenes antes de crear el clúster.

**Conexión con 1.1:** se reutiliza el mismo artefacto Docker, sin ejecutar `docker build`.

<a id="2-3"></a>

### 2.3. Abrir OKE y seleccionar Quick Create

1. En OCI confirme la región y el compartimento de 2.1.
2. Abra el menú principal → **Developer Services → Kubernetes Clusters (OKE)**. Si la consola agrupa OKE en otra categoría, busque `Kubernetes Clusters`.
3. Verifique que no existe un clúster de este grupo para el laboratorio. No cree duplicados ni elimine recursos de otros grupos.
4. Seleccione **Create Cluster**.
5. Seleccione **Quick Create**.
6. Pulse **Submit**, **Launch Workflow** o el botón equivalente que abre el asistente.

Quick Create construye el clúster y los recursos de red nuevos asociados. No seleccione Custom Create: requeriría definir previamente una red compatible con todos los requisitos de OKE.

Referencia visual del procedimiento en la [guía oficial de Oracle sobre Quick Create y Worker Nodes](https://docs.oracle.com/en/learn/oci-oke-cluster-worker-nodes/index.html). Las imágenes de esa guía pueden mostrar nombres o cantidades diferentes; utilice los valores de 2.4.

<a id="2-4"></a>

### 2.4. Configurar el clúster y los dos Worker Nodes

En el formulario de creación complete los siguientes campos:

| Campo en OCI | Valor del laboratorio | Motivo |
|---|---|---|
| Name | `ecored-oke` | Identificar el clúster. |
| Compartment | El compartimento de trabajo; por ejemplo `ecored-dev`. | Mantener los recursos organizados. |
| Kubernetes version | Una versión soportada que ofrezca la consola; registre la elegida. | Compatibilidad de la plataforma. |
| Kubernetes API Endpoint | `Private Endpoint` | Administrar Kubernetes desde la red privada. |
| Node type | `Managed` | Utilizar máquinas virtuales administradas. |
| Kubernetes Worker Nodes | `Private Workers` | Los nodos no necesitan IP pública. |
| Node Shape | `VM.Standard.E4.Flex`, si está disponible, o un shape AMD/Intel compatible. | Ejecutar imágenes AMD64 verificadas en 2.2. |
| OCPU | 2 por nodo | Capacidad inicial del laboratorio. |
| Memory | 8 GB por nodo | Capacidad inicial del laboratorio. |
| Image | Imagen de Worker Node OKE recomendada, compatible con el shape y la versión de Kubernetes. | Evitar instalaciones manuales. |
| Node count | **2** | Disponer de dos servidores. |
| Advanced options | Conservar valores predeterminados. | Reducir configuración inicial. |

Si en 2.2 verificó que todas las imágenes soportan únicamente ARM64, seleccione un shape Ampere compatible en lugar de AMD. Si el shape de ejemplo no está disponible por capacidad o cuota, elija otro shape compatible y confirme su costo. No seleccione Virtual Nodes: se necesitan máquinas virtuales administradas para los experimentos de mantenimiento del Taller 3.

Para cambiar el shape, seleccione **Change shape**, elija la serie compatible, el shape flexible y ajuste OCPU y memoria. Pulse **Select shape**. En **Image**, conserve o seleccione una imagen OKE de la misma versión del clúster. Configure `Node count` en 2.

Pulse **Next** y revise el resumen. Si aparece **Create a Basic cluster**, selecciónelo siempre que no se hayan elegido características que requieran Enhanced Cluster. No habilite características adicionales de pago para este taller.

Antes de crear: compruebe cuota, capacidad y presupuesto para dos nodos y sus volúmenes. Basic Cluster no significa que las máquinas virtuales o la red sean gratuitas. Consulte [precios de OCI](https://www.oracle.com/cloud/pricing/).

**Conexión con 1.2:** los dos Worker Nodes serán el grupo de máquinas del primer Node Pool. Quick Create suele asignar al pool el nombre `pool1`; conserve su nombre real (no es obligatorio que se llame `ecored-pool`).

<a id="2-5"></a>

### 2.5. Crear el clúster y comprobar su estado

1. Pulse **Create cluster** en la pantalla de revisión. No seleccione Save as stack.
2. Examine el resumen de recursos que OCI está creando.
3. Pulse **Close** para regresar a la lista de clústeres.
4. Abra `ecored-oke` y consulte el estado del clúster.
5. Revise **Work Requests** si aparece `Creating`, `Failed` o si la creación no avanza.
6. Cuando aparezca `Active`, abra **Node Pools** y seleccione el pool creado.
7. Compruebe que el tamaño del pool es 2 y que ambos nodos se han aprovisionado.

El clúster puede estar `Active` antes de que termine el aprovisionamiento del Node Pool. No cree otro clúster por esta diferencia de estados.

| Elemento | Estado esperado |
|---|---|
| Cluster `ecored-oke` | Active |
| Node Pool (nombre generado) | Active |
| Worker Nodes | Dos aprovisionados y activos |

Si se presenta un error de cuota, capacidad o autorización, consulte el Work Request y solicite al docente o administrador la corrección. No cambie a ARM64 sin repetir 2.2.

**Evidencia 1:** pantalla de detalles del clúster y pantalla del Node Pool con los dos nodos. Oculte identificadores que no sean necesarios para la evidencia.

<a id="2-6"></a>

### 2.6. Identificar las dos máquinas virtuales

1. Abra **Compute → Instances**.
2. Seleccione el mismo compartimento y región.
3. Identifique las dos instancias asociadas con el Node Pool. Si hay otras máquinas, compare los nombres con la lista de nodos del pool.
4. Abra cada instancia y consulte **Instance information**, el shape, OCPU, memoria y su IP privada.
5. Verifique que no tengan IP pública y que ambas estén en estado `Running`.

No cree instancias Compute manualmente, no instale Docker ni intente acceder por SSH: OKE ya aprovisionó los componentes necesarios para ejecutar Kubernetes.

**Evidencia 2:** listado de las dos máquinas y sus características. Relacione cada instancia con el concepto Worker Node de 1.2.

<a id="2-7"></a>

### 2.7. Identificar la VCN y las subredes

1. Regrese a los detalles del clúster y localice la VCN asociada.
2. Abra **Networking → Virtual Cloud Networks** y seleccione esa VCN (puede tener un nombre generado por OKE).
3. En **Subnets**, identifique la subred privada del endpoint de Kubernetes, la subred privada de Worker Nodes y la subred pública para Load Balancers. Si existe una subred adicional para Pods, regístrela.
4. Abra cada subred. Registre el nombre, el CIDR, el tipo de acceso y el OCID. Para copiar un OCID, utilice la opción **Copy** en los detalles del recurso.
5. En **NAT Gateways**, abra el gateway creado por OKE y consulte su IP pública. Regístrela: se utilizará al autorizar el acceso a bases de datos externas en el siguiente taller, cuando la ruta efectiva de salida utilice ese NAT.
6. En **Service Gateways**, verifique el gateway asociado a la VCN.
7. Abra **Route Tables**. En la tabla asociada a los Worker Nodes identifique la salida hacia NAT Gateway y hacia Service Gateway, cuando corresponda.
8. Abra **Security Lists** y consulte las listas asociadas a las subredes; no elimine las reglas que OKE creó automáticamente.

| Información | Valor obtenido en su entorno |
|---|---|
| VCN / CIDR | |
| Subred del API / CIDR / OCID | |
| Subred Worker Nodes / CIDR / OCID | |
| Subred pública LB / CIDR / OCID | |
| Red o subred de Pods (si aplica) | |
| NAT Gateway / IP pública | |
| Service Gateway | |

En este taller no se crearán los tres Load Balancers ni OCI API Gateway. En el Taller 2 se preparará la subred y las reglas necesarias para los balanceadores privados; no se asumirá que Quick Create ya creó una subred dedicada para ellos.

**Evidencia 3:** VCN, subredes y NAT Gateway identificados. Compare la red real con la figura de 1.3.

<a id="2-8"></a>

### 2.8. Conectar Cloud Shell a la red privada

El endpoint privado de Kubernetes no es accesible directamente desde Internet. Utilizaremos Cloud Shell conectado a la VCN de OKE.

1. En la consola, confirme la región del clúster. Abra **Cloud Shell** desde el icono de terminal superior (si el tenancy home region es otra región, use el lanzamiento regional cuando esté habilitado).
2. En la barra de Cloud Shell, abra el menú **Network**.
3. Seleccione **Ephemeral Private Network Setup** (o cree una definición privada desde **Private Network Definition List** si desea reutilizarla en los siguientes talleres).
4. Seleccione el compartimento, la VCN creada para `ecored-oke` y la subred privada de Worker Nodes identificada en 2.7. Compruebe que dispone de una IP libre y que el administrador autoriza esta asociación.
5. Si el formulario solicita un NSG, seleccione únicamente uno autorizado para la conexión administrativa; no es necesario agregar uno si las reglas de la subred ya permiten la comunicación.
6. Confirme la creación/conexión. Si utiliza una definición persistente, marque **Use as active network**.
7. En el menú Network compruebe que Cloud Shell está conectado a la red privada seleccionada.

Oracle muestra la interfaz en [Cloud Shell Networking](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/cloudshellintro_topic-Cloud_Shell_Networking.htm).

Si el acceso a la red privada no está habilitado, el administrador debe autorizarlo antes de continuar. Si la red está conectada pero kubectl presenta un timeout en 2.9, compruebe que las reglas stateful permiten TCP 6443 desde la IP privada de Cloud Shell (o el CIDR de su subred administrativa autorizada) hacia la subred del endpoint y las salidas necesarias; no abra TCP 6443 a `0.0.0.0/0`. Conserve las reglas de comunicación que OKE generó. Si se utiliza una red efímera, repita esta conexión al abrir una nueva sesión.

<a id="2-9"></a>

### 2.9. Configurar kubectl y acceder al clúster

1. En la consola abra **Kubernetes Clusters (OKE) → ecored-oke**.
2. Copie el OCID del clúster desde los detalles.
3. Seleccione **Access Cluster** (puede aparecer en el menú Actions). Consulte la opción de acceso desde Cloud Shell o el comando de acceso privado que OCI genera.
4. Regrese a Cloud Shell conectado a la VCN. Ejecute el siguiente comando sustituyendo solamente el OCID por el suyo. Si usa otra región, cambie también `sa-saopaulo-1`.

```bash
mkdir -p "$HOME/.kube"
oci ce cluster create-kubeconfig \
  --cluster-id OCID_DEL_CLUSTER \
  --file "$HOME/.kube/ecored-oke.config" \
  --region sa-saopaulo-1 \
  --token-version 2.0.0 \
  --kube-endpoint PRIVATE_ENDPOINT
```

Se utiliza un archivo exclusivo del laboratorio para no cambiar la configuración de otros clústeres. Ejecute:

```bash
chmod 600 "$HOME/.kube/ecored-oke.config"
export KUBECONFIG="$HOME/.kube/ecored-oke.config"
kubectl config current-context
kubectl version
kubectl cluster-info
```

`KUBECONFIG` indica qué archivo de conexión utilizará kubectl. En una sesión nueva, repita el `export` y la conexión privada de 2.8. El comando de OCI crea la configuración de acceso; no crea otro clúster.

Resultado esperado: `kubectl version` muestra la versión del cliente y del servidor; `kubectl cluster-info` muestra el endpoint del plano de control. No publique el archivo kubeconfig como evidencia.

Si aparece `Unauthorized` o `Forbidden`, revise la identidad de OCI, permisos IAM y autorización de Kubernetes con el docente. Si aparece `i/o timeout`, revise 2.8 y la región antes de modificar el clúster.

<a id="2-10"></a>

### 2.10. Verificar nodos y recursos del sistema

Ejecute:

```bash
kubectl get nodes
```

Ejemplo ilustrativo (los nombres y versiones reales serán diferentes):

```text
NAME              STATUS   ROLES    AGE   VERSION
oke-worker-01     Ready    <none>   ...   v1.x.y
oke-worker-02     Ready    <none>   ...   v1.x.y
```

Los dos nodos deben estar `Ready`. El valor `<none>` en ROLES es normal para nodos sin una etiqueta explícita de rol; no indica un error.

```bash
kubectl get nodes -o wide
kubectl get nodes -L kubernetes.io/arch,kubernetes.io/hostname
kubectl get pods -n kube-system -o wide
```

Identifique la columna `INTERNAL-IP`, la arquitectura de CPU y los Pods internos que ejecuta Kubernetes. Los nombres de los Pods del sistema dependen del tipo de clúster y su configuración. No los elimine ni modifique.

Si un nodo aún no aparece `Ready`, revise primero el Node Pool y sus Work Requests, después consulte:

```bash
kubectl describe node NOMBRE_REAL_DEL_NODO
kubectl get events -A --sort-by=.lastTimestamp | tail -n 25
```

**Evidencia 4:** resultado real de `kubectl get nodes -o wide` y consulta de arquitectura; debe coincidir con 2.2.

<a id="2-11"></a>

### 2.11. Crear el namespace ecored

Cree el espacio de trabajo de la aplicación. Se utiliza un comando que puede repetirse sin generar el error `AlreadyExists`:

```bash
kubectl create namespace ecored --dry-run=client -o yaml | kubectl apply -f -
```

Compruebe:

```bash
kubectl get namespaces
kubectl get namespace ecored
```

Configure `ecored` como namespace predeterminado del contexto actual:

```bash
kubectl config set-context --current --namespace=ecored
kubectl config view --minify -o jsonpath='{.contexts[0].context.namespace}'; echo
kubectl get pods -n ecored
```

Resultado esperado:

```text
ecored
No resources found in ecored namespace.
```

Esta salida es correcta porque EcoRed aún no se ha desplegado. `kube-system` seguirá conteniendo Pods del propio clúster.

**Evidencia 5:** namespace `ecored` en estado `Active` y sin Pods de la aplicación.

<a id="2-12"></a>

### 2.12. Comprobar la ejecución de Pods (prueba temporal)

Esta prueba breve verifica que ambos nodos pueden ejecutar contenedores y que la red permite obtener una imagen pública. No despliega EcoRed ni crea balanceadores o bases de datos. Se utiliza `busybox:1.36` como imagen de prueba multiarquitectura.

En Cloud Shell cree la carpeta que conservará para los tres talleres:

```bash
mkdir -p "$HOME/ecored-kubernetes"
cd "$HOME/ecored-kubernetes"
```

Cree un archivo llamado `prueba-nodos.yaml`:

```bash
nano prueba-nodos.yaml
```

Pegue el contenido:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prueba-nodos
  namespace: ecored
spec:
  replicas: 2
  selector:
    matchLabels:
      app: prueba-nodos
  template:
    metadata:
      labels:
        app: prueba-nodos
    spec:
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: kubernetes.io/hostname
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: prueba-nodos
      containers:
        - name: prueba
          image: busybox:1.36
          command: ["sh", "-c", "sleep 3600"]
          resources:
            requests:
              cpu: "10m"
              memory: "16Mi"
```

En nano guarde con `Ctrl+O`, Enter y salga con `Ctrl+X`. Ejecute:

```bash
kubectl apply -f prueba-nodos.yaml
kubectl rollout status deployment/prueba-nodos -n ecored --timeout=3m
kubectl get pods -n ecored -l app=prueba-nodos -o wide
```

Resultado esperado: dos Pods `Running` y `1/1` (uno en cada nodo, cuando ambos son aptos para programar Pods). Compare la columna `NODE` con la lista de 2.10. La restricción de distribución se introduce aquí únicamente para comprobar los dos servidores; en el Taller 2 se explicará y adaptará para la aplicación.

Compruebe que un contenedor puede resolver el DNS interno de Kubernetes:

```bash
kubectl exec -n ecored deployment/prueba-nodos -- nslookup kubernetes.default.svc.cluster.local
```

Si el Pod está en `ImagePullBackOff`, revise la salida a Internet desde los nodos, los límites de Docker Hub y la imagen. Si está `Pending`, revise los eventos de los Pods y la disponibilidad de los nodos. No continúe al Taller 2 sin confirmar que ambos nodos funcionan.

**Evidencia 6:** los dos Pods de prueba en nodos distintos y la resolución DNS. Después, elimine únicamente este Deployment temporal:

```bash
kubectl delete -f prueba-nodos.yaml
kubectl get pods -n ecored
```

Si todavía aparece `Terminating`, vuelva a consultar hasta que desaparezca. No borre el namespace ni el clúster.

<a id="2-13"></a>

### 2.13. Guardar la información para el Taller 2

Desde la carpeta `~/ecored-kubernetes`, cree un archivo de inventario para no repetir las consultas realizadas:

```bash
cd "$HOME/ecored-kubernetes"
nano infraestructura-oke.md
```

Copie la plantilla y complete con los valores reales de las secciones anteriores:

```markdown
# Infraestructura EcoRed OKE

- Región:
- Compartimento y OCID:
- Clúster y OCID:
- Versión de Kubernetes:
- Nombre del Node Pool:
- Shape y arquitectura CPU:
- Worker Node 1 e IP privada:
- Worker Node 2 e IP privada:
- VCN, CIDR y OCID:
- Subred endpoint, CIDR y OCID:
- Subred Worker Nodes, CIDR y OCID:
- Subred pública Load Balancers, CIDR y OCID:
- Subred o red de Pods (si aplica):
- NAT Gateway e IP pública:
- Imagen OCIR Companies (ruta completa y tag):
- Imagen OCIR Materials (ruta completa y tag):
- Imagen OCIR Frontend (ruta completa y tag):
- Namespace: ecored
- Kubeconfig: ~/.kube/ecored-oke.config
```

Conserve el archivo de inventario localmente. No agregue contraseñas, Auth Tokens, claves privadas, URI de MongoDB con credenciales ni descriptores de conexión que contengan secretos. Si presenta evidencias en un repositorio público, oculte los OCID y detalles internos que no sean necesarios.

<a id="2-14"></a>

### 2.14. Verificación final y diagnóstico

Ejecute desde Cloud Shell:

```bash
export KUBECONFIG="$HOME/.kube/ecored-oke.config"
kubectl get nodes -o wide
kubectl get namespace ecored
kubectl get deployment,pod,service -n ecored
```

| Comprobación | Resultado esperado |
|---|---|
| Clúster OCI | Active |
| Worker Nodes | Exactamente dos `Ready`. |
| Namespace | `ecored` Active. |
| Pods de EcoRed | Ninguno todavía. |
| Prueba temporal | Eliminada. |
| Load Balancers y OCI API Gateway | No creados por este taller. |
| Inventario | Guardado y completo. |

| Problema | Qué revisar |
|---|---|
| No aparece el clúster | Región, compartimento y Work Requests de OCI. |
| Un nodo no inicia | Estado del Node Pool, cuota, capacidad del shape e imagen OKE. |
| `kubectl: command not found` | Verifique que ejecuta en OCI Cloud Shell. |
| `i/o timeout` | Red privada activa, endpoint privado, región y TCP 6443. |
| `Unauthorized` / `Forbidden` | Permisos IAM y autorización de Kubernetes. |
| Arquitectura incorrecta | Compatibilidad de las imágenes con el shape, antes del despliegue. |
| `Pending` o `ImagePullBackOff` | Eventos, capacidad de nodos y conectividad hacia el registro. |

**Conserve el clúster y el Node Pool** para el Taller 2 únicamente si el docente autoriza mantener los recursos facturables. Si se requiere liberar recursos por presupuesto, coordine el cierre y la recreación con el docente; no elimine la VCN, bases de datos o repositorios de otros talleres. No se garantiza conservar las IP u OCID si se recrean los recursos.

[↑ Volver a la tabla de contenido](#inicio)

---

<a id="entregable"></a>

## Entregable y criterios de aceptación

Entregue un PDF breve con las seis evidencias solicitadas en la Fase 2 y una explicación de una o dos frases por evidencia. Adjunte una copia del inventario sin información confidencial y el manifiesto `prueba-nodos.yaml`.

- [ ] Clúster `ecored-oke` Active y Node Pool de tamaño 2.
- [ ] Dos instancias Compute correspondientes a Worker Nodes privados.
- [ ] Red OKE identificada, incluyendo subredes y NAT Gateway.
- [ ] `kubectl get nodes -o wide` muestra dos nodos `Ready` y la arquitectura correcta.
- [ ] Namespace `ecored` creado.
- [ ] Prueba temporal en ambos nodos y posteriormente eliminada.
- [ ] Inventario preparado para el Taller 2.

**Pregunta de cierre:** ¿qué relación existe entre una instancia Compute, un Worker Node, un Pod y un contenedor? Explique por qué se necesitan dos nodos para los experimentos del Taller 3.

<a id="referencias"></a>

## Referencias

- Oracle. [Using the Console to create a Cluster with Default Settings in the Quick Create workflow](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengcreatingclusterusingoke_topic-Using_the_Console_to_create_a_Quick_Cluster_with_Default_Settings.htm).
- Oracle. [Set up Oracle Cloud Infrastructure Container Engine for Kubernetes with Three Worker Nodes](https://docs.oracle.com/en/learn/oci-oke-cluster-worker-nodes/index.html). Referencia de interfaz; este laboratorio utiliza dos nodos.
- Oracle. [Cloud Shell Networking](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/cloudshellintro_topic-Cloud_Shell_Networking.htm).
- Oracle. [Setting Up Cluster Access](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengdownloadkubeconfigfile.htm).
- Kubernetes. [Cluster Architecture](https://kubernetes.io/docs/concepts/architecture/).
- Kubernetes. [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).
- Kubernetes. [Pod Topology Spread Constraints](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/).
- EcoRed. [Taller de despliegue desde Docker Hub y OCI Container Instances](https://github.com/adanbeltran/Labs/blob/main/Despliegue%20de%20Microservicios%20Cloud%20Native%20desde%20Docker%20Hub%20Entorno%20Local%20y%20OCI/Lab-Despliegue%20de%20Microservicios%20Cloud%20Native%20desde%20Docker%20Hub%20Entorno%20Local%20y%20Oracle%20Cloud%20Infrastructure%20%28OCI%29.md).
- EcoRed. [Taller 06 de referencia del semestre anterior: Microservicios OKE y API Gateway](https://github.com/adanbeltran/ecored-oci-cloud-native/blob/main/talleres-ecored-cloud-native/taller-06/06-EcoRed-Microservicios-OKE-API-Gateway.md). Se emplea como antecedente técnico, no como prerrequisito completado por el grupo actual.

Las figuras son esquemas didácticos originales; las salidas de ejemplo no corresponden a una ejecución real. Los campos de OCI pueden variar según región, versión y permisos. La guía se ha contrastado con documentación oficial, pero debe verificarse en la tenancy académica antes de su uso, especialmente la disponibilidad de shapes y el acceso privado desde Cloud Shell.

[↑ Volver a la tabla de contenido](#inicio)
