# Kubernetes

## Sumario

- [Kubernetes](#kubernetes)
  - [Sumario](#sumario)
  - [Ingress (Abertura da Aula)](#ingress-abertura-da-aula)
    - [Ingress (Parte01)](#ingress-parte01)
      - [Criando os arquivos de deployments](#criando-os-arquivos-de-deployments)
      - [Criando os arquivos de services](#criando-os-arquivos-de-services)


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

- E agopra criando mais um arquivo de service:

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