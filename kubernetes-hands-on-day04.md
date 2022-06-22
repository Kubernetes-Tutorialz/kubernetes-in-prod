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
      - [Listando o deployment](#listando-o-deployment)
      - [Descrevendo o deployment criado](#descrevendo-o-deployment-criado)
      - [Listando o POD desse deployment criado](#listando-o-pod-desse-deployment-criado)
      - [Descrevendo nosso POD](#descrevendo-nosso-pod-1)
      - [Usando o `kubectl exec` para se conectar dentro desse POD](#usando-o-kubectl-exec-para-se-conectar-dentro-desse-pod)
      - [Deletando nosso deployment](#deletando-nosso-deployment)
  - [CronJobs no Kubernetes](#cronjobs-no-kubernetes)
    - [Criando o Cronjobs](#criando-o-cronjobs)
      - [Listando o Cronjob](#listando-o-cronjob)
      - [Descrevendo esse Cronjob](#descrevendo-esse-cronjob)
      - [Testando o Cronjob](#testando-o-cronjob)
      - [Deletando o Cronjob](#deletando-o-cronjob)
  - [Secrets no kubernetes](#secrets-no-kubernetes)
    - [Criando uma `secret` no Kubernetes](#criando-uma-secret-no-kubernetes)
    - [Listando essa secret](#listando-essa-secret)
    - [Descrevendo a secret](#descrevendo-a-secret)
    - [Analisando o `YML` file dessa secret](#analisando-o-yml-file-dessa-secret)
    - [Usando o `decode` para ver os dados](#usando-o-decode-para-ver-os-dados)
    - [Criando um POD para demonstrar](#criando-um-pod-para-demonstrar)
    - [Listando o POD](#listando-o-pod)
    - [Descrevendo o POD](#descrevendo-o-pod)
    - [Acessando o POD](#acessando-o-pod)
    - [Deletando nossa secret criada](#deletando-nossa-secret-criada)
    - [Criando a `secret` usando o modo literal](#criando-a-secret-usando-o-modo-literal)
      - [Listando a secret](#listando-a-secret)
      - [Descrevendo a secret](#descrevendo-a-secret-1)
      - [Visualizando o `YML` dessa secret](#visualizando-o-yml-dessa-secret)
      - [Criando um POD para a secret com variaveis](#criando-um-pod-para-a-secret-com-variaveis)
  - [ConfigMaps no Kubernetes](#configmaps-no-kubernetes)
    - [Criando o POD de teste](#criando-o-pod-de-teste)

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

#### Listando o deployment

```bash
# kubectl get deployments.apps 
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     1            0           6s
```

#### Descrevendo o deployment criado

`# kubectl describe deployments.apps nginx`

```bash
Name:                   nginx
Namespace:              default
CreationTimestamp:      Sun, 19 Jun 2022 19:36:34 -0300
Labels:                 run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:  run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /giropops from nfs-pv (rw)
  Volumes:
   nfs-pv:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  primeiro-pvc
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-6f559865fb (1/1 replicas created)
Events:          <none>
```

#### Listando o POD desse deployment criado

```bash
# kubectl get pods
NAME                     READY   STATUS    RESTARTS       AGE
busybox                  1/1     Running   29 (39m ago)   3d5h
nginx                    1/1     Running   1 (11h ago)    3d9h
nginx-6f559865fb-s7pnr   1/1     Running   0              3h18m
webserver                1/1     Running   0              7h59m
```

#### Descrevendo nosso POD

`# kubectl describe pods  nginx-6f559865fb-s7pnr`

```bash
Name:         nginx-6f559865fb-s7pnr
Namespace:    default
Priority:     0
Node:         kubernetes-node01/192.168.0.235
Start Time:   Sun, 19 Jun 2022 19:38:16 -0300
Labels:       pod-template-hash=6f559865fb
              run=nginx
Annotations:  <none>
Status:       Running
IP:           10.44.0.2
IPs:
  IP:           10.44.0.2
Controlled By:  ReplicaSet/nginx-6f559865fb
Containers:
  nginx:
    Container ID:   containerd://7fe2731c95e92cdf0198c7cef18c961ed113e0f754b4d3a7943029662e74cc1d
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:2bcabc23b45489fb0885d69a06ba1d648aeda973fae7bb981bafbb884165e514
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 19 Jun 2022 19:38:49 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /giropops from nfs-pv (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jzlfg (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  nfs-pv:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  primeiro-pvc
    ReadOnly:   false
  kube-api-access-jzlfg:
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

#### Usando o `kubectl exec` para se conectar dentro desse POD

`# kubectl exec -ti nginx-6f559865fb-s7pnr -- sh`

Posso usar mais formas de se criar arquivos:

`kubectl exec -ti nginx-b4bd77674-gwc9k -- touch /giropops/STRIGUS`

Bem como essa tambem:

`kubectl exec -ti nginx-b4bd77674-gwc9k -- ls -la  /giropops/`

```bash
#
# 
# 
# pwd
```

Analisando com mais detalhes nosso POD

```bash
@nginx-6f559865fb-s7pnr:/# ls
bin  boot  dev  docker-entrypoint.d  docker-entrypoint.sh  etc  giropops  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@nginx-6f559865fb-s7pnr:/#
root@nginx-6f559865fb-s7pnr:/#
```

Vou acessar o dietorio giropops e criar alguns arquivos la dentro:

```bash
@nginx-6f559865fb-s7pnr:/giropops# ls
ama  nica  teste  testu
```

Agora eu posso sair do POD que eu tenho acesso aos arquivos criados:

```bash
@kubernetes-cluster dados]# pwd     
/opt/dados
[root@kubernetes-cluster dados]# ls
ama  nica  teste  testu
```

O POD esta monstado nesse node:

```bash
# kubectl get pods nginx-6f559865fb-s7pnr  -o wide
NAME                     READY   STATUS    RESTARTS   AGE     IP          NODE                NOMINATED NODE   READINESS GATES
nginx-6f559865fb-s7pnr   1/1     Running   0          3h25m   10.42.0.5   kubernetes-node01   <none>           <none>
```

#### Deletando nosso deployment

Podemos deletar esse PV para testar se realmente esta funcionando:

```bash
# kubectl delete -f nfs-pv.yml 
deployment.apps "nginx" deleted

[root@kubernetes-cluster day-04]# kubectl get deployments.apps 
No resources found in default namespace.

[root@kubernetes-cluster day-04]# cd /opt/dados/
[root@kubernetes-cluster dados]# ls
ama  nica  teste  testu
```
Veja que os dados que foram criados estao da mesma forma, ou seja, os volumes funcionaram usando a forma de PV.

## CronJobs no Kubernetes

Vamos agendar algo dentro do Kubernetes, por exemplo agendar em meses, dias, execute detrminado comando, script. Pensa que o `cronjobs` no kubernetes e a mesma coisa do que existe dentro do LINUX, mas e uma forma clusterizada.

Posso criar uma rotina dentro do Kubernetes, seja um `POD` ou um `deployment` e ele se encarrega de executar isso dentro do horario que eu deixei agendado e depois morre, nao preciso de uma instancia rodando 100% do tempo la, essa que e a sacada dos `cronjobs` dentro do Kubernetes.

### Criando o Cronjobs

`# kubectl create -f primeiro_cronjobs.yml`

```yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: giropops-cron
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: giropops-cron
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII ;sleep 30
          restartPolicy: OnFailure
```

#### Listando o Cronjob

```bash
# kubectl get cronjobs.batch 
NAME            SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE 
giropops-cron   */1 * * * *   False     1        5s              143m
```

#### Descrevendo esse Cronjob

```bash
# kubectl describe cronjobs.batch giropops-cron 
Name:                          giropops-cron
Namespace:                     default      
Labels:                        <none>       
Annotations:                   <none>       
Schedule:                      */1 * * * *  
Concurrency Policy:            Allow        
Suspend:                       False        
Successful Job History Limit:  3
Failed Job History Limit:      1
Starting Deadline Seconds:     <unset>
Selector:                      <unset>
Parallelism:                   <unset>
Completions:                   <unset>
Pod Template:
  Labels:  <none>
  Containers:
   giropops-cron:
    Image:      busybox
    Port:       <none>
    Host Port:  <none>
    Args:
      /bin/sh
      -c
      date; echo Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII ;sleep 30
    Environment:     <none>
    Mounts:          <none>
  Volumes:           <none>
Last Schedule Time:  Mon, 20 Jun 2022 01:50:00 -0300
Active Jobs:         giropops-cron-27595010
Events:
  Type    Reason            Age                   From                Message
  ----    ------            ----                  ----                -------
  Normal  SuccessfulCreate  26m (x117 over 142m)  cronjob-controller  (combined from similar events): Created job giropops-cron-27594984
  Normal  SawCompletedJob   59s (x155 over 141m)  cronjob-controller  (combined from similar events): Saw completed job: giropops-cron-27595009, status: Complete
  ```

  Posso tambem executar o comando abaixo que mostra os jobs que estao sendo executados:

  ```bash
  # kubectl get jobs
NAME                     COMPLETIONS   DURATION   AGE
giropops-cron-27595009   1/1           37s        2m49s
giropops-cron-27595010   1/1           36s        109s
giropops-cron-27595011   1/1           37s        49s
```

#### Testando o Cronjob

Veja a saida do comando abaixo, ele mostra as execucoes de cada container dentro do POD:

```bash
# kubectl get jobs --watch
NAME                     COMPLETIONS   DURATION   AGE
giropops-cron-27595010   1/1           36s        2m48s
giropops-cron-27595011   1/1           37s        108s
giropops-cron-27595012   1/1           37s        48s


giropops-cron-27595013   0/1                      0s
giropops-cron-27595013   0/1           0s         0s
giropops-cron-27595013   0/1           4s         4s
giropops-cron-27595013   0/1           35s        35s
giropops-cron-27595013   1/1           37s        37s
giropops-cron-27595010   1/1           36s        3m37s
```

Veja que listando os PODS, ele mostra como `completed`:

```bash
# kubectl get pods
NAME                           READY   STATUS      RESTARTS       AGE
busybox                        1/1     Running     32 (41m ago)   3d8h
giropops-cron-27595014-4ftq4   0/1     Completed   0              3m24s
giropops-cron-27595015-2v7c6   0/1     Completed   0              2m24s
giropops-cron-27595016-724xj   0/1     Completed   0              84s
giropops-cron-27595017-6v5qk   1/1     Running     0              24s
nginx                          1/1     Running     1 (14h ago)    3d12h
webserver                      1/1     Running     0              11h
```

Olha ele pegando os logs:

```bash
# kubectl logs giropops-cron-27595018-mqctg
Mon Jun 20 05:00:03 UTC 2022
Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII
```

O ciclo de vida do POD:

```bash
]# kubectl get pods --watch
NAME                           READY   STATUS      RESTARTS       AGE
busybox                        1/1     Running     32 (43m ago)   3d8h
giropops-cron-27595017-6v5qk   0/1     Completed   0              2m55s
giropops-cron-27595018-mqctg   0/1     Completed   0              115s
giropops-cron-27595019-d4ndm   0/1     Completed   0              55s
nginx                          1/1     Running     1 (14h ago)    3d12h
webserver                      1/1     Running     0              11h

giropops-cron-27595020-jk98m   0/1     Pending     0              0s
giropops-cron-27595020-jk98m   0/1     Pending     0              0s
giropops-cron-27595020-jk98m   0/1     ContainerCreating   0              0s
giropops-cron-27595020-jk98m   1/1     Running             0              3s
giropops-cron-27595020-jk98m   0/1     Completed           0              33s
giropops-cron-27595020-jk98m   0/1     Completed           0              35s
giropops-cron-27595017-6v5qk   0/1     Terminating         0              3m37s
giropops-cron-27595017-6v5qk   0/1     Terminating         0              3m37s
```

#### Deletando o Cronjob

```bash
# kubectl delete cronjobs.batch giropops-cron 
cronjob.batch "giropops-cron" deleted
```

## Secrets no kubernetes

Passando informacoes sensiveis para o meu sistema dentro do Kubernetes, neste caso eu uso `secrets` do Kubernetes.

Vamos criar uma `secret` bem basica para que possamos entender o cenario dentro do Kubernetes.

`# echo -n "giropops strigus girus" > secret.txt`

### Criando uma `secret` no Kubernetes

`# kubectl create secret generic my-secret --from-file secret.txt `

```bash
# kubectl create secret generic my-secret --from-file secret.txt 
secret/my-secret created
```

### Listando essa secret

```bash
# kubectl get secrets 
NAME        TYPE     DATA   AGE
my-secret   Opaque   1      66s
```

### Descrevendo a secret

```bash
# kubectl describe secrets my-secret 
Name:         my-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
secret.txt:  22 bytes
```

### Analisando o `YML` file dessa secret

```bash
# kubectl get secrets my-secret -o yaml
apiVersion: v1
data:
  secret.txt: Z2lyb3BvcHMgc3RyaWd1cyBnaXJ1cw==
kind: Secret
metadata:
  creationTimestamp: "2022-06-20T05:16:34Z"
  name: my-secret
  namespace: default
  resourceVersion: "205344"
  uid: 2f29641e-33c3-4f16-82ec-e56be6b6670b
type: Opaque
```

### Usando o `decode` para ver os dados

Podemos usar o comando abaixo para analisar esses detalhes da secret.

```bash
# echo "Z2lyb3BvcHMgc3RyaWd1cyBnaXJ1cw==" | base64 --decode
giropops strigus girus[root@kubernetes-cluster day-04]#
```

Veja que ele mostra os dados sensiveis da minha secret. Demais em!?

### Criando um POD para demonstrar

`# kubectl create -f pod-secret.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: test-secret
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy
    command:
      - sleep
      - "3600"
    volumeMounts:
    - mountPath: /tmp/giropops
      name: my-volume-secret
  volumes:
  - name: my-volume-secret
    secret:
      secretName: my-secret
```

### Listando o POD

```bash
# kubectl get pods 
NAME          READY   STATUS    RESTARTS       AGE
busybox       1/1     Running   33 (12m ago)   3d9h
nginx         1/1     Running   1 (15h ago)    3d13h
test-secret   1/1     Running   0              6s
webserver     1/1     Running   0              11h
```

### Descrevendo o POD

```yml
# kubectl describe pods test-secret 
Name:         test-secret
Namespace:    default
Priority:     0
Node:         kubernetes-node01/192.168.0.235
Start Time:   Mon, 20 Jun 2022 02:30:36 -0300
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.44.0.2
IPs:
  IP:  10.44.0.2
Containers:
  busy:
    Container ID:  containerd://1312bea7fdab0eabadad22898eb6d2f7fdc7e8115127dd9aa9eb90f04e9dcc5f
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Mon, 20 Jun 2022 02:30:39 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /tmp/giropops from my-volume-secret (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-cwhkn (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  my-volume-secret:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  my-secret
    Optional:    false
  kube-api-access-cwhkn:
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
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  50s        default-scheduler  Successfully assigned default/test-secret to kubernetes-node01
  Normal  Pulling    <invalid>  kubelet            Pulling image "busybox"
  Normal  Pulled     <invalid>  kubelet            Successfully pulled image "busybox" in 1.562530804s
  Normal  Created    <invalid>  kubelet            Created container busy
  Normal  Started    <invalid>  kubelet            Started container busy
  ```

  ### Acessando o POD

  `# kubectl exec -ti test-secret -- sh`

  Agora vamos ao diretorio de acordo com o POD que foi criado:

```bash
  # kubectl exec -ti test-secret -- sh
/ # 
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # cd tmp/giropops/
/tmp/giropops # ls
secret.txt
```

### Deletando nossa secret criada

`# kubectl delete pods test-secret`

### Criando a `secret` usando o modo literal

`# kubectl create secret generic my-literal-secret --from-literal user=amaury --from-literal  password=teste99`

#### Listando a secret

```bash
# kubectl get secrets 
NAME                TYPE     DATA   AGE
my-literal-secret   Opaque   2      72s
my-secret           Opaque   1      10h
```

#### Descrevendo a secret

```bash
# kubectl describe secrets my-literal-secret 
Name:         my-literal-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  7 bytes
user:      6 bytes
```

#### Visualizando o `YML` dessa secret

```yml
# kubectl get secrets my-literal-secret -o yaml
apiVersion: v1
data:
  password: dGVzdGU5OQ==
  user: YW1hdXJ5
kind: Secret
metadata:
  creationTimestamp: "2022-06-20T16:11:21Z"
  name: my-literal-secret
  namespace: default
  resourceVersion: "263818"
  uid: d03b9dd2-6185-4f35-bc76-a2edbcac2819
type: Opaque
```

Veja, agora foi executar um comando `decode` para ver meu usuario:

```bash
# echo "YW1hdXJ5" | base64 --decode
amaury[root@kubernetes-cluster ~]#
```

#### Criando um POD para a secret com variaveis

`# kubectl create -f pod-secret-env.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: teste-secret-env
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-secret-env
    command:
      - sleep
      - "3600"
    env:
    - name: MEU_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-literal-secret
          key: user
    - name: MEU_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-literal-secret
          key: password
```

- Listando esse POD

```bash
# kubectl get pods
NAME               READY   STATUS    RESTARTS       AGE
busybox            1/1     Running   43 (62m ago)   3d20h
nginx              1/1     Running   1 (26h ago)    4d
teste-secret-env   1/1     Running   0              5s
webserver          1/1     Running   0              22h
```

- Analisando as variaveis dentro do POD

```bash
# kubectl exec -ti teste-secret-env -- sh
/ #
/ #
/ # set
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='teste-secret-env'
IFS='
'
KUBERNETES_PORT='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP_ADDR='10.96.0.1'
KUBERNETES_PORT_443_TCP_PORT='443'
KUBERNETES_PORT_443_TCP_PROTO='tcp'
KUBERNETES_SERVICE_HOST='10.96.0.1'
KUBERNETES_SERVICE_PORT='443'
KUBERNETES_SERVICE_PORT_HTTPS='443'
LINENO=''
MEU_PASSWORD='teste99'
MEU_USERNAME='amaury'
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
```

## ConfigMaps no Kubernetes

O ConfigMaps dentro do kubernetes consiste na mesma ideia dos secrets, diferenca e que voce pode adicionar configuracoes dentro do POD, sem buidar imagem ou volume.

- Criando alguns arquivos aqui de teste

`mkdir frutas`

- Agora crie os arquivos dentro desse diretorio

`echo verde > limao`
`echo verde > vermelho`
`echo vermelho > morango`

### Criando o POD de teste

```yml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    env:
    - name: frutas
      valueFrom:
        configMapKeyRef:
          name: cores-frutas
          key: predileta
```