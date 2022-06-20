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
      - [Inciando a criacao de um volume PV](#inciando-a-criacao-de-um-volume-pv)
        - [Instalando os pacotes de NFS](#instalando-os-pacotes-de-nfs)
        - [Crie o diretorio dentro do `control plane`](#crie-o-diretorio-dentro-do-control-plane)
        - [Ativacao do NFS](#ativacao-do-nfs)
        - [Criando arquivo de teste](#criando-arquivo-de-teste)
      - [Hora de criar o `YML` do nosso PV](#hora-de-criar-o-yml-do-nosso-pv)
      - [Listando o PV que foi criado](#listando-o-pv-que-foi-criado)
      - [Descrevendo o PV](#descrevendo-o-pv)
      - [Hora de criar o `YML` do nosso PVC](#hora-de-criar-o-yml-do-nosso-pvc)
      - [Listando o PVC](#listando-o-pvc)
      - [Criando nosso Deployment para teste](#criando-nosso-deployment-para-teste)
      - [Criando o deployment](#criando-o-deployment)

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

#### Mais sobre Persistent Volume

Quando temos esse tipo de volume estamos com a ideia de criar um disco, um compartilhamento, um volume para que isso torne um disco, um persistente volume.

- precisa ser atachado ao POD (precisa fazer um PVC para que ele seja atachado no POD).

#### Inciando a criacao de um volume PV

##### Instalando os pacotes de NFS

Precisamos fazer a instalacao do pacote NFS no `control plane` antes de qualquer coisa.

- O pacote que precisamos instalar e o `nfs-utils` para sistemas baseados em RED HAT.\
- Para sistemas baseados em Deviab/Ubuntu, usamos o pacote `nfs-kernel-server` e o cliente `nfs-common`

##### Crie o diretorio dentro do `control plane`

`mkdir /opt/dados` e deposi exexute o comando `chmod 1777 /opt/dados/`

##### Ativacao do NFS

Crie o arquivo de nome `export` dentro do `control plane`

`sudo vim /etc/exports` e adicione a seguinte linha `/opt/dados *(rw,sync,no_root_squash,subtree_check)`

Agora depois disso feito, aplique as configuracoes com o comando `sudo exportfs -a` e faca o restart do servico `sudo systemctl restart nfs`

##### Criando arquivo de teste

Crie o seguinte arquivo de teste dentro do diretorio `/opt/dados/teste-funciona`

####  Hora de criar o `YML` do nosso PV

Para criar basta seguir com o comando abaixo:

`kubectl create -f primeiro_pv.yml`

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: primeiro-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  nfs:
    path: /opt/dados
    server: 192.168.1.129 
    readOnly: false
```

#### Listando o PV que foi criado

```bash
# kubectl get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
primeiro-pv   1Gi        RWX            Retain           Available           nfs                     23s
```

#### Descrevendo o PV

`# kubectl describe pv primeiro-pv`

```bash
Name:            primeiro-pv
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    nfs
Status:          Available
Claim:
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:
Source:
    Type:      NFS (an NFS mount that lasts the lifetime of a pod)
    Server:    192.168.0.134
    Path:      /opt/dados
    ReadOnly:  false
Events:        <none>
```

####  Hora de criar o `YML` do nosso PVC

`# kubectl create -f primeiro_pvc.yml`

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: primeiro-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 800Mi
  storageClassName: nfs
```

#### Listando o PVC

```bash
# kubectl get pvc
NAME           STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
primeiro-pvc   Bound    primeiro-pv   1Gi        RWX            nfs            8s
```

#### Criando nosso Deployment para teste

Para que possamos testar esses volumes que foram criados, precisamos por dentro de um POD.

#### Criando o deployment

`# kubectl create -f nfs-pv.yml`

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
        - name: nfs-pv
          mountPath: /giropops
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      volumes:
      - name: nfs-pv
        persistentVolumeClaim:
          claimName: primeiro-pvc
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```


