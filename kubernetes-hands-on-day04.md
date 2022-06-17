## Kubernetes

### Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
- [Volumes no Kubernetes](#volumes-no-kubernetes)
  - [Tipos de volumes](#tipos-de-volumes)
  - [Criando nosso volume](#criando-nosso-volume)

## Volumes no Kubernetes

Hoje vamos aprender mais sobre volumes no Kubernetes, existem dois tipos de volumes:

### Tipos de volumes

- EmptyDir (esta conectado com o POD e sempre inicia vazio, criado junto com o POD).
  - nao existe persistencia de dados com esse tipo de volume, os dados sao apagados.

- Persistent Volume ()

### Criando nosso volume

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

