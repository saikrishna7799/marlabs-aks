# marlabs-aks

# 📘 Internal Blog Website – AKS-Only Architecture (Azure Cloud Engineer Case Study)

## 📌 Project Overview

This case study presents a **fully containerized, AKS-only architecture** for an internal **Blog Web App** that supports:

- 🔐 User Authentication
- ✍️ Blog Creation and Approval
- 💬 Comments & Real-Time Chat
- 🌟 Feature Request Submissions

> 📈 Designed to scale from **50 initial users** to **~2,000 users within one month** using Kubernetes-native scaling strategies and Azure best practices.

---

## 🚀 Scaling Requirements

| Metric              | Value                        |
|---------------------|------------------------------|
| Initial Users       | 50                           |
| Growth Rate         | +100 users/week              |
| Target Load (1 Mo)  | ~2,000 users                 |
| Key Requirement     | Elastic scaling & cost-efficiency |

### ✅ How Scaling Is Handled in AKS-Only Architecture:

| Component             | Scaling Mechanism                                      |
|----------------------|---------------------------------------------------------|
| **Frontend Pods**     | Horizontal Pod Autoscaler (HPA) based on CPU/RPS        |
| **Backend API Pods**  | HPA + Cluster Autoscaler for workload-specific pools    |
| **Redis**             | StatefulSet + custom metrics-based scaling              |
| **AKS Nodes**         | Cluster Autoscaler with custom VM sizes                 |
| **Database**          | Azure SQL and Cosmos DB with auto-scale & geo-replica   |
| **Blob Storage**      | Auto-scale by nature (pay-as-you-go)                   |

---

## 🧱 Architecture Overview (AKS-Only)

Everything runs as containerized workloads in Azure Kubernetes Service (AKS) across **dedicated namespaces** and **node pools**.

### Components

| Layer | Service | Purpose |
|-------|---------|---------|
| **Frontend** | SPA (React/Angular) | Hosted as a container in AKS |
| **API Layer** | RESTful APIs (Node/.NET Core) | Microservices for business logic |
| **Auth** | Azure AD B2C | Centralized identity provider |
| **Database** | Azure SQL (Relational), Cosmos DB (NoSQL) | Core data + chat/comments |
| **Cache** | Azure Redis (via Helm/Operator) | Speed up frequent requests |
| **Storage** | Azure Blob | Media & file uploads |

---

## 🔐 Security Best Practices

| Category             | Implementation Details |
|----------------------|------------------------|
| **Authentication**   | Azure AD B2C with JWT validation in APIs |
| **Authorization**    | Role-based claims mapping + RBAC in AKS |
| **Ingress Security** | NGINX Ingress with TLS (Let's Encrypt or Key Vault certs), WAF at App Gateway |
| **Secrets Management** | Azure Key Vault + CSI Driver in AKS |
| **Network Security** | Private AKS cluster, NSGs, UDRs, Azure CNI |
| **Pod Security**      | PodSecurityPolicies, Network Policies, AppArmor, SecComp |
| **Registry Access**   | ACR + Managed Identity from AKS |

---

## 🔁 CI/CD Automation with Azure DevOps

### CI Pipeline (Build)
- Trigger: On push or PR to `main`
- Steps:
  - Run Tests & Linting
  - Docker Build (Frontend, Backend)
  - Push to Azure Container Registry (ACR)
  - Store artifact manifest

### CD Pipeline (Release)
- Trigger: Manual or on successful CI
- Stages:
  1. **Terraform Deployment**
     - VNet, AKS, Key Vault, ACR, Log Analytics
  2. **App Deployment to AKS**
     - Helm/`kubectl apply` for all services
  3. **Post-deployment Validation**
     - Smoke tests, alerts configured
  4. **Approval Gates**
     - Manual intervention before Prod
     
### 🔁 Rollback Strategy
| Component | Rollback Mechanism |
|-----------|--------------------|
| Frontend/API | `kubectl rollout undo` or Helm rollback |
| Infra | Terraform `state` + version pinning |
| Pipelines | Azure DevOps stages with retry + approval gates |
| Key Vault | Versioned secrets with access history |


