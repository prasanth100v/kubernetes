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
      - Cluster admins  
      - Emergency access  

## 🔒 3. Never Use Wildcards (*) Carelessly

 * ❌ Dangerous:
    * resources: ["*"]
    * verbs: ["*"]  
 * ✅ Safe:
     - Specify exact resources  
     - Specify required actions  

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
   - Wildcards  

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
