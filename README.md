# SkillPulse 🚀

> **Track your skills. Log your learning. Ship via GitHub Actions.**

SkillPulse is an open-source skill tracking web application, enhanced with a production-grade DevSecOps pipeline, GitOps deployment, and full observability stack on AWS EKS.

---

## 📸 Overview

SkillPulse lets you track learning progress across skills, log hours, and visualize your growth — deployed on a fully automated cloud-native infrastructure.

---

## 🏗️ Architecture

```
Developer
    │
    ▼
GitHub (push / PR)
    │
    ▼
GitHub Actions (CI + DevSecOps)
    │  ├── Gitleaks       → Secret scanning
    │  ├── Hadolint       → Dockerfile linting
    │  ├── Trivy          → Container image vulnerability scan
    │  └── Docker Build & Push → Docker Hub
    │
    ▼
ArgoCD (GitOps — pulls K8s manifests from repo)
    │
    ▼
AWS EKS Cluster
    │
    ├── Namespace: skillpulse
    │   ├── Frontend        (React, NodePort → Envoy Gateway)
    │   ├── Backend         (Go + Gin REST API)
    │   └── MySQL 8.4       (StatefulSet + PVC)
    │
    ├── Envoy Gateway       (Gateway API, AWS NLB, TLS via cert-manager)
    │
    └── Monitoring Namespace
        ├── Prometheus      (metrics scraping)
        └── Grafana         (dashboards)
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (open-source base) |
| Backend | Go + Gin framework |
| Database | MySQL 8.4 (StatefulSet) |
| Containerization | Docker, Docker Compose |
| Infrastructure | Terraform (AWS EKS, VPC, IAM) |
| Orchestration | Kubernetes (EKS) |
| GitOps | ArgoCD |
| Gateway | Envoy Gateway (Kubernetes Gateway API) |
| TLS | cert-manager + Let's Encrypt |
| CI/CD | GitHub Actions |
| Secret Scanning | Gitleaks |
| Image Security | Trivy |
| Dockerfile Linting | Hadolint |
| Monitoring | Prometheus + Grafana |

---

## 🔄 CI/CD Pipeline

### Developer Workflow

```
git push → GitHub Actions triggers →
    1. Gitleaks    (scan for secrets/credentials in code)
    2. Hadolint    (lint Dockerfile for best practices)
    3. Trivy       (scan Docker image for CVEs)
    4. Docker Build & Push to Docker Hub
    5. Update K8s manifest with new image tag
    6. ArgoCD detects change → syncs to EKS automatically
```

### GitHub Actions Jobs

```yaml
jobs:
  security-scan:    # Gitleaks secret scan
  lint:             # Hadolint Dockerfile lint
  build-and-push:   # Docker build + Trivy scan + push
  update-manifest:  # Bump image tag in k8s manifests
