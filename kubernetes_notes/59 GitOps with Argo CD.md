# 🌈 GitOps with Argo CD 
### 🚀 What is GitOps?
**GitOps** is a modern DevOps approach where **Git is the single source of truth** for:
- 🧩 Infrastructure
- 🚀 Application deployments

👉 Everything is stored in Git  
👉 Changes are applied automatically 🚀  
- GitOps = Manage deployments using Git as the source of trut

## 🔄 Key Idea
- Push changes to Git 📂  
- Argo CD detects changes 🔍  
- Automatically applies to Kubernetes ⚙️  

❌ No manual `kubectl apply -f deployment.yaml`  
✅ Fully automated deployment  

- 👉 Argo CD automatically syncs Git → Kubernetes
---

# 🚀 What is Argo CD?
**Argo CD** is a **GitOps Continuous Delivery tool for Kubernetes**

👉 It pulls manifests from Git and deploys them into your cluster

## ✨ Features
- 📦 Pulls manifests from Git
- 🔄 Automatic Sync (no manual work) 🚀 Automatic deployments
- 📊 Web UI for visualization
- ⚙️ CLI & API support
- 📦 Supports Helm, Kustomize, Plain YAML
- ❤️ Health monitoring
- 🔙 Rollback to any Git commit

### ⚙️ How Argo CD Works
#### 1️⃣ Configuration
You define an Application in Argo CD:
- 📍 Git repo URL
- 📂 Path to manifests
- 🎯 Target cluster & namespace
- 🔄 Sync policy (auto/manual)

#### 2️⃣ Monitoring
Argo CD continuously watches Git 👀

#### 3️⃣ Comparison
Compares:
```
Desired State (Git) 📘 vs Live State (Cluster) 🚀
```
#### 4️⃣ Synchronization
If drift is detected:
- Auto sync OR manual sync 🔄

#### 5️⃣ Health Check
Checks app health:
- Running
- Available
- Failed

#### 6️⃣ Notifications
Alerts users about:
- Sync status
- Health status

---

### 📦 Argo CD Terminology

| Term | Meaning |
|------|--------|
| Application | What to deploy |
| Source | Git/Helm repo |
| Destination | Cluster + namespace |
| Sync | Apply Git → Cluster |
| Health Status | Resource condition |
| Sync Status | Git vs cluster match |

### 🔁 GitOps Flow
1️⃣ Developer pushes code to Git  
2️⃣ Argo CD detects change  
3️⃣ Automatically deploys to cluster 🚀  
4️⃣ Detects drift & fixes it  

---

## 🛠️ Installation Steps
#### 1️⃣ Create Namespace
```bash
kubectl create namespace argocd
```
#### 2️⃣ Install Argo CD
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
#### 3️⃣ Verify Pods
```bash
kubectl get pods -n argocd
```
✔ Ensure all pods are running

#### 4️⃣ Access UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
👉 Open: https://localhost:8080

#### 5️⃣ Get Admin Password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 🚀 Create First Application
#### 🌐 Via UI

1. Open Argo CD UI  
2. Click **New Application**  
3. 📋 Required Fields
- 📘 Git Repo URL
- 📂 Path (where YAML exists)
- 🎯 Cluster & Namespace
- 🔄 Sync Policy
  
---

### 🎯 Final Visualization
```
Developer 👨‍💻 → Git Push 📘
        ↓
Argo CD 🤖 detects change
        ↓
Applies to Kubernetes 🚀
        ↓
Cluster stays in sync ✅
```

# 📄 Argo CD Application YAML (Declarative GitOps)
```
apiVersion: argoproj.io/v1alpha1      # 📌 Argo CD API version
kind: Application                     # 🚀 Defines an Argo CD Application

metadata:
  name: myapp                         # 🏷 Application name
  namespace: argocd                   # 📍 Must be 'argocd' namespace

spec:
  project: default                    # 📂 Argo CD project (default is commonly used)

  source:
    repoURL: https://github.com/my-user/my-k8s-repo.git        # 📦 Git repo containing Kubernetes manifests
    targetRevision: main                   # 🔖 Branch / tag / commit (main branch here)
    path: k8s/myapp                   # 📁 Folder path inside repo

  destination:
    server: https://kubernetes.default.svc        # 🌐 In-cluster Kubernetes API server
    namespace: myapp-namespace                    # 📍 Target namespace for deployment

  syncPolicy:
    automated:                         # 🔄 Enable auto-sync (GitOps magic)
      prune: true                      # 🧹 Remove resources deleted from Git
      selfHeal: true                   # 🛠 Fix drift (manual changes auto-corrected)

    syncOptions:
      - CreateNamespace=true          # 📦 Auto-create namespace if not exists
```
#### ⚙️ Apply the Application
```
kubectl apply -f myapp-argo-application.yaml
```
### 🔁 What Happens After You Apply?

- Argo CD watches Git repo 👀  Tracks specific path (e.g., k8s/myapp/)
- Changes in repo → auto sync to cluster 🚀 Automatic Synchronization
  ```
    Git Change 📘 → Argo CD Detects 🔍 → Syncs to Cluster 🚀
  ```
- 👉 If someone manually changes resources: kubectl changes → reverted (self-heal) ♻️  
- 👉 If a resource is deleted from Git: ✔ Argo CD automatically Deletes it from cluster 🧹  

