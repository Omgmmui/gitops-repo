# GitOps Repository with ArgoCD

This repository implements a GitOps deployment model where the desired state of Kubernetes applications is stored in Git and ArgoCD continuously reconciles the live cluster state.

## Repository Structure

```
gitops-repo/
├── apps/                           # ArgoCD Application manifests
│   └── my-app/
│       ├── dev.yaml               # Dev environment Application
│       ├── staging.yaml           # Staging environment Application
│       ├── prod.yaml              # Prod environment Application
│       └── applicationset.yaml    # ApplicationSet for auto-generation
├── charts/                        # Helm charts
│   └── my-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           └── service.yaml
├── overlays/                      # Kustomize overlays per environment
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   └── patches/
│   ├── staging/
│   │   └── patches/
│   └── prod/
│       └── patches/
├── sealed-secrets/               # Sealed Secrets for secure Git storage
│   └── README.md
├── .github/workflows/            # GitHub Actions for automation
│   └── update-image-tag.yaml
└── argocd-notifications-configmap.yaml
```

## Environments

| Environment | Replicas | Service Type | Namespace | Image Tag |
|-------------|----------|--------------|-----------|-----------|
| Dev         | 1        | NodePort     | dev       | latest    |
| Staging     | 2        | ClusterIP    | staging   | stable    |
| Prod        | 4        | ClusterIP    | prod      | stable    |

## GitOps Workflow

### 1. Changes Flow Through PRs
- All changes made to the GitOps repository
- Create PR for any modification
- After merge, ArgoCD automatically syncs changes to the cluster

### 2. ArgoCD Configuration
- **Self-Heal**: Enabled - ArgoCD corrects any manual cluster changes
- **Auto-Prune**: Enabled - Removes resources no longer in Git
- **Sync Options**: Creates namespaces if needed

### 3. Promotion Path
```
Dev -> Staging -> Prod
```
Changes are promoted through environments via Git PRs.

## Components Installed

### ArgoCD
- Installed via Helm chart in `argocd` namespace
- UI accessible via NodePort 30080
- Login: admin (password from secret)

### Sealed Secrets
- Controller installed in kube-system namespace
- Enables secure secret storage in Git

## Usage

### Access ArgoCD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Access at: http://localhost:8080

### Login
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Update Application Image
1. Trigger `Update Image Tag` workflow
2. Specify image tag and target environment
3. Workflow commits changes to Git
4. ArgoCD syncs the change automatically

### Monitor Sync Status
```bash
kubectl get applications -n argocd
```

## Slack Notifications

Configured to send notifications to `#argocd-notifications` for:
- Sync Succeeded
- Sync Failed
- Out of Sync

## Best Practices Implemented

- Separate Git branches/directories for each environment
- Automated sync with self-heal enabled
- All changes go through PR (enforced via GitHub branch protection)
- Secrets stored using Sealed Secrets
- ApplicationSet for automated Application generation