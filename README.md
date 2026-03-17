# Homelab GitOps

GitOps-managed Kubernetes cluster deployed via ArgoCD.

## Overview

This repo contains Kubernetes manifests that are automatically deployed to the homelab cluster using ArgoCD.

```
GitHub Repo → ArgoCD → K3s Cluster (Beelink EQ14)
```

## Cluster Info

| Item | Value |
|------|-------|
| Hardware | Beelink EQ14 (Intel N150, 16GB RAM, 500GB NVMe) |
| OS | Ubuntu Server 24.04 LTS |
| Kubernetes | K3s v1.34.5 |
| IP | 192.168.1.17 |

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Beelink EQ14                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   K3s       │  │  Ingress-  │  │      ArgoCD        │ │
│  │ (K8s)       │──│  Nginx     │──│   (GitOps)          │ │
│  │             │  │            │  │                     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
│         │                │                   │              │
│         └────────────────┴───────────────────┘              │
│                         │                                  │
│              ┌──────────┴──────────┐                      │
│              │ Local Path           │                      │
│              │ Provisioner         │                      │
│              │ (Storage)           │                      │
│              └─────────────────────┘                      │
└─────────────────────────────────────────────────────────────┘
```

## Installed Components

| Component | Version | Purpose |
|-----------|---------|---------|
| K3s | 1.34.5 | Kubernetes distribution |
| Ingress-Nginx | 1.11.1 | HTTP ingress controller |
| ArgoCD | 3.3.4 | GitOps continuous delivery |
| kube-prometheus-stack | Latest | Prometheus + Grafana monitoring |

## Applications

### nginx
- Simple nginx deployment for testing
- Namespace: `web`
- Access: http://192.168.1.17:32268

### postgres (not deployed yet)
- PostgreSQL with persistent storage
- Namespace: `data`
- PVC: 1Gi local-path storage

## Managing Apps

### Add a New App

1. Create a new folder (e.g., `myapp/`)
2. Add Kubernetes manifests
3. Either:
   - Add to root-app.yaml as additional source
   - Create standalone Application in ArgoCD

### Deploy Changes

1. Edit manifests locally
2. Commit and push:
   ```bash
   git add .
   git commit -m "description"
   git push
   ```
3. ArgoCD automatically syncs (if automated sync is enabled)

### Manual Sync

```bash
argocd app sync <app-name>
```

### Check Status

```bash
argocd app get <app-name>
```

## Common Commands

```bash
# List apps
argocd app list

# Get app details
argocd app get homelab

# Sync app
argocd app sync homelab

# Delete app
argocd app delete <app-name>

# Get all pods
kubectl get pods -A

# Get services
kubectl get svc -A
```

## Accessing Services

| Service | URL | Credentials |
|---------|-----|-------------|
| ArgoCD | http://192.168.1.17:30157 | admin / (get from secret) |
| Grafana | http://192.168.1.17:31720 | admin / Zm64ILbcHYgQ8ycTpuhi3t6tC3fSr1FJT4ipk8Pr |
| nginx | http://192.168.1.17:32268 | - |

## Backup

### K3s State
```bash
sudo cp /var/lib/rancher/k3s/server/db/state.db /backup/k3s-$(date +%Y%m%d).db
```

### ArgoCD Applications
```bash
kubectl get applications -n argocd -o yaml > backup/applications.yaml
```

## Troubleshooting

```bash
# Check pod logs
kubectl logs <pod-name> -n <namespace>

# Describe resource
kubectl describe <resource> <name> -n <namespace>

# View ArgoCD app events
kubectl get events -n argocd --field-selector involvedObject.name=<app-name>

# Restart K3s
sudo systemctl restart k3s
```

## Security Notes

- GitHub token is stored in ArgoCD repo credentials (not in Git)
- Change default passwords before production use
- Consider enabling HTTPS for ArgoCD and Grafana
