# Portfolio - GitHub Actions, GHCR, FluxCD and Kubernetes GitOps Demo

This repository demonstrates a complete modern CI/CD and GitOps deployment workflow using:

* Docker
* GitHub Actions
* GitHub Container Registry (GHCR)
* FluxCD
* Kubernetes
* GitOps

The purpose of this project is educational and practical: to show how a simple application can be containerized, automatically built, stored in a container registry, deployed through a GitOps repository, and synchronized into Kubernetes using FluxCD.

---

# Learning Objectives

By following this project, you will learn how to:

* Containerize an application using Docker
* Build and publish images automatically using GitHub Actions
* Store container images in GitHub Container Registry (GHCR)
* Implement GitOps deployment practices
* Deploy applications with FluxCD
* Manage Kubernetes manifests through Git
* Automate Kubernetes deployments

---

# Project Architecture

```text
Developer
    │
    │ Git Push
    ▼
Application Repository
(crchiran-portfolio)
    │
    ▼
GitHub Actions
    │
    ├── Build Docker Image
    ├── Push Image to GHCR
    ├── Run Trivy Scan
    └── Update GitOps Repository
                │
                ▼
GitOps Repository
(crchiran-portfolio-gitops)
                │
                ▼
FluxCD
                │
                ▼
Kubernetes Cluster
                │
                ▼
Running Application
```

---

# Repositories Used

This project requires two repositories.

## 1. Application Repository

Repository:

```text
crchiran/crchiran-portfolio
```

Contains:

```text
.
├── .github
│   └── workflows
│       └── deploy.yaml
├── crchiran-portfolio.html
├── Dockerfile
├── nginx.conf
└── README.md
```

Responsibilities:

```text
Build Application
Build Docker Image
Push Image to GHCR
Run Security Scan
Update GitOps Repository
```

---

## 2. GitOps Repository

Repository:

```text
crchiran/crchiran-portfolio-gitops
```

Contains:

```text
.
├── apps
│   ├── crchiran-portfolio
│   │   ├── cert.yaml
│   │   ├── deployment.yaml
│   │   ├── gw.yaml
│   │   ├── kustomization.yaml
│   │   ├── sa.yaml
│   │   ├── svc.yaml
│   │   └── vs.yaml
│   ├── monitoring
│   │   ├── grafana-admin-secret.yaml
│   │   ├── grafana-virtualservice.yaml
│   │   ├── kube-prom-stack-helmrelease.yaml
│   │   ├── kustomization.yaml
│   │   ├── loki-helmrelease.yaml
│   │   ├── loki-memberlist-svc.yaml
│   │   ├── loki-repo.yaml
│   │   ├── monitoring-gateway.yaml
│   │   ├── monitoring-repo.yaml
│   │   ├── namespace.yaml
│   │   └── promtail-helmrelease.yaml
│   └── todo
│       ├── cert.yaml
│       ├── gw.yaml
│       ├── kustomization.yaml
│       ├── ns.yaml
│       ├── svc.yaml
│       ├── todo-deployment.yaml
│       └── vs.yaml
├── clusters
│   └── production
│       ├── crchiran-portfolio-prod.yaml
│       ├── flux-system
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
│       ├── monitoring.yaml
│       └── todo.yaml
└── README.md
```

Responsibilities:

```text
Kubernetes Manifests
FluxCD Resources
Environment Configuration
Deployment Configuration
```

---

# Repository Structure

```text
.
├── crchiran-portfolio.html
├── Dockerfile
└── nginx.conf
```

---

# File Description

| File                            | Description                   |
| ------------------------------- | ----------------------------- |
| `crchiran-portfolio.html`       | Portfolio web page            |
| `Dockerfile`                    | Builds the Docker image       |
| `nginx.conf`                    | NGINX configuration           |
| `.github/workflows/deploy.yaml` | GitHub Actions CI/CD pipeline |

---

# Local Development

## Build Docker Image

