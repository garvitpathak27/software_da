# Kubernetes Deployment for Flask Application

This repository demonstrates the end-to-end deployment of a Flask web application on a Kubernetes cluster (using Minikube). It covers containerizing the application, creating Kubernetes resources, implementing auto-scaling with HPA, rolling updates/rollbacks, logging, and self-healing tests using a load generator.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Setup and Deployment](#setup-and-deployment)
  - [1. Start Kubernetes Cluster](#1-start-kubernetes-cluster)
  - [2. Build and Push the Docker Image](#2-build-and-push-the-docker-image)
  - [3. Create Kubernetes Resources](#3-create-kubernetes-resources)
    - [Deployment](#deployment)
    - [Service](#service)
    - [ConfigMap](#configmap)
  - [4. Implement Auto-Scaling (HPA)](#4-implement-auto-scaling-hpa)
  - [5. Rolling Updates & Rollbacks](#5-rolling-updates--rollbacks)
  - [6. Logging](#6-logging)
  - [7. Self-Healing & Load Generation Tests](#7-self-healing--load-generation-tests)
- [Testing Scenarios](#testing-scenarios)
- [Notes](#notes)

## Prerequisites

- Minikube installed and running.
- kubectl configured for the Minikube cluster.
- Docker installed for building images.
- A Docker Hub account (repository: `<your-docker-username>/hello-world-flask`).

## Assignment Requirements

1. **Setup Kubernetes Cluster**
   - Use Minikube (for local deployment) or Google Kubernetes Engine (GKE), Amazon EKS, or Azure AKS for cloud-based deployment.
   - Ensure the cluster is up and running with at least two worker nodes.

2. **Deploy a Web Application**
   - Use a simple Node.js or Python Flask-based application (or any web app of your choice).
   - Containerize the application using Docker.
   - Push the container image to Docker Hub or a private container registry.

3. **Create Kubernetes Resources**
   - **Deployments:** Deploy the web application using a Kubernetes Deployment with at least 3 replicas.
   - **Services:** Create a Service (NodePort or LoadBalancer) to expose the application.
   - **ConfigMaps & Secrets:** Store environment variables (e.g., database connection string) securely using ConfigMaps and Secrets.

4. **Implement Auto-scaling**
   - Configure Horizontal Pod Autoscaler (HPA) to scale pods based on CPU utilization.
   - Set minimum 2 pods and maximum 5 pods, scaling up when CPU usage exceeds 50%.

5. **Implement Persistent Storage (Optional)**
   - If the application stores data, use Persistent Volume (PV) and Persistent Volume Claim (PVC).
   - Mount the volume in the pod for persistent storage.

6. **Rolling Updates & Rollbacks**
   - Simulate a rolling update by deploying a new version of the application.
   - Perform a rollback in case of a failure.

7. **Logging**
   - Use `kubectl logs` to view application logs.

## Setup and Deployment

### 1. Start Kubernetes Cluster

Start Minikube if it’s not already running:

```bash
minikube start
```

### 2. Build and Push the Docker Image

Clone the repository containing the application code:

```bash
git clone https://github.com/garvitpathak27/software_da.git
cd software_da
```

Build the Docker image:

```bash
docker build -t <your-docker-username>/hello-world-flask:v1 .
```

Push the container image to Docker Hub:

```bash
docker login
docker push <your-docker-username>/hello-world-flask:v1
```

### 3. Create Kubernetes Resources

#### Deployment

View the deployment YAML file:

```bash
cat deployment.yaml
```

Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

#### Service

View the service YAML file:

```bash
cat service.yaml
```

Apply the service:

```bash
kubectl apply -f service.yaml
```

#### ConfigMap

View the config YAML file:

```bash
cat configmap.yaml
```

Apply the ConfigMap:

```bash
kubectl apply -f config.yaml
```

### 4. Implement Auto-Scaling (HPA)

View the HPA YAML file:

```bash
cat hpa.yaml
```

Apply the Horizontal Pod Autoscaler (HPA):

```bash
kubectl apply -f hpa.yaml
kubectl get hpa
```

### 5. Rolling Updates & Rollbacks

Simulate a rolling update:

```bash
kubectl set image deployment/flask-hello-world flask-container=<your-docker-username>/hello-world-flask:v2
```

Check the rollout status:

```bash
kubectl rollout status deployment/flask-hello-world
```

Perform a rollback if needed:

```bash
kubectl rollout undo deployment/flask-hello-world
```

### 6. Logging

To view logs from a running pod:

```bash
kubectl logs <POD_NAME>
```

### 7. Self-Healing & Load Generation Tests

Start a load generator to test autoscaling:

```bash
kubectl run --rm -it --image=busybox load-generator -- /bin/sh
```

Inside the container, run:

```bash
while true; do wget -q -O- http://flask-hello-world-service:5000; done
```

## Testing Scenarios

To ensure the Kubernetes deployment is working as expected, perform the following test cases:

1. **Application Availability Tests**
   - **Test:** Check if the application is accessible via the Kubernetes service.
   - **Command:** 
     ```bash
     kubectl get services 
     curl http://<EXTERNAL-IP>:<PORT>
     ```
   - **Expected Output:** Should return the homepage or API response of the application.

2. **Scaling Tests**
   - **Test:** Trigger high CPU usage to see if the Horizontal Pod Autoscaler (HPA) scales up pods.
   - **Command:** 
     ```bash
     kubectl get hpa 
     kubectl run --rm -it --image=busybox stress-test -- /bin/sh 
     ```
     Inside BusyBox shell:
     ```bash
     while true; do wget -q -O- http://<SERVICE-IP>:<PORT>; done 
     ```
   - **Expected Output:** Number of pods should increase dynamically.
   - **Verification:** 
     ```bash
     kubectl get pods -w 
     ```

3. **Rolling Update & Rollback Test**
   - **Test:** Perform a rolling update and verify zero downtime.
   - **Command:** 
     ```bash
     kubectl set image deployment/<DEPLOYMENT_NAME> <CONTAINER_NAME>=new_image:v2 
     ```
   - **Expected Output:** New version is deployed while keeping the app running.
   - **Test:** Rollback to the previous version in case of failure.
   - **Command:** 
     ```bash
     kubectl rollout undo deployment/<DEPLOYMENT_NAME> 
     ```
   - **Expected Output:** Application reverts to the previous working version.

4. **Pod Failure and Self-Healing Test**
   - **Test:** Manually delete a pod and check if Kubernetes automatically recreates it.
   - **Command:** 
     ```bash
     kubectl delete pod <POD_NAME> 
     ```
   - **Expected Output:** A new pod should be automatically created.
   - **Verification:** 
     ```bash
     kubectl get pods -w 
     ```

5. **Persistent Storage Test (If Implemented)**
   - **Test:** Verify if data persists after pod restart.
   - **Steps:** 
     - Store data in the mounted volume inside the pod. 
     - Delete the pod and check if data is retained.
   - **Command:** 
     ```bash
     kubectl delete pod <POD_NAME> 
     kubectl get pods -w 
     ```
   - **Expected Output:** New pod should start with the same data.

6. **Logging Test**
   - **Test:** Check if application logs are available.
   - **Command:** 
     ```bash
     kubectl logs <POD_NAME> 
     ```
   - **Expected Output:** Should display application logs.

## Notes

- Ensure all commands are executed in the correct context of your Minikube cluster.
- Adjust the deployment configurations as necessary based on your application requirements.

Happy Deploying! 🚀
