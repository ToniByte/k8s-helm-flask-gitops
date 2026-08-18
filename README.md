<div align="center">

T O N I B Y T E

### Kubernetes + Helm Flask Deployment

**From Docker image to Kubernetes with Helm and GitOps-ready structure**

[![Kubernetes](https://img.shields.io/badge/Kubernetes-k3s-blue?logo=kubernetes)](https://k3s.io/)
[![Helm](https://img.shields.io/badge/Helm-3-blue?logo=helm)](https://helm.sh/)
[![Docker](https://img.shields.io/badge/Docker-GHCR-blue?logo=docker)](https://docs.github.com/en/packages)
[![Argo CD](https://img.shields.io/badge/GitOps-Argo%20CD-orange)](https://argo-cd.readthedocs.io/)

</div>

---

# Kubernetes + Helm Flask Deployment

This repository demonstrates a practical DevOps workflow for deploying a containerized Flask application to Kubernetes using Helm.

It covers the real path used in modern infrastructure:

```text
Application code
  → Docker image
  → Container registry (GHCR)
  → Helm chart
  → Kubernetes (k3s)
  → GitOps-ready delivery with Argo CD
```

# Goal  
Show hands-on experience with:  

• packaging an application as a Docker image  
• storing images in GitHub Container Registry  
• writing Kubernetes deployment manifests through Helm  
• installing and upgrading releases with Helm  
• preparing the project for GitOps with Argo CD  

# Architecture
```text
Client
  ↓
Kubernetes Service (NodePort / port-forward)
  ↓
Flask Pods (Deployment)
  ↓
Image pulled from ghcr.io
```

# Repository Structure
```text
k8s-helm-flask-gitops/
├── charts/
│   └── flask-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           ├── service.yaml
│           └── _helpers.tpl
├── argocd/
│   └── application.yaml
└── README.md
```

## What This Project Includes

| Component                    | Purpose                                    |
| ---------------------------- | ------------------------------------------ |
| Docker image in GHCR         | Application artifact                       |
| Helm chart                   | Templated Kubernetes deployment            |
| Deployment                   | Runs application replicas                  |
| Service                      | Exposes the application inside the cluster |
| Argo CD Application manifest | GitOps deployment definition               |


## Prerequisites

• Kubernetes cluster (k3s is fine)  
• kubectl  
• Helm 3  
• Docker image published to GHCR  

Example image:  
```text
ghcr.io/tonibyte/flask-app:latest
```

## Helm Chart Configuration  

Main settings are in charts/flask-app/values.yaml:  

```YAML
replicaCount: 2

image:
  repository: ghcr.io/tonibyte/flask-app
  tag: latest
  pullPolicy: Always

service:
  type: NodePort
  port: 80
  targetPort: 5000
  nodePort: 30080
```

## Deploy with Helm

```bash

helm upgrade --install flask-app ./charts/flask-app

```
  
Check resources:  

```bash
kubectl get pods
kubectl get svc
helm list
```

Access the app locally:  

```bash
kubectl port-forward service/flask-app 8080:80
```

Open in browser: 

```text
http://localhost:8080
```

# Upgrade Example  

Change replicas:  

```bash
helm upgrade flask-app ./charts/flask-app --set replicaCount=3
```

Change image tag:  

```bash
helm upgrade flask-app ./charts/flask-app --set image.tag=v2
```

Rollback:  

```bash
helm history flask-app
helm rollback flask-app 1
```

# GitOps with Argo CD  

This repository is ready for Argo CD.  

Example Application manifest is in argocd/application.yaml:  

```YAML

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: flask-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/tonibyte/k8s-helm-flask-gitops.git
    targetRevision: main
    path: charts/flask-app
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    syncOptions:
      - CreateNamespace=true

```

## Apply it:

```bash

kubectl apply -f argocd/application.yaml

```

Then sync the application in Argo CD UI or CLI.  

# Why This Matters  

This project demonstrates practical understanding of modern delivery:

| Practice               | Implementation in this repo |
| ---------------------- | --------------------------- |
| Containerization       | Docker image                |
| Artifact storage       | GHCR                        |
| Declarative deployment | Kubernetes manifests        |
| Packaging / releases   | Helm                        |
| Desired-state delivery | Argo CD / GitOps            |

This is the same core flow used by many production teams, simplified for a portfolio-ready setup.

## Skills Demonstrated

• Kubernetes Deployments and Services  
• Helm charts, values, install/upgrade/rollback  
• Image delivery through a container registry  
• Git-based deployment structure  
• GitOps application definition with Argo CD  

## License  

Educational / portfolio project. 
