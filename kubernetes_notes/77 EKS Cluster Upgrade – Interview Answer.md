# 🚀 EKS Cluster Upgrade – Interview Answer
## ✅ Sample Interview Answer

 * In my project, we manage Kubernetes using `AWS EKS`. When a new version is released, I upgrade the cluster in a `controlled` and `phased manner` to ensure `zero downtime`.

## 🔹 Step-by-Step Breakdown
### 1️⃣ Control Plane Upgrade (AWS Console)

 * I first check the current version in the `EKS dashboard`. Since EKS supports only sequential minor upgrades, I select the next version and trigger the control plane upgrade.
 * 👉 This step does **not impact running workloads**.

### 2️⃣ Node Group Upgrade
 * Once the control plane is upgraded, I move to the **Compute section** and update each managed node group.
 * 👉 AWS performs a **rolling update** Maintains availability

### 🔁 What happens internally:
  * 1️⃣ New EC2 nodes launched
  * 2️⃣ Old node `cordoned` (no new pods)
  * 3️⃣ Pods drained (`evicted`)
  * 4️⃣ Pods rescheduled on new nodes
  * 5️⃣ Old node terminated

### 3️⃣ Add-on Upgrade
 * Next, I update essential EKS add-ons:
   * `VPC CNI`
   * `CoreDNS`
   * `kube-proxy`
   * 👉 I select the latest compatible versions from the `Add-ons tab`.

## 🔍 Verification
 * After completing upgrades, I verify:
    * Nodes are in **Ready** state
    * Pods are **Running**
    * Workloads are stable
 * 📊 Monitoring tools used:
    * `kubectl`
    * `CloudWatch`
    * `Prometheus dashboards`

---

## 🧠 CLI Automation (Bonus Point)
 * In some environments, I use **eksctl** for scripting upgrades, especially in `staging` or `automated CI/CD pipelines`.

## 🎯 Final Summary
### 👉 Upgrades are done in sequence :
  * **Control Plane → Node Groups → Add-ons**
  * With continuous monitoring to ensure **high availability and zero downtime**.

## 🔄 Rolling Update (Default Behavior)
  * In Amazon EKS, **Managed Node Groups** use a rolling update strategy by default.
  * During upgrade:
      * New EC2 nodes are launched with `updated version` .
      * Old nodes are gracefully `drained`
      * Pods are `rescheduled` automatically
  * 👉 Zero downtime is ensured if :
      * Proper `replicas` are configured
      * `PodDisruptionBudgets` are in place

---

## 🔵🟢 Blue-Green Strategy (Advanced)
 * Blue-Green deployment is a `manual strategy` used for `high-risk environments`.
 * For critical workloads, I’ve also used a `Blue-Green strategy` by creating a `new node group`, validating workloads, and then switching traffic before removing the `old nodes`.
 * When to Use: `High-risk upgrades `& `Production-critical apps`

### 🔧 Steps:
1. Create a new node group (`Green`)
2. Taint old node group (`Blue`)
3. Cordon & drain old nodes
4. Migrate workloads using:
   * `nodeSelector`
   * `affinity`
   * `taints`
5. Validate workloads on new nodes
6. Delete old node group

### ⚖️ Rolling vs Blue-Green

| 🧩 **Feature**  | 🔄 **Rolling Update**                                        | 🔵🟢 **Blue-Green Deployment**                        | 🧠 **Explanation**                       | 
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------- | ---------------------------------------- | 
| ⚙️ **Default**  | ✅ Yes (Kubernetes/EKS default for node groups & deployments) | ❌ Manual setup (separate env/cluster)                | Rolling replaces instances gradually     | 
| ⚠️ **Risk**     | 👍 Low                                                       | 🔥 Very Low ( Zero-downtime critical apps)             | Blue-Green allows instant rollback       |
| 💰 **Cost**     | 💸 Low                                                       | 💵 High (duplicate infra)                              | Two environments double cost temporarily |
| 🔁 **Process**  | In-place update                                              | Parallel environments                                   | One-by-one vs full switch                | 
| 🔄 **Rollback** | ⚠️ Slower                                                    | ⚡ Instant (switch traffic back)                       | Blue-Green is safer for failures         | 
| 🎯 **Use Case** | 🔁 Normal upgrades                                           | 🚀 High-risk / critical changes (Production strategy ) | Depends on impact level                  |

