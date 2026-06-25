# ⚠️ Kubernetes RBAC Security Best Practices

## 🔐 1. Follow Least Privilege Principle
 * 👉 Give ONLY minimum required permissions
 * ❌ Bad Practice:
     * verbs: `["*"]`
     * resources: `["*"] ` 
 * ✅ Good Practice:
     * verbs: `["get","list"]`
     * resources: `["pods"] ` 
 * 🎯 Why?
      * Limits damage if compromised
      * Reduces security risk  

## 🚫 2. Avoid system:masters (Super Admin)
 * 👉 In EKS, this = ROOT access
 * ⚠️ Risks:
     * Full cluster control
     * Can delete everything
     * Can access secrets  
 * ✔️ Use only for:
      * Cluster admins
      * Emergency access

## 🔒 3. Never Use Wildcards (*) Carelessly
 * In Kubernetes RBAC, these `wildcards` mean all resources and all actions.
 * ❌ Dangerous: This grants full access to `every Kubernetes resource` covered by the scope of the `Role` or `ClusterRole`.
   ```hcl 
    * rules:
      - apiGroups: ["*"]         # All API groups
        resources: ["*"]         # All resources
        verbs: ["*"]             # All actions (create, get, list, watch, update, patch, delete, etc.)
   ```
  * ✅ Safe: Specify `exact resources` & Specify `required actions` 

### Example: Full Access Role (Namespace Only)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: full-access
  namespace: dev          #This allows full access only in the dev namespace.

rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
```

## 🔐 4. Protect Secrets Strictly
 Secrets contain:
  - Passwords  
  - Tokens  
  - API keys

* ✅ Safe:
    * resources: `["secrets"]`
    * verbs: `["get"]`  
 * ❌ Avoid:
     - list  
     - watch    👉 Can expose ALL secrets  

## 👥 5. Use Groups Instead of Individual Users
  * ❌ Bad : Assign roles to each user  
  * ✅ Good : Assign roles to groups
  * 🎯 Benefits:
        - Easy management  
        - Scalable  
        - Clean design  

## 🤖 6. Use ServiceAccounts for Applications
  * 👉 Never use human credentials inside pods
  * ✔️ Use:
      - ServiceAccount  
      - RBAC permissions  

## 🔗 7. Use IRSA for AWS Access (EKS)
 * 👉 Secure AWS access using:
    - IAM Roles  
    - ServiceAccounts
    - ✔️ No hardcoded credentials & Temporary tokens  

## 📂 8. Store RBAC YAMLs in Git
 * 👉 Store in GitHub
 * Benefits:
    - Version control  
    - Audit history  
    - Easy rollback  

## 🔍 9. Regularly Audit Permissions
Commands
```hcl
kubectl get roles -A  
kubectl get rolebindings -A  
kubectl get clusterroles  
kubectl get clusterrolebindings  
```
🎯 Check for:
   - Over-permission  
   - Unused roles    

## 🧪 10. Test Access Before Granting
 * 👉 Use: `kubectl auth can-i create pods --as=user1 -n dev  `
 * ✔️ Prevent mistakes
 * ✔️ Validate permissions  

## 🚫 11. Remove Unused Access
* 👉 Clean up:
     - Old users  
     - Unused roles  
     - Temporary access
     - 🎯 Reduces attack surface  

## 🔐 12. Separate Roles by Responsibility
 * Create roles like :
    * 👨‍💻 Developer → pods, deployments
    * 🔍 Viewer → read-only
    * 🔐 Admin → full access  

## 🧠 13. RBAC is Additive Only
  * ✔️ Allow rules only
  * ❌ No deny rules
  * 👉 Be careful what you grant  

## 🔐 14. Limit Namespace Access
 * 👉 Prefer : `Role + RoleBinding `
 * ❌ Avoid : `ClusterRoleBinding` (unless required)    

---

## 🧠 Quick Revision
| 🧩 Practice               | 💡 Meaning                                                    |
| ------------------------- | -------------------------------------------------------------- |
| 🔒 Least Privilege        | 🎯 Grant only minimum required access                         |
| ⚠️ Avoid `system:masters` | 👑 Don’t give `full admin rights` unnecessarily               |
| 🚫 No Wildcards `*`       | ❌ Avoid granting all `permissions/resources `                |
| 🔐 Protect Secrets        | 🛡️ Secure sensitive data (use `encryption`, avoid `exposure`) |
| 👥 Use Groups             | 👨‍👩‍👧 Manage users via groups (better control)                   |
| 🤖 Use ServiceAccounts    | 📦 Assign identities to Pods (avoid using user creds)         |
| 📊 Audit Regularly        | 🔍 Monitor access and review permissions                      |

## 🎯 One-Line Answer
  * RBAC security best practices focus on least privilege, avoiding wildcards, protecting secrets, and carefully controlling access using roles and bindings.

---

### 🚀 Kubernetes RBAC (Role-Based Access Control) – Rapid Fire Interview Questions & Answers
| 🔢     | ❓ Question                                | ✅ Answer                                                                   |
| ------ | ----------------------------------------- | -------------------------------------------------------------------------- |
| 1️⃣    | What is RBAC?                             | 🔐 Role-Based Access Control used to manage permissions in Kubernetes.     |
| 2️⃣    | Why do we need RBAC?                      | 🛡️ To control who can access and perform actions on Kubernetes resources. |
| 3️⃣    | What does RBAC stand for?                 | 🎯 Role-Based Access Control                                               |
| 4️⃣    | What are the main RBAC components?        | 📜 Role, 🌍 ClusterRole, 🔗 RoleBinding, 🔗 ClusterRoleBinding             |
| 5️⃣    | What is a Role?                           | 📜 Defines permissions within a namespace.                                 |
| 6️⃣    | What is a ClusterRole?                    | 🌍 Defines cluster-wide permissions.                                       |
| 7️⃣    | What is a RoleBinding?                    | 🔗 Assigns a Role to a User, Group, or ServiceAccount.                     |
| 8️⃣    | What is a ClusterRoleBinding?             | 🔗 Assigns a ClusterRole to a User, Group, or ServiceAccount.              |
| 9️⃣    | Is Role namespace-scoped?                 | ✅ Yes                                                                      |
| 🔟     | Is ClusterRole namespace-scoped?          | ❌ No                                                                       |
| 1️⃣1️⃣ | Is RoleBinding namespace-scoped?          | ✅ Yes                                                                      |
| 1️⃣2️⃣ | Is ClusterRoleBinding namespace-scoped?   | ❌ No                                                                       |
| 1️⃣3️⃣ | Which API group is used for RBAC?         | 🛡️ `rbac.authorization.k8s.io`                                            |
| 1️⃣4️⃣ | What is the principle of least privilege? | 🔒 Grant only the permissions required.                                    |
| 1️⃣5️⃣ | What is authorization in Kubernetes?      | ✅ Checking whether an authenticated user can perform an action.            |
| 1️⃣6️⃣ | Who can receive RBAC permissions?              | 👤 Users, 👥 Groups, 🤖 ServiceAccounts |
| 1️⃣7️⃣ | What is a Subject?                             | 🎯 Entity receiving permissions.        |
| 1️⃣8️⃣ | Can a ServiceAccount have RBAC permissions?    | ✅ Yes                                   |
| 1️⃣9️⃣ | Can a Group have RBAC permissions?             | ✅ Yes                                   |
| 2️⃣2️⃣ | What is the default ServiceAccount?            | 🤖 `default`                            |
| 2️⃣5️⃣ | Can a Role be assigned directly to a Pod?      | ❌ No, use ServiceAccount.               |
| 2️⃣6️⃣ | What is a Resource in RBAC?              | 📦 Pods, Deployments, Services, Secrets, etc.                 |
| 2️⃣7️⃣ | What are Verbs in RBAC?                  | ⚙️ Actions such as get, list, create, update, delete.         |
| 2️⃣8️⃣ | Common RBAC verbs?                       | `get`, `list`, `watch`, `create`, `update`, `patch`, `delete` |
| 2️⃣9️⃣ | Which verb reads a single resource?      | `get`                                                         |
| 3️⃣0️⃣ | Which verb lists all resources?          | `list`                                                        |
| 3️⃣1️⃣ | Which verb watches resource changes?     | `watch`                                                       |
| 3️⃣2️⃣ | Which verb creates resources?            | `create`                                                      |
| 3️⃣3️⃣ | Which verb modifies resources?           | `update`                                                      |
| 3️⃣4️⃣ | Which verb partially modifies resources? | `patch`                                                       |
| 3️⃣5️⃣ | Which verb removes resources?            | `delete`                                                      |
| 3️⃣6️⃣ | Can RBAC restrict Secrets access?        | ✅ Yes                                                         |
| 3️⃣7️⃣ | Can RBAC restrict Pod creation?          | ✅ Yes                                                         |
| 3️⃣8️⃣ | Can RBAC restrict namespace access?      | ✅ Yes                                                         |
| 3️⃣9️⃣ | Can RBAC restrict Deployments?           | ✅ Yes                                                         |
| 4️⃣0️⃣ | Can RBAC control CRDs?                   | ✅ Yes                                                         |
| 4️⃣1️⃣ | What is the purpose of a RoleBinding?           | 🔗 Connect Role → Subject        |
| 4️⃣2️⃣ | What is the purpose of a ClusterRoleBinding?    | 🔗 Connect ClusterRole → Subject |
| 4️⃣3️⃣ | Is Role alone enough?                           | ❌ No                             |
| 4️⃣4️⃣ | Is ClusterRole alone enough?                    | ❌ No                             |
| 4️⃣5️⃣ | What actually grants permissions?               | 🔗 Binding                       |
| 4️⃣6️⃣ | Can RoleBinding use ClusterRole?                | ✅ Yes                            |
| 4️⃣7️⃣ | Can ClusterRoleBinding use Role?                | ❌ No                             |
| 4️⃣8️⃣ | Can a ClusterRole be used within one namespace? | ✅ Yes via RoleBinding            |
| 4️⃣9️⃣ | What happens if Binding is deleted?             | ❌ Permissions are removed.       |
| 5️⃣0️⃣ | What happens if Role is deleted?                | ❌ Permissions stop working.      |
| 5️⃣1️⃣ | View Roles?                                  | `kubectl get roles`                                                      |
| 5️⃣2️⃣ | View ClusterRoles?                           | `kubectl get clusterroles`                                               |
| 5️⃣3️⃣ | View RoleBindings?                           | `kubectl get rolebindings`                                               |
| 5️⃣4️⃣ | View ClusterRoleBindings?                    | `kubectl get clusterrolebindings`                                        |
| 5️⃣5️⃣ | Describe a Role?                             | `kubectl describe role <name>`                                           |
| 5️⃣6️⃣ | Describe a ClusterRole?                      | `kubectl describe clusterrole <name>`                                    |
| 5️⃣7️⃣ | Check permissions for a user?                | `kubectl auth can-i`                                                     |
| 5️⃣8️⃣ | Example permission check?                    | `kubectl auth can-i create pods`                                         |
| 5️⃣9️⃣ | Check ServiceAccount permissions?            | `kubectl auth can-i list pods --as=system:serviceaccount:default:app-sa` |
| 6️⃣0️⃣ | Most important RBAC troubleshooting command? | ⭐ `kubectl auth can-i`                                                 |
| 6️⃣1️⃣ | How do IAM and RBAC work together in EKS? | 🔐 IAM authenticates, RBAC authorizes.                  |
| 6️⃣2️⃣ | What is `aws-auth ConfigMap` used for?    | 🔗 Maps `IAM users/roles` to Kubernetes RBAC groups.      |
| 6️⃣3️⃣ | Does IAM replace RBAC?                    | ❌ No                                                    |
| 6️⃣4️⃣ | Who handles authentication in EKS?        | ☁️ AWS IAM                                              |
| 6️⃣5️⃣ | Who handles authorization in EKS?         | 🛡️ Kubernetes RBAC                                     |
| 6️⃣6️⃣ | Can an IAM Role become cluster-admin?     | ✅ Through RBAC mapping.                                 |
| 6️⃣7️⃣ | What command checks aws-auth ConfigMap?   | `kubectl get configmap aws-auth -n kube-system -o yaml` |
| 6️⃣8️⃣ | What is IRSA related to?                  | 🤖 ServiceAccount + IAM Role integration.               |
| 6️⃣9️⃣ | Does IRSA replace RBAC?                   | ❌ No                                                    |
| 7️⃣0️⃣ | Best practice in EKS?                     | 🔒 Use IAM + RBAC + IRSA with least privilege.          |
