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

---

# 🚀 GitOps & Argo CD – Rapid Fire Interview Questions & Answers
| 🔢  | ❓ Question                                    | ✅ Answer                                                                                                                   |
| --- | --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| 1️⃣ | What is GitOps?                               | 🚀 A deployment methodology where **Git is the single source of truth** for `infrastructure` and `application configurations`. |
| 2️⃣ | Why use GitOps?                               | 🔄 Automates deployments, improves consistency, and enables easy rollback.                                                 |
| 3️⃣ | What is the Single Source of Truth in GitOps? | 📂 Git Repository                                                                                                          |
| 4️⃣ | What are the main GitOps tools?               | 🚀 Argo CD, Flux                                                                                                           |
| 5️⃣ | What does GitOps manage?                      | 📦 Kubernetes manifests, `Helm Charts`, Kustomize configurations, and `infrastructure code`.                                   |
| 6️⃣ | What triggers deployment in GitOps?           | 📝 Changes committed and pushed to Git.                                                                                    |
| 7️⃣ | Can GitOps detect configuration drift?        | ✅ Yes                                                                                                                      |
| 8️⃣ | What is Configuration Drift?                  | ⚠️ Difference between `Git` and the `actual cluster state`.                                                                    |
| 9️⃣ | Can GitOps automatically fix drift?           | ✅ Yes (if `auto-sync/self-heal` is enabled).                                                                                 |
| 🔟 | Main benefit of GitOps?                       | 🎯 Declarative, automated, and auditable deployments.                                                                      |
| 1️⃣1️⃣ | What is Argo CD?                               | 🚀 A GitOps `continuous delivery tool` for Kubernetes.       |
| 1️⃣2️⃣ | What does Argo CD do?                          | 🔄 Synchronizes Kubernetes clusters with Git repositories. |
| 1️⃣3️⃣ | Is Argo CD only for Kubernetes?                | ✅ Yes                                                      |
| 1️⃣4️⃣ | Where is Argo CD installed?                    | ☸️ Inside the Kubernetes cluster.                          |
| 1️⃣5️⃣ | Default namespace for Argo CD?                 | 📂 `argocd`                                                |
| 1️⃣6️⃣ | Does Argo CD require Git?                      | ✅ Yes                                                      |
| 1️⃣7️⃣ | Which protocol does Argo CD use to access Git? | 🌐 HTTPS or SSH                                            |
| 1️⃣8️⃣ | Can Argo CD manage multiple clusters?          | ✅ Yes                                                      |
| 1️⃣9️⃣ | Can Argo CD manage multiple applications?      | ✅ Yes                                                      |
| 2️⃣0️⃣ | Is Argo CD agent-based?                        | ❌ No, it runs as controllers inside Kubernetes.            |
| 2️⃣1️⃣ | What is an Argo CD Application?                 | 📦 A `Custom Resource` (CR) representing an application deployment.   |
| 2️⃣2️⃣ | What does an Application define?                | 📂 `Git repo`, `path`,` destination cluster`, `namespace`, and `sync policy`. |
| 2️⃣3️⃣ | Can one Git repo contain multiple applications? | ✅ Yes                                                               |
| 2️⃣4️⃣ | Can one Argo CD instance manage multiple repos? | ✅ Yes                                                               |
| 2️⃣5️⃣ | What is the destination in an Application?      | 🎯 `Target cluster` and `namespace`.                                    |
| 2️⃣6️⃣ | What is the source in an Application?           | 📂 Git repository path `containing manifests` or `charts`.              |
| 2️⃣7️⃣ | Can Argo CD deploy Helm Charts?                 | ✅ Yes                                                               |
| 2️⃣8️⃣ | Can Argo CD deploy Kustomize?                   | ✅ Yes                                                               |
| 2️⃣9️⃣ | Can Argo CD deploy plain YAML files?            | ✅ Yes                                                               |
| 3️⃣1️⃣ | What is Sync?                       | 🔄 Applying `Git changes to the Kubernetes cluster`.                                |
| 3️⃣2️⃣ | Manual Sync?                        | 👨‍💻 User triggers deployment `manually`.                                          |
| 3️⃣3️⃣ | Auto Sync?                          | 🤖 Argo CD automatically applies Git changes.                                     |
| 3️⃣4️⃣ | What is Self-Heal?                  | 🛡️ Automatically restores the cluster to match Git if changes are made manually. |
| 3️⃣5️⃣ | What is Prune?                      | 🗑️ Removes Kubernetes resources that were deleted from Git.                      |
| 3️⃣6️⃣ | What is Sync Status?                | 📊 Indicates whether `Git` and the `cluster` match.                                   |
| 3️⃣7️⃣ | What does **Synced** mean?          | ✅ Cluster matches Git.                                                            |
| 3️⃣8️⃣ | What does **OutOfSync** mean?       | ⚠️ Cluster differs from Git.                                                      |
| 3️⃣9️⃣ | What does **Healthy** mean?         | 💚 Application is running correctly.                                              |
| 4️⃣0️⃣ | Can an app be Synced but Unhealthy? | ✅ Yes                                                                             |
| 4️⃣1️⃣ | Which Git providers are supported?         | 🐙 GitHub, 🦊 GitLab, ☁️ Bitbucket, Azure Repos, and others. |
| 4️⃣2️⃣ | How can Git repositories be authenticated? | 🔑 SSH keys, HTTPS credentials, or access tokens.             |
| 4️⃣3️⃣ | Does Argo CD store manifests?              | ❌ No, Git is the source of truth.                            |
| 4️⃣4️⃣ | Can private repositories be used?          | ✅ Yes                                                        |
| 4️⃣5️⃣ | Can Argo CD watch repository changes?      | ✅ Yes                                                        |
| 4️⃣6️⃣ | Get Argo CD Pods?          | `kubectl get pods -n argocd`         |
| 4️⃣7️⃣ | List Argo CD Applications? | `kubectl get applications -n argocd` |
| 4️⃣8️⃣ | Sync using CLI?            | `argocd app sync <app-name>`         |
| 4️⃣9️⃣ | Get application details?   | `argocd app get <app-name>`          |
| 5️⃣0️⃣ | View application history?  | `argocd app history <app-name>`      |
| 5️⃣1️⃣ | Where should Kubernetes manifests be stored?   | 📂 Git Repository                                                                 |
| 5️⃣2️⃣ | Should production changes be made manually?    | ❌ No, c`ommit changes to Git`.                                                      |
| 5️⃣3️⃣ | Can Argo CD replace CI tools like Jenkins?     | ❌ No, Argo CD is primarily a CD (`deployment`) tool.                                |
| 5️⃣4️⃣ | Can Argo CD integrate with CI pipelines?       | ✅ Yes                                                                             |
| 5️⃣5️⃣ | Should Secrets be stored in Git as plain text? | ❌ No                                                                              |
| 5️⃣6️⃣ | How are Secrets commonly managed with Argo CD? | 🔐 Kubernetes Secrets, Sealed Secrets, or External Secrets.                       |
| 5️⃣7️⃣ | Can Argo CD roll back applications?            | ✅ Yes                                                                             |
| 5️⃣8️⃣ | Can Argo CD deploy to multiple environments?   | ✅ Yes (Dev, QA, UAT (User Acceptance Testing), Prod).                             |
| 5️⃣9️⃣ | Why is Git history valuable in GitOps?         | 📝 Provides versioning, audit trail, and rollback capability.                     |
| 6️⃣0️⃣ | Biggest advantage of Argo CD?                  | 🚀 Automated, declarative Kubernetes deployments with Git as the source of truth. |


