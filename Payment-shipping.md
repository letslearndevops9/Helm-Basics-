# Creating Helm Repositories for Payments and Shipping Services

## Scenario
As a DevOps engineer at an e-commerce company, you manage two services: Payments and Shipping. To streamline deployment and management, we will create a Helm repository containing Helm charts for both services.

---

## Prerequisites
- Helm installed on your system.
- A running Kubernetes cluster.
- GitHub account for hosting the Helm repo.
- `kubectl` configured to interact with the cluster.

---

## Step 1: Create the Helm Charts

**Create the directory structure:**
```bash
mkdir -p helm-repo/{payments,shipping}
cd helm-repo
```

**Generate Helm charts for both services:**
```bash
helm create payments
helm create shipping
```

---

## Step 2: Customize the Helm Charts

### BusyBox Deployment Template

Below is the complete deployment template file for the BusyBox service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: {{ .Chart.Name }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
          command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
          imagePullPolicy: {{ .Values.image.pullPolicy }}
```

---

### Payments Chart

**Update `values.yaml` in the payments chart:**
```yaml
image:
  repository: busybox
  tag: latest
  pullPolicy: IfNotPresent
appMessage: "Payments Service"
```

**Update the deployment template (`templates/deployment.yaml`):**
```yaml
spec:
  containers:
    - name: payments
      image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
      command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
```

---

### Shipping Chart

**Update `values.yaml` in the shipping chart:**
```yaml
image:
  repository: busybox
  tag: latest
  pullPolicy: IfNotPresent
appMessage: "Shipping Service"
```

**Update the deployment template (`templates/deployment.yaml`):**
```yaml
spec:
  containers:
    - name: shipping
      image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
      command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
```

---

## Step 3: Package the Charts

**Package the payments chart:**
```bash
helm package payments
```
**Package the shipping chart:**
```bash
helm package shipping
```
**Create an index file:**
```bash
helm repo index .
```

---

## Step 4: Host the Helm Repo on GitHub

**Create a new GitHub repository:**
- Name: `helm-repo`

**Initialize a Git repo:**
```bash
git init
git remote add origin https://github.com/username/helm-repo.git
```

**Push the Helm charts to GitHub:**
```bash
git add .
git commit -m "Add payments and shipping charts"
git push -u origin main
```

**Configure GitHub Pages:**
- Go to the repo settings.
- Enable GitHub Pages from the main branch.

---

## Step 5: Using the Helm Repo

**Add the Helm repo:**
```bash
helm repo add myrepo https://username.github.io/helm-repo
helm repo update
```

**Search for charts:**
```bash
helm search repo myrepo
```

**Install a service (e.g., payments):**
```bash
helm install payments-service myrepo/payments
```
