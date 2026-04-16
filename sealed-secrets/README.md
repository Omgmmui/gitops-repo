# Sealed Secrets

This directory contains sealed secrets for GitOps management.

## Overview

Sealed Secrets allows encrypting Kubernetes secrets so they can be safely stored in Git. Only the Sealed Secrets controller in the cluster can decrypt them.

## Usage

### Prerequisites
- Sealed Secrets controller installed in cluster
- kubeseal CLI tool installed

### Creating a New Sealed Secret

1. Create a Kubernetes secret:
```bash
kubectl create secret generic my-secret --from-literal=key=value --dry-run=client -o yaml > secret.yaml
```

2. Seal the secret using the controller's public certificate:
```bash
kubeseal --cert cert.pem < secret.yaml > sealed-secret.yaml
```

3. Add the sealed secret to this directory and commit to Git.

### Obtaining the Certificate

The public key certificate can be obtained from the running controller:
```bash
kubectl get secret -n kube-system sealed-secrets-controller-tls -o jsonpath='{.data.tls\.crt}' | base64 -d > cert.pem
```

## Example

The `example-secret.yaml` contains a plain secret (for demonstration).
The `sealed-secret-template.yaml` contains a sealed version that can be safely committed to Git.

## Best Practices

- Always use sealed secrets for any sensitive data
- Never commit plain secrets to Git
- Rotate sealed secrets periodically
- Backup the private key used by the controller