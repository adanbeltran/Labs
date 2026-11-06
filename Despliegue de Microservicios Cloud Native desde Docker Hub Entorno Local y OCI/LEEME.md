# Taller 6 — Despliegue de Microservicios Cloud Native desde Docker Hub: Entorno Local y OCI

Abrir `Taller_06_Despliegue_Microservicios_DockerHub_Local_OCI.md`.

Este taller consta de **dos fases**:

1. **Reproducción local:** imágenes del docente u otro compañero publicadas en Docker Hub, con variables `.env` propias.
2. **Despliegue en OCI:** imágenes propias previamente publicadas en Docker Hub, descarga, etiquetado, publicación en OCIR y ejecución de cuatro contenedores en OCI Container Instances.

Para los pasos Docker se ofrecen comandos con variables de PowerShell definidas al comienzo de cada fase y comandos con referencias completas compatibles con CMD. No se realiza `docker build`.

- `assets/`: capturas del taller original y diagrama propio; no son capturas de OCIR.
- `tests/`: archivos HTTP para verificar conectividad local, Firebase y el entorno OCI.

**Seguridad:** las pruebas con IP pública HTTP no garantizan autenticación web; para iniciar sesión desde un origen público configure DNS, HTTPS y dominios autorizados en Firebase. No publique secretos en el repositorio.