---

## ⚡ Kubernetes Cluster Upgrade — Rapid Fire Q&A
| 🔢 Q#   | ❓ Question                             | 💡 Answer                                                                   |
| ------- | -------------------------------------- | ------------------------------------------------------------------------------ |
| 🔹 Q1   | What is a Kubernetes cluster upgrade?  | 👉 Updating `control plane + worker nodes` to a newer Kubernetes version.   |
| 🔹 Q2   | Why upgrade Kubernetes?                | 👉 `Security patches`, `bug fixes`, `new features`, `deprecations`.         |
| 🔹 Q3   | Is upgrade downtime-free?              | 👉 Can be `zero/minimal downtime` if done properly.                         |
| ☁️ Q4   | Who manages control plane in EKS?      | 👉 Amazon Web Services                                                      |
| ☁️ Q5   | How to upgrade EKS control plane?      | 👉 AWS Console / CLI / eksctl                                               |
| ☁️ Q6   | Is control plane upgrade disruptive?   | 👉 ❌ No — AWS handles it with high availability.                          |
| ☁️ Q7   | Can you skip versions in EKS?          | 👉 ❌ No — must `upgrade sequentially` (one version at a time).            |
| 🖥️ Q8  | Who upgrades worker nodes?             | 👉 You (`manual` or` managed node groups`)                                  |
| 🖥️ Q9  | How to upgrade nodes?                  | 👉 Rolling update / new node group / AMI update                             |
| 🖥️ Q10 | What is recommended approach?          | 👉 Create new node group → migrate workloads → delete old                   |
| 🔌 Q11  | What are EKS add-ons?                  | 👉 Core components like: `VPC CNI`, `CoreDNS`, `kube-proxy  `              |
| 🔌 Q12  | Should add-ons be upgraded?            | 👉 ✅ Yes — must match cluster version.                                    |
| ⚙️ Q13  | Pre-check before EKS upgrade?          | 👉 Check `deprecated APIs`, `app compatibility`.                           |
| ⚙️ Q14  | Command to check version skew?         | 👉 Nodes should not lag more than 2 versions behind control plane.         |
| 🛠️ Q15 | Who manages control plane in kubeadm?  | 👉 You                                                                      |
| 🛠️ Q16 | First step in kubeadm upgrade?         | 👉 Upgrade kubeadm binary                                                   |
| 🛠️ Q17 | Command to plan upgrade?               | 👉 kubeadm upgrade plan                                                     |
| 🛠️ Q18 | Command to upgrade control plane?      | 👉 kubeadm upgrade apply <version>                                          |
| 🔄 Q19  | Steps to upgrade worker nodes?         | 👉 Drain node → `Upgrade kubelet & kubectl` → `Restart kubelet → Uncordon node` |
| 🔧 Q20  | What components are upgraded?          | 👉 `API server`, `scheduler`, `controller-manager`, `etcd`                   |
| 💾 Q21  | Why is etcd critical?                  | 👉 Stores cluster state                                                     |
| 💾 Q22  | Best practice before upgrade?          | 👉 Take etcd backup                                                         |
| ⚖️ Q23  | What is version skew policy?           | 👉 Nodes can be up to `2 versions` behind control plane.                    |
| ⚖️ Q24  | Can kubeadm skip versions?             | 👉 `❌ No` — sequential upgrade required.                                  |
| 🛠️ Q25 | Pods not starting after upgrade?       | 👉 Check: `API compatibility`, `Deprecated resources`, `CNI plugin `         |
| 🛠️ Q26 | Node NotReady after upgrade?           | 👉 Check `kubelet version`, `restart service`.                               |
| 🎯 Q27  | How to upgrade with zero downtime?     | 👉 `Rolling upgrade` + `PDB` + `multiple replicas`.                         |
| 🎯 Q28  | Why upgrade failed in kubeadm cluster? | 👉 Skipped steps, version mismatch, etcd issues.                            |
| 🎯 Q29  | Best upgrade strategy for production?  | 👉 `Blue-green nodes` / `rolling upgrades`.                                 |
| 🎯 Q30  | How to rollback upgrade?               | 👉 Restore etcd backup (`kubeadm`) / limited rollback in EKS.               |

## 🚀 Summary

 * This approach ensures `safe`, controlled upgrades with `minimal risk`, maintaining application availability throughout the process.
