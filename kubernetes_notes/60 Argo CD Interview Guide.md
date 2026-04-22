# 🌈 Argo CD – Important Points & Interview Guide
## ✅ Argo CD Key Concepts

### 🔑 Git = Single Source of Truth
  - All changes happen via **Git commits**
  - ❌ No manual `kubectl apply`
  - ✅ Fully Git-driven deployments

### 🔹 1. GitOps Tool for Kubernetes
  - Argo CD follows **GitOps principles**
  - Git stores:
     - Deployments
     - Services
     - Configurations
  - 📘 Git stores desired state, 🤖 Argo CD enforces it, 🚀 Kubernetes runs it

### 🔹 2. Continuous Sync

  - Monitors Git repository 👀
  - Syncs changes automatically 🔄
  - Keeps cluster in **desired state**
  - 📊 Status
      - ✅ Synced → Everything matches            "Sync = Matching desired state (Git) with actual state (cluster)"
      - ❌ OutOfSync → Difference exists

### 🔹 3. Supported Tools
 - 📦 Helm
 - 🧩 Kustomize
 - 📄 Plain YAML

## 🔄 Sync & OutOfSync
### 🔄 Sync
   - Matches Git (`desired`) with Cluster (`actual`)

### ⚠️ OutOfSync
 * Occurs when:
   - 🔄 Git updated
   - 👨‍💻 Manual cluster changes

### 🧠 Self-Heal
```yaml
syncPolicy:
  automated:
    selfHeal: true
```
 * 👉 Fixes drift automatically ♻️ Restores cluster to Git state

### 🧹 Prune
👉 Removes unused resources :
```yaml
syncPolicy:
  automated:
    prune: true
```
 * 🎯 Example :
    - Resource deleted in Git ❌
    - Argo CD deletes it from cluster 🧹

### ⚙️ Sync Policy

| 🧩 **Type**           | 💡 **Behavior**            | 🧠 **How It Works**                                                                       | 🔒 **Control Level** | 💡 **Best Use Case**                       |
| --------------------- | -------------------------- | ----------------------------------------------------------------------------------------- | -------------------- | ------------------------------------------ |
| 🖱 **Manual Sync**    | 👤 User triggers sync      | 👉 Changes in Git are detected but **not applied automatically**<br>👉 User clicks “Sync” | High control         | Production environments (safe deployments) |
| 🔄 **Automated Sync** | 🤖 Auto-sync on Git change | 👉 Argo CD continuously watches Git and **applies changes automatically**                 | Less manual control  | Dev/test, CI/CD pipelines                  |

---

## 🧱 Argo CD Components
```yaml
Git Repo 📘 → Repo Server → Application Controller → Kubernetes 🚀
```

| 🧩 **Component**                     | 💡 **Role**           | 🧠 **How It Works**                                             | 🔍 **Why It Matters**                  |
| ------------------------------------ | --------------------- | --------------------------------------------------------------- | -------------------------------------- |
| 🌐 **argocd-server**                 | UI, CLI, API access   | 👉 Provides web UI, REST API, and CLI interface                 | Central entry point for users          |
| 📦 **argocd-repo-server**            | Git/Helm processing   | 👉 Clones Git repos and renders manifests (YAML/Helm/Kustomize) | Converts source → deployable manifests |
| 🧠 **argocd-application-controller** | Sync + compare engine | 👉 Continuously compares Git vs cluster and syncs state         | Core brain of GitOps                   |
| 🔐 **argocd-dex-server**             | Authentication        | 👉 Handles Single sign-on (SSO) via OIDC (Google, GitHub, etc.)  | Secure user access                     |
| ⚡ **argocd-redis**                  | Caching               | 👉 Stores app state/cache for performance                       | Faster operations & scalability        |


### 📦 What is an Argo CD Application?
#### An Argo CD Application defines what, where, and how to deploy from Git.
  * 👉 A **Custom Resource (CRD)** that defines:
      * 📘 Git repo
      * 📂 Path to manifests
      * 🎯 Cluster + namespace
      * 🔄 Sync policy

## 🔍 How Argo CD Detects Changes

  - Polls Git repo Periodically 🔄, 🔗 Can use webhooks for instant updates
  - Compares:
      - Git state
      - Cluster state
      - Syncs if differences found
  
#### 🔄 Flow
```yaml
Git Change 📘 → Detect 🔍 → Compare → Sync 🚀
```

### 🔄 Sync Options

| 🧩 **Option**   | 💡 **Description**                   | 🧠 **How It Works**                                                        | 🎯 **When to Use**             | ⚠️ **Note**                          |
| --------------- | ------------------------------------ | -------------------------------------------------------------------------- | ------------------------------ | ------------------------------------ |
| 🖱 **Manual**   | 👤 Trigger sync via UI / CLI         | 👉 Changes in Git are detected but applied only when user clicks “Sync”    | Production environments        | Gives full control                   |
| 🔄 **Auto**     | 🤖 Sync automatically on Git changes | 👉 Argo CD continuously watches repo and applies changes                   | Dev/Test, CI/CD pipelines      | Faster but less manual control       |
| 🧹 **prune**    | 🗑 Delete resources not in Git       | 👉 Removes resources that were deleted from Git but still exist in cluster | Keep cluster clean             | ⚠️ Can delete resources accidentally |
| 🔧 **selfHeal** | ♻️ Fix drift automatically           | 👉 If someone changes cluster manually → Argo CD re-syncs to Git state     | Enforce Git as source of truth | Prevents config drift                |


---

### 🚀 Deployment Strategies
  - Rolling Update (default in Kubernetes)
  - Canary / Blue-Green via 🔥 Advanced (with Argo Rollouts)

### 🔐 Secret Management
  - SealedSecrets (`Bitnami`)
  - External Secrets Operator
  - HashiCorp Vault
  - ⚠️ Best Practice : ❌ Never store secrets in plain Git

### 🔙 Rollback Strategy
 - ✅ 2 Methods :
    - UI → Select previous revision
    - Git → Revert commit
    - Rollback is done by reverting Git or selecting a previous revision in Argo CD
    - 👉 `Auto-sync` restores previous state

### 🔒 Production Security (EKS)
  - Use Ingress / Internal LB
  - Enable RBAC
  - Restrict Git access

---

# ❗ Troubleshooting Scenario

## ❗ Git updated but Argo CD didn't deploy
 
  * Possible Reasons:
    - ❌ Auto-sync disabled
    - 🔔 Webhook not configured
    - 📂 Wrong path/branch
    - 🔐 No Git repo access

# 🧠 Final Summary

| 🧩 Concept       | 💡 Meaning                                 |
| ---------------- | ------------------------------------------ |
| 📂 **Git**       | 📚 Source of truth (desired state)         |
| 🚀 **Argo CD**   | 🔄 Sync engine (applies Git → cluster)     |
| 🔁 **Auto-sync** | 🤖 Automatically deploys on Git changes    |
| 🔧 **Self-heal** | ♻️ Fixes drift if cluster changes manually |
| 🧩 **Supports**  | 📦 Helm + 🧱 Kustomize                     |