🎯 Argo CD ensures your cluster always matches Git — no matter what changes happen.

---

## 🔐 Security Best Practices
- 🔒 Limit Git access  (Use protected branches)
- 👥 Use RBAC for Argo CD  
- 🔑 Use:
  - SealedSecrets  
  - HashiCorp Vault  
  - External Secrets  

# 📊 Observability Integration
- 📈 Prometheus metrics support  Monitor: Sync status, Errors and Health
- 📊 Grafana dashboards ( Visualize: App health, Deployment trends )
- 📜 Audit logs (UI + CLI)  

👉 Fully traceable deployments (Audit-friendly)

---

# 📁 GitHub Repo Structure

```
my-k8s-repo:                         # 📦 Root GitOps repository
  README.md:                         # 📄 Documentation for the repo
---------------------------
  apps:                              # 🚀 Plain Kubernetes manifests
    myapp:
      deployment.yaml:               # 🧩 Deployment definition
      service.yaml:                  # 🌐 Service definition
      ingress.yaml:                  # 🌍 Ingress (optional)
---------------------------
  helm:                              # 📦 Helm charts (optional)
    myapp:
      Chart.yaml:                    # 📄 Chart metadata
      values.yaml:                   # ⚙ Default values
      templates:                     # 🧩 Kubernetes templates
        deployment.yaml:             # 🚀 Helm deployment template
        service.yaml:                # 🌐 Helm service template
        ingress.yaml:                # 🌍 Helm ingress template
-----------------------------
  overlays:                          # 🔄 Kustomize environments (optional)
    dev:
      kustomization.yaml:            # 🧪 Dev environment config
    staging:
      kustomization.yaml:            # 🟡 Staging config
    production:
      kustomization.yaml:            # 🔴 Production config
-------------------------
  argo-apps:                         # 🎯 Argo CD Application definitions
    myapp-app.yaml:                  # 🚀 Argo CD app YAML (GitOps entry point)
```

# 📂 Folder Explanation
### 🔄 How This Structure Works (Simple Flow)
- 📦 apps/ → Plain Kubernetes YAMLs
- 📦 helm/ → Helm charts
- 🔄 overlays/ → Environment-specific configs (Kustomize)
- 🎯 argo-apps/ → Argo CD app configs
- README.md → Documentation

---

# 🧱 GitOps Architecture with Argo CD
```
Git Repository 📘
   │
   │ (YAML / Helm / Kustomize)
   ↓
Argo CD Controller 🤖 (inside Kubernetes)
   │
   │ (Syncs desired state)
   ↓
Kubernetes Cluster 🚀
```
🎯 Argo CD can deploy different apps from different folders independently

### 💡 DevOps Insights (Very Important)

- 🔄 Auto Sync → No need for manual kubectl apply
- 🧹 Prune = Clean cluster (removes unused resources)
- 🛠 Self-heal = Drift correction (true GitOps power)
- 📦 Git = Single Source of Truth
- 🚀 Works perfectly with Helm / Kustomize / plain YAML

### What if Resource is Deleted❓
👉 Argo CD will:
- Detect drift 🔍  
- Recreate resource (selfHeal: true) ♻️  

---

# ⚔️ GitOps vs Traditional CI/CD
| Feature         | Traditional CI/CD    | GitOps                 |
| --------------- | -------------------- | ---------------------- |
| Model           | Push-based           | Pull-based             |
| Deployment      | CI pushes to cluster | Cluster pulls from Git |
| Complexity      | High                 | Simple                 |
| Source of Truth | CI pipeline          | Git                    |
| Tools           | Jenkins, scripts     | Argo CD                |


# 🎯 Tools Used
| Feature         | Traditional CI/CD    | GitOps                 |
| --------------- | -------------------- | ---------------------- |
| Model           | Push-based           | Pull-based             |
| Deployment      | CI pushes to cluster | Cluster pulls from Git |
| Complexity      | High                 | Simple                 |
| Source of Truth | CI pipeline          | Git                    |
| Tools           | Jenkins, scripts     | Argo CD                |


---

## 🔁 GitOps Flow Recap

1. Developer commits code 📘
   git commit -m "Update image"
2. Argo CD detects change 
3. Syncs cluster 🔄
4. App deployed/updated 🚀
5. Drift corrected 🔁  ( Auto-heal ♻️ )
6. Old resources removed 🧹

### 🎉 Final Visualization
```
Developer 👨‍💻 → Git Push 📘
        ↓
Argo CD 🤖 monitors repo
        ↓
Syncs changes 🔄
        ↓
Kubernetes updated 🚀
        ↓
Drift corrected + Cleanup 🔁🧹
        ↓
Cluster Matches Git ✅
```
---

### 💡 Real Benefits
- 🔄 Fully automated deployments  
- 🧾 Git as single source of truth  
- 🔐 Improved security & audit  
- 🚀 Faster delivery  

### 🔐 Best Practices
- ✅ Use Git as single source of truth
- ✅ Use separate repos for environments  
- ✅ Enable auto-sync for production
- ✅ Use RBAC for access control  
- ✅ Monitor application health  


### 🧠 Final Summary

✔ Git = Source of truth  
✔ Argo CD = Automation engine  
✔ Auto sync + self-heal  
✔ Clean, secure deployments  

---

🎉 Master GitOps with Argo CD!
