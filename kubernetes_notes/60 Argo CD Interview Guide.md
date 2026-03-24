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
    - ✅ Synced → Everything matches        "Sync = Matching desired state (Git) with actual state (cluster)"
    - ❌ OutOfSync → Difference exists

### 🔹 3. Supported Tools
- 📦 Helm
- 🧩 Kustomize
- 📄 Plain YAML

## 🔄 Sync & OutOfSync
### 🔄 Sync
- Matches Git (desired) with Cluster (actual)

### ⚠️ OutOfSync
Occurs when:
- 🔄 Git updated
- 👨‍💻 Manual cluster changes

### 🧠 Self-Heal
```yaml
syncPolicy:
  automated:
    selfHeal: true
```
👉 Fixes drift automatically ♻️ Restores cluster to Git state

### 🧹 Prune
👉 Removes unused resources :
```yaml
syncPolicy:
  automated:
    prune: true
```
 🎯 Example :
  - Resource deleted in Git ❌
  - Argo CD deletes it from cluster 🧹

### ⚙️ Sync Policy

| Type | Behavior |
|------|--------|
| Manual | User triggers sync |
| Automated | Auto sync on Git change |

---

## 🧱 Argo CD Components
```
Git Repo 📘 → Repo Server → Application Controller → Kubernetes 🚀
```
| Component                     | Role                          |
| ----------------------------- | ----------------------------- |
| argocd-server                 | 🌐 UI, CLI, API access        |
| argocd-repo-server            | 📦 Clones repo & renders YAML |
| argocd-application-controller | 🧠 Sync & compare engine (Brain of Argo CD)  |
| argocd-dex-server             | 🔐 Authentication (SSO)       |
| argocd-redis                  | ⚡ Caching for performance     |

### 📦 What is an Argo CD Application?
#### An Argo CD Application defines what, where, and how to deploy from Git.
👉 A **Custom Resource (CRD)** that defines:

- 📘 Git repo
- 📂 Path to manifests
- 🎯 Cluster + namespace
- 🔄 Sync policy

## 🔍 How Argo CD Detects Changes

- Polls Git repo Periodically 🔄, 🔗 Can use webhooks for instant updates
- Compares:
  - Git state
  - Cluster state
- Syncs if differences found
#### 🔄 Flow
```
Git Change 📘 → Detect 🔍 → Compare → Sync 🚀
```

### 🔄 Sync Options
| Option   | Description             |
| -------- | ----------------------- |
| Manual   | Trigger via UI/CLI      |
| Auto     | Sync automatically      |
| prune    | Delete unused resources |
| selfHeal | Fix drift               |

---

### 🚀 Deployment Strategies
- Rolling Update (default in Kubernetes)
- Canary / Blue-Green via 🔥 Advanced (with Argo Rollouts)

### 🔐 Secret Management
- SealedSecrets (Bitnami)
- External Secrets Operator
- HashiCorp Vault
- ⚠️ Best Practice : ❌ Never store secrets in plain Git

### 🔙 Rollback Strategy
- ✅ 2 Methods
  - UI → Select previous revision
  - Git → Revert commit
  - Rollback is done by reverting Git or selecting a previous revision in Argo CD

👉 Auto-sync restores previous state

### 🔒 Production Security (EKS)
- Use Ingress / Internal LB
- Enable RBAC
- Restrict Git access

---

# ❗ Troubleshooting Scenario

## Problem:
Git updated but Argo CD didn't deploy

## Possible Reasons:
- ❌ Auto-sync disabled
- 🔔 Webhook not configured
- 📂 Wrong path/branch
- 🔐 No Git repo access

# 🧠 Final Summary

✔ Git = Source of truth  
✔ Argo CD = Sync engine  
✔ Auto-sync + self-heal  
✔ Supports Helm, Kustomize  

