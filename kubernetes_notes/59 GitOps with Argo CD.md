# 🌈 GitOps with Argo CD 
### 🚀 What is GitOps?
 * **GitOps** is a modern DevOps approach where **Git is the single source of truth** for:
   - 🧩 Infrastructure
   - 🚀 Application deployments
 * 👉 Everything is stored in Git
 * 👉 Changes are applied automatically 🚀
 * GitOps = Manage deployments using `Git as the source of truth`

## 🔄 Key Idea
  - Push changes to Git 📂  
  - Argo CD detects changes 🔍  
  - Automatically applies to Kubernetes ⚙️  

 * ❌ No manual `kubectl apply -f deployment.yaml`
 * ✅ Fully automated deployment
 * 👉 Argo CD automatically syncs Git → Kubernetes

---

# 🚀 What is Argo CD?
 * **Argo CD** is a **GitOps Continuous Delivery tool for Kubernetes**
 * 👉 It pulls manifests from Git and deploys them into your cluster

## ✨ Features
  - 📦 Pulls manifests from Git
  - 🔄 Automatic Sync (no manual work) 🚀 Automatic deployments
  - 📊 Web UI for visualization
  - ⚙️ CLI & API support
  - 📦 Supports `Helm`, `Kustomize`, `Plain YAML`
  - ❤️ Health monitoring
  - 🔙 Rollback to any Git commit

### ⚙️ How Argo CD Works
#### 1️⃣ Configuration
 * You define an Application in Argo CD:
  - 📍 Git repo URL
  - 📂 Path to manifests
  - 🎯 Target cluster & namespace
  - 🔄 Sync policy (`auto/manual`)

#### 2️⃣ Monitoring
  * Argo CD continuously watches Git 👀

#### 3️⃣ Comparison
Compares:
```yaml
Desired State (Git) 📘 vs Live State (Cluster) 🚀
```
#### 4️⃣ Synchronization
 * If drift is detected:
    - Auto sync OR manual sync 🔄

#### 5️⃣ Health Check
 * Checks app health:
   - Running
   - Available
   - Failed

#### 6️⃣ Notifications
  * Alerts users about:
     - Sync status
     - Health status

---

### 📦 Argo CD Terminology
| 🧩 Term               | 📖 Meaning            | 🧠 How It Works                                    | 💡 Real-World Insight    |
| --------------------- | ---------------------- | -------------------------------------------------- | -------------------------- |
| 📦 **Application**   | 🎯 What to deploy      | 👉 Defines Kubernetes resources managed by Argo CD | Deploy app using Argo CD  |
| 📂 **Source**        | 🔗 Git / Helm repo     | 👉 Location of manifests or charts                 | GitHub repo or Helm chart |
| 🎯 **Destination**   | ☁️ Cluster + namespace | 👉 Where the app is deployed                       | EKS cluster, `prod` namespace |
| 🔄 **Sync**          | ⚙️ Apply Git → Cluster | 👉 ArgoCD applies desired state to cluster         | Auto-sync or manual sync  |
| 💚 **Health Status** | 🩺 Resource condition  | 👉 Shows if app is healthy (Running, Degraded)     | Pod crash → unhealthy    |
| 🔍 **Sync Status**   | 📊 Git vs cluster      | 👉 Indicates if cluster matches Git  (Synced / OutOfSync) | Config drift alerts   |

### 🔁 GitOps Flow
 * 1️⃣ Developer pushes code to Git
 * 2️⃣ Argo CD detects change
 * 3️⃣ Automatically deploys to cluster 🚀
 * 4️⃣ Detects drift & fixes it  

---

