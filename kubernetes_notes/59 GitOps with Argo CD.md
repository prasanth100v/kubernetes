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

### 2️⃣ Monitoring
Argo CD continuously watches Git 👀

---

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

## 5️⃣ Get Admin Password
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

### 💡 Real Benefits
- 🔄 Fully automated deployments  
- 🧾 Git as single source of truth  
- 🔐 Improved security & audit  
- 🚀 Faster delivery  

# 🔐 Best Practices
- ✅ Use Git as single source of truth
- ✅ Use separate repos for environments  
- ✅ Enable auto-sync for production
- ✅ Use RBAC for access control  
- ✅ Monitor application health  

---

🎯 Final Visualization
```
Developer 👨‍💻 → Git Push 📘
        ↓
Argo CD 🤖 detects change
        ↓
Applies to Kubernetes 🚀
        ↓
Cluster stays in sync ✅
```

🎉 Happy Learning GitOps with Argo CD!




