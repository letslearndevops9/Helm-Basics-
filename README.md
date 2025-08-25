# Helm-Basics-

# 🚀 Helm Installation & Customization Guide

This guide provides a **practical step-by-step workflow** for using Helm with Kubernetes:  
- Installing Helm  
- Adding repositories  
- Installing charts  
- Passing custom values  
- Upgrading & rolling back  
- Uninstalling releases  
- Creating your own Helm charts  

Screenshots are provided from a real setup for clarity.

---

## 📖 Prerequisites

Before starting, make sure you have:  
- A Kubernetes cluster running (e.g., Minikube, AKS, EKS, GKE)  
- `kubectl` installed and configured  
- Internet access to fetch Helm charts  

Check cluster connection:

```bash
kubectl get nodes
⚙️ Step 1: Install Helm
Download and install Helm 3:

bash
Copy
Edit
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
Verify installation:

bash
Copy
Edit
helm version
📸 Screenshot:

⚙️ Step 2: Add a Helm Repository
Add the Bitnami repository (commonly used for production-ready charts):

bash
Copy
Edit
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
📸 Screenshot:

Check available charts:

bash
Copy
Edit
helm search repo bitnami
⚙️ Step 3: Install a Chart
Install WordPress with default values:

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress
📸 Screenshot:

Check installed releases:

bash
Copy
Edit
helm list
⚙️ Step 4: Pass Custom Values
Helm allows overriding chart defaults.

Option A: Inline values
bash
Copy
Edit
helm install wordpress-app bitnami/wordpress \
  --set wordpressUsername=admin \
  --set wordpressPassword=Secret123 \
  --set mariadb.auth.rootPassword=Secret123
Option B: Using a custom values.yaml
Create custom-values.yaml:

yaml
Copy
Edit
wordpressUsername: admin
wordpressPassword: Secret123
mariadb:
  auth:
    rootPassword: Secret123
Then install:

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress -f custom-values.yaml
