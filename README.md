# TP DEVOPS 

## 1 Namespaces 
Nous allons créer 2 namespaces en fonction de la partie 2. Nous allons faire un namespace pour devops-303 et un pour hello-world

Ce que les deux namespaces vont contenir :

devops-303 :

nginx et toute la configuration des ports

helloworld :

redirection tcp et l'affichage du hello world 

Configuration des namespaces avec leurs quotas
devops303
```apiVersion: v1
kind: ResourceQuota
metadata:
  name: quota-devops303
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

hello-world :
```apiVersion: v1
kind: ResourceQuota
metadata:
  name: hello-world
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "3"
    limits.memory: 3Gi

```
## 2 Mise en place du systeme devops303 + redis affichage de hello world 

Creation de l'ingress avec un pod de nginx :

```bash
kubectl apply -f ingress-nginx.yaml
```
Verification que tout s'est bien créé 

```bash 
 kubectl get ingress
NAME          CLASS    HOSTS                  ADDRESS   PORTS   AGE
hello-world   <none>   hello-world.baptiste             80      25m
```

```bash
kubectl describe -f ingress-nginx.yaml
Name:             hello-world
Namespace:        default
Address:
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host                  Path  Backends
  ----                  ----  --------
  hello-world.baptiste
                        /   hello-world:80 (172.17.0.3:80,172.17.0.4:80,172.17.0.5:80)
Annotations:            <none>
Events:                 <none>
```

A partir de ce moment il nous faut le nom du pod complet pour faire un forward de port 
```bash
 kubectl get pod
NAME                           READY   STATUS    RESTARTS   AGE
hello-world-7dcdb86496-ggw9n   1/1     Running   0          44m
```
Une fois le nom complet obtenu via la commande précédente on fait :
```bash
 kubectl port-forward pod/hello-world-7dcdb86496-v7hs7 8080:80
```
Notre forward de port ce lance 
Puis on va sur notre navigateur favori et on tape localhost:8080
Et on va obtenir le hello-world 

## 2 ConfigMap 

Pour faire la config map il nous faut la commande pour crée le configamp :

```bash 
kubectl create configmap configmap.yaml
```
fichier configmap.yaml 

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: configmap-devops303
  namespace: devops303
data:
  REDIS_HOST: "service-redis-devops303"
  REDIS_PORT: "6379"
  ```
Pour verifier si le configmap c'est bien créé nous regardons via la commande :

```
kubectl get configmap
```

Cela nous donne 

```bash 
kubectl get configmap
NAME               DATA   AGE
configmap.yaml     0      12s
```