## 🎤 Most Asked Interview Questions
| ❓ Question                            | 🎯 Short Answer                                            |
| ------------------------------------- | ---------------------------------------------------------- |
| What is GitOps?                       | 🚀 Managing deployments using Git as the source of truth.  |
| What is Argo CD?                      | 📦 GitOps continuous delivery tool for Kubernetes.         |
| What is the source of truth?          | 📂 Git Repository                                          |
| What is Sync?                         | 🔄 Apply Git state to the cluster.                         |
| What is Auto Sync?                    | 🤖 Automatically deploy Git changes.                       |
| What is Self-Heal?                    | 🛡️ Restore the cluster to match Git after manual changes. |
| What is Prune?                        | 🗑️ Delete resources removed from Git.                     |
| Can Argo CD deploy Helm Charts?       | ✅ Yes                                                      |
| Can Argo CD deploy Kustomize?         | ✅ Yes                                                      |
| Can Argo CD manage multiple clusters? | ✅ Yes                                                      |


## 🎯 One-Line Interview Answer
 * 🚀 GitOps is a deployment methodology where Git is the `single source of truth` for Kubernetes configurations.
 * Argo CD continuously compares the Git repository with the cluster state, `synchronizes changes`, detects configuration drift, and can automatically `self-heal` and `prune resources` to keep the cluster aligned with the desired state.


## 🗑️ What is Prune? 
 * ✅ Prune is an Argo CD feature that `automatically deletes Kubernetes resources` that exist in the cluster but have been `removed from the Git repository`.
 * It helps keep your cluster `100% synchronized with Git` (GitOps).
 * Suppose your application initially has:
     * 📌 Deployment
     * 📌 Service
     * 📌 Ingress
 * Later you `remove the Ingress YAML from Git`.
 * ❌ Without Prune : Ingress` remains in Kubernetes`.
 * ✅ With Prune : Argo CD `deletes the Ingress` automatically.

---