## 🛠️ Installation Steps
#### 1️⃣ Create Namespace
```yaml
kubectl create namespace argocd
```
#### 2️⃣ Install Argo CD
```yaml
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
#### 3️⃣ Verify Pods
```yaml
kubectl get pods -n argocd
kubectl get svc -n argocd
```
 * ✔ Ensure all pods are running

#### 4️⃣ Access UI (Expose Argo CD)
 * (Recommended for EKS): LoadBalancer
 * Change the service type:
```hcl
kubectl patch svc argocd-server \
-n argocd \
-p '{"spec": {"type": "LoadBalancer"}}'
```
 * 👉 Open: https://af192b1d1d9424b71b0d148226c73e09-399883942.ap-south-1.elb.amazonaws.com

#### 5️⃣ Get Admin Password
```yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 🚀 Create First Application
#### 🌐 Option: 1 Via UI
 1. Open Argo CD UI  
 2. Click **New Application**  
 3. 📋 Required Fields
     - 📘 Git Repo URL
     - 📂 Path (where YAML exists)
     - 🎯 Cluster & Namespace
     - 🔄 Sync Policy

### 🎯 Final Visualization
```hcl
Developer 👨‍💻 → Git Push 📘
        ↓
Argo CD 🤖 detects change
        ↓
Applies to Kubernetes 🚀
        ↓
Cluster stays in sync ✅
```

## 📄Option: 2 Argo CD Application YAML (Declarative GitOps)
```yaml
apiVersion: argoproj.io/v1alpha1              # 📌 Argo CD API version
kind: Application                            # 📦 Creates an Argo CD Application resource
metadata:
  name: nginx-app                            # 🏷️ Name of the Argo CD Application
  namespace: argocd                          # 📂 Namespace where Argo CD is installed

spec:
  project: default                             # 📂 Argo CD project (default is commonly used)

  source:                                    # 📚 Source of application manifests
    repoURL: https://github.com/<username>/my-argocd-demo.git     # 🔗 Git repository containing Kubernetes YAMLs                                        

    targetRevision: main                     # 🌿 Git branch, tag, or commit to deploy  (main branch here)

    path: .                                  # 📂 Folder containing manifests, "." means repository root directory                                          

  destination:                               # 🎯 Deployment target
    server: https://kubernetes.default.svc   # ☸️ Kubernetes API server (Deploy to the same cluster where Argo CD runs)                                          

    namespace: demo                          # 📂 Namespace where the application will be deployed
  syncPolicy:                                 # 🔄 Defines how Argo CD syncs Git with the cluster
    automated:                                # 🤖 Enables Automatic Synchronization
      prune: true                          # 🗑️ Delete resources removed from Git (Keeps the cluster exactly the same as Git)                                       
      selfHeal: true                            # ❤️ Automatically fixes manual changes
                                                # If someone edits the cluster manually,  Argo CD restores it back to the Git state
    syncOptions:                           # ⚙️ Additional synchronization options
    - CreateNamespace=true                 # 📂 Automatically create the 'demo' namespace if not exists                    
```

#### ⚙️ Apply the Application
```yaml
kubectl apply -f myapp-argo-application.yaml
```
### 🔁 What Happens After You Apply?
  - Argo CD watches Git repo 👀  Tracks specific path (e.g., k8s/myapp/)
  - Changes in repo → auto sync to cluster 🚀 Automatic Synchronization
  ```yaml
    Git Change 📘 → Argo CD Detects 🔍 → Syncs to Cluster 🚀
  ```
  - 👉 If someone manually changes resources: kubectl changes → reverted (`self-heal`) ♻️  
  - 👉 If a resource is deleted from Git: `Argo CD automatically Deletes it from cluster` 🧹
  - 🎯 Argo CD ensures your cluster always matches Git — no matter what changes happen.

### Verify Application:
```hcl
kubectl get applications -n argocd
kubectl get pods -n demo
kubectl get svc -n demo
```
### Verify Self-Heal
Delete one Pod manually:
```
kubectl delete pod -n demo --all
```
 * Argo CD and the Kubernetes Deployment controller ensure the `desired state is restored`, so `new Pods are created automatically`.

---

