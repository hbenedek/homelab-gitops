# GitOps Repo Setup

## 1. Push to GitHub

```bash
cd /home/harsanyi/projects/k8s/manifests

# Create a repo on GitHub first, then:
git remote add origin https://github.com/YOUR_USERNAME/homelab-apps.git

# Edit root-app.yaml and app-nginx.yaml to replace YOUR_USERNAME with your actual username

git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

## 2. Update the Application manifests

Before pushing, edit these files and replace `YOUR_USERNAME` with your actual GitHub username:

- `root-app.yaml` - change repoURL
- `app-nginx.yaml` - change repoURL

## 3. Connect to ArgoCD

Once pushed, create the application in ArgoCD:

```bash
# Option 1: Via CLI
argocd app create homelab \
  --repo https://github.com/YOUR_USERNAME/homelab-apps.git \
  --path . \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd

# Option 2: Via UI
# - Open ArgoCD at http://192.168.1.17:30157
# - Click "+ New App"
# - Fill in the details
```

## Repo Structure

```
homelab-apps/
├── root-app.yaml          # Main Application (deploys everything)
├── app-nginx.yaml        # Standalone nginx app
├── nginx/
│   └── deployment.yaml  # Nginx with ingress
└── postgres/
    └── deployment.yaml  # PostgreSQL with PVC
```

## How it Works

1. **Push to GitHub** → Code lives in Git
2. **ArgoCD watches Git** → Detects changes
3. **Auto-sync** → Deploys to cluster automatically
4. **Self-heal** → If cluster state drifts, ArgoCD fixes it

## Adding More Apps

1. Create a new folder (e.g., `myapp/`)
2. Add your Kubernetes manifests
3. Add to `root-app.yaml` path or create separate Application
4. Commit and push

## Useful Commands

```bash
# Check app status
argocd app get homelab

# Sync manually
argocd app sync homelab

# View logs
argocd app logs homelab

# Delete app
argocd app delete homelab
```
