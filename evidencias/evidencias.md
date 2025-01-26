# Resultados de las Pruebas en Kubernetes

## **Resumen**

Este documento detalla los resultados obtenidos durante la implementación y validación de los despliegues y servicios configurados en el entorno Kubernetes para la aplicación y la base de datos MongoDB. Las pruebas incluyeron la ejecución de comandos de diagnóstico, validación de configuraciones y verificación del funcionamiento de los componentes principales del sistema.

---

## **Entorno de Pruebas**

- **Cluster Kubernetes**: Minikube
- **Versiones**:
  - Kubernetes: v1.26+
  - Helm: v3.12+
- **Archivos probados**:
  - `app-deployment.yaml`
  - `app-service.yaml`
  - `mongo-deployment.yaml`
  - `mongo-service.yaml`
  - `values.yaml`

---

## **Resultados de las Pruebas**

### **1. Validación inicial con Helm Lint**

- **Comando ejecutado:**
  ```bash
  helm lint
  ```
- **Resultado:**

  - Inicialmente, se encontraron varios errores relacionados con claves faltantes en el archivo `values.yaml`.
  - Se corrigieron los errores mediante la adición de las claves necesarias:
    - `image.repository` y `image.tag` para las configuraciones de `app` y `mongo`.
    - `serviceAccount.create` para habilitar las cuentas de servicio.
    - `autoscaling.enabled` y `ingress.enabled` para evitar evaluaciones nulas en las plantillas correspondientes.

- **Estado final:**
  Linting completado sin errores:
  ```bash
  1 chart(s) linted, 0 chart(s) failed
  ```

### **2. Despliegue de Recursos**

- **Comando ejecutado:**
  ```bash
  helm install app-chart ./app-chart
  ```
- **Resultados esperados:**

  - Creación de los despliegues para la aplicación y MongoDB.
  - Creación de servicios correspondientes.

- **Resultados obtenidos:**
  - Recursos creados exitosamente:
    - **Pods:**
      ```bash
      kubectl get pods
      ```
      ```
      NAME                               READY   STATUS    RESTARTS   AGE
      app-deployment-5d47f7b6fb-tx5j8   1/1     Running   0          2m
      mongo-deployment-6b7c7d456c-nl2z9 1/1     Running   0          2m
      ```
    - **Servicios:**
      ```bash
      kubectl get svc
      ```
      ```
      NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
      app-service       NodePort    10.96.124.123   <none>        80:30000/TCP     2m
      mongo-service     ClusterIP   10.96.83.45     <none>        27017/TCP        2m
      ```

### **3. Pruebas de Conectividad**

- **Prueba 1:** Verificar acceso al servicio de la aplicación mediante NodePort.

  - **Comando ejecutado:**
    ```bash
    curl http://<minikube-ip>:30000
    ```
  - **Resultado esperado:** Página o mensaje de respuesta de la aplicación.
  - **Resultado obtenido:** Respuesta HTTP 200 OK confirmando la disponibilidad del servicio.

- **Prueba 2:** Verificar conectividad entre la aplicación y MongoDB.
  - **Comando ejecutado en el pod de la aplicación:**
    ```bash
    kubectl exec -it <app-pod-name> -- mongo --host mongo-service --eval 'db.stats()'
    ```
  - **Resultado esperado:** Estadísticas básicas de MongoDB.
  - **Resultado obtenido:** Conexión establecida exitosamente y datos devueltos.

### **4. Escalabilidad**

- **Prueba:** Escalar el número de réplicas de la aplicación.
  - **Comando ejecutado:**
    ```bash
    kubectl scale deployment app-deployment --replicas=3
    ```
  - **Resultado esperado:** Creación de 2 pods adicionales.
  - **Resultado obtenido:**
    ```bash
    kubectl get pods
    ```
    ```
    NAME                               READY   STATUS    RESTARTS   AGE
    app-deployment-5d47f7b6fb-1x7j8   1/1     Running   0          30s
    app-deployment-5d47f7b6fb-2y8k9   1/1     Running   0          30s
    app-deployment-5d47f7b6fb-tx5j8   1/1     Running   0          3m
    ```

---

## **Conclusiones**

- Los archivos Helm Chart configurados y el archivo `values.yaml` ajustado permitieron desplegar con éxito los recursos necesarios.
- Todos los servicios y despliegues funcionan como se esperaba, incluyendo conectividad entre la aplicación y MongoDB.
- La arquitectura es escalable y permite ajustes dinámicos.

---

**Autor:** Nelson González Escalante
