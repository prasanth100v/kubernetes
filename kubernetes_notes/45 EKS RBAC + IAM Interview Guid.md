# 🔐 EKS RBAC + IAM 
## What happens if a user has NO RoleBinding or ClusterRoleBinding❓

 * 👉 They **cannot perform any actions**
 * ❌ Kubernetes will return : **Forbidden Error**
 * 💡 Reason:
     * `Authentication` may `succeed` (IAM)
     * But `Authorization` (RBAC) `fails`
     * ❌ No permissions = No access

## 🔥 `system:masters` Group (Very Important)

  * Built-in Kubernetes group
  * Has **cluster-admin permissions**
  * ⚠️ What it means:
     * Full access to cluster
     * Bypasses ALL RBAC restrictions
     * 👉 Equivalent to **root/admin access**

## 🧪 kubectl auth can-i Command

  * 🎯 Purpose: Check if a `user/service account` has permission
  * 📌 Example: `kubectl auth can-i create pods --as=dev-user -n dev`
  * 👉 Output:
       * yes ✅ → allowed
       * no  ❌ → denied  

## ✅ 1. How RBAC Works in EKS?

  * EKS uses:
     * ☁️ IAM → Authentication
     * 🔐 RBAC → Authorization  

### 🔄 Flow:
| 🔢 Step | 📖 What Happens                       | 🧠 Explanation                 |
| ------- | ------------------------------------- | ------------------------------ |
| 1️⃣     | 🔑 IAM user/role logs in              | 👉 AWS authenticates identity  |
| 2️⃣     | 🔗 `aws-auth` ConfigMap maps identity | 👉 IAM → Kubernetes user/group |
| 3️⃣     | 🛡️ RBAC checks permissions           | 👉 Allows or denies actions    |


## 🧾 2. What is aws-auth ConfigMap?

  - Located in: `kube-system` namespace  
  - Used to` map IAM `→ Kubernetes  

### 📌 Example:
```yaml
mapUsers:
- userarn: arn:aws:iam::111122223333:user/dev-user  
  username: dev-user  
  groups:
    - dev-group  
```
---

## ⚠️ Important Limitation
### ❌ IAM Groups are NOT supported

* 👉 aws-auth supports ONLY:
   - IAM Users  
   - IAM Roles
   - ✅ Solution : Attach same `IAM Role` to multiple users


## 🧪 Testing Access in EKS
### 📌 Command:
```yaml
kubectl auth can-i get pods --as=dev-user -n dev
```
👉 Checks:
      - If IAM mapping + RBAC both allow access  

## ❌ What if IAM user is NOT mapped in aws-auth?

 * 👉 User CANNOT access cluster
 * Even if:
      - RBAC permissions exist ❗
 * 💡 Reason:
      - Authentication fails  

## ⚖️ Important Concept
 * 👉 BOTH are required:
    - IAM → Who are you  
    - RBAC → What can you do
    - ❌ Missing any → `Access denied`  

## 🔗 ServiceAccount + IAM (IRSA)
### Can they be connected ❓
   
   * 👉 YES ✅ using IRSA (IAM Roles for Service Accounts)
   * 🎯 What it does:
        * Links Kubernetes ServiceAccount → `IAM Role `
        * Allows Pods to access AWS services
     * 💡 Example Use:
        * Pod → Access S3
        * Pod → Access DynamoDB
        * No credentials stored & Secure access  

---

## 🧠 Quick Revision
| 🧩 Concept                | 📖 Meaning                 | 🧠 Explanation                                   | 💡 Real-World Insight                    |
| ------------------------- | -------------------------- | ------------------------------------------------- | ----------------------------------------- |
| ❌ **No RoleBinding**     | 🚫 No access to resources | 👉 IAM allows login but RBAC denies actions       | ⚠️ Common reason for “Forbidden” errors  |
| 🔥 **system:masters**     | 👑 Full admin access       | 👉 Complete control over cluster                 | ⚠️ Use carefully (admin-level)           |
| 🔗 **aws-auth**           | 🔄 IAM to Kubernetes mapping | 👉 Maps IAM users/roles to Kubernetes identities | 🔑 Required for EKS authentication     |
| 🧪 **kubectl auth can-i** | 🔍 Permission check command | 👉 Tests what actions are allowed                | 🛠️ Useful for debugging RBAC issues     |
| 🔐 **IAM + RBAC**         | 🔑 + 🛡️ Combined security | 👉 IAM = authentication, RBAC = authorization    | 🎯 Core EKS concept in EKS security      |
| 🤖 **IRSA**               | ☁️ Pod → AWS access        | 👉 ServiceAccount linked to IAM Role             | 🔐 Secure way for Pods to access AWS services  |

