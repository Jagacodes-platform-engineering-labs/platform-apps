# Week 1 – Platform Engineering GitOps Walkthrough

## Objective

The goal of Week 1 was to build a realistic, end-to-end platform foundation that mirrors how modern platform and DevOps teams operate in production. The focus was on learning by doing, while producing artefacts that can be confidently used in a professional portfolio.

By the end of this week, we achieved:

- A multi-node Kubernetes cluster  
- A clear platform repository structure  
- A working GitOps workflow using ArgoCD  
- A real backend application deployed automatically from GitHub  
- Verified application access via service, port-forward, and ingress  

---

## Architecture Overview

### Local Platform Stack

- **Kubernetes distribution:** Minikube  
- **Cluster topology:** 1 control plane + 2 worker nodes  
- **GitOps engine:** ArgoCD  
- **Application runtime namespace:** `apps`  
- **Platform control namespace:** `platform`  
- **GitOps control namespace:** `argocd`  

### GitHub Repositories  
*(Organization-scoped – Jagacodes-platform-engineering-labs)*

- `platform-infra` → platform and cluster-level concerns  
- `platform-apps` → GitOps-managed application manifests  
- `backend-service` → application source code  
- `developer-templates` → future golden paths (not implemented yet)  

---

## Folder Structure

### Top Level

<img src="https://github.com/user-attachments/assets/a2cec36d-72b6-4288-b521-be1fcc5eeef1" />

### Platform-apps

<img src="https://github.com/user-attachments/assets/4b628caa-407b-4b25-a884-ed76d1c4dc90" />



## Step 1: Kubernetes Cluster Setup

### Cluster Creation

A multi-node Kubernetes cluster was created using Minikube on Ubuntu:

- 1 control plane node  
- 2 worker nodes  

Command used:

    minikube start \
      --nodes=3 \
      --cpus=2 \
      --memory=12000

This topology mirrors real production environments where control plane and workload responsibilities are separated.

### Cluster Verification

Commands used:

    kubectl cluster-info

<img src="https://github.com/user-attachments/assets/14611a5d-66df-4474-805d-d2cfef49f10d" />

    kubectl get nodes

<img src="https://github.com/user-attachments/assets/8a1c06aa-0ab6-4f86-b650-40f0ed6af46e" />



## Namespace Strategy

To enforce separation of concerns and future RBAC boundaries, the following namespaces were created:

- `platform` → platform-level components  
- `argocd` → GitOps control plane  
- `apps` → all application workloads  

<img src="https://github.com/user-attachments/assets/c4d5f345-0a5b-4a51-94da-71dfe6506343" />

This structure matches enterprise Kubernetes best practices.

---

## Step 2: Installing ArgoCD (GitOps Engine)

ArgoCD was installed into the `argocd` namespace using the official manifests.

Commands used:

    kubectl apply -n argocd \
      -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

    kubectl get pods -n argocd

<img src="https://github.com/user-attachments/assets/007e59eb-baf9-4f6a-8afd-f8d615d815c3" />

Post-installation verification included:

- Ensuring all ArgoCD pods were in `Running` state  
- Confirming the ArgoCD API server was reachable  

### Accessing the ArgoCD Dashboard

Port-forward command:

    kubectl port-forward svc/argocd-server -n argocd 8080:443

Access URL:

    https://localhost:8080

Retrieve the admin password:

    kubectl -n argocd get secret argocd-initial-admin-secret \
      -o jsonpath="{.data.password}" | base64 -d

<img src="https://github.com/user-attachments/assets/c03f30bf-53d5-4f38-9813-daff953e66be" />

---

## Step 4: Backend Application (Real Workload)

### Backend Application Repository

A simple backend service was implemented and pushed to a dedicated repository (`backend-service`).

**Characteristics:**

- HTTP-based service  
- Exposes a service port (3000)  
- Provides a basic response and/or health endpoint  

<img src="https://github.com/user-attachments/assets/bdbd261c-9f6e-46e9-afec-acddabbcd83e" />

---

## Step 5: Application Manifests (GitOps Source of Truth)

All Kubernetes manifests for the backend application live in:

    platform-apps/apps/sample-backend

Included resources:

- Deployment (3 replicas)  
- Service (ClusterIP)  
- Ingress (for external access)  

These manifests represent the desired state of the application and are the only deployment mechanism.

No manual `kubectl apply` is used for applications.

<img src="https://github.com/user-attachments/assets/1cb61f19-7cd1-4ec2-a4eb-4204dcfc06a8" />

---

## Step 6: ArgoCD Application Definition

ArgoCD does not automatically discover repositories. An explicit `Application` resource was created to link GitHub to the cluster.

A bootstrap folder contains an `app-of-apps.yaml` file which defines how applications are structured and displayed on the ArgoCD GitOps platform. The `apps` folder inside the bootstrap directory contains `application.yaml` files referencing individual applications.

Path:

    platform-apps/bootstrap/apps/application

This manifest defines:

- The GitHub repository (`platform-apps`)  
- The exact folder path (`apps/sample-backend`)  
- The destination cluster and namespace (`apps`)  
- Automated synchronization policy  

Once applied, ArgoCD began continuously reconciling GitHub state with the cluster.

This is the critical GitOps control point.

---

## Step 7: GitOps in Action (Automatic Sync)

After committing manifests to GitHub:

- ArgoCD detected changes  
- Applied manifests automatically  
- Created Deployment, Service, and Ingress  
- Maintained application health continuously  

No manual redeployments were required.

---

## Step 8: Application Verification

The deployed application was validated using multiple methods.

### 1. Port-Forwarding

- Forwarded the service port to localhost  
- Verified HTTP response  

<img src="https://github.com/user-attachments/assets/c9a4ac8c-cbd0-4bb6-8912-90dc57f126b3" />

### 2. Internal Cluster Access

- Tested service discovery via Kubernetes DNS  
- Confirmed pod-to-service communication  

### 3. Ingress Access

- Enabled Minikube ingress addon  
- Configured Ingress resource  
- Resolved domain using Minikube IP  
- Successfully accessed the app via browser and `curl`  

<img src="https://github.com/user-attachments/assets/4961f59e-b45c-4045-9c11-d6d5458c7f1c" />
<img src="https://github.com/user-attachments/assets/582304b3-b92c-4a31-b941-8027c089bb5b" />

This confirms the application is reachable through realistic access patterns.

---

## Outcome and Key Learnings

By the end of Week 1, the platform demonstrates:

- A reproducible Kubernetes environment  
- Clear platform/app separation  
- GitOps-driven continuous reconciliation  
- Real application lifecycle management  
- Production-aligned namespace and access patterns  