## 🔐 Security Best Practices
  - 🔒 Limit Git access  (Use protected branches)
  - 👥 Use RBAC for Argo CD  
  - 🔑 Use:
      - SealedSecrets  
      - HashiCorp Vault  
      - External Secrets  

## 📊 Observability Integration
   - 📈 Prometheus metrics support `Monitor: Sync status`, `Errors and Health`
   - 📊 Grafana dashboards ( Visualize: `App health`, `Deployment trends` )
   - 📜 Audit logs (`UI + CLI`)
   - 👉 Fully traceable deployments (Audit-friendly)

## 📁 GitHub Repo Structure
```yaml
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

## 📂 Folder Explanation
### 🔄 How This Structure Works (Simple Flow)
  - 📦 `apps/` → Plain Kubernetes YAMLs
  - 📦 `helm/` → Helm charts
  - 🔄 `overlays/` → Environment-specific configs (Kustomize)
  - 🎯 `argo-apps/` → Argo CD app configs
  - `README.md` → Documentation

## 🧱 GitOps Architecture with Argo CD
```yaml
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
 * 🎯 Argo CD can deploy different apps from different folders independently

### 💡 DevOps Insights (Very Important)
  - 🔄 Auto Sync → No need for manual kubectl apply
  - 🧹 Prune = Clean cluster (removes unused resources)
  - 🛠 Self-heal = Drift correction (true GitOps power)
  - 📦 Git = Single Source of Truth
  - 🚀 Works perfectly with Helm / Kustomize / plain YAML

### What if Resource is Deleted❓
 * 👉 Argo CD will:
   - Detect drift 🔍  
   - Recreate resource (`selfHeal: true`) ♻️

---

# ⚔️ GitOps vs Traditional CI/CD
| 🧩 Feature             | 🔧 Traditional CI/CD | 🔄 GitOps               | 🧠 Explanation                                                    | 💡 Real-World Insight            |
| ---------------------- | -------------------- | ------------------------ | ----------------------------------------------------------------- | -------------------------------- |
| 🔁 **Model**           | 📤 Push-based        | 📥 Pull-based          | CI pipeline pushes changes to cluster; GitOps tools pull from Git  | 🔐 Reduces direct cluster access |
| 🚀 **Deployment**      | CI pushes to cluster | Cluster pulls from Git  | Jenkins/scripts deploy; GitOps agents sync automatically            | ⚙️ More secure & controlled      |
| 🎯 **Complexity**      | ⚠️ High              | 😊 Simple              | Many scripts vs declarative Git-driven model                       | 🛠 Easier maintenance in GitOps   |
| 📂 **Source of Truth** | 🛠 CI pipeline       | 📚 Git repository       | Pipeline defines state vs Git defines desired state                | 🔍 GitOps ensures consistency     |
| 🛠 **Tools**           | Jenkins, scripts     | Argo CD, Flux            | Different ecosystem tools                                          | 🚀 GitOps is Kubernetes-native    |

---

## 🔁 GitOps Flow Recap

1. Developer commits code 📘
    `git commit -m "Update image"`
2. Argo CD detects change 
3. Syncs cluster 🔄
4. App deployed/updated 🚀
5. Drift corrected 🔁  ( `Auto-heal` ♻️ )
6. Old resources removed 🧹

### 🎉 Final Visualization
```yaml
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

### 💡 Real Benefits
 - 🔄 Fully automated deployments  
 - 🧾 `Git` as single source of truth  
 - 🔐 Improved `security & audit`  
 - 🚀 Faster delivery  

### 🔐 Best Practices
 - ✅ Use Git as single source of truth
 - ✅ Use separate repos for environments  
 - ✅ Enable `auto-sync` for production
 - ✅ Use RBAC for access control  
 - ✅ Monitor application health  

### 🧠 Final Summary
  * Git = Source of truth
  * Argo CD = Automation engine
  * Auto sync + self-heal
  * Clean, secure deployments  

---

🎉 Master GitOps with Argo CD!
