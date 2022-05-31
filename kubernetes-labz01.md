## Kubernetes

### Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
- [Primeiros passos com Kubernetes](#primeiros-passos-com-kubernetes)
  - [Listando nossos nodes com `kubectl get nodes`:](#listando-nossos-nodes-com-kubectl-get-nodes)
  - [Antes de prosseguir alguns conceitos...](#antes-de-prosseguir-alguns-conceitos)
  - [Usando o comando `kubectl describe` para mais detalhes](#usando-o-comando-kubectl-describe-para-mais-detalhes)
  - [Adicionando novcs nodes ao cluster](#adicionando-novcs-nodes-ao-cluster)
  - [Ativando o auto-complete para sistemas Red Hat](#ativando-o-auto-complete-para-sistemas-red-hat)
  - [Buscando os POD's do namespace `kube-systems`](#buscando-os-pods-do-namespace-kube-systems)
    - [Concepts](#concepts)
    - [Concepts](#concepts-1)

## Primeiros passos com Kubernetes

### Listando nossos nodes com `kubectl get nodes`:


1. Iniciando a aula e vendo que nosso cluster esta funcionando legal:

```bash
# kubectl get nodes
NAME                    STATUS   ROLES                  AGE    VERSION
kube-worker01           Ready    <none>                 9h     v1.23.1
kube-worker2            Ready    <none>                 114s   v1.23.1
localhost.localdomain   Ready    control-plane,master   10h    v1.23.1
```
### Antes de prosseguir alguns conceitos... 

- no Kubernetes temos nodes *worker* e nodes *master*.
- no master vai ter o APIserver, gerencia do meu cluster.
  - se os nodes etstao saudaveis, se ele esta comunicando com o *master*.
- por default o *master* nao recebe containers, somente nos *workers*.

### Usando o comando `kubectl describe` para mais detalhes

2. Vamos agora testar o comando `kubectl describe` para ver os detalhes do master:

```bash
# kubectl describe nodes k8smaster
Name:               k8smaster
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=k8smaster
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sun, 27 Feb 2022 12:32:58 -0300
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  k8smaster
  AcquireTime:     <unset>
  RenewTime:       Sat, 05 Mar 2022 14:02:00 -0300
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Wed, 02 Mar 2022 15:19:09 -0300   Wed, 02 Mar 2022 15:19:09 -0300   WeaveIsUp                    Weave pod has set this
  MemoryPressure       False   Sat, 05 Mar 2022 13:57:52 -0300   Sun, 27 Feb 2022 12:32:54 -0300   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Sat, 05 Mar 2022 13:57:52 -0300   Sun, 27 Feb 2022 12:32:54 -0300   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Sat, 05 Mar 2022 13:57:52 -0300   Sun, 27 Feb 2022 12:32:54 -0300   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Sat, 05 Mar 2022 13:57:52 -0300   Sun, 27 Feb 2022 12:40:03 -0300   KubeletReady                 kubelet is posting ready status
Addresses:
```
### Adicionando novcs nodes ao cluster

3. Agora vamos executar um comando para adicionar novos nodes ao cluster:

```bash
# kubeadm token create --print-join-command
kubeadm join 192.168.1.114:6434 --token j7ivmo.up6haifmk55pou7c --discovery-token-ca-cert-hash sha256:3fc69efd6451dedb945ce665bdf436ac3e54e0e55774fb0654b60562677944f7
```

### Ativando o auto-complete para sistemas Red Hat

4. Para ativar o auto-complete Red Hat based:

```bash
$ yum install bash-completion -y ; kubectl completion bash > /etc/bash_completion.d/kubectl
```

5. Para ativar o auto-complete execute:

```bash
$ source <(kubectl completion bash)
```

### Buscando os POD's do namespace `kube-systems`

6. Vamos agora descrever um POD:

```bash
# kubectl get pods -n kube-system
NAME                                READY   STATUS    RESTARTS       AGE
coredns-64897985d-dkmwn             1/1     Running   1 (3d1h ago)   6d4h
coredns-64897985d-sj8s8             1/1     Running   1 (3d1h ago)   6d4h
etcd-k8smaster                      1/1     Running   1 (3d1h ago)   6d4h
kube-apiserver-k8smaster            1/1     Running   1 (3d1h ago)   6d4h
kube-controller-manager-k8smaster   1/1     Running   1 (3d1h ago)   6d4h
kube-proxy-5c7lt                    1/1     Running   1 (3d1h ago)   6d3h
kube-proxy-7cmng                    1/1     Running   1 (3d1h ago)   6d4h
kube-proxy-clgln                    1/1     Running   1 (3d1h ago)   6d3h
kube-scheduler-k8smaster            1/1     Running   1 (3d1h ago)   6d4h
weave-net-bkc5w                     2/2     Running   3 (3d1h ago)   6d4h
weave-net-cqvn8                     2/2     Running   2 (3d1h ago)   6d3h
weave-net-tfskb                     2/2     Running   2 (3d1h ago)   6d3h
```

#### Concepts
- um POD por ter mais de um container veja o caso acima do `weave-net-bkc5w`.
  - esses containers compartlha o mesmo IP, nome, mesmo namespace.
  - `namespace`: funciona como um cercado, voce tem maior controle da sua app.
  
Aqui agora descrevendo os POD's do Kube-System:

```bash
# kubectl describe pods -n kube-system
Name:                 coredns-64897985d-7wkj2
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Node:                 localhost.localdomain/192.168.0.155
Start Time:           Sat, 15 Jan 2022 16:21:31 -0300
Labels:               k8s-app=kube-dns
                      pod-template-hash=64897985d
Annotations:          <none>
Status:               Running
IP:                   10.32.0.2
IPs:
  IP:           10.32.0.2
Controlled By:  ReplicaSet/coredns-64897985d
Containers:
  coredns:
    Container ID:  docker://921353442b349cc1e77c7cc150701486b9782c6682324aae1cea7b1100062544
    Image:         k8s.gcr.io/coredns/coredns:v1.8.6
    Image ID:      docker-pullable://k8s.gcr.io/coredns/coredns@sha256:5b6ec0d6de9baaf3e92d0f66cd96a25b9edbce8716f5f15dcd1a616b3abd590e
    Ports:         53/UDP, 53/TCP, 9153/TCP
    Host Ports:    0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
    State:          Running
      Started:      Mon, 17 Jan 2022 10:53:19 -0300
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Sat, 15 Jan 2022 16:49:02 -0300
      Finished:     Mon, 17 Jan 2022 10:52:03 -0300
    Ready:          True
    Restart Count:  1
```

- eu posso ter mais de 1 container dentro de um mesmo POD.
- esses containers compartilham  o mesmo IP entre eles.

Mais detalhes do POD `weave-net-cqvn8`:

```bash
# kubectl describe pods -n kube-system weave-net-cqvn8
Name:                 weave-net-cqvn8
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 k8snode01/192.168.0.220
Start Time:           Sun, 27 Feb 2022 14:49:49 -0300
Labels:               controller-revision-hash=59d968cb54
                      name=weave-net
                      pod-template-generation=1
Annotations:          <none>
Status:               Running
IP:                   192.168.0.220
IPs:
  IP:           192.168.0.220
Controlled By:  DaemonSet/weave-net
Init Containers:
  weave-init:
    Container ID:  docker://01cfc8b8e6e4041c75ce9f7ad43426705ab4bd053f354f96f824eedf7aee5c93
    Image:         ghcr.io/weaveworks/launcher/weave-kube:2.8.1
    Image ID:      docker-pullable://ghcr.io/weaveworks/launcher/weave-kube@sha256:d797338e7beb17222e10757b71400d8471bdbd9be13b5da38ce2ebf597fb4e63
    Port:          <none>
    Host Port:     <none>
    Command:
      /home/weave/init.sh
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Wed, 02 Mar 2022 15:19:14 -0300
      Finished:     Wed, 02 Mar 2022 15:19:17 -0300
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /host/etc from cni-conf (rw)
      /host/home from cni-bin2 (rw)
      /host/opt from cni-bin (rw)
      /lib/modules from lib-modules (rw)
      /run/xtables.lock from xtables-lock (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bs7dw (ro)
Containers:
  weave:
    Container ID:  docker://d591211f201a4412a76670a158d64b5ee1807ee47e9bb011ef0aa3a99550e86e
    Image:         ghcr.io/weaveworks/launcher/weave-kube:2.8.1
    Image ID:      docker-pullable://ghcr.io/weaveworks/launcher/weave-kube@sha256:d797338e7beb17222e10757b71400d8471bdbd9be13b5da38ce2ebf597fb4e63
    Port:          <none>
    Host Port:     <none>
    Command:
      /home/weave/launch.sh
```


7. Para visualizar todos os namespaces dos meus PODS ativos:

```bash
]# kubectl get pods --all-namespaces
NAMESPACE     NAME                                READY   STATUS    RESTARTS       AGE
kube-system   coredns-64897985d-dkmwn             1/1     Running   1 (3d1h ago)   6d4h
kube-system   coredns-64897985d-sj8s8             1/1     Running   1 (3d1h ago)   6d4h
kube-system   etcd-k8smaster                      1/1     Running   1 (3d1h ago)   6d4h
kube-system   kube-apiserver-k8smaster            1/1     Running   1 (3d1h ago)   6d4h
kube-system   kube-controller-manager-k8smaster   1/1     Running   1 (3d1h ago)   6d4h
kube-system   kube-proxy-5c7lt                    1/1     Running   1 (3d1h ago)   6d3h
kube-system   kube-proxy-7cmng                    1/1     Running   1 (3d1h ago)   6d4h
kube-system   kube-proxy-clgln                    1/1     Running   1 (3d1h ago)   6d4h
kube-system   kube-scheduler-k8smaster            1/1     Running   1 (3d1h ago)   6d4h
kube-system   weave-net-bkc5w                     2/2     Running   3 (3d1h ago)   6d4h
kube-system   weave-net-cqvn8                     2/2     Running   2 (3d1h ago)   6d4h
kube-system   weave-net-tfskb                     2/2     Running   2 (3d1h ago)   6d3h
```

- Para visualizar todos os namespaces dos meus PODS ativos:

```bash
# kubectl get pods --all-namespaces -o wide
NAMESPACE     NAME                                READY   STATUS    RESTARTS       AGE    IP              NODE        NOMINATED NODE   READINESS GATES
kube-system   coredns-64897985d-dkmwn             1/1     Running   1 (3d1h ago)   6d4h   10.32.0.3       k8smaster   <none>           <none>
kube-system   coredns-64897985d-sj8s8             1/1     Running   1 (3d1h ago)   6d4h   10.32.0.2       k8smaster   <none>           <none>
kube-system   etcd-k8smaster                      1/1     Running   1 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   kube-apiserver-k8smaster            1/1     Running   1 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   kube-controller-manager-k8smaster   1/1     Running   1 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   kube-proxy-5c7lt                    1/1     Running   1 (3d1h ago)   6d3h   192.168.0.136   k8snode02   <none>           <none>
kube-system   kube-proxy-7cmng                    1/1     Running   1 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   kube-proxy-clgln                    1/1     Running   1 (3d1h ago)   6d4h   192.168.0.217   k8snode01   <none>           <none>
kube-system   kube-scheduler-k8smaster            1/1     Running   1 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   weave-net-bkc5w                     2/2     Running   3 (3d1h ago)   6d4h   192.168.0.235   k8smaster   <none>           <none>
kube-system   weave-net-cqvn8                     2/2     Running   2 (3d1h ago)   6d4h   192.168.0.215   k8snode01   <none>           <none>
kube-system   weave-net-tfskb                     2/2     Running   2 (3d1h ago)   6d3h   192.168.0.135   k8snode02   <none>           <none>
```

8. Para visualizar todos os namespaces que eu tenho criado no meu control-plane, cluster:

```bash
# kubectl get namespaces
NAME              STATUS   AGE
default           Active   44h
kube-node-lease   Active   44h
kube-public       Active   44h
kube-system       Active   44h
```

#### Concepts
- esses `namespaces` sao padroes do Kubernetes.

8.1.  Vamos agora criar um novo *namespace*, para isso devemos executar o comando abaixo:

```bash
# kubectl create namespace devops
namespace/devops created
```

- Agora tenho ele listado:

```bash
# kubectl get namespaces
NAME              STATUS   AGE
amarops           Active   118s
default           Active   44h
kube-node-lease   Active   44h
kube-public       Active   44h
kube-system       Active   44h
```

9. Criando agora um novo *POD* (quando ele executa o comando run ele cria um POD):

```bash
# kubectl run nginx --image=nginx
pod/nginx created
```

9.1 Listando meu *POD* criado:

```bash
# kubectl get pods
NAME    READY   STATUS    RESTARTS      AGE
nginx   1/1     Running   1 (98m ago)   35h
```

9.2 Vendo mais detalhes desse *POD*:

```bash
# kubectl get pods nginx  -o wide
NAME    READY   STATUS    RESTARTS   AGE    IP          NODE        NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          100s   10.44.0.2   k8snode01   <none>           <none>
```

9.3 Mais detalhes do *POD*:

```bash
# kubectl describe pods nginx
Name:         nginx
Namespace:    default
Priority:     0
Node:         kube-worker01/192.168.1.210
Start Time:   Sun, 16 Jan 2022 00:48:48 -0300
Labels:       run=nginx
Annotations:  <none>
Status:       Running
IP:           10.44.0.1
IPs:
  IP:  10.44.0.1
Containers:
  nginx:
    Container ID:   docker://223d5b1e749a2065a760dd4cc8e71826504e5832b6632d67753f9e23c3673d52
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 17 Jan 2022 10:53:03 -0300
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Sun, 16 Jan 2022 00:49:14 -0300
      Finished:     Mon, 17 Jan 2022 10:52:10 -0300
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bx8dc (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-bx8dc:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
```

10. Agora quero visualizar mais detalhes do *POD* do nginx, buscando o manifesto (objeto YML) no formato `yml`:

- esse *POD* vem com mais sujeiras do k8s (podemos remover algumas linhas).

```yml
# kubectl get pods nginx -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-03-05T20:22:00Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "372146"
  uid: 4f255e36-4725-4e0e-82f1-4f34235d8e5d
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-mfq45
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: k8snode01
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-mfq45
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2022-03-05T06:39:08Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2022-03-05T06:39:13Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2022-03-05T06:39:13Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2022-03-05T20:22:00Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://87a580be57bb0ea333fe860eb1ef480a70acede42cf3b1e8a0065fcc2582aee2
    image: nginx:latest
    imageID: docker-pullable://nginx@sha256:859ab6768a6f26a79bc42b231664111317d095a4f04e4b6fe79ce37b3d199097
    lastState: {}
    name: nginx
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2022-03-05T06:39:12Z"
  hostIP: 192.168.0.220
  phase: Running
  podIP: 10.44.0.2
  podIPs:
  - ip: 10.44.0.2
  qosClass: BestEffort
  startTime: "2022-03-05T06:39:08Z"
```

- como ele foi criado para o Kubernetes
- quando eu quiser criar um POD atraves do `yml` preciso fazer algo assim
- muita coisa e lixo eu nao preciso

10.1. Primeiro vamos redirecionar um POD de teste para que eu tenha um exemplo:

```bash
# kubectl get pods nginx -o yaml >> meu_primeiro_pod_nginx.yml
```

10.2. Nosso `yml` file modificado:

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: amarops
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

10.3. Agora sim vamos criar nosso primeiro POD e usar ele no meu namesoace `devops`:

```bash
# kubectl apply -f primeiro_pod_nginx.yml
pod/nginx created
```

10.3.a. Eu tambem posso remover/deletar esse *POD*:

```bash
# kubectl delete -f primeiro_pod_nginx.yml
pod "nginx" deleted
```
10.3.b. Tambem posso simular uma criacao de *POD* ja com o `YML`:

```bash
# kubectl run nginx --image=nginx --dry-run=client
pod/nginx created (dry run)
```

10.3.c. Ele vem com o `YML`enxuto:

```yml
# kubectl run nginx --image=nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- Posso dar `apply`:

```bash
# kubectl apply -f segundo_pod_nginx.yml
pod/nginx created
```

10.4. Se eu listar agora meu *namespace* eu posso ver meu *POD*:

```bash
]# kubectl get pods -n devops
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          2m21s
```

10.5. Veja que eu posso tambem aproveitar um comando que ja me fornece o `yml` exatamente como eu preciso:

- a opcao `dry-run` apenas simula a criacao do POD

```yml
# kubectl run nginx --image=nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- veja agora:

```bash
# kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          16s
```
