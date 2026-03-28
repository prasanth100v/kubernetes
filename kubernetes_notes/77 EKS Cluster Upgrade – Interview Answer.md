# 🚀 EKS Cluster Upgrade – Interview Answer
## ✅ Sample Interview Answer

In my project, we manage Kubernetes using AWS EKS. When a new version is released, I upgrade the cluster in a controlled and phased manner to ensure zero downtime.

---

## 🔹 Step-by-Step Breakdown
### 1️⃣ Control Plane Upgrade (AWS Console)

I first check the current version in the EKS dashboard. Since EKS supports only sequential minor upgrades, I select the next version and trigger the control plane upgrade.

👉 This step does **not impact running workloads**.

### 2️⃣ Node Group Upgrade
Once the control plane is upgraded, I move to the **Compute section** and update each managed node group.

👉 AWS performs a **rolling update**:

* Launches new nodes
* Drains old nodes safely
* Maintains availability

### 3️⃣ Add-on Upgrade
Next, I update essential EKS add-ons:

* VPC CNI
* CoreDNS
* kube-proxy

👉 I select the latest compatible versions from the **Add-ons tab**.

## 🔍 Verification
After completing upgrades, I verify:

* Nodes are in **Ready** state
* Pods are **Running**
* Workloads are stable

📊 Monitoring tools used:

* kubectl
* CloudWatch
* Prometheus dashboards

---

## 🧠 CLI Automation (Bonus Point)
In some environments, I use **eksctl** for scripting upgrades, especially in staging or automated pipelines.

## 🎯 Final Summary
### 👉 Upgrades are done in sequence:
  * **Control Plane → Node Groups → Add-ons**
  * With continuous monitoring to ensure **high availability and zero downtime**.

## 🔄 Rolling Update (Default Behavior)
In Amazon EKS, **Managed Node Groups** use a rolling update strategy by default.

During upgrade:
* New EC2 nodes are launched with updated version
* Old nodes are gracefully drained
* Pods are rescheduled automatically

👉 Zero downtime is ensured if:
* Proper replicas are configured
* PodDisruptionBudgets are in place

---

## 🔵🟢 Blue-Green Strategy (Advanced)
Blue-Green deployment is a manual strategy used for high-risk environments.

### 🔧 Steps:
1. Create a new node group (Green)
2. Taint old node group (Blue)
3. Cordon & drain old nodes
4. Migrate workloads using:
   * nodeSelector
   * affinity
   * taints
5. Validate workloads on new nodes
6. Delete old node group

👉 Not default, but useful for:

* Critical workloads
* Regulated environments

---

## 🚀 Summary

This approach ensures safe, controlled upgrades with minimal risk, maintaining application availability throughout the process.
