# Learnathon – Kubernetes Microservices (Pinger)

A simple microservice-based application built to learn **Docker, Kubernetes, monitoring, service-to-service communication, and canary deployments** as part of the **Juspay SRE Learnathon**.

---

## 📌 Description

**Pinger** is an application that continuously checks whether a given URL is reachable and records:
- Latency
- HTTP response status code
- Response size

The application is designed using a **microservices architecture** and deployed on a **local Kubernetes cluster**.

---

## 🧩 Microservices Architecture

The system consists of **three services**:

- **frontend**
  - Provides the UI
  - Acts as the entry point for users
  - Communicates with backend services

- **pinger**
  - Periodically pings URLs provided by the UI
  - Records availability and latency
  - Runs in two versions: `v1` (stable) and `v2` (new)

- **details**
  - Provides additional details about URLs

---

## ⚙️ Prerequisites

Make sure the following tools are installed:

- Docker
- kubectl
- kind (Kubernetes in Docker)

Install kind:  
https://kind.sigs.k8s.io/

---

## 🚀 Setting up Local Kubernetes Cluster

A sample cluster configuration is available at:

k8s/cluster/kind-cluster-config.yaml


Create the cluster using:
```bash

kind create cluster --config k8s/cluster/kind-cluster-config.yaml

Verify the cluster:

kubectl get nodes --context kind-kind


Expected output:

kind-control-plane   Ready   control-plane
kind-worker          Ready
kind-worker2         Ready
kind-worker3         Ready

🐳 Building Docker Images

Build and load Docker images into the kind cluster using the provided script:

./scripts/build-docker.sh


This script:

Builds Docker images for all services

Loads them directly into the kind cluster

☸️ Deploying the Application

All Kubernetes resources are defined in a single manifest file:

k8s/configs/pinger-all-in-one.yaml


Deploy the application:

kubectl apply -f k8s/configs/pinger-all-in-one.yaml


This creates:

Deployments:

frontend

details

pinger-v1

pinger-v2

Services:

frontend-service

details-service

pinger-v1-service

pinger-v2-service

🔍 Verifying Deployment
Check Pods
kubectl get pods

Check Services
kubectl get svc

Check Deployments
kubectl get deploy

🌐 Accessing the Application

The frontend service is internal (ClusterIP), so use port forwarding to access it locally.

Find the frontend pod:

kubectl get pods | grep frontend


Port forward:

kubectl port-forward <frontend-pod-name> 9000:9000


Open in browser:

http://localhost:9000

🔗 Service-to-Service Communication

The frontend communicates with backend services using Kubernetes internal DNS.

Environment variables in the frontend deployment:

env:
- name: PINGER_BASE_URL
  value: "http://pinger-v1-service:3000"
- name: DETAILS_BASE_URL
  value: "http://details-service:4000"


Kubernetes resolves these service names automatically using internal DNS.

📝 Logs & Debugging

To view logs of any pod:

kubectl logs -f <pod-name>


Example:

kubectl logs -f frontend-xxxxx
