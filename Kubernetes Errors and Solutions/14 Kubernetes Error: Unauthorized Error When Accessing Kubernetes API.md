## 🚨 Kubernetes Error: Unauthorized Error When Accessing Kubernetes API — Common Reasons & Solutions
### 📌 What does "Unauthorized" mean?
 * An `Unauthorized (401)` or `Forbidden (403)` error occurs when a user or service account tries to access the Kubernetes API without the required authentication or authorization.
 * Common errors:
    * Error from server (Unauthorized): `Unauthorized` (or)
    * Error from server (Forbidden): `pods is forbidden` : User "john" cannot list resource "pods" in API group "" in namespace "default"
 * Note:
    * 401 Unauthorized → Authentication failed (`invalid or expired credentials`).
    * 403 Forbidden → Authentication succeeded, but the user lacks permission (`RBAC issue`).

## ☸️ Kubernetes Unauthorized (401/403) Errors — Common Reasons & Solutions
| 🚨 **Cause**                                   | 📖 **Description**                                            | 🛠️ **Solution**                                              | 💻 **Useful Commands**                       |
| ---------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------- | -------------------------------------------- |
| 🔐 **Invalid Credentials**                     | Incorrect or expired `kubeconfig` credentials                 | Update or regenerate the kubeconfig and authenticate again    | `kubectl config view`                        |
| 👤 **Insufficient RBAC Permissions**           | User or ServiceAccount lacks the required Role or ClusterRole | Create or update the appropriate Role/ClusterRole and Binding | `kubectl auth can-i <verb> <resource>`       |
| 📂 **Wrong Kubeconfig**                        | Using the wrong kubeconfig file                               | Switch to the correct kubeconfig                              | `echo $KUBECONFIG`                           |
| 🔑 **Expired Token / Certificate**             | Authentication token or client certificate has expired        | Renew the token/certificate or regenerate kubeconfig          | Re-authenticate with the cluster             |
| 🏷️ **Incorrect Context**                      | `kubectl` is pointing to the wrong cluster or namespace       | Switch to the correct context and namespace                    | `kubectl config get-contexts`                |
| 🤖 **Incorrect ServiceAccount**                | Pod is using the wrong ServiceAccount                         | Update the Pod/Deployment to use the correct ServiceAccount   | `kubectl get serviceaccount`                 |
| ❌ **Missing RoleBinding / ClusterRoleBinding** | User or ServiceAccount is not bound to a role                 | Create the required RoleBinding or ClusterRoleBinding        | `kubectl get rolebinding,clusterrolebinding` |

## 🚀 Unauthorized Error Troubleshooting Flow
```hcl
Unauthorized / Forbidden Error
            ↓
Check Current Context
(kubectl config current-context)
            ↓
Correct Cluster & User?
            ↓
Check Permissions
(kubectl auth can-i)
            ↓
Permission Granted?
      ↓             ↓
     Yes            No
      ↓             ↓
Check Token      Update RBAC
or Certificate   (Role/RoleBinding)
      ↓             ↓
Verify Kubeconfig
            ↓
Access Kubernetes API Successfully ✅
```

## 🔍 Useful Troubleshooting Commands
| 💻 **Command**                               | 🎯 **Purpose**                    |
| -------------------------------------------- | --------------------------------- |
| `kubectl config view`                        | View kubeconfig configuration     |
| `kubectl config get-contexts`                | List available contexts           |
| `kubectl config current-context`             | Show the current (active) context |
| `kubectl config use-context <context-name>`  | Switch to another cluster context |
| `kubectl auth can-i get pods`                | Check RBAC permissions            |
| `kubectl auth can-i --list`                  | List all allowed actions          |
| `kubectl get rolebinding,clusterrolebinding` | Verify RBAC bindings              |
| `kubectl describe serviceaccount <sa-name>`  | Inspect a ServiceAccount          |
| `kubectl describe rolebinding <binding-name>` | View RBAC details             |






