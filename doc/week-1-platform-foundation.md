Week 1 – Platform Engineering GitOps Walkthrough
Objective
The goal of Week 1 was to build a realistic, end‑to‑end platform foundation that mirrors how modern platform and DevOps teams operate in production. The focus was on learning by doing, while producing artefacts that can be confidently used in a professional portfolio.
By the end of this week, we achieved: - A multi‑node Kubernetes cluster - A clear platform repository structure - A working GitOps workflow using ArgoCD - A real backend application deployed automatically from GitHub - Verified application access via service, port‑forward, and ingress
________________________________________
Architecture Overview
Local Platform Stack - Kubernetes distribution: Minikube - Cluster topology: 1 control plane + 2 worker nodes  
GitOps engine: ArgoCD
 - Application runtime namespace: apps 
- Platform control namespace: platform 
- GitOps control namespace: argocd
GitHub Repositories (Organization‑scoped - Jagacodes-platform-engineering-labs) 
- platform-infra → platform and cluster‑level concerns 
- platform-apps → GitOps‑managed application manifests 
- backend-service → application source code 
- developer-templates → future golden paths (not implemented yet)

Folder Structure
Top Level
<img width="478" height="659" alt="image" src="https://github.com/user-attachments/assets/a2cec36d-72b6-4288-b521-be1fcc5eeef1" />

 
Platform-apps
<img width="492" height="408" alt="image" src="https://github.com/user-attachments/assets/4b628caa-407b-4b25-a884-ed76d1c4dc90" />


Step 1: Kubernetes Cluster Setup
Cluster Creation
A multi‑node Kubernetes cluster was created using Minikube on Ubuntu:
•	1 control plane node
•	2 worker nodes

minikube start \
  --nodes=3 \
  --cpus=2 \
  --memory=12000

This topology mirrors real production environments where control plane and workload responsibilities are separated.
After cluster creation, connectivity was validated: 
- kubectl cluster-info 
<img width="975" height="99" alt="image" src="https://github.com/user-attachments/assets/14611a5d-66df-4474-805d-d2cfef49f10d" />

- kubectl get nodes
<img width="975" height="191" alt="image" src="https://github.com/user-attachments/assets/8a1c06aa-0ab6-4f86-b650-40f0ed6af46e" />
 
Namespace Strategy
To enforce separation of concerns and future RBAC boundaries, the following namespaces were created:
•	platform → platform‑level components
•	argocd → GitOps control plane
•	apps → all application workloads
<img width="947" height="422" alt="image" src="https://github.com/user-attachments/assets/c4d5f345-0a5b-4a51-94da-71dfe6506343" />

 
This structure matches enterprise Kubernetes best practices.
Step 2: Installing ArgoCD (GitOps Engine)
ArgoCD was installed into the argocd namespaces namespace using the official manifests.
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get pods -n argocd
<img width="975" height="220" alt="image" src="https://github.com/user-attachments/assets/007e59eb-baf9-4f6a-8afd-f8d615d815c3" />

Post‑installation verification included: - Ensuring all ArgoCD pods were in Running state - Confirming the ArgoCD API server was reachable
To access the ArgoCD dashboard, we use kubectl port-forward svc/argocd-server -n argocd 8080:443
Then open browser on https://localhost:8080
Obtain the login password from kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
<img width="975" height="448" alt="image" src="https://github.com/user-attachments/assets/c03f30bf-53d5-4f38-9813-daff953e66be" />

 

Step 4: Backend Application (Real Workload)
Backend Application Repository
A simple backend service was implemented and pushed to a dedicated repository (backend-service).
Characteristics: - HTTP‑based service - Exposes a service port (3000) - Provides a basic response and/or health endpoint
Backend-Service
<img width="458" height="258" alt="image" src="https://github.com/user-attachments/assets/bdbd261c-9f6e-46e9-afec-acddabbcd83e" />


________________________________________
Step 5: Application Manifests (GitOps Source of Truth)
All Kubernetes manifests for the backend application live in:
platform-apps/apps/sample-backend
Included resources: - Deployment (3 replicas) - Service (ClusterIP) - Ingress (for external access)
These manifests represent the desired state of the application and are the only deployment mechanism.
No manual kubectl apply is used for applications.
<img width="483" height="502" alt="image" src="https://github.com/user-attachments/assets/1cb61f19-7cd1-4ec2-a4eb-4204dcfc06a8" />

 
________________________________________
Step 6: ArgoCD Application Definition
ArgoCD does not automatically discover repositories. An explicit Application resource was created to link GitHub to the cluster.
A bootstrap folder containing app-of-apps.yaml file which provides a defined structure of how the applications will be displayed and linked on Argocd Gitops platform. The app folder inside of the bootstrap folder will contain application.yaml files referencing individual application. i.e we can have multiple applications
Platform-apps/bootstrap/apps/application
This manifest defines: - The GitHub repository (platform-apps) - The exact folder path (apps/sample-backend) - The destination cluster and namespace (apps) - Automated synchronization policy
Once applied, ArgoCD began continuously reconciling GitHub state with the cluster.
This is the critical GitOps control point.
________________________________________
Step 7: GitOps in Action (Automatic Sync)
After committing manifests to GitHub: - ArgoCD detected changes - Applied manifests automatically - Created Deployment, Service, and Ingress - Maintained application health continuously
No manual redeployments were required.
________________________________________
Step 8: Application Verification
The deployed application was validated using multiple methods:
1. Port‑Forwarding
•	Forwarded the service port to localhost
•	Verified HTTP response
<img width="790" height="169" alt="image" src="https://github.com/user-attachments/assets/c9a4ac8c-cbd0-4bb6-8912-90dc57f126b3" />

 
2. Internal Cluster Access
•	Tested service discovery via Kubernetes DNS
•	Confirmed pod‑to‑service communication
3. Ingress Access
•	Enabled Minikube ingress addon
•	Configured Ingress resource
•	Resolved domain using Minikube IP
•	Successfully accessed the app via browser and curl
This confirms the application is reachable through realistic access patterns.

<img width="1611" height="669" alt="image" src="https://github.com/user-attachments/assets/4961f59e-b45c-4045-9c11-d6d5458c7f1c" />

<img width="1617" height="787" alt="image" src="https://github.com/user-attachments/assets/582304b3-b92c-4a31-b941-8027c089bb5b" />

________________________________________
Outcome and Key Learnings
By the end of Week 1, the platform demonstrates:
•	A reproducible Kubernetes environment
•	Clear platform/app separation
•	GitOps‑driven continuous reconciliation
•	Real application lifecycle management
•	Production‑aligned namespace and access patterns
________________________________________
Portfolio Summary Statement
“Built a local multi‑node Kubernetes platform using Minikube and implemented GitOps‑based application delivery with ArgoCD. Designed repository separation for platform infrastructure and applications, deployed a real backend service via automated synchronization, and validated runtime access using Kubernetes services and ingress.”
This completes Week 1 and establishes a strong foundation for advanced platform capabilities in subsequent weeks.