# 🔑 Kubernetes RBAC Key Concepts 
## 🌟 RBAC = Who Can Do What

  * RBAC controls:
     - 👤 Who → User / Group / ServiceAccount  
     - ⚙️ What action → get, list, create, delete  
     - 📦 On what resource → pods, secrets, deployments
     - 👉 RBAC = `Who + Action + Resource`

## 🎯 Role vs ClusterRole 
| 🧩 Aspect    | 🔹 Role                                    | 🌐 ClusterRole                                 |
| ------------ | ------------------------------------------- | ----------------------------------------------- |
| 📍 Scope     | 📦 Namespace-level                         | 🌍 Cluster-wide                                 | 
| 🎯 Use Case  | 🔒 Limit access to a namespace             | 🌐 Access across namespaces / cluster resources |
| 📦 Resources | 📦 Namespaced (Pods, Services, ConfigMaps) | 📂 Namespaced + Cluster-level (Nodes, PVs)      | 
| 🔗 Binding   | 🔗 RoleBinding                             | 🔗 RoleBinding / ClusterRoleBinding             |         |
| 🔒 Security  | ✅ More restricted                         | ⚠️ More powerful                                | 

---

## 🔐 Important Concept

### RBAC ONLY controls Kubernetes API access
  * ❌ It does NOT control:
      * Container-level permissions
      * Network traffic
      * Pod-to-Pod communication  

## 🔐 Authentication vs Authorization (RBAC)
| 🧩 Concept            | 💡 Meaning         | 🧠 Details                                 |
| --------------------- | ------------------ | -------------------------------------------- |
| 🔐 **Authentication** | 👤 Who you are     | 🔑 Verified using IAM, certificates, tokens  |
| 🔒 **Authorization**  | 🎯 What you can do | 🛡️ Controlled by RBAC (roles & permissions) |

   👉 Both are required  

## 🔐 Key RBAC Rules

### 🧾 1. Roles = What can be done

 * Define:
  - Verbs:
    - get, list, watch → Read  
    - create, update, patch, delete → Write  
    - * → All actions  

  - Resources:
    - pods  
    - services  
    - deployments  
    - secrets  

### 🔗 2. Bindings = Who can do it

 Subjects can be:
   - 👤 User  
   - 👥 Group  
   - 📦 ServiceAccount  


## ⚠️ Important Warning

 * ❌ Avoid using: `system:masters  `
 * 👉 Because:
    - Gives FULL admin access  
    - Bypasses all RBAC rules  

## 🔐 Security Best Practices
  - ✅ Follow least privilege principle  
  - ✅ Give only required permissions  
  - ✅ Avoid "*" (`wildcards`) in resources  
  - ✅ Be very strict with secrets access  
  - ✅ Use groups instead of individual users  

## 📂 Git Best Practice

 * Store all RBAC YAML files in Git:
   - Role  
   - RoleBinding  
   - ClusterRole  
   - ClusterRoleBinding  
 * 👉 Benefits:
   - Version control  
   - Easy auditing  
   - Change tracking  

---

## 🧠 Quick Revision
| 🧩 Concept                | 💡 Meaning                                                |
| ------------------------- | --------------------------------------------------------- |
| 🛡️ RBAC                  | 🎯 Controls **who + what + resource**                     |
| 📄 Role                   | 📦 Namespace-level permissions                            |
| 🌐 ClusterRole            | 🌍 Cluster-wide permissions                               |
| 🔗 Binding                | 🔑 Assigns permissions (RoleBinding / ClusterRoleBinding) |
| ⚠️ Avoid `system:masters` | 👑 Full admin access (use only when necessary)            |
| 🚫 Avoid `"*"`            | ❌ Don’t give all permissions/resources (least privilege)  |


## 🎯 One-Line Answer
   RBAC controls who can perform what actions on which Kubernetes resources using roles and bindings.
