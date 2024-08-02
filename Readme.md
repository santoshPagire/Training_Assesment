## Project Overview

The application is composed of three microservices:
- Front-End Service
- Product Catalog Service
- Order Processing Service

The goal is to automate the deployment and configuration of these services across development, testing, and production environments.


## Git Repository Setup

### Repository Creation
1. Create a Git repository to store all project files, including:
   - Git Reposatory
   - Dockerfiles
   - Kubernetes manifests

### Branching Strategy
1. **Branches**:
   - `Production` (Production)
   - `development` (Development)
   - `testing` (Testing)

![alt text](<images/Screenshot from 2024-08-02 15-44-23.png>)
---


2. **Merging Strategy**:
   - Develop features and fixes in the `development` branch.
   - Merge changes from `development` to `testing` for testing purposes.
   - After successful testing, merge `testing` changes into `Production` for production deployment.
   
![alt text](<images/Screenshot from 2024-08-02 18-00-06.png>)
![alt text](<images/Screenshot from 2024-08-02 18-01-54.png>)

## Dockerize Microservices

### Dockerfile Creation

1. Create a `Dockerfile` for each microservice:
   - `frontend/Dockerfile`
   - `product_catlog/Dockerfile`
   - `order_proc/Dockerfile`

> frontend/Dockerfile
```yml
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```
> product_catlog/Dockerfile
```yml
FROM postgres:latest
ENV POSTGRES_USER=user
ENV POSTGRES_PASSWORD=password
ENV POSTGRES_DB=mydatabase
```
> order_proc/Dockerfile
```yml
FROM node:latest

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["node", "index.js"]

```

### Image Building and Management
1. Build Docker images for each microservice:
```bash
docker build -t santoshpagire/frontend:latest 
docker build -t santoshpagire/product-catlog:latest
docker build -t santoshpagire/order_proc:latest 
```
![alt text](<images/Screenshot from 2024-08-02 16-11-34.png>)
![alt text](<images/Screenshot from 2024-08-02 15-51-18.png>)
![alt text](<images/Screenshot from 2024-08-02 15-56-51.png>)

2. Push images to a container registry (e.g., Docker Hub):
```bash
docker push santoshpagire/frontend:latest 
docker push santoshpagire/product-catlog:latest
docker push santoshpagire/order_proc:latest
```
![alt text](<images/Screenshot from 2024-08-02 16-12-31.png>)
![alt text](<images/Screenshot from 2024-08-02 16-08-09.png>)
![alt text](<images/Screenshot from 2024-08-02 16-06-19.png>)

---


### Kubernetes Deployment

### Kubernetes Manifests
1. Create Kubernetes manifests for each microservice:
   - `frontend-deployment.yaml`
   - `catalog-deployment.yaml`
   - `order-processing-deployment.yaml`

> `frontend-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: santoshpagire/frontend:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer

```
> orderproc-deployment.yaml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: orderproc-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: orderproc
  template:
    metadata:
      labels:
        app: orderproc
    spec:
      containers:
      - name: orderproc
        image: santoshpagire/order_proc:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: orderproc-service
spec:
  selector:
    app: orderproc
  ports:
    - protocol: TCP
      port: 81
      targetPort: 80
  type: LoadBalancer
```
> productcatlog-deployment.yaml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: productcatlog-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: productcatlog
  template:
    metadata:
      labels:
        app: productcatlog
    spec:
      containers:
      - name: productcatlog
        image: santoshpagire/product-catlog:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: productcatlog-service
spec:
  selector:
    app: productcatlog
  ports:
    - protocol: TCP
      port: 82
      targetPort: 80
  type: LoadBalancer
```

### Deployment to Kubernetes
1. Apply the Kubernetes manifests:
```bash
kubectl apply -f Deployment/frontend-deployment.yaml
kubectl apply -f Deployment/orderproc-deployment.yaml
kubectl apply -f Deployment/productcatlog-deployment.yaml
```
![alt text](<images/Screenshot from 2024-08-02 17-45-19.png>)

---


2. Verify the deployment:
```bash
kubectl get pods
kubectl get services
```
![alt text](<images/Screenshot from 2024-08-02 17-45-47.png>)
---


### OutPut

![alt text](<images/Screenshot from 2024-08-02 17-18-22.png>)
---

 