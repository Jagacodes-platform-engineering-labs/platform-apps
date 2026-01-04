# Platform Applications (GitOps)

This repository represents the desired state of all applications and environments managed by the platform.

## Responsibilities
- Application deployments
- Environment definitions (dev, staging)
- Platform services managed via GitOps

## Deployment Model
- ArgoCD continuously reconciles this repository
- No direct kubectl deployments

## Key Pattern
- App-of-Apps for scalability and governance
