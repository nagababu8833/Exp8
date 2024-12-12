Here is the complete README.md file with the content provided:

# EXPERIMENT NO.: 8  

## AIM: Integrate Kubernetes and Docker  

---

## Step-by-Step Guide for Integrating Kubernetes and Docker  

---

### Step 1: Install Prerequisites  

Ensure the following tools are installed on your system:  

- Docker: To build and manage container images.  
- Kubernetes (kubectl): To interact with your Kubernetes cluster.  
- Minikube (or Kubernetes Cluster): To create a local Kubernetes environment (if not using a remote cluster).  
- Container Registry Account: For example, Docker Hub or Google Container Registry.  

Commands to install (for Linux/Ubuntu as an example):  
```bash
# Docker Installation
sudo apt-get update
sudo apt-get install -y docker.io

# Kubernetes kubectl Installation
sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

# Minikube Installation
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube


---

Step 2: Start Minikube Cluster

Set up a local Kubernetes cluster using Minikube (or use an existing cluster).

minikube start

Confirm the cluster is running:

kubectl cluster-info


---

Step 3: Create a Dockerfile

Create a Dockerfile to define the container image for your application. For example, for a simple Python web server:

# Base Image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy application files
COPY app.py /app

# Install dependencies
RUN pip install flask

# Define the command to run the application
CMD ["python", "app.py"]

Create the app.py file:

from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Kubernetes and Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)


---

Step 4: Build the Docker Image

Use Docker to build the container image:

docker build -t my-docker-k8s-app:v1 .

Check the image:

docker images


---

Step 5: Push the Image to a Registry

Push the image to Docker Hub (or another registry).
Login to Docker Hub:

docker login

Push the image:

docker tag my-docker-k8s-app:v1 <your-dockerhub-username>/my-docker-k8s-app:v1
docker push <your-dockerhub-username>/my-docker-k8s-app:v1


---

Step 6: Create Kubernetes Deployment and Service

Define Kubernetes manifests for deploying the application.

Deployment File (deployment.yaml):

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-docker-k8s-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-docker-k8s-app
  template:
    metadata:
      labels:
        app: my-docker-k8s-app
    spec:
      containers:
      - name: my-docker-k8s-app
        image: <your-dockerhub-username>/my-docker-k8s-app:v1
        ports:
        - containerPort: 5000

Service File (service.yaml):

apiVersion: v1
kind: Service
metadata:
  name: my-docker-k8s-service
spec:
  selector:
    app: my-docker-k8s-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: NodePort

Apply the manifests:

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml


---

Step 7: Access the Application

Find the NodePort assigned to the service:

kubectl get service my-docker-k8s-service

Access the application in your browser or using curl:

curl http://<minikube-ip>:<NodePort>

To get Minikube IP:

minikube ip


---

Step 8: Monitor and Manage the Application

Check pods:

kubectl get pods

View logs:

kubectl logs <pod-name>

Scale replicas:

kubectl scale deployment my-docker-k8s-app --replicas=5

Rolling updates: Update the image in the deployment.yaml file to a new version, then apply:

kubectl apply -f deployment.yaml


---

Step 9: Automate with CI/CD

Integrate these steps in a CI/CD pipeline (e.g., using Jenkins, GitHub Actions, or GitLab CI):

1. Automate Docker image builds and pushes.


2. Use kubectl commands or Helm charts in the pipeline to update the Kubernetes cluster.




---

By following these steps, you can successfully integrate Kubernetes and Docker for efficient container management and orchestration.



