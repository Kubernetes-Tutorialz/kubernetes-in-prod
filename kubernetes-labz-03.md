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

### Deployments
Toda vez que eu crio um deployment sabemos que ele vai criar um replicaset, o deployment que gerencia o replicaset.

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
  replicas: 1
  selector:
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