# Week 1 – Platform Engineering GitOps Walkthrough

## Objective

The goal of **Week 1** was to build a realistic, end-to-end platform foundation that mirrors how modern Platform and DevOps teams operate in production.

The focus was on **learning by doing**, while producing artefacts that can be confidently used in a professional portfolio.

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

### Namespace Strategy

- `platform` → platform-level components  
- `argocd` → GitOps control plane  
- `apps` → application workloads  

### GitHub Repositories  
*(Organization-scoped: `Jagacodes-platform-engineering-labs`)*

- `platform-infra` → platform and cluster-level concerns  
- `platform-apps` → GitOps-managed application manifests  
- `backend-service` → application source code  
- `developer-templates` → future golden paths *(not implemented yet)*

---

## Folder Structure

### Top Level

<img width="478" height="659" alt="image" src="https://github.com/user-attachments/assets/a2cec36d-72b6-4288-b521-be1fcc5eeef1" />

### platform-apps

<img width="492" height="408" alt="image" src="https://github.com/user-attachments/assets/4b628caa-407b-4b25-a884-ed76d1c4dc90" />

---

## Step 1: Kubernetes Cluster Setup

### Cluster Creation

A multi-node Kubernetes cluster was created using Minikube on Ubuntu:

- 1 control plane node  
- 2 worker nodes  

```bash
minikube start \
  --nodes=3 \
  --cpus=2 \
  --memory=12000
