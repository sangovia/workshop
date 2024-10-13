![Alt text](ing.jpg)

# Fundamentos de Kubernetes & Openshift para developers 
## Ejercicio Práctico

### Comandos de limpieza del cluster
<details><summary>show</summary>
<p>

```bash
oc project default
oc delete project ejercicio-practico
oc delete project my-imp-namespace
oc delete project my-namespace    
oc delete project ejercicio-practico  
oc describe pod first-app-pod
oc delete deployment my-imperative-dep
oc delete pod my-app-pod
oc delete pod my-multi-app-pod
oc delete deployment my-app-deployment
oc delete svc my-app-svc-clusterip
oc delete svc my-app-svc-loadbalancer
oc delete svc my-app-svc-node-port     
oc delete svc my-node-port-service 
oc delete sa my-service-account
oc delete routes my-app-svc-clusterip
oc delete secret my-app-secret                        
oc delete secret my-app-secret1                       
oc delete secret my-app-secret2    
oc delete configmap my-app-config 
oc delete configmap my-imp-app-config1
oc delete configmap my-imp-app-config2
oc delete route my-app-svc-node-port
```


</p>
</details>

