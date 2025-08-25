# Helm Basics

---

## 🎯 Why Helm?

Managing applications on Kubernetes can become complex:

- You often need to write multiple YAML manifests (Deployments, Services, ConfigMaps, Ingress, Secrets, etc.)
- Keeping track of versions, rollbacks, and upgrades is difficult
- Sharing applications across teams requires consistency

**Helm** solves these problems by acting as the **package manager for Kubernetes** (like `apt` for Ubuntu or `yum` for RHEL).

---

## 📖 What is Helm?

Helm is a tool that helps you **define, install, and upgrade Kubernetes applications** using **Helm Charts**.

A **Helm Chart** is a pre-packaged collection of YAML files that describe Kubernetes resources.

- Instead of applying 10 different YAML files manually → you install one Helm chart.
- Instead of manually editing YAML for upgrades → you pass values and run `helm upgrade`.

---

## ⚡ Benefits of Using Helm

- **Simplifies Deployment**  
  No need to write huge YAML files – use charts with pre-defined templates.

- **Reusability**  
  Charts can be reused across environments (dev, test, prod) with different `values.yaml`.

- **Version Control & Rollback**  
  Track release history and rollback easily:
  ```bash
  helm rollback my-release 1
  ```

- **Customization with Values**  
  Easily override defaults using `--set`, `--set-string`, or `-f custom-values.yaml`.

- **Faster Team Collaboration**  
  Teams can share charts instead of raw YAMLs, ensuring consistent deployments.

- **Large Ecosystem**  
  Thousands of pre-built charts available (e.g., WordPress, MySQL, NGINX, Prometheus, Grafana).

- **CI/CD Friendly**  
  Integrates well with GitOps and automation pipelines (ArgoCD, Flux, Jenkins, GitHub Actions).

---

# 🚀 Helm Installation & Customization Guide

This guide provides a **practical step-by-step workflow** for using Helm with Kubernetes:

- Installing Helm
- Adding repositories
- Installing charts
- Passing custom values
- Upgrading & rolling back
- Uninstalling releases
- Creating your own Helm charts

---

## 📖 Prerequisites

Make sure you have:

- A Kubernetes cluster running (e.g., Minikube, AKS, EKS, GKE)
- `kubectl` installed and configured
- Internet access to fetch Helm charts

---

## ⚙️ Step 0: Install Minikube (Kubernetes Cluster Setup)

[Minikube](https://minikube.sigs.k8s.io/docs/start/) is the easiest way to create a local cluster for testing.

### 🔹 Install Minikube on Linux

1. **Download Minikube binary:**
   ```bash
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   ```
### 🔹 Install Minikube on Linux

1. **Download Minikube binary:**
   ```bash
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   ```

2. **Install to /usr/local/bin:**
   ```bash
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   ```

3. **Start a cluster:**
   ```bash
   minikube start --driver=docker
   ```

4. **Verify:**
   ```bash
   kubectl get nodes
   ```

> 📌 By default, Minikube uses Docker as the driver, but you can also use VirtualBox, KVM, or others.

---

### 🔹 Install Minikube on Windows

1. **Download the Minikube installer:**
   - Minikube Windows Installer (.exe)

2. **Run the installer**
   - It will add `minikube.exe` to your PATH.

3. **Start a cluster from PowerShell (Run as Administrator):**
   ```powershell
   minikube start --driver=docker
   ```

4. **Verify:**
   ```powershell
   kubectl get nodes
   ```

> 📌 Requirements:
> - Windows 10 or later  
> - Hyper-V or Docker Desktop installed (recommended driver is Docker)

---

### 🔹 Useful Minikube Commands

```bash
# Start cluster
minikube start

# Stop cluster
minikube stop

# Delete cluster
minikube delete

# Access dashboard
minikube dashboard

# Get cluster IP
minikube ip
```

✅ Now you have a local Kubernetes cluster running with Minikube.  
Next, proceed with Helm installation → **Step 1: Install Helm.**

---

## ⚙️ Step 1: Install Helm

**Download and install Helm 3:**

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

**Verify installation:**
```bash
helm version
```

---

## ⚙️ Step 2: Add a Helm Repository

Add the Bitnami repository (commonly used for production-ready charts):

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

Check available charts:
```bash
helm search repo bitnami
```

---

## ⚙️ Step 3: Install a Chart

Install WordPress with default values:
```bash
helm install wordpress-app bitnami/wordpress
```

Check installed releases:
```bash
helm list
```

---

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
```

---

### 🔹 Option B: Using a custom values.yaml

**Create `custom-values.yaml`:**
```yaml
wordpressUsername: admin
wordpressPassword: Secret123
mariadb:
  auth:
    rootPassword: Secret123
```

**Install with the file:**
```bash
helm install wordpress-app bitnami/wordpress -f custom-values.yaml
```

---

### 🔹 Option C: Using `--set-string`

Sometimes values can be misinterpreted (like numbers being treated as integers, or true/false as booleans).  
Use `--set-string` to force values to remain strings.

```bash
helm install wordpress-app bitnami/wordpress \
  --set-string service.type="LoadBalancer" \
  --set-string replicaCount="3" \
  --set-string wordpressPassword="1234567890"
```

This ensures `replicaCount` is treated as "3" (string), not 3 (integer).

---

### 🔹 Option D: Using `--set-file`

If you have large secrets, TLS certs, or config files, you can inject them directly.

This way your documentation now covers **all methods of passing values**:  
- `--set` (inline)  
- `-f values.yaml` (file-based)  
- `--set-string` (force string type)  
- `--set-file` (inject file content like secrets/certs)

**Example with `config.yaml`:**
```yaml
config:
  key1: value1
  key2: value2
```

**Command:**
```bash
helm install wordpress-app bitnami/wordpress \
  --set-file config=./config.yaml
```

**Example with a secret file (`db-password.txt`):**
```bash
helm install wordpress-app bitnami/wordpress \
  --set-file mariadb.auth.rootPassword=./db-password.txt
```

This securely passes the contents of `db-password.txt` as the value.

---

## ⚙️ Step 5: Upgrade a Release

Modify configuration or update chart version:

```bash
helm upgrade wordpress-app bitnami/wordpress -f custom-values.yaml
```

Check release history:

```bash
helm history wordpress-app
```

Rollback if needed:

```bash
helm rollback wordpress-app 1
```

---

## ⚙️ Step 6: Uninstall a Release

Remove WordPress from the cluster:

```bash
helm uninstall wordpress-app
```

---

## ⚙️ Step 7: Create Your Own Helm Chart

Generate a new chart:

```bash
helm create mychart
```

This creates a folder structure:

```
mychart/
  ├── charts/
  ├── templates/
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   └── _helpers.tpl
  └── values.yaml
```

Install your custom chart:

```bash
helm install myapp ./mychart
```

---

## 📂 Useful Helm Commands

```bash
# List all installed releases across namespaces
helm list -A

# Show default values of a chart
helm show values bitnami/wordpress

# Dry run to test install
helm install --dry-run --debug wordpress-app bitnami/wordpress

# Render templates without installing
helm template wordpress-app bitnami/wordpress
```

---

## ✅ Summary

In this guide, we learned how to:

✔️ Install Helm  
✔️ Add repositories  
✔️ Install and customize charts  
✔️ Upgrade, rollback, and uninstall releases  
✔️ Create custom Helm charts  

With Helm, managing Kubernetes applications becomes simpler, repeatable, and more reliable 🚀
