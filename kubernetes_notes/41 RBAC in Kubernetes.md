# 🔐 RBAC in Kubernetes (Easy + Complete Guide)

## 🌟 What is RBAC?
 
RBAC (Role-Based Access Control) is a security mechanism in Kubernetes that controls **who can do what** in your Kubernetes cluster.
- It uses **roles and bindings** to give permissions.

### 🧠 Simple Understanding
- 👤 Who → User / Group / ServiceAccount  
- 📦 What → Resource (pods, services, etc.)  
- ⚙️ Action → get, create, delete, update  

👉 RBAC = Who + What + Action

---

## 🎯 Purpose of RBAC

- Control access to Kubernetes resources  
- Secure the cluster  
- Prevent unauthorized actions  
- Follow least privilege principle  

---

## 🧠 Core Components

### 🔹 Role
- Namespace-scoped permissions  
- Applies only inside one namespace  

---

### 🔹 ClusterRole
- Cluster-wide permissions  
- Works across all namespaces  

---

### 🔹 RoleBinding
- Connects Role → User/Group/ServiceAccount  
- Namespace-scoped  

---

### 🔹 ClusterRoleBinding
- Connects ClusterRole → User/Group/ServiceAccount  
- Cluster-wide  

---

## 🔐 RBAC Flow (Interview Important)

1. User/Pod makes request  
2. Kubernetes API receives request  
3. RBAC checks Role/ClusterRole  
4. Checks binding (RoleBinding/ClusterRoleBinding)  
5. Permission (Allow) / Deny  

---

## ✅ 1. Namespace-Scoped RBAC (Role + RoleBinding)

### 🧾 Role (Permissions inside namespace)
```
apiVersion: rbac.authorization.k8s.io/v1  
kind: Role  
metadata:  
  namespace: development  
  name: developer-role  
rules:  
- apiGroups: [""]  
  resources: ["pods","services","configmaps"]  
  verbs: ["get","list","create","update","delete"]  
- apiGroups: ["apps"]  
  resources: ["deployments"]  
  verbs: ["get","list","create"]  
```
---

### 🔗 RoleBinding (Assign Role)
```
apiVersion: rbac.authorization.k8s.io/v1  
kind: RoleBinding  
metadata:  
  name: developer-binding  
  namespace: development  
subjects:  
- kind: User  
  name: johndoe@company.com  
  apiGroup: rbac.authorization.k8s.io  
- kind: Group  
  name: dev-team  
  apiGroup: rbac.authorization.k8s.io  
roleRef:  
  kind: Role  
  name: developer-role  
  apiGroup: rbac.authorization.k8s.io  
```
🧩 Use Case:
- 👉 Used when access should be limited to a specific namespace
- Developer team can manage pods only in development namespace
---

## 🌍 2. Cluster-Scoped RBAC (ClusterRole + ClusterRoleBinding)

### 🧾 ClusterRole (Cluster-wide permissions)
```
apiVersion: rbac.authorization.k8s.io/v1  
kind: ClusterRole  
metadata:  
  name: global-viewer  
rules:  
- apiGroups: [""]  
  resources: ["nodes","namespaces","persistentvolumes"]  
  verbs: ["get","list","watch"]  
- apiGroups: ["rbac.authorization.k8s.io"]  
  resources: ["roles","rolebindings"]  
  verbs: ["get","list"]  
```
---

### 🔗 ClusterRoleBinding (Global access)
```
apiVersion: rbac.authorization.k8s.io/v1  
kind: ClusterRoleBinding  
metadata:  
  name: viewer-global-binding  
subjects:  
- kind: User  
  name: auditor@company.com  
  apiGroup: rbac.authorization.k8s.io  
- kind: Group  
  name: security-team  
  apiGroup: rbac.authorization.k8s.io  
roleRef:  
  kind: ClusterRole  
  name: global-viewer  
  apiGroup: rbac.authorization.k8s.io  
```
🧩 Use Case:
- 👉 Used for global access across the cluster
- Security/audit team can view resources across entire cluster
---

## 🔥 Important Combinations

1. Role + RoleBinding → Namespace-level access  
2. ClusterRole + ClusterRoleBinding → Cluster-wide access  
3. 👉 Special case: ClusterRole + **RoleBinding** → ✅ Result: Cluster permissions but limited to one namespace  

---

## 🔐 Why RBAC is Important?

- Controls who can create/delete resources  
- Protects sensitive data (like secrets)  
- Prevents accidental damage  
- Integrates with IAM (AWS, GCP, Azure)  

---

## ⚠️ Common Mistakes

- ❌ Giving admin access to everyone  * (all permissions)
- ❌ Using ClusterRoleBinding unnecessarily 
- ❌ Assigning permissions to individual users instead of groups  
- ❌ Forgetting namespace in RoleBinding
- ❌ Overusing admin privileges

---

## ✅ Best Practices

- Use least privilege principle  
- Prefer groups over individual users  
- Separate roles for different teams  
- Avoid using default/admin roles unnecessarily  

---

## 🧠 Quick Revision

- Role → Namespace permissions  
- ClusterRole → Cluster permissions  
- RoleBinding → Attach Role  
- ClusterRoleBinding → Attach ClusterRole
- ClusterRole + RoleBinding → ***Namespace-limited*** cluster permissions

---

## 🎯 One-Line Answer

RBAC in Kubernetes controls who can do what on which resources using roles and bindings.
