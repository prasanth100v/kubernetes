# 🔐 Kubernetes RBAC Rules & Resources
  * RBAC rules define access using `apiGroups`, `resources`, and `verbs`. These rules are attached to roles and assigned via bindings to control access securely in Kubernetes.
  * 🌟 Structure of a Rule :
      * Each RBAC rule defines **what access is allowed**.
  * 🧩 A Rule Contains:

| 🏷️ **Field**    | 🎯 **Purpose**               | 💡 **Example**                               |
| ---------------- | ---------------------------- | -------------------------------------------- |
| 🔹 **apiGroups** | API group of the resource    | `""` (Core API), `"apps"`, `"batch"`         |
| 🔹 **resources** | Which Kubernetes resources   | `pods`, `services`, `deployments`, `secrets` |
| 🔹 **verbs**     | Allowed actions on resources | `get`, `list`, `create`, `update`, `delete`  |

## 🔐 Kubernetes RBAC Verbs (Actions)
| 🧩 Verb               | 💡 Meaning                               |
| --------------------- | ---------------------------------------- |
| 🔍 `get`              | 📄 Read a single resource                |
| 📋 `list`             | 📑 List multiple resources               |
| 👀 `watch`            | 🔄 Watch for changes (real-time updates) |
| ➕ `create`            | 🆕 Create a new resource                 |
| ✏️ `update`           | 🔁 Update an existing resource           |
| 🩹 `patch`            | 🧩 Partial update of a resource          |
| 🗑️ `delete`           | ❌ Delete a resource                      |
| 🧹 `deletecollection` | 🗑️ Delete multiple resources at once    |

## 📦 Example Rule
```yaml
rules:
- apiGroups: [""]  
  resources: ["pods","services"]  
  verbs: ["get","list"]  

- apiGroups: ["apps"]  
  resources: ["deployments"]  
  verbs: ["create","update","delete"]  
```
---

## 🌐 Kubernetes API Groups
| 🧩 **API Group**                  | 📖 **Purpose**            | 📦 **Resources**                                                       | 💡 **Real-World Usage**            |
| --------------------------------- | --------------------------- | ---------------------------------------------------------------------- | ---------------------------------- |
| 1️⃣ **Core (`apiGroups: [""]`)**  | Basic cluster resources     | 📦 Pods, Services, ConfigMaps, Secrets, Namespaces, PV, PVC, Endpoints | 🔗 Core building blocks of any app |
| 2️⃣ **apps**                      | Workload management         | 🚀 Deployments, ReplicaSets, StatefulSets, DaemonSets                  | ⚙️ Managing app lifecycle          |
| 3️⃣ **batch**                     | Batch processing            | 🧪 Jobs, CronJobs                                                      | ⏰ Scheduled tasks                   |
| 4️⃣ **rbac.authorization.k8s.io** | Access control              | 🔐 Roles, RoleBindings, ClusterRoles, ClusterRoleBindings              | 🛡 Security & permissions          |
| 5️⃣ **networking.k8s.io**         | Networking rules            | 🌐 Ingresses, NetworkPolicies, IngressClasses                          | 🌍 Traffic routing & security      |
| 6️⃣ **autoscaling**               | Auto scaling                | 📈 HorizontalPodAutoscalers                                            | ⚖️ Scale apps based on load        |
| 7️⃣ **policy**                    | Policy & disruption control | ⚖️ PodDisruptionBudgets, (⚠️ PodSecurityPolicies deprecated)           | 🧯 Maintain app availability       |
| 8️⃣ **storage.k8s.io**            | Storage management          | 💾 StorageClasses, CSIDrivers, CSINodes, VolumeAttachments             | 📦 Persistent storage handling     |
| 9️⃣ **apiextensions.k8s.io**      | Custom resources            | 🧩 CustomResourceDefinitions (CRDs)                                    | 🔧 Extend Kubernetes functionality |  

---

## 🔍 Useful Command
  * To list all resources in your cluster: `kubectl api-resources`
  * An API Group is a way Kubernetes organizes its `resources into categories`.

---

