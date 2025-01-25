# Desarrollo de un Helm Chart para la Aplicación y MongoDB

Para resolver este desafío, voy a desarrollar un Helm chart que me permita gestionar el deployment de la aplicación y su base de datos MongoDB. Utilizaré Helm para optimizar el código y manejar configuraciones dinámicas. A continuación, detallo los pasos que seguiré:

---

## **1. Preparar el Entorno de Trabajo**

1. Primero, me aseguro de tener Helm instalado en mi equipo. Si no lo tengo, lo instalaré con el siguiente comando:

   ```bash
   curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
   ```

   Luego verifico que esté correctamente instalado ejecutando:

   ```bash
   helm version
   ```

2. Descargo el código de la aplicación clonado el repositorio desde GitHub:
   ```bash
   git clone https://github.com/yosoyfunes/app-template-nestjs
   cd app-template-nestjs
   ```

---

## **2. Crear el Helm Chart**

1. Genero la estructura básica de un Helm chart con este comando:

   ```bash
   helm create app-chart
   cd app-chart
   ```

2. Reviso la estructura generada. Me enfoco principalmente en:
   - `templates/`: Aquí se encuentran las plantillas de Kubernetes.
   - `values.yaml`: Aquí definiré los valores configurables del chart.

---

## **3. Crear Plantillas para los Recursos**

### 3.1 Deployment para la Aplicación

En el archivo `templates/app-deployment.yaml`, escribo la plantilla para el deployment de la aplicación:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-app
  labels:
    app: app
spec:
  replicas: {{ .Values.app.replicas }}
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
        - name: app
          image: {{ .Values.app.image }}
          ports:
            - containerPort: {{ .Values.app.containerPort }}
          resources:
            requests:
              memory: {{ .Values.app.resources.requests.memory }}
              cpu: {{ .Values.app.resources.requests.cpu }}
            limits:
              memory: {{ .Values.app.resources.limits.memory }}
              cpu: {{ .Values.app.resources.limits.cpu }}
```

### 3.2 Service para la Aplicación

Creo un archivo `templates/app-service.yaml` para el servicio de la aplicación:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-app-service
spec:
  type: NodePort
  selector:
    app: app
  ports:
    - protocol: TCP
      port: {{ .Values.app.service.port }}
      targetPort: {{ .Values.app.containerPort }}
      nodePort: {{ .Values.app.service.nodePort }}
```

### 3.3 Deployment para MongoDB

Luego, creo `templates/mongo-deployment.yaml` para el deployment de MongoDB:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-mongo
  labels:
    app: mongo
spec:
  replicas: {{ .Values.mongo.replicas }}
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: {{ .Values.mongo.image }}
          ports:
            - containerPort: {{ .Values.mongo.containerPort }}
          env:
            - name: MONGO_INITDB_DATABASE
              value: {{ .Values.mongo.initDatabase }}
          resources:
            requests:
              memory: {{ .Values.mongo.resources.requests.memory }}
              cpu: {{ .Values.mongo.resources.requests.cpu }}
            limits:
              memory: {{ .Values.mongo.resources.limits.memory }}
              cpu: {{ .Values.mongo.resources.limits.cpu }}
```

### 3.4 Service para MongoDB

Finalmente, agrego `templates/mongo-service.yaml` para el servicio de MongoDB:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-mongo-service
spec:
  type: ClusterIP
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: {{ .Values.mongo.service.port }}
      targetPort: {{ .Values.mongo.containerPort }}
```

---

## **4. Configurar `values.yaml`**

Modifico el archivo `values.yaml` para definir valores configurables:

```yaml
app:
  replicas: 1
  image: "nginx:latest" # Cambiar por la imagen de mi aplicación
  containerPort: 80
  resources:
    requests:
      memory: "128Mi"
      cpu: "250m"
    limits:
      memory: "256Mi"
      cpu: "500m"
  service:
    port: 80
    nodePort: 30000

mongo:
  replicas: 1
  image: "mongo:latest"
  containerPort: 27017
  initDatabase: "exampledb"
  resources:
    requests:
      memory: "256Mi"
      cpu: "500m"
    limits:
      memory: "512Mi"
      cpu: "1000m"
  service:
    port: 27017
```

---

## **5. Probar el Helm Chart**

1. Primero, valido el chart ejecutando:

   ```bash
   helm lint
   ```

2. Despliego el chart en mi clúster de Kubernetes:

   ```bash
   helm install app-release ./app-chart
   ```

3. Verifico los recursos creados con:

   ```bash
   kubectl get all
   ```

4. Accedo a la aplicación a través del puerto especificado en `nodePort` y me aseguro de que funcione correctamente y que se conecte a MongoDB.

---

## **6. Documentar y Entregar**

Por último, documento el proceso y detallo cómo instalar y configurar el Helm chart, incluyendo instrucciones sobre cómo personalizar el archivo `values.yaml`. Esto asegura que otros miembros del equipo puedan usarlo de manera eficiente.
