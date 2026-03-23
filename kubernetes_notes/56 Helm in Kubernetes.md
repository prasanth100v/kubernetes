# 🌈 Helm in Kubernetes – Complete Guide
## 🔄 What is Helm?

**Helm** is a **package manager for Kubernetes**, similar to:
- 🐧 apt (Ubuntu)
- 🎯 yum (CentOS)

👉 It helps you **define, install, upgrade, and manage Kubernetes applications** using reusable packages called **Helm Charts**

🎯 Helm = Package manager that simplifies Kubernetes deployments

## 🚀 Why Use Helm?
### 1️⃣ Simplifies Deployments
⏱️ Deploy complex apps with a single command  
Example:
```bash
helm install my-app bitnami/nginx
```
#### Example apps:
- Prometheus 📊
- NGINX Ingress 🌐
- Grafana 📈

### 2️⃣ Version Control
📦 Manage application lifecycle Easily:
```
helm upgrade myapp chart-name
helm rollback myapp 1
helm uninstall myapp
```
- Upgrade
- Rollback
- Uninstall applications

---

### 3️⃣ Reusable & Shareable
📁 Write templates once → reuse everywhere in:
- Dev 🧪
- Staging 🚧
- Production 🚀

### 4️⃣ Customizable Config
 Override values at runtime:
```bash
helm install myapp ./chart -f custom-values.yaml
```
✔ Environment-specific configs

### 5️⃣ GitOps Friendly
🌀 Works with:
- ArgoCD
- Flux
- Enables automated CI/CD pipelines

---

## 📦 What is a Helm Chart?
A Helm Chart is a collection of YAML files that defines a Kubernetes application.

🎯 Helm Chart = Packaged Kubernetes manifests + templates

### Contains:
- Chart.yaml → Metadata
- values.yaml → Default configs
- templates/ → Kubernetes manifests

## 📂 Helm Chart Structure

```
mychart/
├── Chart.yaml        # 📄 Metadata (name, version)
├── values.yaml       # ⚙️ Default configuration
├── templates/        # 🧩 Kubernetes templates
│   ├── deployment.yaml
│   ├── service.yaml
├── charts/           # 📦 Subcharts (dependencies)
├── README.md         # 📘 Documentation (optional)
```

---

## 📌 Real-Time Use Case

### Without Helm 😓
You create:
- Deployment
- Service
- PVC
- ConfigMap

### With Helm 😎
```bash
helm install my-postgres bitnami/postgresql
```

👉 Helm handles everything automatically!

---

## 💼 Enterprise Usage

✅ Deploy:
- Prometheus
- Grafana
- Elasticsearch

✅ Used with:
- ArgoCD
- Flux

---

## 🛠 Creating Your Own Helm Chart
### ✅ Prerequisites

- Kubernetes cluster (Minikube / EKS / GKE)
- Helm installed (`helm version`)
- Basic Kubernetes knowledge

---

### 🧱 Step 1: Create Chart

```bash
helm create my-nodejs
cd my-nodejs
```

---

### 🧹 Step 2: Clean Up

```bash
rm -rf templates/* charts/ Chart.lock
```

## 📂 Generated Structure

```
my-Node.js/
├── Chart.yaml        # 📄 Chart metadata
├── values.yaml       # ⚙ Default config values
├── charts/           # 📦 Dependencies (sub-charts)
└── templates/        # 🧩 Kubernetes YAML templates
    ├── deployment.yaml   # 🚀 App deployment
    ├── service.yaml      # 🌐 Service exposure
    └── ingress.yaml      # 🌍 Optional ingress
```

## 📄 Chart.yaml Example
Describes chart metadata:
```yaml
apiVersion: v2                 # 📌 Helm chart API version (v2 for Helm 3)
name: myapp                   # 📦 Name of your Helm chart
description: A Helm chart for a Node.js microservice  # 📝 Short description

type: application             # 🚀 application = deployable app (not library)

version: 0.1.0                # 🔖 Chart version (Helm chart version)
appVersion: "1.0.0"           # 🎯 Actual app version (Docker image version)
```
✔ Chart identity
✔ Version tracking
---

## 📄 values.yaml Example
Default configuration values:
```yaml
replicaCount: 2                # 🔁 Number of application replicas (Pods)

image:                         # 🐳 Docker image configuration
  repository: myregistry/myapp   # 📦 Your Docker image (change this)
  tag: "1.0.0"                   # 🏷 Image version/tag
  pullPolicy: IfNotPresent       # ⬇ Pull only if not already present

service:                      # 🌐 Service configuration
  type: ClusterIP                # 🔗 Service type (ClusterIP / NodePort / LoadBalancer)
  port: 80                       # 🌍 Service exposed port
  containerPort: 3000            # 📡 App port inside container

resources:                    # ⚙ Resource requests & limits
  limits:
    cpu: 200m                    # 🚫 Max CPU usage
    memory: 256Mi                # 🚫 Max memory usage
  requests:
    cpu: 100m                    # ✅ Guaranteed CPU
    memory: 128Mi                # ✅ Guaranteed memory
```
✔ Central place to manage configs
✔ Can override during install
---
### What are Templates in Helm?
In Helm, the templates/ folder contains Kubernetes YAML files with dynamic values.