```bash
docker build -t crchiran-portfolio:latest .
```

## Run Container

```bash
docker run -d \
  --name crchiran-portfolio \
  -p 8080:8080 \
  crchiran-portfolio:latest
```

Open:

```text
http://localhost:8080
```

---

# Container Registry

Images are stored in:

```text
ghcr.io/crchiran/crchiran-portfolio
```

Example:

```text
ghcr.io/crchiran/crchiran-portfolio:prod-abc123
```

---

# GitHub Actions CI/CD Pipeline

The GitHub Actions workflow automatically performs:

```text
Checkout Source Code
        │
        ▼
Build Docker Image
        │
        ▼
Push Image to GHCR
        │
        ▼
Run Trivy Security Scan
        │
        ▼
Update GitOps Repository
        │
        ▼
Commit and Push Changes
```

---

# FluxCD Deployment

## Prerequisites

Install:

* Kubernetes Cluster
* kubectl
* Flux CLI
* Git

Verify:

```bash
kubectl get nodes
```

```bash
flux check --pre
```

---

## Install Flux CLI

### macOS

```bash
brew install fluxcd/tap/flux
```

### Linux

```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

---

## Export GitHub Token

Create a GitHub Personal Access Token for Flux bootstrap.

Export:

```bash
export GITHUB_TOKEN=<github-personal-access-token>
```

Verify:

```bash
echo $GITHUB_TOKEN
```

---

## Bootstrap FluxCD

```bash
flux bootstrap github \
  --owner=<github-username> \
  --repository=<gitops-repository-name> \
  --branch=main \
  --path=clusters/production \
  --personal=true \
  --token-auth \
  --network-policy=false
```

---

# Deployment Workflow

## Step 1

Developer updates code:

```bash
git add .
git commit -m "Update portfolio"
git push origin main
```

---

## Step 2

GitHub Actions automatically:

```text
Build Docker Image
Push Image to GHCR
Run Trivy Scan
Update GitOps Repository
```

---

## Step 3

GitOps Repository receives new image tag.

Example:

Before:

```yaml
images:
  - name: crchiran-portfolio
    newTag: prod-old
```

After:

```yaml
images:
  - name: crchiran-portfolio
    newTag: prod-new
```

---

## Step 4

FluxCD detects changes.

Verify:

```bash
flux get sources git -A
```

```bash
flux get kustomizations -A
```

---

## Step 5

Kubernetes deploys the new version.

Verify:

```bash
kubectl get deploy -n portfolio
```

```bash
kubectl rollout status deployment/crchiran-portfolio -n portfolio
```

---

# Verify Deployment

Check Flux:

```bash
flux get all -A
```

Check Git sources:

```bash
flux get sources git -A
```

Check Kustomizations:

```bash
flux get kustomizations -A
```

Check application:

```bash
kubectl get pods -n portfolio
kubectl get svc -n portfolio
kubectl get deploy -n portfolio
```

---

# Troubleshooting

View Flux events:

```bash
flux events
```

View Flux logs:

```bash
kubectl logs -n flux-system deployment/source-controller
kubectl logs -n flux-system deployment/kustomize-controller
kubectl logs -n flux-system deployment/helm-controller
kubectl logs -n flux-system deployment/notification-controller
```

Force synchronization:

```bash
flux reconcile source git flux-system
```

```bash
flux reconcile kustomization flux-system
```

---

# Technologies Used

* HTML
* NGINX
* Docker
* GitHub Actions
* GitHub Container Registry (GHCR)
* Trivy
* FluxCD
* Kubernetes
* GitOps

---

# Learning Outcomes

This project demonstrates:

* Application containerization
* CI/CD pipeline creation
* Container image publishing
* GitOps workflows
* FluxCD deployments
* Kubernetes automation
* Production deployment patterns

---

# Maintainer

**Chandan Richard Chiran**

GitHub:

```text
https://github.com/crchiran
```
