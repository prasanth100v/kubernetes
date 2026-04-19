# 🔐 RBAC Users & ServiceAccounts in kubeadm Cluster (Easy + Complete Guide)
## 👤 Creating Users for RBAC in kubeadm Cluster

* In a kubeadm cluster, Kubernetes does NOT create users directly.
* 👉 You manually create:
    - Private key  
    - Certificate  
    - kubeconfig context  
* In a kubeadm-based cluster (unlike EKS):
    - 👉 There is NO IAM / aws-auth
    - 👉 You manually create users using certificates

## 🟢 Step 1: 🔑 Generate Private Key 👉 This is the user’s private identity
```yaml
openssl genrsa -out johndoe.key 2048
```

## 🟢 Step 2: Create CSR (Certificate Signing Request)
```yaml
openssl req -new -key johndoe.key -out johndoe.csr -subj "/CN=johndoe/O=developers"
```
### 🧠 Important:
   - CN (Common Name) → Username (johndoe)  
   - O (Organization) → Group (developers)
   - 👉 Group is used later in RBAC

## 🟢 Step 3: 🔐 Sign CSR with Kubernetes CA 
```yaml
sudo openssl x509 -req -in johndoe.csr \
-CA /etc/kubernetes/pki/ca.crt \
-CAkey /etc/kubernetes/pki/ca.key \
-CAcreateserial -out johndoe.crt -days 365
```

## 🟢 Step 4: Configure kubectl for User
Set Credentials
```yaml
kubectl config set-credentials johndoe \
--client-certificate=johndoe.crt \
--client-key=johndoe.key
```
Create Context
```yaml
kubectl config set-context johndoe-context \
--cluster=kubernetes \
--user=johndoe
```
## 🔐 RBAC Configuration
### 🧾 Create ClusterRole (Cluster-wide permissions)
```yaml
apiVersion: rbac.authorization.k8s.io/v1  
kind: ClusterRole  
metadata:  
  name: viewer  
rules:  
- apiGroups: [""]  
  resources: ["pods","services","deployments"]  
  verbs: ["get","list","watch"]  
```

### 🔗 RoleBinding (Bind Role to User)
```yaml
apiVersion: rbac.authorization.k8s.io/v1  
kind: RoleBinding  
metadata:  
  name: developer-binding  
  namespace: development  
subjects:  
- kind: User  
  name: johndoe  
  apiGroup: rbac.authorization.k8s.io  
roleRef:  
  kind: Role  
  name: developer-role  
  apiGroup: rbac.authorization.k8s.io  
```

## 🧪 Step 5: Verify Access
### Switch Context:
```yaml
kubectl config use-context johndoe-context
```

### Test:
```yaml
kubectl get pods -n development  
kubectl get nodes
```
👉 Expected:
    - pods → ✅ allowed  
    - nodes → ❌ denied (if not permitted)  

---

# 🤖 Creating ServiceAccount for RBAC

 ServiceAccounts are used by **Pods inside cluster** to talk to Kubernetes API.

## 🟢 Step 1: Create Namespace
```yaml
kubectl create namespace dev
```

## 🟢 Step 2: Create ServiceAccount
```yaml
apiVersion: v1  
kind: ServiceAccount  
metadata:  
  name: dev-app-sa  
  namespace: dev  
```

## 🟢 Step 3: Attach Permissions

* 👉 Create:
      - Role  
      - RoleBinding  (define rules as per requirement)

## 🔐 How ServiceAccount Works

   - Token is automatically mounted inside Pod  
   - Used for authentication with API server
   - 👉 Inside pod📍 Token path : `/var/run/secrets/kubernetes.io/serviceaccount/`
   * 👉 This token is:
       * Auto-generated
       * Auto-mounted
       * Used to call Kubernetes API

## 👥 Groups in Kubernetes

  - Kubernetes does NOT manage groups directly  
  - Groups come from:
     - OIDC  
     - IAM  
     - External providers  
  * 👉 You can still:
        - Define RBAC rules for a group  
        - As long as user belongs to that group  

---

## ☁️ Important EKS Concept
###  Can we use RBAC alone without aws-auth in EKS❓

  * ❌ No
  * 👉 You need BOTH:
     * IAM (aws-auth) → Authentication
     * RBAC → Authorization  