👉 These files use Go templating ({{ }}) to inject values from:
   - values.yaml ⚙️
   - Helm system variables 🔧
#### 🔄 How Templating Works
```
values.yaml ⚙️ → templates/ 📄 → Rendered YAML → Kubernetes 🚀
```
✔ Helm replaces placeholders ({{ }}) with actual values during deployment

### 📄 templates/deployment.yaml (Helm Templated Deployment)
```
apiVersion: apps/v1                 # 📌 API version for Deployment
kind: Deployment                   # 🚀 Kubernetes Deployment resource
metadata:
  name: {{ .Release.Name }}-deployment   # 🏷 Dynamic name (release-based)

spec:
  replicas: {{ .Values.replicaCount }}  # 🔁 Number of pod replicas

  selector:
    matchLabels:
      app: {{ .Release.Name }}          # 🎯 Select pods with this label

  template:
    metadata:
      labels:
        app: {{ .Release.Name }}        # 🏷 Pod label (must match selector)

    spec:
      containers:
        - name: {{ .Release.Name }}                                          # 📦 Container name
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"     # 🐳 Image + tag (from values.yaml)
          imagePullPolicy: {{ .Values.image.pullPolicy }}                     # ⬇ Image pull behavior
          ports:
            - containerPort: {{ .Values.containerPort }}                      # 📡 Port inside container

          resources:
            limits:
              cpu: {{ .Values.resources.limits.cpu }}                 # 🚫 Max CPU usage
              memory: {{ .Values.resources.limits.memory }}           # 🚫 Max memory usage

            requests:
              cpu: {{ .Values.resources.requests.cpu }}               # ✅ Guaranteed CPU
              memory: {{ .Values.resources.requests.memory }}         # ✅ Guaranteed memory
```
### 📄 templates/service.yaml (Helm Templated Service)
 📘 Purpose : Defines a Kubernetes Service to expose your application.
```
apiVersion: v1                     # 📌 API version for Service
kind: Service                     # 🌐 Kubernetes Service resource
metadata:
  name: {{ .Release.Name }}-service   # 🏷 Dynamic service name
spec:
  type: {{ .Values.service.type }}    # 🔗 Service type (ClusterIP / NodePort / LB)
  selector:
    app: {{ .Release.Name }}          # 🎯 Routes traffic to matching pods
  ports:
    - port: {{ .Values.service.port }}        # 🌍 External service port
      targetPort: {{ .Values.containerPort }} # 📡 Container port inside pod
```
### 🎯 Deploy the Helm Chart
Once your chart is ready, you can install and deploy it into Kubernetes using Helm.

📦 1️⃣ Install the Chart
```
helm install myapp ./myapp
```
✔ myapp → Release name
✔ ./myapp → Path to your Helm chart

### ⚙️2️⃣ Override Values at Install Time
You can customize your deployment without editing values.yaml:
```
helm install myapp ./myapp \
  --set image.repository=myrepo/myapp \
  --set image.tag=2.0.0 \
  --set replicaCount=3
```
#### 🧠 What This Does : ✔ Changes image repository, ✔ Updates image version and ✔ Scales app to 3 replicas

##🔄 Output example:
Creates:
- A deployment with 3 replicas of your Node.js app
- A ClusterIP service exposing port 80 → container port 3000

## 🔍 Verify Deployment
```
kubectl get pods
kubectl get svc
```

### 🔐 Best Practices
- ✅ Use .Values instead of hardcoding
- ✅ Keep templates clean and readable

### ⚠️ Common Mistakes
- ❌ Hardcoding image names
- ❌ Mismatched labels between Service & Deployment
- ❌ Missing resource limits
- ❌ Incorrect indentation (YAML sensitive!)

---

## 💡 Key DevOps Insights (Important)
- 🔗 Selector must match labels → otherwise Service won’t work
- 🔁 replicas controlled via values.yaml → easy scaling
- 🐳 Image fully dynamic → change version without editing template
- ⚙ Resources prevent overuse → production best practice

## 💡 Pro Tips

- Use **values.yaml** for environment configs
- Keep charts **modular and reusable**
- Use **helm lint** to validate charts
- Use **helm upgrade --install** for CI/CD

---
## 🔄 What is "Helm Render"?
🎯 Helm Render = Converting templates into final Kubernetes YAML files
| Concept       | Meaning                              |
| ------------- | ------------------------------------ |
| Helm Chart 📦 | YAML files with variables            |
| Render 🔄     | Replace variables with actual values |
| Output 📄     | Plain Kubernetes YAML                |

### 🔧 Example
```
replicas: {{ .Values.replicaCount }}       #🔹 Template (Before Rendering)
name: {{ .Release.Name }}

replicas: 3                            #🔹 After Rendering
name: myapp
```
### 🔁 Full Helm Workflow
```
Helm Chart 📦
   ↓
values.yaml ⚙️
   ↓
helm template 🔄 (Render)
   ↓
Final YAML 📄
   ↓
helm install 🚀
   ↓
Kubernetes Resources ✅
```

## 🧠 Final Summary

✔ Helm = Kubernetes Package Manager  
✔ Charts = Reusable templates  
✔ Simplifies deployments  
✔ Enables GitOps workflows  

---

🎉 Happy Learning Helm!
