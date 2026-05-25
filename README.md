<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0c29,50:302b63,100:24243e&height=200&section=header&text=SkillPulse&fontSize=72&fontColor=ffffff&fontAlignY=38&desc=Track%20Skills.%20Ship%20Fearlessly.%20Own%20the%20Cloud.&descSize=18&descAlignY=60&animation=fadeIn" width="100%"/>

<br/>


[![Docker](https://img.shields.io/badge/Docker-Hub-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://hub.docker.com/u/harsh9308)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://aws.amazon.com/eks/)
[![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
[![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)](https://argoproj.github.io/cd/)
[![License](https://img.shields.io/badge/License-Open%20Source-F59E0B?style=for-the-badge&logo=opensourceinitiative&logoColor=white)](#license)

<br/>

### 🧰 Built With

<p align="center">
  <img src="https://skillicons.dev/icons?i=react,go,mysql,docker,kubernetes,terraform,githubactions,aws,prometheus,grafana,linux,bash&theme=dark&perline=6" />
</p>

<br/>

### 🔐 Secured By

<p align="center">
  <img src="https://img.shields.io/badge/Gitleaks-Secret%20Scanning-FF4B4B?style=flat-square&logo=git&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Hadolint-Dockerfile%20Lint-00B4D8?style=flat-square&logo=docker&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Trivy-CVE%20Scanning-1904DA?style=flat-square&logo=aquasecurity&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/TLS-Let's%20Encrypt-003A70?style=flat-square&logo=letsencrypt&logoColor=white"/>
</p>

### ☁️ Runs On

<p align="center">
  <img src="https://img.shields.io/badge/AWS-EKS-FF9900?style=flat-square&logo=amazonaws&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?style=flat-square&logo=argo&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Envoy-Gateway-AC6DFF?style=flat-square&logo=envoy&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/cert--manager-Auto%20TLS-00ADB5?style=flat-square&logo=kubernetes&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Helm-Charts-0F1689?style=flat-square&logo=helm&logoColor=white"/>
</p>

<br/>

> **SkillPulse** is a cloud-native skill tracking platform running on a production-grade DevSecOps pipeline — from a `git push` to a live, TLS-secured deployment on AWS EKS, fully automated via GitHub Actions, ArgoCD, and Terraform, with end-to-end observability.

<br/>

</div>

---

## 📸 What is SkillPulse?

SkillPulse is more than a skill tracker — it's a **living DevOps portfolio project**. The application lets you log learning hours and visualize growth across technologies. The infrastructure around it demonstrates the full breadth of a modern DevSecOps engineer's toolkit:

- 🔒 **Shift-left security** — secrets, Dockerfile, and image scanning before anything ships
- 🚢 **GitOps delivery** — every manifest change is the source of truth; ArgoCD does the rest
- ☁️ **Cloud-native infrastructure** — fully provisioned via Terraform, zero click-ops
- 📡 **Production observability** — Prometheus metrics + Grafana dashboards out of the box

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          DEVELOPER MACHINE                              │
│                          git push / PR                                  │
└───────────────────────────────┬─────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         GITHUB ACTIONS  (CI/CD)                         │
│                                                                         │
│   ┌──────────────┐  ┌──────────────┐  ┌────────────────────────────┐   │
│   │   Gitleaks   │  │   Hadolint   │  │          Trivy             │   │
│   │ Secret scan  │  │  Dockerfile  │  │  Image CVE scan            │   │
│   │  (pre-build) │  │    lint      │  │  (blocks HIGH/CRITICAL)    │   │
│   └──────┬───────┘  └──────┬───────┘  └────────────┬───────────────┘   │
│          └─────────────────┴───────────────────────┘                   │
│                                  │                                      │
│                   ┌──────────────▼──────────────┐                      │
│                   │  Docker Build & Push         │                      │
│                   │  Update K8s image tag        │                      │
│                   └──────────────┬──────────────┘                      │
└──────────────────────────────────┼──────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         ARGOCD  (GitOps)                                │
│              Watches repo → detects manifest change → syncs             │
└───────────────────────────────┬─────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        AWS EKS CLUSTER                                  │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  Namespace: skillpulse                                          │   │
│  │                                                                 │   │
│  │  ┌───────────────┐  ┌──────────────────┐  ┌─────────────────┐  │   │
│  │  │   Frontend    │  │     Backend      │  │   MySQL 8.4     │  │   │
│  │  │ React · :80   │  │  Go+Gin · :8080  │  │ StatefulSet     │  │   │
│  │  │   NodePort    │  │   ClusterIP      │  │ PVC gp2 · 1Gi   │  │   │
│  │  └───────┬───────┘  └────────┬─────────┘  └─────────────────┘  │   │
│  │          └──────────┬────────┘                                  │   │
│  └───────────────────┬─┴──────────────────────────────────────────┘   │
│                      │                                                  │
│  ┌───────────────────▼──────────────────────────────────────────────┐  │
│  │  Envoy Gateway  (Kubernetes Gateway API)                         │  │
│  │  AWS NLB → TLS (cert-manager + Let's Encrypt)                   │  │
│  │  /      → frontend    /api   → backend                          │  │
│  └──────────────────────────────────────────────────────────────────┘  │
│                                                                         │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │  Namespace: monitoring                                            │  │
│  │  Prometheus (scraping)  +  Grafana (dashboards)                  │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology | Role |
|---|---|---|
| **Frontend** | React | Skill tracking UI |
| **Backend** | Go + Gin | REST API |
| **Database** | MySQL 8.4 (StatefulSet) | Persistent storage |
| **Containerization** | Docker, Docker Compose | Local + prod parity |
| **Infrastructure** | Terraform (AWS) | EKS, VPC, IAM — zero click-ops |
| **Orchestration** | Kubernetes on EKS | Production workloads |
| **GitOps** | ArgoCD | Continuous delivery |
| **Ingress / Gateway** | Envoy Gateway (K8s Gateway API) | L7 routing, NLB |
| **TLS** | cert-manager + Let's Encrypt | Automated HTTPS |
| **CI/CD** | GitHub Actions | Build, test, secure, deploy |
| **Secret Scanning** | Gitleaks | Prevent credential leaks |
| **Image Security** | Trivy | Block CVEs before they ship |
| **Dockerfile Linting** | Hadolint | Enforce best practices |
| **Observability** | Prometheus + Grafana | Metrics & dashboards |

---

## 🔄 CI/CD Pipeline

Every `git push` flows through a gated, security-first pipeline before anything reaches the cluster.

```
git push
   │
   ├─► [1] Gitleaks         Secret & credential scan (blocks on leak)
   │
   ├─► [2] Hadolint         Dockerfile best-practice lint (non-root, layer opt)
   │
   ├─► [3] Docker Build     Multi-stage image build
   │
   ├─► [4] Trivy Scan       CVE scan — HIGH/CRITICAL blocks the pipeline
   │
   ├─► [5] Docker Push      Publish image to Docker Hub
   │
   ├─► [6] Update Manifest  Bump image tag in k8s/ directory
   │
   └─► [7] ArgoCD Sync      Detects manifest diff → deploys to EKS automatically
```

### GitHub Actions Job Matrix

```yaml
jobs:
  security-scan:      # Gitleaks — scans entire commit history for secrets
  lint:               # Hadolint — Dockerfile quality gate
  build-and-push:     # Multi-stage Docker build + Trivy CVE scan + Docker Hub push
  update-manifest:    # Patches image tag in k8s manifests; triggers ArgoCD sync
```

> **Policy**: The pipeline is non-negotiable. No HIGH or CRITICAL CVE, no hardcoded secret, and no Dockerfile violation makes it past the gate — regardless of urgency.

---

## 🔐 DevSecOps

Security is embedded into every stage of the pipeline, not bolted on at the end.

| Stage | Tool | What It Catches |
|---|---|---|
| **Pre-build** | Gitleaks | Hardcoded API keys, passwords, tokens in source code & history |
| **Pre-build** | Hadolint | Running as root, missing `HEALTHCHECK`, unpinned base images, layer bloat |
| **Post-build** | Trivy | Known CVEs in OS packages and language dependencies (HIGH/CRITICAL gate) |

**Fail fast, fix early.** A broken pipeline is better than a compromised cluster.

<p align="center">
  <img src="https://img.shields.io/badge/%F0%9F%9A%A8%20Pipeline%20Failed%3F-Fix%20it%2C%20don't%20skip%20it-FF4B4B?style=flat-square"/>
  &nbsp;
  <img src="https://img.shields.io/badge/%F0%9F%94%92%20Security-Shift%20Left-22c55e?style=flat-square"/>
  &nbsp;
  <img src="https://img.shields.io/badge/%F0%9F%9B%A1%EF%B8%8F%20Zero%20Trust-Always%20Scan-blue?style=flat-square"/>
</p>

---

## ☸️ Kubernetes Manifests

```
namespace: skillpulse
│
├── Secret             skillpulse-db          DB credentials (base64-encoded)
├── ConfigMap          mysql-init             init.sql — seeds skills & learning_logs tables
│
├── StatefulSet        mysql                  MySQL 8.4, gp2 PVC 1Gi
├── Service            mysql                  Headless ClusterIP (stable DNS for StatefulSet)
│
├── Deployment         backend                Go API · image: harsh9308/skillpulse-backend
│   └── Service        backend                ClusterIP :8080, /health liveness + readiness
│
├── Deployment         frontend               React · image: harsh9308/skillpulse-frontend
│   └── Service        frontend               NodePort :30080, / liveness + readiness
│
├── Gateway            skillpulse-gateway     Envoy Gateway · AWS NLB
├── HTTPRoute          skill-route            / → frontend · /api → backend
├── Certificate        skillpulse-tls         cert-manager · Let's Encrypt · auto-renew
└── BackendTrafficPolicy  skillpulse-session  Consistent-hash load balancing
```

**Seeded data (via init.sql):** Docker, Kubernetes, Go, Azure DevOps, Terraform

---

## 🌍 Infrastructure — Terraform

All AWS infrastructure is code. Zero manual provisioning.

```
terraform/
├── vpc.tf          VPC, public/private subnets, route tables, NAT gateway
├── eks.tf          EKS cluster, managed node groups, add-ons
├── iam.tf          IAM roles — EKS nodes, Load Balancer Controller, ArgoCD
├── variables.tf    Input variables (region, cluster name, instance types)
├── outputs.tf      Cluster endpoint, OIDC issuer, node group ARNs
└── README.md       Post-apply steps: Envoy Gateway, cert-manager
```

```bash
cd terraform/
terraform init
terraform plan -out=tfplan
terraform apply tfplan
aws eks update-kubeconfig --name <cluster-name> --region us-west-2
```

---

## 📊 Observability

Prometheus scrapes metrics from all services. Grafana surfaces them into actionable dashboards.

| Dashboard | Metrics |
|---|---|
| **Pod Health** | CPU, memory per container; restarts, OOMKill events |
| **API Performance** | HTTP request rate, p50/p95/p99 latency, error rate |
| **Database** | MySQL connection pool, query duration, slow queries |
| **Infrastructure** | Node CPU/memory saturation, disk I/O, network throughput |

```bash
# Access Grafana locally
kubectl port-forward svc/grafana 3000:3000 -n monitoring

# Access Prometheus locally
kubectl port-forward svc/prometheus-server 9090:9090 -n monitoring
```

---

## 🚀 Getting Started

### Prerequisites

| Tool | Purpose |
|---|---|
| `aws` CLI | AWS access (configured with credentials) |
| `kubectl` | Cluster management |
| `terraform` | Infrastructure provisioning |
| `helm` | Chart installation |
| `docker` | Local builds |

---

### Step 1 — Provision Infrastructure

```bash
cd terraform/
terraform init && terraform apply
aws eks update-kubeconfig --name <cluster-name> --region us-west-2
```

---

### Step 2 — Install Cluster Dependencies

```bash
# Gateway API CRDs
kubectl apply --server-side \
  -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml

# Envoy Gateway
helm install eg oci://docker.io/envoyproxy/gateway-helm \
  --version v1.2.6 -n envoy-gateway-system --create-namespace --skip-crds --wait

# cert-manager
helm repo add jetstack https://charts.jetstack.io --force-update
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --set config.enableGatewayAPI=true \
  --set crds.enabled=true

# ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

---

### Step 3 — Deploy via ArgoCD (GitOps)

```bash
kubectl apply -f argocd/application.yaml
# ArgoCD continuously syncs k8s/ manifests from the repo to EKS
```

---

### Step 4 — Local Development

```bash
# Full stack: React + Go API + MySQL
docker compose up

# App: http://localhost:3000
# API: http://localhost:8080
```

---

## 📁 Project Structure

```
skillpulse/
├── .github/
│   └── workflows/
│       └── ci.yaml              GitHub Actions pipeline
│
├── frontend/                    React app (skill tracker UI)
├── backend/                     Go + Gin REST API
│
├── k8s/                         Kubernetes manifests
│   ├── namespace.yaml
│   ├── mysql.yaml               StatefulSet + Headless Service + Secret + ConfigMap
│   ├── backend.yaml             Deployment + ClusterIP Service
│   ├── frontend.yaml            Deployment + NodePort Service
│   ├── gateway.yaml             Envoy Gateway + HTTPRoute + BackendTrafficPolicy
│   └── certificate.yaml         cert-manager Certificate
│
├── terraform/                   AWS infrastructure (EKS, VPC, IAM)
├── monitoring/                  Prometheus + Grafana configs
├── argocd/                      ArgoCD Application manifest
│
├── Dockerfile                   Multi-stage Docker build
├── docker-compose.yaml          Local dev stack
└── README.md
```

---

## 📌 Current Image Tags

| Service | Image |
|---|---|
| Backend | `harsh9308/skillpulse-backend:d46b700` |
| Frontend | `harsh9308/skillpulse-frontend:d46b700` |

> Tags are automatically bumped by the CI pipeline on every successful build.

---

## 🙏 Credits

SkillPulse UI is based on an open-source skill tracking project.

All DevOps infrastructure, CI/CD pipeline, GitOps configuration, DevSecOps tooling, TLS automation, and observability stack designed and implemented by **[Harsh](https://github.com/harsh9308)**.

---

## 📄 License

This project inherits the license of the original open-source UI. All DevOps additions — pipelines, manifests, Terraform, monitoring configs — are freely available for learning and reference.

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:24243e,50:302b63,100:0f0c29&height=100&section=footer" width="100%"/>

<p align="center">
  <img src="https://img.shields.io/badge/Made%20with-%E2%9D%A4%EF%B8%8F%20by%20Harsh-FF6B6B?style=for-the-badge"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Powered%20by-AWS%20EKS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Shipped%20via-ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/No%20Click--Ops-%E2%9C%85%20Terraform%20Only-7B42BC?style=flat-square&logo=terraform&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/GitOps-%E2%9C%85%20Source%20of%20Truth-EF7B4D?style=flat-square&logo=argo&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Observability-%E2%9C%85%20Prometheus%20%2B%20Grafana-E6522C?style=flat-square&logo=prometheus&logoColor=white"/>
  &nbsp;
  <img src="https://img.shields.io/badge/TLS-%E2%9C%85%20Auto%20Renewed-003A70?style=flat-square&logo=letsencrypt&logoColor=white"/>
</p>

<br/>

**Built with obsession by [Harsh](https://github.com/harsh9308) · Deployed on AWS EKS · Secured end-to-end · GitOps all the way**

</div>