# 🚀 Argo CD Scenario-Based Interview Questions & Answers
| 🎯 Scenario                                                                                   | ✅ Answer                                                                                                                                                                            |
| ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. A developer pushed new code to Git, but Argo CD didn't deploy it. What would you check?** | Check Application status (`kubectl get applications -n argocd`), repository connectivity, sync status, Git branch (`targetRevision`), repository path, and Argo CD controller logs. |
| **2. Application shows `OutOfSync` but Pods are running correctly. Why?**                      | Someone may have manually modified the cluster, or Git differs from the live state. Argo CD detects configuration drift.                                                            |
| **3. How do you automatically fix manual changes in the cluster?**                             | Enable `selfHeal: true` in the application's `syncPolicy`. Argo CD restores the desired state from Git.                                                                             |
| **4. A resource was deleted from Git but still exists in the cluster. Why?**                   | `prune: true` is not enabled, or the application has not synced after the Git change.                                                                                               |
| **5. What does `prune: true` do?**                                                             | It removes Kubernetes resources from the cluster when they are deleted from the Git repository.                                                                                     |
| **6. An application remains in `Progressing`. What could be the reason?**                      | Pods are not Ready, image pull failures, failed probes, insufficient resources, or failed Jobs preventing the application from becoming Healthy.                                    |
| **7. Argo CD cannot access a private GitHub repository. How do you fix it?**                   | Add repository credentials using SSH keys or a GitHub Personal Access Token (PAT), then verify with `argocd repo list`.                                                             |
| **8. Your application status is `Missing`. What does it mean?**                                | The resource defined in Git does not exist in the cluster. Argo CD will create it during synchronization.                                                                           |
| **9. Argo CD UI is inaccessible. How do you troubleshoot?**                                    | Check `argocd-server` Pod, Service type, LoadBalancer/Ingress status, port-forwarding, and browser/network connectivity.                                                            |
| **10. A deployment is Healthy but `OutOfSync`. Why?**                                          | The application is running successfully, but the live configuration differs from what is stored in Git.                                                                             |
| **11. How do you manually synchronize an application?**                                        | Use the Argo CD UI and click **Sync**, or run `argocd app sync <app-name>`.                                                                                                         |
| **12. How do you rollback to a previous version?**                                             | Revert the Git commit or use the Argo CD UI to sync to a previous Git revision if available. Git remains the source of truth.                                                       |
| **13. A namespace doesn't exist when deploying. How do you avoid deployment failure?**         | Enable `CreateNamespace=true` in `syncOptions` or create the namespace before deployment.                                                                                           |
| **14. How do you deploy to multiple Kubernetes clusters?**                                     | Register additional clusters with Argo CD and create Applications targeting the appropriate cluster and namespace.                                                                  |
| **15. Why is Git called the "Single Source of Truth" in GitOps?**                              | The desired state is stored in Git. Argo CD continuously compares Git with the cluster and reconciles differences.                                                                  |
| **16. How do you stop users from changing resources manually?**                                | Use Kubernetes RBAC to restrict access. If changes occur, `selfHeal` can automatically restore the Git-defined state.                                                               |
| **17. A Pod was deleted manually. What happens?**                                              | The Kubernetes Deployment recreates the Pod. If the Deployment itself was deleted, Argo CD recreates it from Git during reconciliation.                                             |
| **18. How do you deploy different environments (Dev, QA, Prod)?**                              | Use separate folders, branches, Helm values files, Kustomize overlays, or separate Argo CD Applications.                                                                            |
| **19. Can Argo CD deploy Helm charts?**                                                        | Yes. Argo CD can deploy Helm charts from Git repositories or Helm repositories without requiring Helm to be installed in the target cluster.                                        |
| **20. Can Argo CD deploy Kustomize applications?**                                             | Yes. Argo CD has built-in support for Kustomize and generates the manifests before applying them.                                                                                   |
| **21. A deployment succeeds, but Pods are in `CrashLoopBackOff`. What does Argo CD show?**                    | Sync status is usually **Synced**, but Health status becomes **Degraded** because the workload is unhealthy.                                 |
| **22. How do you deploy automatically after every Git commit?**                                               | Configure `syncPolicy.automated` with optional `selfHeal` and `prune` settings.                                                              |
| **23. A YAML file contains a syntax error. What happens?**                                                    | The sync fails, and the application reports an error. Check the application events and controller logs for details.                          |
| **24. How do you monitor Argo CD?**                                                                           | Monitor the `argocd-*` Pods, view application health in the UI, and integrate metrics with Prometheus and Grafana.                           |
| **25. One application depends on another (for example, CRDs before Custom Resources). How do you handle it?** | Use sync waves, resource hooks, or separate Applications with controlled deployment order.                                                   |
| **26. Developers accidentally changed replicas from 3 to 10 using `kubectl scale`. What happens?**            | With `selfHeal: true`, Argo CD changes the replica count back to the value defined in Git.                                                   |
| **27. What happens if the Git repository becomes unavailable?**                                               | Existing workloads continue running. Argo CD cannot fetch new changes until Git becomes reachable again.                                     |
| **28. How do you deploy only one microservice without affecting others?**                                     | Create a separate Argo CD Application for each microservice or use an app-of-apps pattern with independent child applications.               |
| **29. How do you securely manage Secrets with Argo CD?**                                                      | Avoid storing plain-text secrets in Git. Use tools such as Sealed Secrets, External Secrets Operator, or SOPS with encrypted files.          |
| **30. Why do companies prefer Argo CD over manual `kubectl apply`?**                                          | It provides GitOps, automated synchronization, drift detection, version history, auditing, rollback through Git, and consistent deployments. |

