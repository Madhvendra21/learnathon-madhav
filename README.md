# Learnathon – Kubernetes Microservices (Pinger)

This repository contains a **microservices-based application** built as part of the **Juspay SRE Learnathon**.  
The project demonstrates **Docker, Kubernetes, monitoring foundations, service-to-service communication, and versioned deployments**.

---

## 📌 Project Overview

**Pinger** is an application that continuously checks whether a given URL is reachable and records:
- Latency
- HTTP response status code
- Response size

The application is deployed on a **local Kubernetes cluster** using `kind`.

---

## 🧩 Microservices Architecture

The system consists of **three microservices**:

### 1. Frontend
- Provides the user interface
- Acts as the entry point for users
- Communicates with backend services

### 2. Pinger
- Periodically pings URLs provided by the frontend
- Records availability and latency
- Deployed in **two versions**:
  - `pinger-v1` (stable version)
  - `pinger-v2` (new version for rollout experiments)

### 3. Details
- Provides additional information about URLs

---

## ⚙️ Prerequisites

Ensure the following tools are installed:

- Docker
- kubectl
- kind (Kubernetes in Docker)

Install kind from:  
https://kind.sigs.k8s.io/

---

## 🚀 Setting Up Local Kubernetes Cluster

Cluster configuration is defined in:

k8s/cluster/kind-cluster-config.yaml


Create the cluster using:

```bash
kind create cluster --config k8s/cluster/kind-cluster-config.yaml

Verify cluster status:

kubectl get nodes --context kind-kind


Expected output:

kind-control-plane   Ready   control-plane
kind-worker          Ready
kind-worker2         Ready
kind-worker3         Ready

🐳 Building Docker Images

Build and load Docker images into the kind cluster:

./scripts/build-docker.sh


This script:

Builds images for all services

Loads them into the local Kubernetes cluster

☸️ Deploying the Application

All Kubernetes resources are defined in a single manifest:

k8s/configs/pinger-all-in-one.yaml


Deploy the application:

kubectl apply -f k8s/configs/pinger-all-in-one.yaml


This creates:

Deployments: frontend, details, pinger-v1, pinger-v2

Services: frontend-service, details-service, pinger-v1-service, pinger-v2-service

🔍 Verifying Deployment

Check pods:

kubectl get pods


Check services:

kubectl get svc


Check deployments:

kubectl get deploy

🌐 Accessing the Application

Since services use ClusterIP, access the frontend via port forwarding.

Find the frontend pod:

kubectl get pods | grep frontend


Forward the port:

kubectl port-forward <frontend-pod-name> 9000:9000


Open in browser:

http://localhost:9000

🔗 Service-to-Service Communication

The frontend communicates with backend services using Kubernetes internal DNS.

Environment variables used in the frontend deployment:

PINGER_BASE_URL=http://pinger-v1-service:3000
DETAILS_BASE_URL=http://details-service:4000


Kubernetes resolves service names automatically using internal DNS.

📝 Logs and Debugging

View logs of any pod:

kubectl logs -f <pod-name>


Example:

kubectl logs -f frontend-xxxxx