## 🔐 Real-Time Use Cases
| 🎯 **Use Case**                        | 🛠 **Components Used**                       | 🧠 **How It Works**                                                                                                        | 💡 **Real-World Insight**                            |
| -------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| ✅ **Read-only access (dev namespace)** | 🔹 Role + 🔗 RoleBinding                     | 👉 Role defines read-only (`get`, `list`) permissions on Pods<br>👉 RoleBinding assigns it to user/ServiceAccount in `dev` | 🔒 Developers can view logs/resources but not modify |
| 📊 **Monitoring access (Prometheus)**  | 🔹 ClusterRole + 🔗 ClusterRoleBinding       | 👉 ClusterRole allows listing Pods across namespaces<br>👉 Binding grants access to monitoring system                      | 📈 Used by Prometheus to scrape metrics cluster-wide    |
| ⚙️ **CI/CD access ((GitHub Actions)**  | 🔐 ServiceAccount + 🔹 Role + 🔗 RoleBinding | 👉 (GitHub Actions uses ServiceAccount<br>👉 Role allows managing `Pods/Deployments`<br>👉 Binding connects them           | 🚀 Used by (GitHub Actions to deploy apps            |
| ☁️ **AWS IAM + Kubernetes (IRSA)**     | 🔑 IAM + 🔐 ServiceAccount + 🛡 RBAC         | 👉 IAM handles AWS permissions (`S3`, `ECR`)<br>👉 RBAC controls Kubernetes access                                             | 🔐 Secure cloud + cluster integration               |


### 🔹 Pod access to S3 (Secure way)
| 🔢 **Step** | 📖 **What You Do**                  | 🧠 **How It Works**                                            | 💡 **Why It Matters**                      |
| ----------- | ----------------------------------- | -------------------------------------------------------------- | ------------------------------------------ |
| 1️⃣         | 🔗 Enable OIDC Provider             | Connects EKS identity with AWS IAM                             | Required for trust between EKS and AWS     |
| 2️⃣         | 📜 Create IAM Policy                | Define S3 permissions (`GetObject`, `PutObject`, `ListBucket`) | Follows least-privilege principle          |
| 3️⃣         | 🔐 Create IAM Role + Trust Policy   | Allows a Kubernetes ServiceAccount to assume the IAM role      | Secure identity mapping                    |
| 4️⃣         | 🤖 Create & Annotate ServiceAccount | Link IAM Role using `eks.amazonaws.com/role-arn` annotation    | Connects K8s identity to AWS identity      |
| 5️⃣         | 📦 Use ServiceAccount in Pod        | Pod automatically receives temporary AWS credentials           | Secure access to S3 without hardcoded keys |

### ⚙️ Example Annotation
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: s3-access-sa
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/s3-access-role       # 👉 Link IAM role
```
---

### Can multiple Pods use the same ServiceAccount❓
   * 👉 Yes.
   * Multiple Pods can share `one ServiceAccount `
   * Useful when `same permissions` are needed  

## ⚠️ Important Notes
| 🧩 Component      | 💡 Meaning                                         |
| ----------------- | -------------------------------------------------- |
| 🛡️ RBAC           | 🎯 Authorization system (what actions are allowed) |
| 👤 ServiceAccount | 🆔 Identity for Pods inside Kubernetes             |
| ☁️ IAM            | 🔑 Cloud permissions (AWS/GCP/Azure access)        |
 
 * 👉 All work together for secure access  

---

## 🧠 Quick Revision
| 🧩 Concept       | 💡 Meaning                                                  |
| ---------------- | ----------------------------------------------------------- |
| 📂 `apiGroups`   | 🌍 Where the resource belongs (`core`, `apps`, `batch`, etc.)     |
| 📦 `resources`   | 🎯 What you are accessing (`pods`, `deployments`,` services`)     |
| ⚙️ `verbs`       | ✏️ What actions you can perform (`get`, `list`, `create`, `delete`) |
| 📄 `Role`        | 📦 Namespace-level permissions                              |
| 🌐 `ClusterRole` | 🌍 Cluster-wide permissions                                 |

## 🎯 One-Line Answer
   * RBAC rules define which actions (`verbs`) are allowed on which resources within specific `API groups`.
