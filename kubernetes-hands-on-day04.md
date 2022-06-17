# Kubernetes

## Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
  - [Volumes no Kubernetes](#volumes-no-kubernetes)
    - [Tipos de volumes](#tipos-de-volumes)
    - [Volume tipo `EmptyDir`](#volume-tipo-emptydir)
      - [Criando nosso volume](#criando-nosso-volume)
      - [Analisando nosso POD](#analisando-nosso-pod)
      - [Acessando o POD criado](#acessando-o-pod-criado)
      - [Visualizando nosso volume](#visualizando-nosso-volume)
      - [Descrevendo nosso POD](#descrevendo-nosso-pod)
      - [Onde fica no disco esse volume criado?](#onde-fica-no-disco-esse-volume-criado)
    - [Volume do tipo PV `Persistent Volume`](#volume-do-tipo-pv-persistent-volume)
  - [Mais sobre Persistent Volume](#mais-sobre-persistent-volume)

## Volumes no Kubernetes

Hoje vamos aprender mais sobre volumes no Kubernetes, existem dois tipos de volumes:

### Tipos de volumes

- EmptyDir (esta conectado com o POD e sempre inicia vazio, criado junto com o POD).
  - nao existe persistencia de dados com esse tipo de volume, os dados sao apagados.

- Persistent Volume ()

### Volume tipo `EmptyDir`

#### Criando nosso volume

`# kubectl create -f pod_emptydir.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy
    command:
      - sleep
      - "3600"
    volumeMounts:
    - mountPath: /giropops
      name: giropops-dir
  volumes:
  - name: giropops-dir
    emptyDir: {}
```

#### Analisando nosso POD

```bash
# kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE  
busybox   1/1     Running   0          8m55s
nginx     1/1     Running   0          4h9m 
```

#### Acessando o POD criado

`# kubectl exec -ti busybox -- sh`

#### Visualizando nosso volume

```bash
# kubectl exec -ti busybox -- sh
/ # ls
bin       dev       etc       giropops  home      proc      root      sys       tmp       usr       var
```

#### Descrevendo nosso POD

```bash
# kubectl describe pods busybox 
Name:         busybox
Namespace:    default
Priority:     0
Node:         kubernetes-node03/192.168.0.236
Start Time:   Thu, 16 Jun 2022 17:16:32 -0300
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.36.0.1
IPs:
  IP:  10.36.0.1
Containers:
  busy:
    Container ID:  containerd://f7e99187774d2a355731b699e75fd11c984b659904410745226223c271d542fc
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Thu, 16 Jun 2022 17:16:38 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /giropops from giropops-dir (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9bp2z (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  giropops-dir:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:  <unset>
  kube-api-access-9bp2z:
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
  Normal  Pulling    17m   kubelet            Pulling image "busybox"
  Normal  Pulled     17m   kubelet            Successfully pulled image "busybox" in 5.068789715s
  Normal  Created    17m   kubelet            Created container busy
  Normal  Started    17m   kubelet            Started container busy
  Normal  Scheduled  15m   default-scheduler  Successfully assigned default/busybox to kubernetes-node03
  ```

#### Onde fica no disco esse volume criado?

```bash
# kubectl get pods busybox -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP          NODE                NOMINATED NODE   READINESS GATES
busybox   1/1     Running   0          22m   10.36.0.1   kubernetes-node03   <none>           <none>
```

Veja que no node-03 que ele fica dessa forma:

```bash
# ls
0f0c9b06-0133-402f-9614-757567910ca1  dc90bea1-4721-464f-9aa9-bc4126492e24  e2e9c2e2-8a90-4b15-9925-a6d501b9e93f
```

Acessando o volume dentro desse node:

```bash
# find . -iname "giropops-dir"
./e2e9c2e2-8a90-4b15-9925-a6d501b9e93f/volumes/kubernetes.io~empty-dir/giropops-dir
./e2e9c2e2-8a90-4b15-9925-a6d501b9e93f/plugins/kubernetes.io~empty-dir/giropops-dir
```

### Volume do tipo PV `Persistent Volume`

Aqui dentro do Kubernetes, temos dois tipos de persistente volumes (PV e PVC), sendo PV == Persistent Volume e PVC == Persistent Volume Claim.

## Mais sobre Persistent Volume

Quando temos esse tipo de volume estamos com a ideia de criar um disco, um compartilhamento,