```md
# Platform Applications (GitOps)

This directory contains Kubernetes manifests representing the desired state of all applications deployed via the platform.

Applications in this folder are managed using **GitOps** and continuously reconciled by ArgoCD.

---

## Purpose

- Define application deployments declaratively
- Provide a single source of truth for runtime state
- Enable safe, auditable, and repeatable deployments

---

## GitOps Model

- Git is the source of truth
- ArgoCD continuously reconciles cluster state
- Manual `kubectl apply` is not permitted for application deployments
- Changes are introduced via pull requests

---

## Directory Structure

```text
apps/
├── sample-backend/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
```
Each application folder represents:

- One deployable service

- One or more environments (initially dev)

## Deployment Lifecycle

- Application image is built and pushed to a container registry

- Kubernetes manifests are updated in this repository

- ArgoCD detects changes and syncs the cluster

- Rollbacks are performed by reverting Git commits

## Design Principles

- Namespaced isolation (apps namespace)

- Resource requests and limits are mandatory

- Health probes are required

- Stateless workloads only

## Environments

Initial implementation supports:

- dev

Future iterations will introduce:

- staging

- production

Environment promotion workflows Governance

## This repository enforces:

- Declarative deployments

- Auditability

- Change traceability

- Consistent deployment patterns

## Audience

This repository is primarily used by:

- Platform engineers

- Site reliability engineers

Application developers typically interact with the platform via:

- Developer portals

- Templates

- Self-service workflows














