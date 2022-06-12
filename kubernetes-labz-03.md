## Kubernetes

### Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
  - [Deployments](#deployments)
    - [Vamos criar o manifesto de deployment](#vamos-criar-o-manifesto-de-deployment)
    - [Criando nosso deployment para o Kubernetes](#criando-nosso-deployment-para-o-kubernetes)
    - [Analisando o deployment criado](#analisando-o-deployment-criado)
    - [Criando o segundo manifesto do deployment](#criando-o-segundo-manifesto-do-deployment)
    - [Criando no segundo deployment](#criando-no-segundo-deployment)
    - [Visualizando os deployments](#visualizando-os-deployments)
    - [Descrevendo os POD's criados](#descrevendo-os-pods-criados)
    - [Pegando os PODS somente da Holanda](#pegando-os-pods-somente-da-holanda)
    - [Pegando os PODS somente da outra regiao de UK](#pegando-os-pods-somente-da-outra-regiao-de-uk)
    - [Agora pegando os LABELS](#agora-pegando-os-labels)
  - [Replicaset](#replicaset)
    - [Criando o primeiro replicaset no Kubernetes](#criando-o-primeiro-replicaset-no-kubernetes)

### Deployments
Toda vez que eu crio um deployment, sabemos que ele vai criar um replicaset, o deployment que gerencia o replicaset.

#### Vamos criar o manifesto de deployment

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: giropops
  name: primeiro-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: UK
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources:
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

#### Criando nosso deployment para o Kubernetes

`#kubectl create -f primeiro_deployment.yml`

#### Analisando o deployment criado

```bash
# kubectl get deployments.apps -n default 
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
primeiro-deployment   1/1     1            1           8s
```

#### Criando o segundo manifesto do deployment

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: giropops
  name: primeiro-deployment
  namespace: default
spec:
  
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: NL
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources:
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

#### Criando no segundo deployment

`# kubectl create -f segundo_deployment.yml`

#### Visualizando os deployments

```bash
# kubectl get deployments.apps -n default 
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
primeiro-deployment   1/1     1            1           8m36s
segundo-deployment    1/1     1            1           12s
```

#### Descrevendo os POD's criados

```bash
# kubectl describe pods -n default primeiro-deployment-57c778d58-lz4pk 
Name:         primeiro-deployment-57c778d58-lz4pk
Namespace:    default
Priority:     0
Node:         k8snode02/192.168.0.132
Start Time:   Fri, 03 Jun 2022 09:03:05 -0300
Labels:       dc=UK
              pod-template-hash=57c778d58
              run=nginx
Annotations:  <none>
Status:       Running
IP:           10.47.0.2
IPs:
  IP:           10.47.0.2
Controlled By:  ReplicaSet/primeiro-deployment-57c778d58
Containers:
  nginx2:
    Container ID:   docker://feb1b533706bbf35d70d4314ce49e277fd8262f5c416301fc80f5e09623c46ea
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2bcabc23b45489fb0885d69a06ba1d648aeda973fae7bb981bafbb884165e514
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 03 Jun 2022 09:03:08 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lzbw2 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-lzbw2:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Pulling    166m  kubelet            Pulling image "nginx"
  Normal  Pulled     166m  kubelet            Successfully pulled image "nginx" in 1.913274346s
  Normal  Created    166m  kubelet            Created container nginx2
  Normal  Started    166m  kubelet            Started container nginx2
  Normal  Scheduled  10m   default-scheduler  Successfully assigned default/primeiro-deployment-57c778d58-lz4pk to k8snode02
```

#### Pegando os PODS somente da Holanda

```bash
# kubectl get pods -n default -l dc=NL
NAME                                  READY   STATUS    RESTARTS   AGE
segundo-deployment-6b664b7fb8-7rs9b   1/1     Running   0          11m
```

#### Pegando os PODS somente da outra regiao de UK

```bash
# kubectl get pods -n default -l dc=UK
NAME                                  READY   STATUS    RESTARTS   AGE
primeiro-deployment-57c778d58-lz4pk   1/1     Running   0          21m
```

#### Agora pegando os LABELS 
Veja que ele adiciona tambem um outra coluna na saida do comando

```bash
# kubectl get pods -n default -L dc
NAME                                  READY   STATUS    RESTARTS   AGE   DC
primeiro-deployment-57c778d58-lz4pk   1/1     Running   0          22m   UK
segundo-deployment-6b664b7fb8-7rs9b   1/1     Running   0          13m   NL
```

### Replicaset

#### Criando o primeiro replicaset no Kubernetes

```yml
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: replica-set-primeiro
spec:
  replicas: 3
  template:
    metadata:
      labels:
        system: Giropops
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

