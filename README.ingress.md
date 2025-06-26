Instead of NodePort service if you want to expose service using Ingress follow these instructions

# 🐶🐱 Dog-Cat Classifier – FastAPI on Minikube with Ingress

This project runs a FastAPI-based dog-cat image classifier in a local Kubernetes (Minikube) cluster using an Ingress controller for clean URL access.

---

## 📦 Requirements

- Docker
- Minikube
- kubectl
- FastAPI app (served on port 9000)
- Kubernetes manifests:
  - `deployment.yaml`
  - `service.yaml`
  - `ingress.yaml`

---

## 🛠 Setup Steps

### 1. Start Minikube

```bash
minikube start
```
### 2. Point Docker CLI to Minikube’s Docker daemon

eval $(minikube docker-env)
### 3. Build the Docker Image

docker build -t dogcat-classifier:latest .

### 4. Enable the Ingress Controller

minikube addons enable ingress
kubectl get pods -n ingress-nginx
Wait until the ingress-nginx-controller pod is in Running state.

### 5. Create Kubernetes Manifests
Create a file named deployment.yaml with the following contents:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: dogcat-classifier-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: dogcat-classifier
  template:
    metadata:
      labels:
        app: dogcat-classifier
    spec:
      containers:
        - name: dogcat-classifier
          image: dogcat-classifier:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9000

Create a file named service.yaml with the following contents:


apiVersion: v1
kind: Service
metadata:
  name: dogcat-classifier-service
spec:
  type: ClusterIP
  selector:
    app: dogcat-classifier
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9000


Create a file named ingress.yaml with the following contents:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dogcat-classifier-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: dogcat.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: dogcat-classifier-service
                port:
                  number: 80
```
### 6. Apply All Kubernetes Resources

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
### 7. Map Ingress Host to Minikube IP

minikube ip
Edit your /etc/hosts file:


sudo nano /etc/hosts
Add this line at the bottom (replace with your actual Minikube IP):


192.168.49.2  dogcat.local
### 8. Access the App
Open your browser and go to:


http://dogcat.local
You should see your FastAPI app or documentation UI if enabled.