## ⚖️ Key Differences
v| 🧩 **Feature**     | 👤 **User (kubeadm)**                    | 📦 **ServiceAccount**       | 🧠 **Explanation**                                                      |
| ------------------ | ---------------------------------------- | --------------------------- | ----------------------------------------------------------------------- |
| 👥 **Used by**     | 👨‍💻 Humans (admins, devs)                    | 🤖 Pods / applications         | Users are external identities; ServiceAccounts are for workloads        |
| 🔑 **Auth method** | 🪪 Certificate (client cert via kubeconfig) | 🔑 Token (auto-mounted in Pod) | Different authentication mechanisms                                     |
| ⚙️ **Created by**  | 🛠️ Manual (admin creates certs/kubeconfig)  | ⚡ Automatically by Kubernetes | Every namespace gets a default ServiceAccount                           |
| 🌍 **Scope**       | 🌐  External to cluster                      | 📦 Internal to cluster         | Users access cluster from outside; ServiceAccounts operate inside cluster |

## 🧠 Quick Revision
| 🧩 Concept        | 💡 Meaning                                |
| ----------------- | ----------------------------------------- |
| 👤 Users          | 🪪 Created using certificates             |
| 🏷️ CN            | 🧑 Username (Common Name in cert)         |
| 👥 O              | 👨‍👩‍👧 Group (Organization field)       |
| 🛡️ RBAC          | 🎯 Controls permissions (what you can do) |
| 📦 ServiceAccount | 🤖 Pod identity inside cluster            |
| 🔑 Token          | 🔐 Auto-mounted credential for Pods       |

## 🎯 One-Line Answer
   In kubeadm, users are created using `certificates` and assigned` RBAC roles`, while service accounts use `auto-mounted tokens` for pod-level access.

# 🔐 Kubernetes RBAC - Commands Cheat Sheet
### 📋 1. LIST & VIEW RBAC RESOURCES
```hcl
 kubectl get roles,clusterroles --all-namespaces                    # 📦 List all Roles & ClusterRoles (all namespaces)
 kubectl get rolebindings,clusterrolebindings --all-namespaces      # 🔗 List all RoleBindings & ClusterRoleBindings
 kubectl get rolebindings,clusterrolebindings -A                    # 🔍 Alternative (short flag)
 kubectl -n kube-system get configmap aws-auth -o yaml              # ☁️ Check IAM users mapped to EKS (aws-auth ConfigMap)
```
#### 🚀 2. APPLY RBAC YAML FILES
```hcl
 kubectl apply -f role.yaml                       # 📦 Apply Role (namespace-scoped)
 kubectl apply -f clusterrole.yaml                # 🌐 Apply ClusterRole (cluster-wide)
 kubectl apply -f rolebinding.yaml                # 🔗 Bind Role to user/service account
 kubectl apply -f clusterrolebinding.yaml         # 🌍 Bind ClusterRole globally
```
---
### 🛠️ 3. CREATE ROLE USING COMMAND

 ➕ Create Role (read pods in default namespace
```yaml
 kubectl create role pod-reader \
   --verb=get,list,watch \
   --resource=pods \
   --namespace=default
```

```hcl
 kubectl get roles -n default                       # 📋 List Roles in namespace
 kubectl get rolebindings -n default                # 🔗 List RoleBindings in namespace
 kubectl describe role pod-reader -n default        # 🔍 Describe Role
 kubectl delete role pod-reader -n default          # ❌ Delete Role
```

### ✅ 4. TEST PERMISSIONS (VERY IMPORTANT 🔥)
 🔍 Check if user can perform action
```hcl
 kubectl auth can-i list pods \
   --as=dev-user \
   --namespace=default
```

#### 🔎 5. CHECK RBAC RESOURCES (QUICK DEBUG)
 📦 Namespace-specific
```hcl
kubectl get roles -n <namespace>
kubectl get rolebindings -n <namespace>
```
 🌐 Cluster-wide
```hcl
 kubectl get clusterroles
 kubectl get clusterrolebindings
```

### 📋 6. DESCRIBE (DETAILED INFO)
```hcl
 kubectl describe role <role-name> -n <namespace>               # 🔍 Role
 kubectl describe rolebinding <binding-name> -n <namespace>     # 🔗 RoleBinding
 kubectl describe clusterrole <clusterrole-name>                # 🌐 ClusterRole
 kubectl describe clusterrolebinding <binding-name>             # 🌍 ClusterRoleBinding
```
#### ☁️ 7. EKS IAM ACCESS MANAGEMENT
```hcl
 kubectl edit configmap aws-auth -n kube-system     # ✏️ Edit aws-auth ConfigMap (map IAM users/roles)
```
   ⚠️ Important:
      - This controls IAM → Kubernetes RBAC mapping
      - Used in AWS EKS clusters only

