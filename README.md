# FastAPI + Argo CD GitOps Demo on K3s

A hands-on demo project to learn **Argo CD** and **GitOps** using a simple FastAPI application deployed on Kubernetes (K3s).

This repository is built for learning how Argo CD works in a real workflow by syncing Kubernetes manifests from GitHub to your cluster.

---

# What You'll Learn

By using this repository, you’ll understand:

- Argo CD fundamentals
- GitOps workflow
- Kubernetes deployment using YAML manifests
- Continuous sync between Git and Kubernetes
- Auto Sync
- Self Healing
- Drift Detection
- Rollbacks
- Reconciliation Loop

---

# Project Architecture

```text
Developer → GitHub Repository → Argo CD → Kubernetes Cluster (K3s)
```

## Workflow

1. Make changes locally
2. Push changes to GitHub
3. Argo CD watches the repository
4. Argo CD detects changes
5. Argo CD syncs changes to Kubernetes
6. Kubernetes deploys the application

---

# Repository Structure

```bash
.
├── app/
│   ├── main.py
│   └── requirements.txt
│
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
│
├── Dockerfile
├── argocd-app.yaml
└── README.md
```

---

# Prerequisites

Before getting started, make sure you have:

- Docker
- Kubernetes / K3s cluster
- kubectl configured
- Git installed
- GitHub account
- Docker Hub account
- Argo CD installed in your cluster

---

# Clone the Repository

```bash
git clone <your-repository-url>
cd <repository-name>
```

---

# Run FastAPI Locally

Install dependencies:

```bash
pip install -r app/requirements.txt
```

Run the application:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Access it in browser:

```bash
http://localhost:8000
```

---

# Build Docker Image

```bash
docker build -t your-dockerhub-username/fastapi-argocd-demo:latest .
```

---

# Push Docker Image

Login:

```bash
docker login
```

Push image:

```bash
docker push your-dockerhub-username/fastapi-argocd-demo:latest
```

---

# Deploy to Kubernetes (Optional Manual Deployment)

Before using Argo CD, you can test the manifests manually.

Apply manifests:

```bash
kubectl apply -f k8s/
```

Verify:

```bash
kubectl get pods
kubectl get svc
```

Delete again before using Argo CD:

```bash
kubectl delete -f k8s/
```

---

# Install Argo CD on K3s

Create namespace:

```bash
kubectl create namespace argocd
```

Install Argo CD:

```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify installation:

```bash
kubectl get pods -n argocd
```

Wait until all pods are in `Running` state.

---

# Access Argo CD UI

Port forward:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open browser:

```bash
https://localhost:8080
```

---

# Login to Argo CD

Get admin password:

```bash
kubectl get secret argocd-initial-admin-secret \
-n argocd \
-o jsonpath="{.data.password}" | base64 -d
```

Login with:

```text
Username: admin
Password: <decoded-password>
```

---

# Create Argo CD Application

Apply:

```bash
kubectl apply -f argocd-app.yaml
```

This connects Argo CD to your GitHub repository and deploys the manifests from the `k8s/` directory.

---

# Hands-On Argo CD Learning Exercises

This repository is mainly meant to help you learn Argo CD by experimenting.

---

## 1. Test GitOps Sync

Edit:

```bash
k8s/deployment.yaml
```

Example:

Change:

```yaml
replicas: 1
```

To:

```yaml
replicas: 3
```

Commit and push:

```bash
git add .
git commit -m "Scale app to 3 replicas"
git push
```

Now watch:

```bash
kubectl get pods
```

Argo CD will automatically sync the change.

---

# 2. Test Self-Healing

Scale manually:

```bash
kubectl scale deployment fastapi-app --replicas=1
```

If Git still says `3`, Argo CD will detect drift and restore it back.

This demonstrates:

- Drift Detection
- Self Healing

---

# 3. Test Delete Recovery

Delete deployment manually:

```bash
kubectl delete deployment fastapi-app
```

Argo CD will recreate it automatically from Git.

This demonstrates reconciliation.

---

# 4. Observe Argo CD UI

Open Argo CD dashboard and explore:

- Application Tree
- Pods
- Deployments
- Services
- Sync Status
- Health Status

---

# Important Argo CD Concepts

## Sync Status

### Synced
Git state matches cluster state.

### OutOfSync
Git state does not match cluster state.

---

## Health Status

### Healthy
Application is running correctly.

### Degraded
Application has issues.

---

## Auto Sync

Argo CD automatically deploys changes whenever code is pushed to Git.

---

## Self Heal

If someone changes the cluster manually, Argo CD restores it back to Git state.

---

## Prune

If a resource is removed from Git, Argo CD deletes it from Kubernetes too.

---

## Rollback

Rollback is done by reverting Git commits.

Example:

```bash
git revert <commit-id>
git push
```

Argo CD redeploys previous version automatically.

---

# Important Note

## Argo CD does NOT:

- build Docker images
- run tests
- package applications

## Argo CD DOES:

- watch Git repositories
- compare desired vs actual state
- sync manifests to Kubernetes
- deploy applications
- detect drift
- self-heal resources

---

# Recommended Next Steps

Once you're comfortable with this repo, continue with:

- Helm + Argo CD
- ApplicationSet
- Multi-environment deployments
- Private Git repositories
- Docker registry secrets
- Ingress + TLS
- Multi-cluster Argo CD
- Argo CD on EKS

---

# Final Goal

After working through this repository, you should be able to understand:

- What GitOps is
- Why Argo CD is used
- How Argo CD syncs Git to Kubernetes
- How drift detection works
- How self-healing works
- How rollback works

---

# Best Way to Learn

Clone the repo.

Deploy it.

Modify it.

Break it.

Push changes.

Watch Argo CD fix everything.

That’s the fastest way to learn Argo CD hands-on.

---