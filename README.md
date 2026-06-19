# Portfolio Website - Kubernetes CI/CD & GitOps Platform

A production-inspired portfolio website demonstrating modern application delivery using:

* Docker
* GitHub Actions
* GitHub Container Registry (GHCR)
* Trivy Security Scanning
* FluxCD
* Kubernetes
* GitOps

This repository contains the application source code and CI/CD pipeline used to build, scan, publish, and deploy the portfolio website into Kubernetes through a GitOps workflow.

---

## Project Overview

The goal of this project is to demonstrate a complete end-to-end cloud-native deployment workflow.

A simple portfolio website is:

1. Developed and maintained in Git
2. Containerized using Docker
3. Built automatically using GitHub Actions
4. Security scanned using Trivy
5. Published to GitHub Container Registry (GHCR)
6. Delivered through a GitOps repository
7. Deployed automatically by FluxCD
8. Running inside Kubernetes

This repository focuses on the **application layer and CI/CD pipeline**.

---

## Related Repository

This project uses two repositories.

### Application Repository (Current Repository)

```text
crchiran/crchiran-portfolio
```

Responsibilities:

* Application source code
* Docker image creation
* CI/CD pipeline
* Security scanning
* Container image publishing
* GitOps repository updates

### GitOps Repository

```text
crchiran/crchiran-portfolio-gitops
```

Responsibilities:

* Kubernetes manifests
* FluxCD configuration
* Istio resources
* TLS certificates
* Monitoring stack
* Application deployment

---

## End-to-End Deployment Flow

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
    ├── Trivy Security Scan
    ├── Push Image to GHCR
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
Portfolio Website
```

---

## How Deployment Works

### Step 1

Developer updates application code.

```bash
git add .
git commit -m "Update portfolio"
git push origin main
```

---

### Step 2

GitHub Actions automatically:

```text
Build Docker Image
        │
        ▼
Run Trivy Security Scan
        │
        ▼
Push Image to GHCR
        │
        ▼
Update GitOps Repository
```

---

### Step 3

The GitOps repository receives a new image tag.

Example:

Before:

```yaml
images:
  - name: ghcr.io/crchiran/crchiran-portfolio
    newTag: prod-old
```

After:

```yaml
images:
  - name: ghcr.io/crchiran/crchiran-portfolio
    newTag: prod-new
```

---

### Step 4

FluxCD detects the GitOps repository change.

---

### Step 5

FluxCD synchronizes Kubernetes.

---

### Step 6

Kubernetes deploys the new application version automatically.

No manual deployment actions are required.

---

## Repository Structure

```text
.
├── Dockerfile
├── README.md
├── clusters
│   └── production
│       └── flux-system
│           ├── gotk-components.yaml
│           ├── gotk-sync.yaml
│           └── kustomization.yaml
├── crchiran-portfolio.html
├── images
│   └── profile-photo.jpg
└── nginx.conf
```

---

## Components

### Portfolio Website

```text
crchiran-portfolio.html
```

The static portfolio webpage.

---

### Profile Image

```text
images/profile-photo.jpg
```

Profile image displayed on the website.

---

### NGINX Configuration

```text
nginx.conf
```

NGINX configuration used to serve the application.

---

### Dockerfile

```text
Dockerfile
```

Container image definition.

---

### Flux Bootstrap Resources

```text
clusters/production/flux-system
```

FluxCD bootstrap manifests used during cluster initialization.

---

## Dockerfile

```dockerfile
FROM nginxinc/nginx-unprivileged:1.29-alpine

COPY --chown=101:101 nginx.conf /etc/nginx/conf.d/default.conf
COPY --chown=101:101 crchiran-portfolio.html /usr/share/nginx/html/index.html
COPY --chown=101:101 images /usr/share/nginx/html/images

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
```

### Why This Image?

* Lightweight Alpine Linux image
* Unprivileged NGINX container
* Reduced attack surface
* Production-oriented configuration
* Kubernetes-friendly deployment

---

## Quick Start

### Clone Repository

```bash
git clone https://github.com/crchiran/crchiran-portfolio.git

cd crchiran-portfolio
```

---

### Build Image

```bash
docker build -t crchiran-portfolio:latest .
```

Verify:

```bash
docker images
```

---

### Run Container

```bash
docker run -d \
  --name crchiran-portfolio \
  -p 8080:8080 \
  crchiran-portfolio:latest
```

Verify:

```bash
docker ps
```

---

### Access Application

Open:

```text
http://localhost:8080
```

Or verify:

```bash
curl http://localhost:8080
```

---

### View Logs

```bash
docker logs -f crchiran-portfolio
```

---

### Stop Container

```bash
docker stop crchiran-portfolio
```

Remove:

```bash
docker rm crchiran-portfolio
```

---

## CI/CD Pipeline

GitHub Actions automatically performs:

```text
Checkout Source Code
        │
        ▼
Build Docker Image
        │
        ▼
Run Trivy Scan
        │
        ▼
Push Image to GHCR
        │
        ▼
Update GitOps Repository
        │
        ▼
Commit & Push Changes
```

Benefits:

* Automated builds
* Automated security scanning
* Automated image publishing
* Automated deployment updates
* GitOps-driven releases

---

## Container Registry

Images are automatically published to GitHub Container Registry.

Repository:

```text
ghcr.io/crchiran/crchiran-portfolio
```

Example image tags:

```text
latest
prod-a1b2c3d
prod-7f9e3ab
```

Pull image:

```bash
docker pull ghcr.io/crchiran/crchiran-portfolio:latest
```

---

## Security Scanning

Container images are scanned using Trivy during the CI/CD pipeline.

The scan helps identify:

* Critical vulnerabilities
* High vulnerabilities
* Dependency issues
* Container image risks

This ensures validated images are delivered into Kubernetes environments.

---

## Deployment Verification

Verify image availability:

```bash
docker pull ghcr.io/crchiran/crchiran-portfolio:latest
```

Verify workflow execution:

```text
GitHub
└── Actions
    └── Workflow Runs
```

Verify GitOps update:

```text
crchiran-portfolio-gitops
└── Commit History
```

---

## Technology Stack

| Category           | Technology                       |
| ------------------ | -------------------------------- |
| Frontend           | HTML5                            |
| Web Server         | NGINX                            |
| Containerization   | Docker                           |
| CI/CD              | GitHub Actions                   |
| Container Registry | GitHub Container Registry (GHCR) |
| Security           | Trivy                            |
| GitOps             | FluxCD                           |
| Orchestration      | Kubernetes                       |

---

## Learning Outcomes

This project demonstrates practical implementation of:

* Docker Containerization
* NGINX Application Hosting
* GitHub Actions CI/CD
* Container Security Scanning
* Container Image Publishing
* GitHub Container Registry (GHCR)
* GitOps Workflows
* FluxCD Deployments
* Kubernetes Application Delivery
* Production-Inspired Deployment Practices

---

## Future Enhancements

Potential improvements include:

* Helm-based deployments
* Multi-environment GitOps
* Progressive delivery
* Canary deployments
* Prometheus monitoring
* Grafana dashboards
* Loki log aggregation
* OpenTelemetry tracing
* Policy enforcement with Kyverno
* Runtime security with Falco

---

## License

This project is provided for educational and demonstration purposes.
