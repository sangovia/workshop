![Alt text](Kubernetes-logo.png)

# Fundamentos de Kubernetes & Openshift para developers

## Ejercicio Práctico

### Creamos un nuevo projecto "ejercicio_practico"
<details><summary>show</summary>
<p>

```bash
oc new-project ejercicio-practico
```
```bash
oc project ejercicio-practico
```
```bash
oc adm policy add-scc-to-user anyuid -z default 
```


</p>
</details>

### Creamos un deployment en el namespace "ejercicio-practico"
<details><summary>show</summary>
<p>

```bash
cat ejercicio/ejercicio-deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ejercicio
  labels:
    app: ejercicio-practico
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ejercicio-practico
  template:
    metadata:
      labels:
        app: ejercicio-practico
    spec:
      containers:
        - name: nginx-container
          image: public.ecr.aws/nginx/nginx:alpine-slim
          ports:
            - containerPort: 80
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 2
 ```

```bash
oc apply -f ejercicio/ejercicio-deployment.yaml
```
```bash
oc get deployments -o wide
```
```bash
oc get pods -o wide
```

```bash
oc get deployment ejercicio -o yaml 
```
```bash
oc describe deployment ejercicio
```
</p>
</details>

### Creamos clusterIP para el deployment y lo exponemos en un route
<details><summary>show</summary>
<p>

```bash
oc expose deployment ejercicio --port=80 --target-port=80
```
```bash
oc get services -o wide
```
```bash
oc expose svc ejercicio
```
```bash
oc get routes
```

Buscamos y accedemos:

</p>
</details>

Accedemos a la <a href="http://ejercicio-ejercicio-practico.apps-crc.testing/" target="_blank">URL</a>
y refrescamos el navegador

### Creamon un nuevo configmap en el namespace "ejercicio"
<details><summary>show</summary>
<p>

```bash
cat ejercicio/index-html-configmap.yml
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: index-html-configmap
data:
  index.html: |
    <html>
    <h1>Hola Mundo</h1>
    <body>
    Esto es un ejercicio practico del workshop de ING...
    </body>
    </html>
```

```bash
oc apply -f ejercicio/index-html-configmap.yml
```

</p>
</details>


### Actualizamos el deployment en el namespace "ejercicio"
<details><summary>show</summary>
<p>

```bash
oc rollout status deployment/ejercicio
```
```bash
oc rollout history deployment/ejercicio
```
```bash
oc get deployment ejercicio -o yaml
```
```bash
cat ejercicio/ejercicio-deployment-update.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ejercicio
  labels:
    app: ejercicio-practico
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ejercicio-practico
  template:
    metadata:
      labels:
        app: ejercicio-practico
    spec:
      containers:
        - name: nginx-container
          image: public.ecr.aws/nginx/nginx:alpine-slim
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nginx-index-file
              mountPath: /usr/share/nginx/html/
          volumes:
            - name: nginx-index-file
              configMap:
                name: index-html-configmap
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 2
```

```bash
oc apply -f ejercicio/ejercicio-deployment-update.yaml
```
Accedemos a la <a href="http://ejercicio-ejercicio-practico.apps-crc.testing/" target="_blank">URL</a>
y refrescamos el navegador


</p>
</details>


### Observamos el nuevo deployment
<details><summary>show</summary>
<p>

```bash
oc rollout status deployment/ejercicio
```
```bash
oc rollout history deployment/ejercicio
```
```bash
oc get deployments
```
```bash
oc get replicaset
```
```bash
oc get pods
```
```bash
oc rollout undo deployment/ejercicio
```
```bash
oc rollout status deployment/ejercicio
```
```bash
oc rollout history deployment/ejercicio
```
</p>
</details>

Accedemos a la <a href="http://ejercicio-ejercicio-practico.apps-crc.testing/" target="_blank">URL</a>
y refrescamos el navegador