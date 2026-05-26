FastAPI + Argo CD GitOps Demo on K3s

A hands-on repository to learn GitOps with Argo CD using a simple FastAPI application deployed on Kubernetes (K3s).

This repo is designed for anyone who wants to understand:

* Argo CD fundamentals
* GitOps workflow
* Kubernetes deployments using manifests
* Continuous sync between Git and Kubernetes
* Self-healing
* Rollbacks
* Auto Sync

⸻

Architecture

Developer → GitHub Repo → Argo CD → Kubernetes Cluster (K3s)

Flow

1. Make code or manifest changes locally
2. Push changes to GitHub
3. Argo CD watches the repository
4. Argo CD detects changes
5. Argo CD syncs them to Kubernetes
6. Kubernetes deploys/updates the application

⸻

What This Repository Contains

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

⸻

Purpose of This Repository

This project exists mainly to help you learn Argo CD.

The FastAPI application is intentionally simple.

The real learning happens in:

* Kubernetes manifests
* Argo CD Application configuration
* Syncing Git → Cluster
* Watching Argo CD reconcile state

⸻

Prerequisites

Before starting:

* Docker installed
* K3s or Kubernetes cluster running
* kubectl configured
* GitHub account
* Docker Hub account
* Argo CD installed in cluster

⸻

Clone the Repository

git clone <your-repo-url>
cd fastapi-argocd-demo

⸻

Run FastAPI Locally

Install dependencies:

pip install -r app/requirements.txt

Run:

uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

Open:

http://localhost:8000

⸻

Build Docker Image

docker build -t your-dockerhub-username/fastapi-argocd-demo:latest .

⸻

Push Image to Docker Hub

Login:

docker login

Push:

docker push your-dockerhub-username/fastapi-argocd-demo:latest

⸻

Deploy to Kubernetes Manually (Optional)

Before using Argo CD, deploy manually once to understand what Argo CD automates.

Apply manifests:

kubectl apply -f k8s/

Check:

kubectl get pods
kubectl get svc

Delete:

kubectl delete -f k8s/

⸻

Install Argo CD on K3s

Create namespace:

kubectl create namespace argocd

Install:

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Verify:

kubectl get pods -n argocd

Wait until all pods are Running.

⸻

Access Argo CD UI

Port-forward:

kubectl port-forward svc/argocd-server -n argocd 8080:443

Open browser:

https://localhost:8080

⸻

Get Argo CD Admin Password

kubectl get secret argocd-initial-admin-secret -n argocd -o yaml

Decode:

echo <base64-password> | base64 --decode

Login:

username: admin
password: <decoded-password>

⸻

Create Argo CD Application

Apply:

kubectl apply -f argocd-app.yaml

This tells Argo CD:

* which Git repo to watch
* which branch to watch
* which folder contains manifests
* where to deploy inside Kubernetes

⸻

Learning Exercises (Recommended)

This is where Argo CD becomes fun.

⸻

1. Test GitOps Deployment

Change something inside:

k8s/deployment.yaml

Example:

replicas: 1

Change to:

replicas: 3

Commit and push:

git add .
git commit -m "scale app"
git push

Then watch:

kubectl get pods

Argo CD will sync automatically.

⸻

2. Test Self-Healing

Scale deployment manually:

kubectl scale deployment fastapi-app --replicas=1

If Git says replicas should be 3…

Argo CD will detect drift and restore it back to 3.

This demonstrates:

Self Healing

Git remains the source of truth.

⸻

3. Test Delete Recovery

Delete deployment:

kubectl delete deployment fastapi-app

Argo CD will recreate it automatically from Git.

This shows:

Reconciliation

⸻

4. Observe Sync Status

Open Argo CD UI.

You’ll see:

* Application Tree
* Deployment
* Pods
* Service
* Sync Status
* Health Status

Important statuses:

Synced

Git state = Cluster state

OutOfSync

Git state ≠ Cluster state

Healthy

Application running correctly

Degraded

Something is failing

⸻

5. Try Manual Sync

Disable auto-sync and push a change.

Argo CD will show:

OutOfSync

Then click:

Sync

This helps understand manual reconciliation.

⸻

6. Rollback Practice

Deploy one version.

Push another version.

Then rollback to older commit in Git:

git revert <commit-id>
git push

Argo CD redeploys previous version.

This is one of the biggest GitOps benefits.

⸻

Key Argo CD Concepts You’ll Learn

Through this repo you’ll understand:

GitOps

Git is the source of truth.

⸻

Sync

Apply Git changes to cluster.

⸻

Auto Sync

Automatically deploy on Git push.

⸻

Self Heal

Fix manual drift in cluster.

⸻

Prune

Delete Kubernetes resources removed from Git.

⸻

Rollback

Return to previous working state using Git history.

⸻

Reconciliation Loop

Argo CD continuously compares:

Desired State (Git)
vs
Actual State (Cluster)

⸻

Important Note

Argo CD does NOT:

* build Docker images
* run tests
* package applications

Argo CD DOES:

* monitor Git repositories
* compare desired vs actual state
* deploy manifests
* sync Kubernetes
* self-heal drift

⸻

Next Things to Explore

Once comfortable with this repo, try:

* Helm + Argo CD
* ApplicationSets
* Multiple environments (dev/stage/prod)
* Private Git repos
* Ingress
* TLS
* Docker registry authentication
* Multi-cluster deployments
* Argo CD + Helm charts
* Argo CD on EKS

⸻

Final Goal

By working through this repo you should be able to confidently answer:

* What is GitOps?
* Why use Argo CD?
* How does Argo CD sync with Git?
* What is reconciliation?
* How does self-healing work?
* How do rollbacks work in GitOps?

⸻

If you’re learning Argo CD:

Clone this repo.

Break things.

Push changes.

Watch Argo CD fix them.

That’s the fastest way to learn it.

⸻