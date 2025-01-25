## Proyecto: Desarrollo de un Helm Chart para la Gestión de Deployments en Kubernetes

## Descripción

Nuestro equipo ha identificado la necesidad de optimizar la gestión de los deployments en Kubernetes. Durante este sprint, se nos asignó la tarea de desarrollar un **Helm Chart** que permita:

1. Reducir la duplicación de código en los manifiestos de Kubernetes.
2. Simplificar y estandarizar el despliegue de nuestra aplicación.
3. Gestionar de manera eficiente tanto la aplicación principal como su base de datos MongoDB.

Este proyecto busca implementar una solución escalable, reutilizable y fácil de mantener mediante el uso de Helm.

---

## Objetivo del Proyecto

El objetivo principal es crear un **Helm Chart** que:

- Despliegue la aplicación junto con los recursos asociados, como servicios y configuraciones.
- Incluya la base de datos MongoDB como parte del stack.
- Permita la configuración dinámica mediante valores definidos en un archivo `values.yaml`.

---

## Estructura del Proyecto

```
/helm-chart-project
│
├── charts/                # Subcharts adicionales (si es necesario).
├── templates/             # Manifiestos Kubernetes con placeholders para Helm.
│   ├── deployment.yaml    # Deployment de la aplicación.
│   ├── service.yaml       # Service para exponer la aplicación.
│   ├── mongo-deployment.yaml # Deployment para MongoDB.
│   └── mongo-service.yaml    # Service para MongoDB.
├── values.yaml            # Valores predeterminados para personalización.
├── Chart.yaml             # Metadatos del Helm Chart.
└── README.md              # Documentación principal.
```

---

## Archivos Clave

### 1. `Chart.yaml`

Este archivo define los metadatos básicos del Helm Chart, como el nombre, la versión y una breve descripción.

### 2. `values.yaml`

Contiene los valores configurables para el chart. Algunos ejemplos:

- Cantidad de réplicas para los deployments.
- Recursos solicitados y límites para los contenedores.
- Configuraciones específicas de MongoDB.

### 3. `templates/`

Incluye los manifiestos Kubernetes parametrizados con placeholders de Helm para adaptarse a diferentes entornos.

---

## Instrucciones de Uso

### 1. Prerrequisitos

- Kubernetes configurado (Minikube, Kind, u otro clúster).
- Helm instalado en tu máquina local.

### 2. Instalar el Helm Chart

1. Clonar este repositorio:

   ```bash
   git clone https://github.com/tuusuario/helm-chart-project.git
   cd helm-chart-project
   ```

2. Instalar el chart:
   ```bash
   helm install mi-aplicacion .
   ```

### 3. Personalizar Valores

Puedes editar el archivo `values.yaml` para ajustar configuraciones específicas antes de instalar el chart:

```yaml
replicaCount: 2
mongo:
  resources:
    requests:
      memory: "256Mi"
      cpu: "500m"
    limits:
      memory: "512Mi"
      cpu: "1"
```

### 4. Verificar el Despliegue

Ejecuta el siguiente comando para confirmar que los recursos están corriendo correctamente:

```bash
kubectl get all
```

### 5. Desinstalar el Chart

Para eliminar el despliegue:

```bash
helm uninstall mi-aplicacion
```

---

## Conclusión.

El Helm Chart desarrollado simplifica la gestión de los deployments de nuestra aplicación y reduce significativamente la duplicación de código. Con esta solución, hemos mejorado la escalabilidad y el mantenimiento de nuestros recursos en Kubernetes, garantizando una implementación más eficiente y reutilizable.
