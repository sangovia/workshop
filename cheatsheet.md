![Alt text](Kubernetes-logo.png)

# Fundamentos de Kubernetes & Openshift para developers


## Preliminar

### Preparacion de entorno


<p>

```bash
crc stop
```
```bash
crc start --disk-size 250
```

Al terminar el arranque, nos conectamos al cluster:

```bash
oc login -u kubeadmin https://api.crc.testing:6443
```

Abrimos la consola de CRC, entrando en el sigiente enlace:

<a href="https://console-openshift-console.apps-crc.testing" target="_blank">Link to web console</a>

Nos logueamos con el usuario ***kubeadmin***, y el ***password*** que encontraremos en los logs de arranque.



A continuación lanzamos el siguiente comando para evitar problemas de permisos con usuarios en contenedores:

```bash
oc adm policy add-scc-to-user anyuid -z default 
```
</p>


## Introducción

### Paseo por consola 

<p>
Comprobamos la consola WEB de un proyecto
</p>


## PODs

### Creamos un nuevo POD de forma imperativa

<p>

```bash
oc run first-app-pod --image public.ecr.aws/nginx/nginx:alpine-slim
```

</p>


### Observamos el POD

<p>


```bash
oc get pods 
```
```bash
oc get pod first-app-pod -o yaml 
```
```bash
oc get pods first-app-pod -o wide 
```
```bash
oc describe pod first-app-pod
```
</p>

### Borramos el POD

<p>

```bash
oc delete pod first-app-pod
```

</p>


### Creamos un nuevo POD de forma declarativa

<p>

```bash
cat manifest/pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    type: frontend
spec:
  containers:
  - name: nginx-container
    image: public.ecr.aws/nginx/nginx:alpine-slim
```
```bash
oc create -f manifest/pod.yaml
```
```bash
oc get pods 
```
</p>

### Creamos nuevo POD multicontainer de forma declarativa

<p>

```bash
cat manifest/pod-multi-container.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-multi-app-pod
  labels:
    app: my-multi-app
    type:  frontend
spec:
  containers:
    - name: nginx-container
      image: public.ecr.aws/nginx/nginx:alpine-slim
    - name: ubuntu
      image: ubuntu:latest
      command: [ "/bin/bash", "-c", "--" ]
      args: [ "while true; do sleep 30; done;" ]
```

```bash
oc create -f manifest/pod-multi-container.yaml
```
</p>


### Observamos ambos PODs

<p>

```bash
oc get pods 
```
```bash
oc get pod my-app-pod -o wide 
```
```bash
oc get pod my-multi-app-pod -o yaml 
```
</p>


## Deployments

### Podemos crear un nuevo deployment de forma imperativa

<p>

```bash
oc create deployment my-imperative-dep --image=public.ecr.aws/nginx/nginx:alpine-slim --replicas=3
```
</p>


### Observamos el deployment y lo borramos 

<p>

```bash
oc get deployments 
```

```bash
oc get pods
```
```bash
oc delete deployment my-imperative-dep
```
```bash
oc get pods
```
</p>


### Creamos un nuevo Deployment de forma declarativa

<p>

```bash
cat manifest/deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
  labels:
    app: my-app
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx-container
        image: public.ecr.aws/nginx/nginx:alpine-slim
        ports:
          - containerPort: 80
        env:
         - name: VERSION
           value: "DEPLOYMENT 1"
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 2
```

```bash
oc apply -f manifest/deployment.yaml
```

</p>


### Observamos el nuevo deployment

<p>

```bash
oc get deployments
```
```bash
oc get replicaset
```
```bash
oc get pods
```

</p>

### Observamos el rollout y actualizamos el deployment

<p>

```bash
oc rollout status deployment/my-app-deployment
```
```bash
oc rollout history deployment/my-app-deployment
```
```bash
cat manifest/deployment-update.yaml
```
```yaml
kind: Deployment
metadata:
  name: my-app-deployment
  labels:
    app: my-app
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: nginx-container
          image: public.ecr.aws/nginx/nginx:alpine-slim
          ports:
            - containerPort: 80
          env:
            - name: VERSION
              value: "DEPLOYMENT 2"
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 2
 ```

