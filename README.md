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


⚙️ Step 2: Add a Helm Repository
Add the Bitnami repository (commonly used for production-ready charts):

bash
Copy
Edit
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update


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

## ⚙️ Step 4: Pass Custom Values

Helm allows overriding chart defaults in **3 ways**:  
1. Inline values (`--set`)  
2. Strings (`--set-string`)  
3. External files (`--set-file` or `-f values.yaml`)  

---

### 🔹 Option A: Inline values
```bash
helm install wordpress-app bitnami/wordpress \
  --set wordpressUsername=admin \
  --set wordpressPassword=Secret123 \
  --set mariadb.auth.rootPassword=Secret123
🔹 Option B: Using a custom values.yaml
Create custom-values.yaml:

yaml
Copy
Edit
wordpressUsername: admin
wordpressPassword: Secret123
mariadb:
  auth:
    rootPassword: Secret123
Then install with the file:

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress -f custom-values.yaml
🔹 Option C: Using --set-string
Sometimes values can be misinterpreted (like numbers being treated as integers, or true/false as booleans).
Use --set-string to force values to remain strings.

Example:

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress \
  --set-string service.type="LoadBalancer" \
  --set-string replicaCount="3" \
  --set-string wordpressPassword="1234567890"
This ensures replicaCount is treated as "3" (string), not 3 (integer).

🔹 Option D: Using --set-file
If you have large secrets, TLS certs, or config files, you can inject them directly.

Example with config.yaml:

yaml
Copy
Edit
config:
  key1: value1
  key2: value2
Command:

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress \
  --set-file config=./config.yaml
Example with a secret file (db-password.txt):

bash
Copy
Edit
helm install wordpress-app bitnami/wordpress \
  --set-file mariadb.auth.rootPassword=./db-password.txt
This securely passes the contents of db-password.txt as the value.



yaml
Copy
Edit

---

✅ This way your documentation now covers **all methods of passing values**:  
- `--set` (inline)  
- `-f values.yaml` (file-based)  
- `--set-string` (force string type)  
- `--set-file` (inject file content like secrets/certs)  

---

👉 Do you also want me to add a **sample full `values.yaml`** (for WordPress or Nginx) so that user


⚙️ Step 5: Upgrade a Release

Modify configuration or update chart version:

helm upgrade wordpress-app bitnami/wordpress -f custom-values.yaml


📸 Screenshot:


Check release history:

helm history wordpress-app


📸 Screenshot:


Rollback if needed:

helm rollback wordpress-app 1


📸 Screenshot:


⚙️ Step 6: Uninstall a Release

Remove WordPress from the cluster:

helm uninstall wordpress-app


📸 Screenshot:


⚙️ Step 7: Create Your Own Helm Chart

Generate a new chart:

helm create mychart


This creates a folder structure:

mychart/
  ├── charts/
  ├── templates/
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   └── _helpers.tpl
  └── values.yaml


📸 Screenshot:


Install your custom chart:

helm install myapp ./mychart


📸 Screenshot:


📂 Useful Helm Commands
# List all installed releases across namespaces
helm list -A

# Show default values of a chart
helm show values bitnami/wordpress

# Dry run to test install
helm install --dry-run --debug wordpress-app bitnami/wordpress

# Render templates without installing
helm template wordpress-app bitnami/wordpress

✅ Summary

In this guide, we learned how to:
✔️ Install Helm
✔️ Add repositories
✔️ Install and customize charts
✔️ Upgrade, rollback, and uninstall releases
✔️ Create custom Helm charts

With Helm, managing Kubernetes applications becomes simpler, repeatable, and more reliable 🚀
