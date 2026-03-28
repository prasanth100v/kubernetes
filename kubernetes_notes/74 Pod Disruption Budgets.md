# 🛑 Kubernetes Pod Disruption Budgets (PDB)
## 📌 What is a Pod Disruption Budget (PDB)?
A **Pod Disruption Budget (PDB)** is a Kubernetes policy ensures that a minimum number of pods are always running during **voluntary disruptions**.

### 🔧 Examples of Voluntary Disruptions:
- Node drain 🧹  
- Cluster upgrades ⬆️  
- Rolling updates 🔄  
- Manual pod eviction  

## ✅ Purpose of PDB
  * 👉 Ensures **High Availability (HA)** by controlling how many pods can go down at once.
  * 👉 PDB = Safety rule for pods during disruptions

### 🎯 Why PDB (Pod Disruption Budget) is Important
| 🧩 Scenario    | 📌 Behavior                                              | 💡 Impact                                    |
| --------------- | ------------------------------------------------------- | -------------------------------------------- |
| ❌ Without PDB | ⚠️ All pods may go down during updates (killed at once) | 😨 Application downtime                      |
| ✅ With PDB    | 🛡️ Controlled disruptions (minimum pods stay running)   | 🚀 High Availability (HA), zero/low downtime |


## 🔄 Voluntary vs Involuntary Disruptions
| 🧩 Type                    | 📌 Examples                                                                     | 💡 PDB Control          |
| -------------------------- | ------------------------------------------------------------------------------- | ----------------------- |
| 🟡 Voluntary Disruptions   | 🚧 Node drain (`kubectl drain`)<br>🔄 Rolling updates<br>🧹 Manual pod eviction | ✅ Controlled by PDB     |
| 🔴 Involuntary Disruptions | 💥 Node crash<br>🛠️ Hardware failure<br>⚠️ Kernel panic<br>📦 Pod crash        | ❌ Not controlled by PDB |

👉 PDB **only works for voluntary disruptions**

---

## 🧾 Example 1: minAvailable
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```
✅ At least **2 pods must always be running**

## 🧾 Example 2: maxUnavailable
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  maxUnavailable: 25%
  selector:
    matchLabels:
      app: my-app
```
✅ Allows **25% of pods to be unavailable**

---

## ⚠️ Important Rule
👉 PDB defines how many pods must stay available, You can use ONLY ONE:
  * ✅ minAvailable
  * ✅ maxUnavailable

❌ Not both together

---

## 🔄 Real Scenario
### Setup:
- Deployment with **5 pods**
- PDB: `minAvailable: 4`

### 🔄 What happens during node drain?

1️⃣ Node has 2 pods
2️⃣ Kubernetes tries to evict both
3️⃣ ❌ PDB allows only 1 eviction
4️⃣ ⏸️ Drain pauses
5️⃣ New pod starts elsewhere
6️⃣ Then second pod can be evicted

## 🧠 Key Clarification
- ❌ PDB does NOT control pod placement  
- ❌ Does NOT force pods to stay on nodes  
- ✅ Only limits disruption count  

## 🛑 Without PDB
⚠️ Risk:
- All pods may go down at once  
- Leads to downtime  
- Causes instability  

## 📌 Key Notes
  * Works with Deployments, StatefulSets ☸️
  * Not guaranteed for crashes ⚠️
  * Ensures safe updates 🔄
  * Critical for zero downtime 🚀  

---

## ✨ Summary

PDB helps:
- 🛑 Prevent too many pods going down  
- 🔄 Enable safe rolling updates  
- 📈 Maintain high availability  
- 🚀 Ensure production stability  

👉 Essential for reliable Kubernetes deployments 💙