```bash
oc apply -f manifest/deployment-update.yaml
```
```bash
oc rollout status deployment/my-app-deployment
```
```bash
oc rollout history deployment/my-app-deployment
```
```bash
oc get deployment my-app-deployment -o yaml
```
</p>

### Hacemos rollback de deployment

<p>

```bash
oc rollout undo deployment/my-app-deployment
```
```bash
oc rollout status deployment/my-app-deployment
```
```bash
oc rollout history deployment/my-app-deployment
```
```bash
oc get deployment my-app-deployment -o yaml
```

</p>


## Namespaces
### Creamos un nuevo namespace de forma imperativa

<p>

```bash
oc create namespace my-imp-namespace
```
</p>


### Creamos un nuevo namespace de forma declarativa

<p>

```bash
cat manifest/namespace.yaml
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

```bash
oc apply -f manifest/namespace.yaml 
```
</p>


### Observamos los namespaces

<p>

```bash
oc get namespaces
```
```bash
oc get pods --all-namespaces
```
```bash
oc get deployments --all-namespaces
```
</p>


### Creamos un nuevo POD en el nuevo namespace 

<p>

```bash
oc run namespaced-pod --image public.ecr.aws/nginx/nginx:alpine-slim -n my-namespace
```

</p>


### Observamos el nuevo pod  en el nuevo namespace

<p>

```bash
oc get pods --namespace=my-namespace
```

</p>


## Job, Cronjobs, Daemonsets y Statefulset

### Los observamos en la consola de ICHP

<p>
Comprobamos la consola WEB de un proyecto
</p>



## ResourceQuota

### Observamos en la consola de ICHP los ResourceQuotas de recursos y objetos en projecto ICHP real

<p>
Comprobamos la consola WEB de un proyecto
</p>



## ConfigMaps
### Podemos crear un nuevo ConfigMap de forma imperativa

<p>

```bash
oc create configmap my-imp-app-config1 --from-literal=APP_COLOR=blue --from-literal=APP_MODE=dev
```
```bash
oc get configmaps
```
```bash
oc get configmap my-imp-app-config1 -o yaml
```

```bash
oc create configmap my-imp-app-config2 --from-file=properties/config.properties  
```
```bash
oc get configmaps
```
```bash
oc get configmap my-imp-app-config2 -o yaml
```

</p>


### Creamos un nuevo ConfigMap de forma declarativa

<p>

```bash
cat manifest/config-map.yaml
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
data:
  APP_COLOR: blue
  APP_MODE: dev
```

```bash
oc apply -f manifest/config-map.yaml 
```
</p>


### Observamos el ConfigMap

<p>

```bash
oc get configmaps
```
```bash
oc get configmap my-app-config -o yaml
```
```bash
oc describe configmap my-app-config
```
</p>


## Secrets
### Podemos crear un nuevo Secret de forma imperativa

<p>

```bash
oc create secret generic my-app-secret1 --from-literal=DB_USER=user --from-literal=DB_PASSWORD=mypassword
```
```bash
oc get secrets
```
```bash
oc get secrets my-app-secret1 -o yaml
```

```bash
oc create secret generic my-app-secret2 --from-file=properties/secrets.properties 
```
```bash
oc get secrets
```
```bash
oc get secrets my-app-secret2 -o yaml
```


</p>


### Creamos un nuevo Secret de forma declarativa

<p>

```bash
cat manifest/secret.yaml
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
data:
  DB_PASSWORD: bXlwYXNzd29yZA==
  DB_USER: dXNlcg==