```

---

## 🔐 DevSecOps

| Tool | Purpose | Stage |
|---|---|---|
| **Gitleaks** | Detects hardcoded secrets, API keys, passwords in source code | Pre-build |
| **Hadolint** | Lints Dockerfile against best practices (non-root user, layer optimization) | Pre-build |
| **Trivy** | Scans built Docker image for known CVEs (HIGH/CRITICAL block pipeline) | Post-build |

Pipeline fails and blocks deployment if any HIGH/CRITICAL vulnerability or secret leak is detected.

---

## ☸️ Kubernetes

```
namespace: skillpulse
├── Secret           skillpulse-db        (DB credentials)
├── ConfigMap        mysql-init           (init.sql seed data)
├── StatefulSet      mysql                (MySQL 8.4, PVC gp2 1Gi)
├── Service          mysql                (Headless ClusterIP)
├── Deployment       backend              (Go API, ClusterIP :8080)
├── Deployment       frontend             (React, NodePort :30080)
├── Gateway          skillpulse-gateway   (Envoy Gateway, AWS NLB)
├── HTTPRoute        skill-route          (/ → frontend, /api → backend)
├── Certificate      skillpulse-tls       (cert-manager, Let's Encrypt)
└── BackendTrafficPolicy  skillpulse-session  (consistent hash LB)
```

---

## 🌍 Infrastructure (Terraform)

Terraform provisions the complete AWS infrastructure:

```
terraform/
├── eks.tf          # EKS cluster + node groups
├── vpc.tf          # VPC, subnets, route tables
├── iam.tf          # IAM roles for EKS + Load Balancer Controller
├── variables.tf
├── outputs.tf
└── README.md       # Envoy Gateway + cert-manager install steps
```

```bash
cd terraform/
terraform init
terraform plan
terraform apply
```

---

## 📊 Monitoring

Prometheus scrapes metrics from all services. Grafana provides dashboards for:

- Pod CPU / Memory usage
- HTTP request rates and error rates
- MySQL connection pool metrics
- Node-level resource utilization

```bash
# Access Grafana
kubectl port-forward svc/grafana 3000:3000 -n monitoring
# Open http://localhost:3000
```

---

## 🚀 Getting Started

### Prerequisites

- AWS CLI configured
- `kubectl`, `terraform`, `helm` installed
- Docker Hub account

### 1. Provision Infrastructure

```bash
cd terraform/
terraform init && terraform apply
aws eks update-kubeconfig --name <cluster-name> --region us-west-2
```

### 2. Install Cluster Dependencies

```bash
# Gateway API CRDs
kubectl apply --server-side \
  -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml

# Envoy Gateway
helm install eg oci://docker.io/envoyproxy/gateway-helm \
  --version v1.2.6 -n envoy-gateway-system --create-namespace --skip-crds --wait

# cert-manager
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --set config.enableGatewayAPI=true \
  --set crds.enabled=true

# ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Deploy Application via ArgoCD

```bash
kubectl apply -f argocd/application.yaml
# ArgoCD will sync all K8s manifests from the repo automatically
```

### 4. Local Development

```bash
# Run full stack locally
docker compose up

# App available at http://localhost:3000
```

---

## 📁 Project Structure

```
skillpulse/
├── .github/
│   └── workflows/
│       └── ci.yaml          # GitHub Actions pipeline
├── frontend/                # React app
├── backend/                 # Go + Gin API
├── k8s/                     # Kubernetes manifests
│   ├── namespace.yaml
│   ├── mysql.yaml
│   ├── backend.yaml
│   ├── frontend.yaml
│   ├── gateway.yaml
│   └── certificate.yaml
├── terraform/               # AWS infrastructure
├── monitoring/              # Prometheus + Grafana configs
├── argocd/                  # ArgoCD application manifest
├── Dockerfile               # Multi-stage Docker build
├── docker-compose.yaml      # Local development stack
└── README.md
```

---

## 🙏 Credits

SkillPulse is based on an open-source skill tracking project. DevOps infrastructure, CI/CD pipeline, GitOps setup, monitoring, and DevSecOps tooling added by [Harsh](https://github.com/harsh9308).

---

## 📄 License

This project inherits the license of the original open-source project. All DevOps additions are freely available for learning and reference.
# SkillPulse 🚀

> **Track your skills. Log your learning. Ship via GitHub Actions.**

SkillPulse is an open-source skill tracking web application, enhanced with a production-grade DevSecOps pipeline, GitOps deployment, and full observability stack on AWS EKS.

---

## 📸 Overview

SkillPulse lets you track learning progress across skills, log hours, and visualize your growth — deployed on a fully automated cloud-native infrastructure.

---

## 🏗️ Architecture

```
Developer
    │
    ▼
GitHub (push / PR)
    │
    ▼
GitHub Actions (CI + DevSecOps)
    │  ├── Gitleaks       → Secret scanning
    │  ├── Hadolint       → Dockerfile linting
    │  ├── Trivy          → Container image vulnerability scan
    │  └── Docker Build & Push → Docker Hub
    │
    ▼
ArgoCD (GitOps — pulls K8s manifests from repo)
    │
    ▼
AWS EKS Cluster
    │
    ├── Namespace: skillpulse
    │   ├── Frontend        (React, NodePort → Envoy Gateway)
    │   ├── Backend         (Go + Gin REST API)
    │   └── MySQL 8.4       (StatefulSet + PVC)
    │
    ├── Envoy Gateway       (Gateway API, AWS NLB, TLS via cert-manager)
    │
    └── Monitoring Namespace
        ├── Prometheus      (metrics scraping)
        └── Grafana         (dashboards)
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (open-source base) |
| Backend | Go + Gin framework |
| Database | MySQL 8.4 (StatefulSet) |
| Containerization | Docker, Docker Compose |
| Infrastructure | Terraform (AWS EKS, VPC, IAM) |
| Orchestration | Kubernetes (EKS) |
| GitOps | ArgoCD |
| Gateway | Envoy Gateway (Kubernetes Gateway API) |
| TLS | cert-manager + Let's Encrypt |
| CI/CD | GitHub Actions |
| Secret Scanning | Gitleaks |
| Image Security | Trivy |
| Dockerfile Linting | Hadolint |
| Monitoring | Prometheus + Grafana |

---

## 🔄 CI/CD Pipeline

### Developer Workflow

```
git push → GitHub Actions triggers →
    1. Gitleaks    (scan for secrets/credentials in code)
    2. Hadolint    (lint Dockerfile for best practices)
    3. Trivy       (scan Docker image for CVEs)
    4. Docker Build & Push to Docker Hub
    5. Update K8s manifest with new image tag
    6. ArgoCD detects change → syncs to EKS automatically
```

### GitHub Actions Jobs

```yaml
jobs:
  security-scan:    # Gitleaks secret scan
  lint:             # Hadolint Dockerfile lint
  build-and-push:   # Docker build + Trivy scan + push
  update-manifest:  # Bump image tag in k8s manifests
```

---

## 🔐 DevSecOps

| Tool | Purpose | Stage |
|---|---|---|
| **Gitleaks** | Detects hardcoded secrets, API keys, passwords in source code | Pre-build |
| **Hadolint** | Lints Dockerfile against best practices (non-root user, layer optimization) | Pre-build |
| **Trivy** | Scans built Docker image for known CVEs (HIGH/CRITICAL block pipeline) | Post-build |

Pipeline fails and blocks deployment if any HIGH/CRITICAL vulnerability or secret leak is detected.

---

## ☸️ Kubernetes

```
namespace: skillpulse
├── Secret           skillpulse-db        (DB credentials)
├── ConfigMap        mysql-init           (init.sql seed data)
├── StatefulSet      mysql                (MySQL 8.4, PVC gp2 1Gi)
├── Service          mysql                (Headless ClusterIP)
├── Deployment       backend              (Go API, ClusterIP :8080)
├── Deployment       frontend             (React, NodePort :30080)
├── Gateway          skillpulse-gateway   (Envoy Gateway, AWS NLB)
├── HTTPRoute        skill-route          (/ → frontend, /api → backend)
├── Certificate      skillpulse-tls       (cert-manager, Let's Encrypt)
└── BackendTrafficPolicy  skillpulse-session  (consistent hash LB)
```

---

## 🌍 Infrastructure (Terraform)

Terraform provisions the complete AWS infrastructure:

```
terraform/
├── eks.tf          # EKS cluster + node groups
├── vpc.tf          # VPC, subnets, route tables
├── iam.tf          # IAM roles for EKS + Load Balancer Controller
├── variables.tf
├── outputs.tf
└── README.md       # Envoy Gateway + cert-manager install steps
```

```bash
cd terraform/
terraform init
terraform plan
terraform apply
```

---

## 📊 Monitoring

Prometheus scrapes metrics from all services. Grafana provides dashboards for:

- Pod CPU / Memory usage
- HTTP request rates and error rates
- MySQL connection pool metrics
- Node-level resource utilization

```bash
# Access Grafana
kubectl port-forward svc/grafana 3000:3000 -n monitoring
# Open http://localhost:3000
```

---

## 🚀 Getting Started

### Prerequisites

- AWS CLI configured
- `kubectl`, `terraform`, `helm` installed
- Docker Hub account

### 1. Provision Infrastructure

```bash
cd terraform/
terraform init && terraform apply
aws eks update-kubeconfig --name <cluster-name> --region us-west-2
```

### 2. Install Cluster Dependencies

```bash
# Gateway API CRDs
kubectl apply --server-side \
  -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml

# Envoy Gateway
helm install eg oci://docker.io/envoyproxy/gateway-helm \
  --version v1.2.6 -n envoy-gateway-system --create-namespace --skip-crds --wait

# cert-manager
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --set config.enableGatewayAPI=true \
  --set crds.enabled=true

# ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Deploy Application via ArgoCD

```bash
kubectl apply -f argocd/application.yaml
# ArgoCD will sync all K8s manifests from the repo automatically
```

### 4. Local Development

```bash
# Run full stack locally
docker compose up

# App available at http://localhost:3000
```

---

## 📁 Project Structure

```
skillpulse/
├── .github/
│   └── workflows/
│       └── ci.yaml          # GitHub Actions pipeline
├── frontend/                # React app
├── backend/                 # Go + Gin API
├── k8s/                     # Kubernetes manifests
│   ├── namespace.yaml
│   ├── mysql.yaml
│   ├── backend.yaml
│   ├── frontend.yaml
│   ├── gateway.yaml
│   └── certificate.yaml
├── terraform/               # AWS infrastructure
├── monitoring/              # Prometheus + Grafana configs
├── argocd/                  # ArgoCD application manifest
├── Dockerfile               # Multi-stage Docker build
├── docker-compose.yaml      # Local development stack
└── README.md
```

---

## 🙏 Credits

SkillPulse is based on an open-source skill tracking project. DevOps infrastructure, CI/CD pipeline, GitOps setup, monitoring, and DevSecOps tooling added by [Harsh](https://github.com/harsh9308).

---

## 📄 License

This project inherits the license of the original open-source project. All DevOps additions are freely available for learning and reference.
