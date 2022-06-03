## Kubernetes

### Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
- [Services](#services)
  - [Verificando o cluster antes de comecar](#verificando-o-cluster-antes-de-comecar)
    - [Analisando se o cluster esta com `ClusterIP`](#analisando-se-o-cluster-esta-com-clusterip)




## Services

### Verificando o cluster antes de comecar

a) Iniciando a aula e vendo que nosso cluster esta funcionando legal:

```bash
# kubectl get nodes
NAME                    STATUS   ROLES                  AGE    VERSION
kube-worker01           Ready    <none>                 9h     v1.23.1
kube-worker2            Ready    <none>                 114s   v1.23.1
localhost.localdomain   Ready    control-plane,master   10h    v1.23.1
```
b) Vamos ver se temos o *deployment*:

```bash
# kubectl get deployments
No resources found in default namespace.
```

c) Agora analisando se temos *services* 

```bash
# kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE 
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7d9h
```

d) Analisando se temos *replica-set*:

```bash
# kubectl get replicasets
No resources found in default namespace.
```

#### Analisando se o cluster esta com `ClusterIP`

2.  Uma coisa importante que precisamos saber, quando eu criei meu service veja ele veio com o *type* setado como *ClusterIP*.

```bash
# kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE  
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   7d13h
nginx        ClusterIP   10.109.244.99   <none>        80/TCP    22m
```

- ou seja, nao preciso passar a porta *80* para ele porque ele sabe que tem acesso dentro do cluster.

Se eu der um comando *curl*:

```html
# curl 10.109.244.99 
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

3.  Vamos agora criar um *service*:

```yml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  clusterIP: 10.109.244.99
  clusterIPs:
  - 10.109.244.99
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: ClusterIP
```

- veja que eu fiz a retirada de alguns itens que sao inuteis.
- feito isso podemos testar.

```bash
# kubectl apply -f meu_primeiro_service-v2.yml 
service/nginx created
```

e agora podemos ver que ele esta criado:

```bash
# kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE  
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   7d14h
nginx        ClusterIP   10.109.244.96   <none>        80/TCP    10s
```