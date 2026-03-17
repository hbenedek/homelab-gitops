# Homelab GitOps

Kubernetes manifests deployed via ArgoCD.

## Structure

```
manifests/
├── root-app.yaml          # Main Application
├── app-nginx.yaml         # nginx app
├── nginx/
│   └── deployment.yaml
└── postgres/
    └── deployment.yaml   # Not yet deployed
```

## Quick Start

### 1. Update Repo URL

Edit `root-app.yaml` and `app-nginx.yaml`, replace `YOUR_USERNAME` with your GitHub username.

### 2. Push to GitHub

```bash
git remote add origin https://github.com/YOUR_USERNAME/homelab-apps.git
git branch -M main
git push -u origin main
```

### 3. Create ArgoCD Application

```bash
argocd app create homelab \
  --repo https://github.com/YOUR_USERNAME/homelab-apps.git \
  --path . \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd
```

Or via UI: ArgoCD → + New App

## Deploy Changes

```bash
git add . && git commit -m "update" && git push
```

ArgoCD auto-syncs if enabled, otherwise:

```bash
argocd app sync homelab
```

## Common Commands

```bash
argocd app list
argocd app get homelab
argocd app sync homelab
argocd app delete <name>
```
