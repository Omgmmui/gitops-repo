# GitOps Repository

A GitOps repository for managing Kubernetes application deployments using the Kubernetes NGINX image.

## Project Overview

This repository contains Kubernetes manifests for deploying a sample application called `my-app` using the NGINX image.

## Repository Structure

```
gitops-repo/
├── apps/
│   └── my-app/
│       ├── deployment.yaml  # Kubernetes Deployment manifest
│       └── service.yaml     # Kubernetes Service manifest
└── README.md
```

## What Was Done

### 1. Initial Setup
- Created a GitOps repository structure following best practices
- Organized Kubernetes manifests under `apps/my-app/` directory

### 2. Kubernetes Manifests Added
- **Deployment** (`apps/my-app/deployment.yaml`): Defines a Kubernetes Deployment with:
  - Application name: `my-app`
  - NGINX container image
  - Container port: 80
  - Initial replica count: 1

- **Service** (`apps/my-app/service.yaml`): Defines a Kubernetes Service with:
  - Service type: NodePort
  - Exposed port: 80
  - NodePort: 30007 for external access
  - Selector targeting `my-app` pods

### 3. Scaling Changes
- **Commit 2**: Scaled the deployment from 1 to 4 replicas
- **Commit 3**: Scaled the deployment back down to 1 replica

## Commit History

| Commit | Description |
|--------|-------------|
| `719bb9c` | Added Kubernetes manifests |
| `b0c7cbd` | Scale to 4 replicas |
| `c678ff5` | Scale down to 1 |

## Usage

### Apply the manifests to your cluster:

```bash
kubectl apply -f apps/my-app/
```

### Verify the deployment:

```bash
kubectl get deployment my-app
kubectl get service my-app-service
```

## Technologies Used

- **Kubernetes**: Container orchestration
- **GitOps**: Infrastructure as Code approach
- **NGINX**: Web server container image
