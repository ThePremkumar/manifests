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

## 🔗 Related Repositories

* **Continuous Integration (CI) / Source Code**: [ThePremkumar/moodscape](https://github.com/ThePremkumar/moodscape)
  Contains the application source code for the FastAPI backend and frontend, as well as the workflows responsible for building the container images and publishing them to AWS ECR.

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

---

## 📸 Infrastructure & Deployment Gallery

The following screenshots illustrate the complete CI/CD lifecycle, from Jenkins pipelines and AWS ECR to the ArgoCD deployment synchronization on our EKS cluster.

<details>
<summary>Click to view detailed infrastructure screenshots</summary>

<br>

### Continuous Integration (CI)
**1. Jenkins CI Pipeline**
![Jenkins Pipeline](screenshots/Screenshot%202026-08-18%20080619.png)
*The Jenkins pipeline successfully building the application, pushing images to ECR, and automatically updating the Kubernetes manifests in this repository.*

**2. GitHub Manifests Repository**
![GitHub Manifests](screenshots/Screenshot%202026-08-18%20080243.png)
*Automated commit to the `cd` directory updating the deployment image tag to `v17`.*

---

### Container Registry (AWS ECR)
**3. AWS ECR - Backend Repository**
![ECR Backend](screenshots/Screenshot%202026-08-18%20080322.png)
*The `moodscape-backend` Docker images securely stored in Amazon Elastic Container Registry.*

**4. AWS ECR - Frontend Repository**
![ECR Frontend](screenshots/Screenshot%202026-08-18%20080345.png)
*The `moodscape-frontend` Docker images stored and ready for deployment.*

---

### Continuous Deployment (ArgoCD)
**5. ArgoCD - Initial Sync (Failure Recovery)**
![ArgoCD Initial Sync](screenshots/Screenshot%202026-08-16%20194036.png)
*ArgoCD tree view showing an initial deployment state, demonstrating visibility into pod failures (e.g., CrashLoopBackOff) before self-healing.*

**6. ArgoCD - Healthy Deployment**
![ArgoCD Healthy](screenshots/Screenshot%202026-08-18%20072026.png)
*A fully healthy and synchronized state where all frontend and backend pods are running successfully.*

**7. ArgoCD - Pod Details**
![ArgoCD Pod Details](screenshots/Screenshot%202026-08-18%20073406.png)
*Detailed view of a running backend pod, confirming the successful pull and execution of the specific image tag.*

**8. ArgoCD - Rolling Update**
![ArgoCD Rolling Update](screenshots/Screenshot%202026-08-18%20080127.png)
*ArgoCD managing a rolling update, cleanly spinning up a new ReplicaSet while terminating the old one to ensure zero downtime.*

**9. ArgoCD - Sync Status**
![ArgoCD Sync Status](screenshots/Screenshot%202026-08-18%20080145.png)
*The main ArgoCD dashboard confirming the application is perfectly synced to the `HEAD` of the main branch following an image update.*

**10. ArgoCD - Full Application Tree**
![ArgoCD Full Tree](screenshots/Screenshot%202026-08-18%20080204.png)
*A comprehensive view of the entire Moodscape application architecture (Services, Deployments, ReplicaSets, and Pods) managed by ArgoCD.*

---

### Cloud Infrastructure (AWS EKS)
**11. Amazon EKS Cluster**
![EKS Cluster](screenshots/Screenshot%202026-08-18%20080706.png)
*The `moodscape-cluster` running smoothly on Amazon Elastic Kubernetes Service (EKS), acting as the foundation for the entire deployment.*

</details>
