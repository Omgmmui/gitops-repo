# GitOps Repository

This repository manages Kubernetes application deployments using ArgoCD with GitOps principles.

## Repository Structure

```
gitops-repo/
├── apps/                    # ArgoCD Application manifests
│   └── my-app/
│       ├── dev.yaml        # Application for dev environment
│       ├── staging.yaml    # Application for staging environment
│       └── prod.yaml       # Application for prod environment
├── charts/                 # Helm charts
│   └── my-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           └── service.yaml
└── overlays/               # Kustomize overlays per environment
    ├── dev/
    │   ├── kustomization.yaml
    │   └── patches/
    │       ├── deployment-patch.yaml
    │       └── service-patch.yaml
    ├── staging/
    │   ├── kustomization.yaml
    │   └── patches/
    │       └── deployment-patch.yaml
    └── prod/
        ├── kustomization.yaml
        └── patches/
            └── deployment-patch.yaml
```

## Environments

### Dev Environment
- Replicas: 1
- Service Type: NodePort
- Namespace: dev

### Staging Environment
- Replicas: 2
- Service Type: ClusterIP
- Namespace: staging

### Prod Environment
- Replicas: 4
- Service Type: ClusterIP
- Namespace: prod

## Usage

### Prerequisites
- Kubernetes cluster
- ArgoCD installed
- kubectl configured

### Access ArgoCD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Login with:
- Username: `admin`
- Password: Get from `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

### Deploy Applications

Apply ArgoCD Applications:
```bash
kubectl apply -f apps/my-app/
```

### Promotion Flow

Changes are promoted through environments via Git:

1. **Dev**: Make changes to `overlays/dev/`
2. **Staging**: Merge to `overlays/staging/` via PR
3. **Prod**: Merge to `overlays/prod/` via PR

## Technologies Used

- **ArgoCD**: GitOps continuous delivery
- **Helm**: Package management
- **Kustomize**: Configuration management
- **Kubernetes**: Container orchestration