```

```bash
oc apply -f manifest/secret.yaml 
```
</p>


### Lo observamos

<p>

```bash
oc get secrets
```
```bash
oc get secrets my-app-secret -o yaml
```
```bash
oc describe secrets my-app-secret
```
</p>


## Service Accounts
### Creamos un service account

<p>

```bash
oc create serviceaccount my-service-account
```
</p>



### Observamos el service account y el secreto asociado donde se almacena el token

<p>

```bash
oc get serviceaccount my-service-account
```
```bash
oc describe serviceaccount my-service-account
```
```bash
oc get secrets
```

</p>


### Observamos en la consola de ICHP los service account y secret prom-tenancy-access y prom-tenancy-access-token

<p>
Comprobamos la consola WEB de un proyecto
</p>


## Resource requirements.
### Mostramos los resource requirements en un POD de ICHP 


<p>
Comprobamos la consola WEB de un proyecto
</p>




## Taints and Tolerations, Node affinity and PodAntiaffinity

### Observamos en la consola de ICHP en la defición de POD de un Merak real

<p>
Comprobamos la consola WEB de un proyecto
</p>


## Readiness & Liveness probes

### Observamos en la consola de ICHP en la defición de POD de un Merak real

<p>
Comprobamos la consola WEB de un proyecto
</p>



## Events
### Podemos observar los eventos en la consola de ICHP de un Merak real

<p>
Comprobamos la consola WEB de un proyecto
</p>



### Podemos ver los eventos de un determinado objeto con get events o describe

<p>

```bash
oc get events 
```
```bash
oc describe pod my-app-pod 
```
```bash
oc get events --namespace my-namespace
```
```bash
oc get events --sort-by=.metadata.creationTimestamp
```
```bash
oc get event --field-selector type=Warning
```

</p>


## Container Logging
### Podemos observar los logs en la consola de ICHP en la defición de POD de un Merak real

<p>
Comprobamos la consola WEB de un proyecto
</p>



### Podemos ver los logs de un determinado POD mono-container con get events o describe

<p>

```bash
oc logs --follow my-app-pod
```
</p>


### Podemos ver los logs de un determinado POD multi-container especificando el container

<p>

```bash
oc logs --follow my-multi-app-pod nginx-container
```
</p>


## Services
### Observamos los servicios existentes

<p>

```bash
oc get services
```
</p>


### Creamos un nuevo clusterIP y exponemos nuestro desployment en él

<p>

```bash
oc expose deployment my-app-deployment --name=my-app-svc-clusterip --port=80 --target-port=8080
```
```bash
oc get services
```
```bash
oc get service my-app-svc-clusterip -o yaml
```

</p>


### Observamos el clusterIP de nuestro Merak en la consola de Openshift

<p>
Comprobamos la consola WEB de un proyecto
</p>



### Creamos un nuevo NodePort y exponemos nuestro desployment 

<p>

```bash
cat manifest/node-port.yaml 
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-node-port-service
  labels:
    app: my-node-port-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30080
```

```bash
oc apply -f manifest/node-port.yaml 
```

```bash
oc get services
```
```bash
oc get service my-node-port-service -o yaml
```

</p>


### Creamos un nuevo Load Balancer

<p>

```bash
oc expose deployment my-app-deployment --name=my-app-svc-loadbalancer --port=80 --target-port=8080 --type=LoadBalancer 
```
```bash
oc get services
```
```bash
oc get service my-app-svc-loadbalancer -o yaml
```
</p>


## Network Policies

### Observamos la network Policy allow-touchpoint-mesh-traffic en consola de Openshift

<p>
Comprobamos la consola WEB de un proyecto
</p>


## Access Control & RBAC Authorization

### Observamos el rol prom-tenancy-access en consola de Openshift

<p>
Comprobamos la consola WEB de un proyecto
</p>



## APIGroups

### Observamos los api groups en la consola de Openshift

<p>
Comprobamos la consola WEB de un proyecto
</p>


## Custom Resource Definitions

### Observamos los api groups en la consola de Openshift

<p>
Comprobamos la consola WEB de un proyecto
</p>


## Projects
### Creamos un nuevo Projects de forma imperativa

<p>

```bash
oc new-project workshop
```
```bash
oc project workshop
```
```bash
oc describe project workshop
```

```bash
oc get projects
```
</p>


## Routes
### Creamos una nueva Route

<p>

```bash
oc project default
```

```bash
oc expose svc my-node-port-service
```
```bash
oc get routes
```

<a href="http://my-app-svc-node-port-default.apps-crc.testing/" target="_blank">Link a pagina NGINX</a>

</p>

Nos vamos a la consola y comprobamos los objetos creados:

<a href="https://console-openshift-console.apps-crc.testing" target="_blank">Link a consola WEB</a>


