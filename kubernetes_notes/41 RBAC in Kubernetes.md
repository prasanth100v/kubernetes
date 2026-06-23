# 🔐 RBAC in Kubernetes 
## 🌟 What is RBAC?
  * RBAC (Role-Based Access Control) is a `security mechanism` in Kubernetes that controls **who can do what** in your Kubernetes cluster.
  * It uses **roles and bindings** to give permissions.

### 🧠 Simple Understanding
| 🧩 **Component** | 💡 **Meaning**                 | 📖 **Example**                               |
| ---------------- | ------------------------------ | -------------------------------------------- |
| 👤 **Who**       | User, Group, or ServiceAccount | `dev-user`, `developers`, `backend-sa`       |
| 📦 **What**      | Kubernetes Resource            | `pods`, `services`, `deployments`, `secrets` |
| ⚙️ **Action**    | Operation on Resource          | `get`, `list`, `create`, `update`, `delete`  |

## 🎯 Purpose of RBAC
   - Control access to Kubernetes resources  
   - Secure the cluster  
   - Prevent unauthorized actions  
   - Follow least privilege principle  

## 🛡 Kubernetes RBAC Core Components
| 🧩 **Component**          | 📖 **Scope**    | 🔗 **Purpose**                                 | 🧠 **How It Works**                                           | 💡 **Real-World Example**                    |
| ------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------------- |
| 🔹 **Role**               | 📦 Namespace    | Defines permissions within a namespace         | 👉 Specifies allowed actions (`get`, `list`, `create`) on resources | Allow reading Pods in `dev` namespace    |
| 🔹 **ClusterRole**        | 🌐 Cluster-wide | Defines permissions across all namespaces      | 👉 Can access cluster-level resources (`nodes`, `PVs`)            | Allow viewing nodes across cluster         |
| 🔹 **RoleBinding**        | 📦 Namespace    | Links Role to User/Group/ServiceAccount        | 👉 Grants permissions **only within that namespace**            | Bind `dev-role` to a ServiceAccount in `dev` |
| 🔹 **ClusterRoleBinding** | 🌐 Cluster-wide | Links ClusterRole to User/Group/ServiceAccount | 👉 Grants permissions across entire cluster                      | Give admin access to a user                  |

---

## 🔐 RBAC Authorization Flow (Step-by-Step)
| 🔢 **Step** | 📖 **What Happens**                       | 🧠 **Explanation**                                 | 
| ----------- | ----------------------------------------- | ---------------------------------------------------- | 
| 1️⃣         | 👤 User / 🤖 Pod makes request            | 👉 Request sent to Kubernetes API server             | 
| 2️⃣         | 🌐 Kubernetes API Server receives request | 👉 Acts as the central entry point                   | 
| 3️⃣         | 🛡️ RBAC checks Role / ClusterRole        | 👉 Determines what actions are allowed               |
| 4️⃣         | 🔗 Check RoleBinding / ClusterRoleBinding | 👉 Verifies if user/ServiceAccount is linked to role | 
| 5️⃣         | ✅ Permission `Allow / ❌ Deny `          | 👉 Final decision based on `RBAC rules`               |

---

## ✅ 1. Namespace-Scoped RBAC (Role + RoleBinding)
### 🧾 Role (Permissions inside namespace)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer-role                # 🏷️ Role name
  namespace: development              # 📦 Applies ONLY to this namespace

rules:
  - apiGroups: [""]                    # 🔹 Core API Group ("" = core resources)
    resources:
      - pods
      - services
      - configmaps
    verbs:
      - get
      - list
      - create
      - update
      - delete

  - apiGroups: ["apps"]                # 🔹 Apps API Group (deployments etc.)
    resources:
      - deployments
    verbs:
      - get
      - list
      - create
```

### 🔗 ROLE BINDING (Attach Role to Users/Groups)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding                    # 🏷️ Binding name
  namespace: development                     # ⚠️ Must match Role namespace

subjects:
  - kind: User                                  # 👤 Specific User
    name: johndoe@company.com
    apiGroup: rbac.authorization.k8s.io

  - kind: Group                                 # 👥 Group of Users
    name: dev-team
    apiGroup: rbac.authorization.k8s.io

roleRef:
  kind: Role
  name: developer-role                           # 🔗 Must match Role name
  apiGroup: rbac.authorization.k8s.io
```
🧩 Use Case:
     - 👉 Used when access should be limited to a specific namespace
     - Developer team can manage pods only in development namespace

---

## 🌍 2. Cluster-Scoped RBAC (ClusterRole + ClusterRoleBinding)
### 🧾 ClusterRole (Cluster-wide permissions)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: global-viewer              # 🏷️ Cluster-wide role (no namespace)

rules:                             # 🔹 Cluster-level core resources
  - apiGroups: [""]
    resources:
      - nodes
      - namespaces
      - persistentvolumes
    verbs:
      - get
      - list
      - watch
  - apiGroups: ["rbac.authorization.k8s.io"]         # 🔹 RBAC resources visibility
    resources:
      - roles
      - rolebindings
    verbs:
      - get
      - list

```

### 🔗 ClusterRoleBinding (Global access)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: viewer-global-binding      # 🏷️ Binding name

subjects:
  - kind: User                              # 👤 Auditor User
    name: auditor@company.com
    apiGroup: rbac.authorization.k8s.io
  - kind: Group                             # 👥 Security Team Group
    name: security-team
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: global-viewer                       # 🔗 Must match ClusterRole name
  apiGroup: rbac.authorization.k8s.io
```
🧩 Use Case:
     - 👉 Used for global access across the cluster
     - Security/audit team can view resources across entire cluster

## 🔥 Important Combinations
| 🧩 Combination                       | 🌍 Scope             | 💡 Result                                                   |
| ------------------------------------ | -------------------- | ----------------------------------------------------------- |
| 1️⃣ Role + RoleBinding               | 📦 Namespace-level   | ✅ Access only within that namespace                         |
| 2️⃣ ClusterRole + ClusterRoleBinding | 🌐 Cluster-wide      | ✅ Access across entire cluster                              |
| 3️⃣ ClusterRole + RoleBinding        | 📦 Namespace-limited | ⚡ Cluster-level permissions **restricted to one namespace** |
  
## 🔐 Why RBAC is Important?
   - Controls who can `create/delete` resources  
   - Protects sensitive data (like `secrets`)  
   - Prevents accidental damage  
   - Integrates with `IAM` (AWS, GCP, Azure)  

## ⚠️ Common Mistakes
   - ❌ Giving admin access to everyone ` * ` (all permissions)
   - ❌ Using ClusterRoleBinding unnecessarily 
   - ❌ Assigning permissions to individual users instead of groups  
   - ❌ Forgetting `namespace` in RoleBinding
   - ❌ Overusing `admin privileges`

## ✅ Best Practices
   - Use least privilege principle  
   - Prefer groups over individual users  
   - Separate roles for different teams  
   - Avoid using default/admin roles unnecessarily  

---

 ## 🎯 One-Line Answer
   RBAC in Kubernetes controls who can do what on which resources using `roles` and `bindings`.
