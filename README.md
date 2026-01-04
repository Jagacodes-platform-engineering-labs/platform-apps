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

## Sample App Set Up

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: sample-backend
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<YOUR_ORG>/platform-apps.git
    targetRevision: main
    path: apps/sample-backend
  destination:
    server: https://kubernetes.default.svc
    namespace: apps
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
