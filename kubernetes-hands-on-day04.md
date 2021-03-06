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
    - [Criando o `ConfigMap`](#criando-o-configmap)
    - [Criando o POD de teste para o ConfigMap](#criando-o-pod-de-teste-para-o-configmap)
    - [Testando o ConfigMap com mais variaveis](#testando-o-configmap-com-mais-variaveis)
    - [Criando mais outro POD para arquivos do Configmap](#criando-mais-outro-pod-para-arquivos-do-configmap)
  - [InitContainer no Kubernetes](#initcontainer-no-kubernetes)
    - [Criando o POD para o InitContainer](#criando-o-pod-para-o-initcontainer)
    - [Entendendo o role do InitContainer](#entendendo-o-role-do-initcontainer)
  - [RBAC](#rbac)
    - [Criando uma `serviceaccount`](#criando-uma-serviceaccount)
    - [Listando mais clusterole](#listando-mais-clusterole)
    - [Criando um `clusterolebinding` completa](#criando-um-clusterolebinding-completa)
    - [Criando um exemplo com `YML`](#criando-um-exemplo-com-yml)
  - [HELM](#helm)
    - [Mais sobre HELM](#mais-sobre-helm)
    - [O que e `charts` dentro do `HELM`](#o-que-e-charts-dentro-do-helm)
    - [Instalacao do HELM no cluster do Kubernetes](#instalacao-do-helm-no-cluster-do-kubernetes)
    - [Iniciando com Helm Charts](#iniciando-com-helm-charts)
    - [Criando a estrutura do Charts](#criando-a-estrutura-do-charts)
    - [Helm hands-on com NGINX](#helm-hands-on-com-nginx)
    - [Alguns do Helm utilizados](#alguns-do-helm-utilizados)

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

### Criando o `ConfigMap`

- Vamos criar o configmap dentro do Kubernetes:

`# kubectl create configmap cores-frutas --from-literal=uva=roxa --from-file=predileta --from-file=frutas/`

- Listando ele:

```bash
# kubectl get configmaps 
NAME               DATA   AGE  
cores-frutas       6      11s  
kube-root-ca.crt   1      4d15h
```

- Descrevendo esse Configmap:

```bash
# kubectl describe configmaps cores-frutas 
Name:         cores-frutas
Namespace:    default     
Labels:       <none>      
Annotations:  <none>      

Data
====
uva:
----
roxa
banana:
----
amarelo

limao:
----
verde

melancia:
----
verde e vermelho

morango:
----
vermelho

predileta:
----
kiwi


BinaryData
====

Events:  <none>
```

### Criando o POD de teste para o ConfigMap

`# kubectl create -f pod_configmap.yml`

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

- Listando esse POD:

```bash
# kubectl get pods -n default busybox-configmap 
NAME                READY   STATUS    RESTARTS   AGE
busybox-configmap   1/1     Running   0          64s
```

- Descrevendo esse POD;

```bash
# kubectl describe pods -n default busybox-configmap 
Name:         busybox-configmap
Namespace:    default
Priority:     0
Node:         kubernetes-node01/192.168.0.235
Start Time:   Tue, 21 Jun 2022 03:35:38 -0300
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.44.0.2
IPs:
  IP:  10.44.0.2
Containers:
  busy-configmap:
    Container ID:  containerd://1515fd45bdf19f9aa5962ebf1d42cd599c270c5f31bb2f9c59dcf110f142bcf1
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Tue, 21 Jun 2022 03:35:41 -0300
    Ready:          True
    Restart Count:  0
    Environment:
      frutas:  <set to the key 'predileta' of config map 'cores-frutas'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-md6dr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-md6dr:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m16s  default-scheduler  Successfully assigned default/busybox-configmap to kubernetes-node01
  Normal  Pulling    4s     kubelet            Pulling image "busybox"
  Normal  Pulled     3s     kubelet            Successfully pulled image "busybox" in 1.634778673s
  Normal  Created    3s     kubelet            Created container busy-configmap
  Normal  Started    2s     kubelet            Started container busy-configmap
  ```

  - Acessando esse POD:

Veja que ele me trouxe os valores dentro das variaveis sendo frutas = kiwi.

```bash
# kubectl exec -ti busybox-configmap -- sh
/ #
/ # set
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='busybox-configmap'
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
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
frutas='kiwi
```

### Testando o ConfigMap com mais variaveis

- Primeiro precisamos criar o POD:

`# kubectl create -f pod_configmapv2.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap-env
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    envFrom:
    - configMapRef:
        name: cores-frutas
```

- Listando esse POD:

```bash
# kubectl get pods busybox-configmap-env 
NAME                    READY   STATUS    RESTARTS   AGE
busybox-configmap-env   1/1     Running   0          24s
```

- Acessando esse POD e verificando que ele trouxe os valores atraves de variaveis

```bash
 kubectl exec -ti busybox-configmap-env -- sh
/ #
/ # set
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='busybox-configmap-env'
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
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
banana='amarelo
'
limao='verde
'
melancia='verde e vermelho
'
morango='vermelho
'
predileta='kiwi
'
uva='roxa'
```

### Criando mais outro POD para arquivos do Configmap

`# kubectl create -f pod_configmap_file.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap-file
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    volumeMounts:
    - name: meu-configmap-vol
      mountPath: /etc/frutas
  volumes:
  - name: meu-configmap-vol
    configMap:
      name: cores-frutas
```

- Listando esse POD:

```bash
# kubectl get pods busybox-configmap-file 
NAME                     READY   STATUS    RESTARTS   AGE
busybox-configmap-file   1/1     Running   0          17s
```

- Descrevendo esse POD

```bash
# kubectl describe pods busybox-configmap-file 
Name:         busybox-configmap-file
Namespace:    default
Priority:     0
Node:         kubernetes-node02/192.168.0.200
Start Time:   Tue, 21 Jun 2022 03:44:36 -0300
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.32.0.4
IPs:
  IP:  10.32.0.4
Containers:
  busy-configmap:
    Container ID:  containerd://d07c5f819d21eca96fe965881b75c04a00e4e6ba313cd591ea8cd3ba542f63f5
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Tue, 21 Jun 2022 03:44:42 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /etc/frutas from meu-configmap-vol (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-xqqgs (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  meu-configmap-vol:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      cores-frutas
    Optional:  false
  kube-api-access-xqqgs:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  103s   default-scheduler  Successfully assigned default/busybox-configmap-file to kubernetes-node02
  Normal  Pulling    7m37s  kubelet            Pulling image "busybox"
  Normal  Pulled     7m32s  kubelet            Successfully pulled image "busybox" in 4.73437854s
  Normal  Created    7m32s  kubelet            Created container busy-configmap
  Normal  Started    7m32s  kubelet            Started container busy-configmap
```

- Acessando esse POD:

```bash
# kubectl exec -ti busybox-configmap-file -- sh
/ #
/ # ls -lh /etc/frutas/
total 0
lrwxrwxrwx    1 root     root          13 Jun 21 06:44 banana -> ..data/banana
lrwxrwxrwx    1 root     root          12 Jun 21 06:44 limao -> ..data/limao
lrwxrwxrwx    1 root     root          15 Jun 21 06:44 melancia -> ..data/melancia
lrwxrwxrwx    1 root     root          14 Jun 21 06:44 morango -> ..data/morango
lrwxrwxrwx    1 root     root          16 Jun 21 06:44 predileta -> ..data/predileta
lrwxrwxrwx    1 root     root          10 Jun 21 06:44 uva -> ..data/uva
```

## InitContainer no Kubernetes

Esse recurso tras a ideia de voce executar outro comando, alguma tarefa antes da app subir, ai que entra esse recurso de InitContainer. Ele entra antes do meu container especial, pensa que ele funciona como o init, primeiro processo do sistema do sistema LINUX.

### Criando o POD para o InitContainer

`# kubectl create -f nginx_initcontainer.yml`

```yml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  initContainers:
  - name: install
    image: busybox
    command: ['wget','-O','/work-dir/index.html','http://linuxtips.io']
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  dnsPolicy: Default
  volumes:
  - name: workdir
    emptyDir: {}
```

- Listando o POD:

```bash
# kubectl get pods
NAME                     READY   STATUS            RESTARTS        AGE
busybox-configmap-file   1/1     Running           11 (52m ago)    3d13h
init-demo                0/1     PodInitializing   0               15s
nginx                    1/1     Running           2 (6h53m ago)   8d
webserver                1/1     Running           1 (6h53m ago)   5d2h
```

- Descrevendo o POD:

```bash
# kubectl describe pods init-demo 
Name:         init-demo
Namespace:    default
Priority:     0
Node:         kubernetes-node01/192.168.0.235
Start Time:   Fri, 24 Jun 2022 17:37:11 -0300
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.44.0.1
IPs:
  IP:  10.44.0.1
Init Containers:
  install:
    Container ID:  containerd://2fc08a44b192df474e2d984c4d2eca69cecb9aad282981866faafe48bfc19107
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
    Port:          <none>
    Host Port:     <none>
    Command:
      wget
      -O
      /work-dir/index.html
      http://linuxtips.io
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Fri, 24 Jun 2022 17:37:14 -0300
      Finished:     Fri, 24 Jun 2022 17:37:15 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-p5wv6 (ro)
      /work-dir from workdir (rw)
Containers:
  nginx:
    Container ID:   containerd://b74976b2508a9d85cffda6dd623d59296318f80d6af7ba24191a31a0b549d5b3
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:10f14ffa93f8dedf1057897b745e5ac72ac5655c299dade0aa434c71557697ea
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 24 Jun 2022 17:37:38 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /usr/share/nginx/html from workdir (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-p5wv6 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  workdir:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:  <unset>
  kube-api-access-p5wv6:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Pulling    11m    kubelet            Pulling image "busybox"
  Normal  Pulled     11m    kubelet            Successfully pulled image "busybox" in 1.644626294s
  Normal  Created    11m    kubelet            Created container install
  Normal  Started    11m    kubelet            Started container install
  Normal  Pulling    11m    kubelet            Pulling image "nginx"
  Normal  Pulled     11m    kubelet            Successfully pulled image "nginx" in 21.431061881s
  Normal  Created    11m    kubelet            Created container nginx
  Normal  Started    11m    kubelet            Started container nginx
  Normal  Scheduled  5m37s  default-scheduler  Successfully assigned default/init-demo to kubernetes-node01
  ```

### Entendendo o role do InitContainer

Basicamente ele executou o comando que estava sendo pedido dentro do POD, para depois ele criar o container principal. Ele prepara tudo antes para depois criar o container:

```bash
  # kubectl get pods
NAME                     READY   STATUS            RESTARTS        AGE
busybox-configmap-file   1/1     Running           11 (52m ago)    3d13h
init-demo                0/1     PodInitializing   0               15s
nginx                    1/1     Running           2 (6h53m ago)   8d
webserver                1/1     Running           1 (6h53m ago)   5d2h
```

  - Agora vamos acessar esse POD para pegar mais detalhes do arquivo que ele tinha criado:

`# kubectl exec -ti init-demo -- cat /usr/share/nginx/html/index.html`


## RBAC

Vamos aprender mais sobre `RBAC` dentro do kubernetes, gerenciamento de usuarios, permissoes. O controle de acesso baseado em fun????o (RBAC) ?? um m??todo de regular o acesso a recursos de computador ou rede com base nas fun????es de usu??rios individuais em sua organiza????o.

### Criando uma `serviceaccount`

`# kubectl create serviceaccount amaury`

- Listando essa serviceaccount:

```bash
# kubectl get serviceaccounts 
NAME      SECRETS   AGE
amaury    0         6s
default   0         8d
```

- Descrevendo esse serviceaccount:

```bash
# kubectl describe serviceaccounts amaury 
Name:                amaury 
Namespace:           default
Labels:              <none> 
Annotations:         <none> 
Image pull secrets:  <none> 
Mountable secrets:   <none> 
Tokens:              <none> 
Events:              <none>
```

Podemos associar esse serviceaccount a um determinado `cluster-role`.

### Listando mais clusterole

```bash
# kubectl get clusterrole
NAME                                                                   CREATED AT
admin                                                                  2022-06-16T15:11:27Z
cluster-admin                                                          2022-06-16T15:11:27Z
edit                                                                   2022-06-16T15:11:27Z
kubeadm:get-nodes                                                      2022-06-16T15:11:30Z
system:aggregate-to-admin                                              2022-06-16T15:11:27Z
system:aggregate-to-edit                                               2022-06-16T15:11:27Z
system:aggregate-to-view                                               2022-06-16T15:11:27Z
system:aggregated-metrics-reader                                       2022-06-19T17:45:08Z
system:auth-delegator                                                  2022-06-16T15:11:27Z
system:basic-user                                                      2022-06-16T15:11:27Z
system:certificates.k8s.io:certificatesigningrequests:nodeclient       2022-06-16T15:11:27Z
system:certificates.k8s.io:certificatesigningrequests:selfnodeclient   2022-06-16T15:11:28Z
system:certificates.k8s.io:kube-apiserver-client-approver              2022-06-16T15:11:28Z
system:certificates.k8s.io:kube-apiserver-client-kubelet-approver      2022-06-16T15:11:28Z
system:certificates.k8s.io:kubelet-serving-approver                    2022-06-16T15:11:28Z
system:certificates.k8s.io:legacy-unknown-approver                     2022-06-16T15:11:28Z
system:controller:attachdetach-controller                              2022-06-16T15:11:28Z
system:controller:certificate-controller                               2022-06-16T15:11:28Z
system:controller:clusterrole-aggregation-controller                   2022-06-16T15:11:28Z
system:controller:cronjob-controller                                   2022-06-16T15:11:28Z
system:controller:daemon-set-controller                                2022-06-16T15:11:28Z
system:controller:deployment-controller                                2022-06-16T15:11:28Z
system:controller:disruption-controller                                2022-06-16T15:11:28Z
system:controller:endpoint-controller                                  2022-06-16T15:11:28Z
system:controller:endpointslice-controller                             2022-06-16T15:11:28Z
system:controller:endpointslicemirroring-controller                    2022-06-16T15:11:28Z
system:controller:ephemeral-volume-controller                          2022-06-16T15:11:28Z
system:controller:expand-controller                                    2022-06-16T15:11:28Z
system:controller:generic-garbage-collector                            2022-06-16T15:11:28Z
system:controller:horizontal-pod-autoscaler                            2022-06-16T15:11:28Z
system:controller:job-controller                                       2022-06-16T15:11:28Z
system:controller:namespace-controller                                 2022-06-16T15:11:28Z
system:controller:node-controller                                      2022-06-16T15:11:28Z
system:controller:persistent-volume-binder                             2022-06-16T15:11:28Z
system:controller:pod-garbage-collector                                2022-06-16T15:11:28Z
system:controller:pv-protection-controller                             2022-06-16T15:11:28Z
system:controller:pvc-protection-controller                            2022-06-16T15:11:28Z
system:controller:replicaset-controller                                2022-06-16T15:11:28Z
system:controller:replication-controller                               2022-06-16T15:11:28Z
system:controller:resourcequota-controller                             2022-06-16T15:11:28Z
system:controller:root-ca-cert-publisher                               2022-06-16T15:11:28Z
system:controller:route-controller                                     2022-06-16T15:11:28Z
system:controller:service-account-controller                           2022-06-16T15:11:28Z
system:controller:service-controller                                   2022-06-16T15:11:28Z
system:controller:statefulset-controller                               2022-06-16T15:11:28Z
system:controller:ttl-after-finished-controller                        2022-06-16T15:11:28Z
system:controller:ttl-controller                                       2022-06-16T15:11:28Z
system:coredns                                                         2022-06-16T15:11:30Z
system:discovery                                                       2022-06-16T15:11:27Z
system:heapster                                                        2022-06-16T15:11:27Z
system:kube-aggregator                                                 2022-06-16T15:11:27Z
system:kube-controller-manager                                         2022-06-16T15:11:27Z
system:kube-dns                                                        2022-06-16T15:11:27Z
system:kube-scheduler                                                  2022-06-16T15:11:28Z
system:kubelet-api-admin                                               2022-06-16T15:11:27Z
system:metrics-server                                                  2022-06-19T17:45:08Z
system:monitoring                                                      2022-06-16T15:11:27Z
system:node                                                            2022-06-16T15:11:27Z
system:node-bootstrapper                                               2022-06-16T15:11:27Z
system:node-problem-detector                                           2022-06-16T15:11:27Z
system:node-proxier                                                    2022-06-16T15:11:28Z
system:persistent-volume-provisioner                                   2022-06-16T15:11:27Z
system:public-info-viewer                                              2022-06-16T15:11:27Z
system:service-account-issuer-discovery                                2022-06-16T15:11:28Z
system:volume-scheduler                                                2022-06-16T15:11:28Z
view                                                                   2022-06-16T15:11:27Z
weave-net                                                              2022-06-16T15:15:20Z
```

- Vamos acessar essa:

```bash
# kubectl get clusterrole | grep admin
admin                                                                  2022-06-16T15:11:27Z
cluster-admin                                                          2022-06-16T15:11:27Z
system:aggregate-to-admin                                              2022-06-16T15:11:27Z
system:kubelet-api-admin                                               2022-06-16T15:11:27Z
```

- Vamos pegar mais detalhes dessa role

Veja que eu posso fazer de tudo com essa role dentro do cluster, perceba em resources:

```bash
# kubectl describe clusterrole cluster-admin 
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]
```

- Acessando uma outra role para colher mais detalhes:

A ideia aqui e saber que eu posso pegar meu usuario amaury que  foi criado e atribuir a ele essa role de view dentro do cluster de Kubernetes. Para esse caso eu teria priviligeios apenas de `view`, ou seja, para PODS eu so poderia executar verbos como get, list e watch.

```bash
# kubectl describe clusterrole view
Name:         view
Labels:       kubernetes.io/bootstrapping=rbac-defaults
              rbac.authorization.k8s.io/aggregate-to-edit=true
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources                                    Non-Resource URLs  Resource Names  Verbs
  ---------                                    -----------------  --------------  -----
  bindings                                     []                 []              [get list watch]
  configmaps                                   []                 []              [get list watch]
  endpoints                                    []                 []              [get list watch]
  events                                       []                 []              [get list watch]
  limitranges                                  []                 []              [get list watch]
  namespaces/status                            []                 []              [get list watch]
  namespaces                                   []                 []              [get list watch]
  persistentvolumeclaims/status                []                 []              [get list watch]
  persistentvolumeclaims                       []                 []              [get list watch]
  pods/log                                     []                 []              [get list watch]
  pods/status                                  []                 []              [get list watch]
  pods                                         []                 []              [get list watch]
  replicationcontrollers/scale                 []                 []              [get list watch]
  replicationcontrollers/status                []                 []              [get list watch]
  replicationcontrollers                       []                 []              [get list watch]
  resourcequotas/status                        []                 []              [get list watch]
  resourcequotas                               []                 []              [get list watch]
  serviceaccounts                              []                 []              [get list watch]
  services/status                              []                 []              [get list watch]
  services                                     []                 []              [get list watch]
  controllerrevisions.apps                     []                 []              [get list watch]
  daemonsets.apps/status                       []                 []              [get list watch]
  daemonsets.apps                              []                 []              [get list watch]
  deployments.apps/scale                       []                 []              [get list watch]
  deployments.apps/status                      []                 []              [get list watch]
  deployments.apps                             []                 []              [get list watch]
  replicasets.apps/scale                       []                 []              [get list watch]
  replicasets.apps/status                      []                 []              [get list watch]
  replicasets.apps                             []                 []              [get list watch]
  statefulsets.apps/scale                      []                 []              [get list watch]
  statefulsets.apps/status                     []                 []              [get list watch]
  statefulsets.apps                            []                 []              [get list watch]
  horizontalpodautoscalers.autoscaling/status  []                 []              [get list watch]
  horizontalpodautoscalers.autoscaling         []                 []              [get list watch]
  cronjobs.batch/status                        []                 []              [get list watch]
  cronjobs.batch                               []                 []              [get list watch]
  jobs.batch/status                            []                 []              [get list watch]
  jobs.batch                                   []                 []              [get list watch]
  endpointslices.discovery.k8s.io              []                 []              [get list watch]
  daemonsets.extensions/status                 []                 []              [get list watch]
  daemonsets.extensions                        []                 []              [get list watch]
  deployments.extensions/scale                 []                 []              [get list watch]
  deployments.extensions/status                []                 []              [get list watch]
  deployments.extensions                       []                 []              [get list watch]
  ingresses.extensions/status                  []                 []              [get list watch]
  ingresses.extensions                         []                 []              [get list watch]
  networkpolicies.extensions                   []                 []              [get list watch]
  replicasets.extensions/scale                 []                 []              [get list watch]
  replicasets.extensions/status                []                 []              [get list watch]
  replicasets.extensions                       []                 []              [get list watch]
  replicationcontrollers.extensions/scale      []                 []              [get list watch]
  nodes.metrics.k8s.io                         []                 []              [get list watch]
  pods.metrics.k8s.io                          []                 []              [get list watch]
  ingresses.networking.k8s.io/status           []                 []              [get list watch]
  ingresses.networking.k8s.io                  []                 []              [get list watch]
  networkpolicies.networking.k8s.io            []                 []              [get list watch]
  poddisruptionbudgets.policy/status           []                 []              [get list watch]
  poddisruptionbudgets.policy                  []                 []              [get list watch]
```

### Criando um `clusterolebinding` completa

Vamos criar uma `clusterrolebinding` para associar com o `serviceaccount` amaury com a funcao de `clusterole`:

`kubectl create clusterrolebinding nika --serviceaccount=default:amaury --clusterrole=cluster-admin`

- Vamos listar essa clusterolebinding:

```bash
# kubectl get clusterrolebindings.rbac.authorization.k8s.io | grep nika
nika                                                   ClusterRole/cluster-admin                                                          30s
```

- Descrevendo essa clusterolebinding criada:

Isso significa que agora o serviceaccount amaury pode executar tudo que existe de permissoes dentro do clusterole setado como cluster-admin.

```bash
# kubectl describe clusterrolebindings.rbac.authorization.k8s.io nika
Name:         nika
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind            Name    Namespace
  ----            ----    ---------
  ServiceAccount  amaury  default
```

- Veja o poder que o serviceaccount pode fazer dentro do cluster:

```bash
# kubectl describe clusterrole cluster-admin 
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]
```

### Criando um exemplo com `YML`

Vamos primeiro criar esse serviceaccount usando `YML`:

`# kubectl create -f admin-user.yml`

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
```

Agora depois de criado vaos listar isso:

```bash
# kubectl get serviceaccounts -n kube-system admin-user 
NAME         SECRETS   AGE
admin-user   0         57s
```

- Vamos criar nosso `clusterrolebinding` 

`# kubectl create -f cluster-role-binding.yml`

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

- Listando:

```bash
# kubectl get clusterrolebindings.rbac.authorization.k8s.io admin-user  
NAME         ROLE                        AGE
admin-user   ClusterRole/cluster-admin   86s
```

## HELM

Hora de aprender mais sobre o querido `Helm` dentro do Kubernetes, este que e um projeto graduado da CNCF, ou seja, usado em larga escala por diversas empresas e projetos ao redor do mundo em ambientes de producao. Quando se fala em Helm, estamos falando sobre o gerenciador de pacotes do kubernetes. Os pacotes gerenciados pelo Helm, s??o chamados de `charts`, que basicamente s??o formados por um conjunto de manifestos Kubernetes no formato `YAML` e alguns templates que ajudam a manter vari??veis din??micas de acordo com o ambiente.

### Mais sobre HELM

Para as pessoas que estejam  interessadas em aprender mais sobre HELM, fica ligado nesse [link](https://www.cncf.io/projects/helm/) aqui da CNCF.

### O que e `charts` dentro do `HELM`

Charts dentro do HELM nada mais e que do que um conjunto de manifestos do Kubernetes (Services, Deployments, Secrets, CronJobs, RBAC), reuni,os tudo isso para que possamos fazer um unico deploy, a ideia e por isso tudo dentro do `charts` do HELM e quando a gente executar `helm install --charts-name` ele cria tudo de uma unica vez.

Funciona como se fosse um pacote `.deb` dentro do `apt-get`, essa seria uma analogia entre o gerenciador de pacotes do Debian.

### Instalacao do HELM no cluster do Kubernetes 

Para que seja possivel a instalacao siga as etapas abaixo:

`curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash -`

Para verificar se esta correta a sua instalacao digite:

```bash
# helm version
version.BuildInfo{Version:"v3.9.0", GitCommit:"7ceeda6c585217a19a1131663d8cd1f7d641b2a7", GitTreeState:"clean", GoVersion:"go1.17.5"}
```
### Iniciando com Helm Charts

Vamos agora iniciar alguns projetos bem legais com Helm usando os charts do Grafana e do Prometheus.

- Para isso, vamos adicionar os repostirios de ambas as ferramentas com Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
```

- Listando os repositorios que foram adicionados:

```bash
# helm repo list
NAME                    URL
prometheus-community    https://prometheus-community.github.io/helm-charts
grafana                 https://grafana.github.io/helm-charts
```

### Criando a estrutura do Charts

Para que seja possivel criar a estrutura do Helm Charts, execute o seguinte comando:

`# helm create devops`

```bash
# tree devops/
devops/
????????? charts
????????? Chart.yaml
????????? templates
???   ????????? deployment.yaml
???   ????????? _helpers.tpl
???   ????????? hpa.yaml
???   ????????? ingress.yaml
???   ????????? NOTES.txt
???   ????????? serviceaccount.yaml
???   ????????? service.yaml
???   ????????? tests
???       ????????? test-connection.yaml
????????? values.yaml

3 directories, 10 files
```

### Helm hands-on com NGINX

Para que seja possivel treinar mais com Helm, criei um repositorio a parte para que possamos executar outros passos com Hel Chart. A ideia e usar o NGINX para ser deployado com Helm, assim tudo fica mais facil de ser gerenciado com os charts.

Acesse [aqui](https://github.com/Kubernetes-Tutorialz/helm-charts-kubernetes-hands-on) o lab de Hel com NGINX.

### Alguns do Helm utilizados

```bash
# wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.3-linux-amd64.tar.gz
# tar -vxzf helm-v2.11.0-linux-amd64.tar.gz
# cd linux-amd64/
# mv helm /usr/local/bin/
# mv tiller /usr/local/bin/
# helm init
# kubectl create serviceaccount --namespace=kube-system tiller
# kubectl create clusterrolebinding tiller-cluster-role --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
# kubectl patch deployments -n kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
# helm install --namespace=monitoring --name=prometheus --version=7.0.0 --set alertmanager.persistentVolume.enabled=false,server.persistentVolume.enabled=false stableprometheus
# helm list
# helm search grafana
# helm install --namespace=monitoring --name=grafana --version=1.12.0 --set=adminUser=admin,adminPassword=admin,service.type=NodePort stable/grafana
# helm list
# kubectl  get deployments.
# kubectl  get service
# helm delete prometheus
# helm delete grafana
# helm list
# helm reset
```
