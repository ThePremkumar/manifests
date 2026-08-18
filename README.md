# Moodscape - Continuous Deployment (CD) 🚀

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-%23EF7B4D.svg?style=for-the-badge&logo=argo&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)

Welcome to the Continuous Deployment repository for the **Moodscape** project. This repository manages the GitOps workflow and Kubernetes manifests to deploy the Moodscape full-stack application (FastAPI backend + Frontend) to an Amazon EKS cluster.

## 📖 Overview

The deployment architecture leverages **ArgoCD** for declarative GitOps continuous delivery. Changes made to the manifests in this repository are automatically synchronized and applied to the target Kubernetes cluster. 

The application is exposed to the internet using an **AWS Application Load Balancer (ALB)** via the AWS Load Balancer Controller.

### Key Components
* **Frontend**: Containerized web application serving the user interface.
* **Backend**: Containerized **FastAPI** service handling core business logic and API requests.
* **Ingress**: AWS ALB Ingress controller routing traffic based on paths (`/` to frontend, `/api` to backend).
* **GitOps**: ArgoCD monitors this repository and manages the application lifecycle in the `moodscape` namespace.

---

## 📂 Repository Structure

* `application.yaml`: The core ArgoCD Application definition linking this repository to the cluster.
* `cd/k8s.yaml`: The comprehensive Kubernetes manifests containing the Namespace, Deployments, Services, and Ingress routing rules for Moodscape.
* `iam_policy.json`: The required AWS IAM policy to grant the AWS Load Balancer Controller the necessary permissions to provision and manage ELB resources on AWS.

---

## 🛠️ Infrastructure Requirements

Before deploying the manifests, ensure the following prerequisites are met:
1. **Amazon EKS Cluster**: A running EKS cluster.
2. **AWS Load Balancer Controller**: Deployed within the cluster with the IAM role utilizing the policy defined in `iam_policy.json`.
3. **ArgoCD**: Installed and running in the cluster (typically in the `argocd` namespace).
4. **AWS ECR**: The container images (`moodscape-frontend:v1` and `moodscape-backend:v1`) must be available in the configured Amazon Elastic Container Registry.

---

## 🚀 Deployment Workflow

1. **Update Manifests**: Make changes to the deployment specifications, replica counts, or image tags within the `cd/k8s.yaml` file.
2. **Commit & Push**: Commit the updates and push them to the `main` branch.
3. **ArgoCD Sync**: ArgoCD will detect the configuration drift and automatically reconcile the cluster state to match the definitions in this repository (self-healing and pruning are enabled).

To manually trigger the initial setup, apply the ArgoCD application manifest:
```bash
kubectl apply -f application.yaml
```

---

## 🔒 Security & Access

The ArgoCD repository secret is securely defined in `application.yaml` to authenticate with GitHub. Ensure that your AWS credentials, Kubernetes RBAC, and repository access tokens are securely managed and rotated according to best practices.
