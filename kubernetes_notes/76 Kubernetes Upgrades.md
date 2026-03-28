# 🔄 Kubernetes Upgrades & Versioning 
## 📌 Why Kubernetes Upgrade is Important?

👉 Kubernetes Upgrade means updating our cluster to a newer version to get:

- 🔒 Security patches  
- 🐞 Bug fixes  
- ✨ New features  
- ⚡ Performance improvements  

### 🎯 Why Upgrades are Important?
 🔥 Benefits:
   * Stay secure from vulnerabilities
   * Avoid unsupported versions
   * Access new Kubernetes features
   * Maintain cluster stability

👉 Best Practice: Stay **1–2 versions behind latest** to remain stable & supported.

---

## 🧭 Kubernetes Versioning
Kubernetes follows **Semantic Versioning**:

```
[major].[minor].[patch]
Example: 1.35.1
```

### 🔍 Version Meaning:
| 🧩 Version Type | 📌 Description                   | 💡 What it Means                         |
| --------------- | -------------------------------- | ---------------------------------------- |
| 🔴 Major        | ⚠️ Rare breaking changes         | 🚨 Big changes, may break compatibility  |
| 🟡 Minor        | ✨ New features (~every 4 months) | 🚀 Regular updates with new capabilities |
| 🟢 Patch        | 🛠️ Bug fixes & security updates | 🔒 Safe updates, no breaking changes     |


## 📊 Supported Versions
👉 Kubernetes supports **🟢 last 3 minor versions only**

Example:
- 1.35  (released on January 28, 2026)
- 1.34  
- 1.33
- 1.32 ❌ (deprecated soon)

## ☁️ Amazon EKS Version Support
  * 🟢 Standard support → 14 months
  * 🟡 Extended support → +12 months (paid extra cost)

---

## 🔄 Upgrade Order (Important)

❌ Wrong: NOT allowed
```
1.27 → 1.29
```
✅ Correct: You MUST upgrade step-by-step
```
1.27 → 1.28 → 1.29
```

👉 Always upgrade **one minor version at a time**

---

## 🚀 Best Practices

- 🧪 Upgrade in **non-prod first**  
- 📊 Monitor cluster health:
  - kubectl get cs  
  - Prometheus  
- 🛑 Use PodDisruptionBudgets (PDB)  
- 🔄 Follow staging → production strategy  


## 🛑 Prerequisites for EKS Upgrade
Before upgrading:

- ✅ Cluster is in supported version  
- 💾 Take backups  
- 🔁 Use Deployments/StatefulSets  
- 🛡️ Apply PodDisruptionBudgets  
- ⚙️ Use managed/self-managed node groups  


## ⚙️ Safe Upgrade Strategy

1️⃣ Upgrade control plane  
2️⃣ Upgrade node groups  
3️⃣ Verify workloads  
4️⃣ Monitor logs & metrics  
5️⃣ Roll forward (or rollback if needed)  

## ⚠️ Risks Without Proper Upgrade

- ❌ Downtime  
- ❌ API deprecations  
- ❌ Unsupported features  
- ❌ Security vulnerabilities  

---

## ✨ Summary

Kubernetes upgrades:
- 🔄 Keep cluster secure & updated  
- ⚡ Improve performance  
- 🚀 Add new features  
- 🛡️ Maintain compatibility  (exist together without problems or conflict) .

### 👉 Upgrade carefully = Zero downtime deployments 💙
