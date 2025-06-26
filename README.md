# Dog Cat Classifier

This is a Dog Cat Classifier that uses image classification to distinguish between images of dogs and cats.

## Getting Started

### Prerequisites

Before you begin, make sure you have [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) and [Docker](https://docs.docker.com/get-docker/) installed on your machine.

### Setting up a Virtual Environment

1. Create a virtual environment using conda or python venv:

    ```bash
    conda create -p ./env python=3.9
    ```

2. Activate the virtual environment:

    ```bash
    conda activate ./env
    ```

3. Install the required dependencies:

    ```bash
    pip install -r requirements.txt
    ```

## Containerization with Docker

### Building the Docker Image

To containerize the Dog Cat Classifier using Docker, follow these steps:

1. Create a Dockerfile in the root of your project with the following content:

    ```dockerfile
    FROM python:3.9
    COPY . /app
    WORKDIR /app
    RUN pip3 install -r requirements.txt
    EXPOSE $PORT
    CMD ["sh", "-c", "uvicorn clientApp:app --host 0.0.0.0 --port ${PORT:-9000} --workers 4"]
    ```

2.  Install Minikube on mac
```
brew install minikube
```
4.  start the minikube cluster
```
minikube start
```

5. Point Docker CLI to Minikube's Docker daemon
```eval $(minikube docker-env)```
This ensures Docker builds the image inside Minikube's internal Docker environment instead of local MAC 

6. Build the Docker image
```docker build -t dogcat-classifier:latest .```

7. Deploy to Kubernetes
```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```
8.  Check Status

kubectl get pods
kubectl get services
Ensure the pod status is Running. If not, use:

kubectl describe pod <pod-name>
kubectl logs <pod-name>

9. Access the App
Option 1: Use Minikube to open the service in a browser

minikube service dogcat-classifier-service

Option 2: Use the direct URL

minikube service dogcat-classifier-service --url
Example output:

Edit
http://127.0.0.1:49678
Copy-paste this into your browser
Now, your Dog Cat Classifier is running inside a Docker container.

Feel free to explore and enhance the classifier as needed. Happy classifying! 🐶🐱


10. Access minikube dashboard if you want to visualize pods and other statuses
minikube dashboard

11. Cleanup

To delete all resources:

kubectl delete -f deployment.yaml
kubectl delete -f service.yaml

To stop Minikube:

minikube stop
