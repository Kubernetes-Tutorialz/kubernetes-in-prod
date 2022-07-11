# Kubernetes

## Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
  - [Ingress (Abertura da Aula)](#ingress-abertura-da-aula)
    - [Ingress (Parte01)](#ingress-parte01)
      - [Criando os arquivos de deployments](#criando-os-arquivos-de-deployments)
      - [Criando os arquivos de services](#criando-os-arquivos-de-services)
      - [Alguns termos importantes](#alguns-termos-importantes)


## Ingress (Abertura da Aula)

Devemos pensar que Ingress  é uma forma de expor nosso service, para além do cluster do kubernetes. É uma forma de os usuarios acessarem de fora do cluster os servicos.

### Ingress (Parte01)

Vamos criar nosso primeiro arquivo para exemplificar nosso ingress.

#### Criando os arquivos de deployments

`vim app1.yml`

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - image: dockersamples/static-site
        name: app1
        env:
        - name: AUTHOR
          value: GIROPOPS
        ports:
        - containerPort: 80
```

Criando outro arquivo de teste com a mesma ideia de conteudo:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - image: dockersamples/static-site
        name: app2
        env:
        - name: AUTHOR
          value: nika
        ports:
        - containerPort: 80
```

- Criando os arquivos dentro do cluster de kubernetes:

`# kubectl create -f app1.yml` e `# kubectl create -f app2.yml`

#### Criando os arquivos de services

`# kubectl create -f svc-app1.yml`

```yml
apiVersion: v1
kind: Service
metadata:
  name: appsvc1
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: app1
```

- E agora criando mais um arquivo de service:

`# kubectl create -f svc-app2.yml`

```yml
apiVersion: v1
kind: Service
metadata:
  name: appsvc2
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: app2
```

- Vamos agora criar outro arquivo de deployment que e de extrema importancia:

`# kubectl create -f default-backend.yml`

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: default-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: default-backend
  template:
    metadata:
      labels:
        app: default-backend
    spec:
      terminationGracePeriodSeconds: 60
      containers:
      - name: default-backend
        image: gcr.io/google_containers/defaultbackend:1.0
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 30
          timeoutSeconds: 5
        ports:
        - containerPort: 8080
        resources:
          limits:
            cpu: 10m
            memory: 20Mi
          requests:
            cpu: 10m
            memory: 20Mi
```

#### Alguns termos importantes

- `terminationGracePeriodSeconds` => Tempo em segundos que ele irá aguardar o pod ser finalizado com o sinal SIGTERM, antes de realizar a finalização forçada com o sinal de SIGKILL.
- `livenessProbe` => Verifica se o pod continua em execução, caso não esteja, o kubelet irá remover o contêiner e iniciará outro em seu lugar.

- Agora temos que criar mais um arquivo de services que vai servir de backend para nossas apps.

`kubectl create -f default-backend-service.yaml -n ingress `

```yml
apiVersion: v1
kind: Service
metadata:
  name: default-backend
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: default-backend
```


