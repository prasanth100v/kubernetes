# 🔐 ServiceAccount in Kubernetes 
## 🌟 What is a ServiceAccount?
 * A **ServiceAccount** is an identity used by **Pods** to interact with the Kubernetes API server securely.
 * 🧠 Simple Understanding :
    * 👤 Humans → use `kubectl`
    * 📦 Pods → use **ServiceAccounts**
  * 👉 Just like users need `credentials`, Pods also need `identity` and `permissions`.
       * 👉 User accounts are for humans
       * 👉 ServiceAccounts are for Pods  

 ## 🎯 Purpose
   * 🔹 Identity for Pods (`not humans`)
   * 🔹 Namespace-scoped
   * 🔹 Secure communication with `Kubernetes API`

## 🏠 Real-Life Analogy
   - 🏢 Kubernetes → Office  
   - 👨 User → Employee ID card  
   - 📦 Pod → ServiceAccount
   - 👉 No ID → `No access`  👉 With ID → `Controlled access ` 

---

## 🛠 Default ServiceAccount
### 🔹 Default Behavior
  * If you create a Pod **without specifying a ServiceAccount**:
      * Kubernetes automatically assigns the **default ServiceAccount**
      * Every namespace has `one default ServiceAccount`
      * Used automatically if `none` is specified

### 🔍 Check:
```bash
kubectl get serviceaccount
```

## 🧠 Key Concepts
 * Every namespace has a default ServiceAccount.
 * Automatically mounted inside Pod at:
    * `/var/run/secrets/kubernetes.io/serviceaccount/`
    * Used to authenticate API requests.

 * RBAC:
    * ServiceAccount gives identity only.❗, Permissions come from:
        * 🧾 Role → what actions allowed
        * 🔗 RoleBinding → who gets permission

---

## 🔐 ServiceAccount Flow in Kubernetes
| 🔢 **Step**                     | 📖 **What Happens**                                 | 🧠 **Explanation**                        | 💡 **Real-World Insight**      |
| ------------------------------- | ---------------------------------------------------- | ----------------------------------------- | ------------------------------- |
| 1️⃣ **Pod Created**             | 📦 Pod is scheduled on a node                        | 👉 Kubernetes starts the Pod              | Every Pod needs an identity     |
| 2️⃣ **ServiceAccount Attached** | 👤 Pod gets a ServiceAccount (default or custom)     | 👉 Defines identity of the Pod            | Used for API access control     |
| 3️⃣ **Token Injected**          | 🔑 Token mounted inside Pod (`/var/run/secrets/...`) | 👉 JWT token used for authentication      | Pod can securely talk to API    |
| 4️⃣ **API Call**                | 🌐 Pod sends request to Kubernetes API               | 👉 Uses token for authentication          | Example: list Pods, read config |
| 5️⃣ **RBAC Check**              | 🛡️ API server verifies permissions                   | 👉 Checks Role/RoleBinding or ClusterRole | Enforces least privilege        |
| 6️⃣ **Access Decision**         | ✅ Request allowed or denied                         | 👉 Based on RBAC rules                    | Final security control point    |


## 🚀 Why Use ServiceAccounts
| 🎯 **Purpose**             | 📖 **Explanation**                                                | 💡 **Real-World Value**                   |
| -------------------------- | ------------------------------------------------------------------ | ----------------------------------------- |
| 🔐 **Secure API Access**   | Pods use ServiceAccount tokens to authenticate with Kubernetes API | 👉 Prevents unauthorized access           |
| 🎯 **Control Permissions** | Works with RBAC to allow/deny actions                              | 👉 Enforces **least privilege principle** |
| ⚙️ **Automation Support**  | Required for tools interacting with cluster                        | 👉 Enables CI/CD, monitoring, logging     |

## 🌍 Real-Time Use Cases
| 🧩 **Use Case**           | 🛠 **Tool / Platform**   | 📖 **What It Does**               | 💡 **Why ServiceAccount Needed**                   |
| ------------------------- | ------------------------ | --------------------------------- | -------------------------------------------------- |
| ⚙️ **CI/CD**              | Argo CD, Jenkins         | Deploy applications to Kubernetes | 👉 Needs API access to create/update resources     |
| 📊 **Monitoring**         | Prometheus               | Collects metrics from cluster     | 👉 Requires permission to read metrics & resources |
| 📜 **Logging**            | Fluentd                  | Aggregates logs from Pods/nodes   | 👉 Needs access to Pod metadata                    |
| 🔑 **Secrets Management** | `HashiCorp Vault`, `AWS IAM` | Securely manage secrets       | 👉 Authenticates Pods to external systems        |

---

## 🧾 Create ServiceAccount
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-sa                # 🏷️ Name of the ServiceAccount
  namespace: default         # 📦 Namespace (change if needed)
```

## 🔗 Use in Pod / Deployment
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod                 # 🏷️ Pod name
spec:
  serviceAccountName: my-sa      # 🔗 Attach ServiceAccount to Pod
  containers:
    - name: my-container
      image: nginx               # 🌐 Sample container image
```

## 🔐 Give Permissions
### 🧾 Role (Defines what actions are allowed)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: read-pods
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list"]
```

## 🔗 RoleBinding (Attach Role to ServiceAccount)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: bind-read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: Role
  name: read-pods
  apiGroup: rbac.authorization.k8s.io
```
---

## ❓ Interview Questions
| ❓ **Question**                                      | ✅ **Answer**                              | 🧠 **Explanation**                                                          | 💡 **Interview Tip**                             |
| --------------------------------------------------- | ----------------------------------------- | --------------------------------------------------------------------------- | ------------------------------------------------ |
| What happens if you don’t specify a ServiceAccount❓ | 👉 Default ServiceAccount is used         | Every namespace has a default ServiceAccount automatically attached to Pods | Mention namespace-level default behavior         |
| How do you assign a ServiceAccount❓                 | 👉 Use `serviceAccountName` in Pod spec   | Example: `serviceAccountName: my-service-account`                           | Highlight this is defined in Pod/Deployment YAML |
| Can ServiceAccount access Kubernetes API❓           | 👉 Yes, using a token                     | Kubernetes injects a token inside the Pod for authentication                | Mention token-based authentication               |
| What happens internally❓                            | 👉 Token is injected into Pod             | Token is mounted at `/var/run/secrets/...` and used for API calls           | Shows understanding of internal mechanism        |
| What is the purpose of RoleBinding❓                 | 👉 Defines permissions for ServiceAccount | Links ServiceAccount to Role/ClusterRole                                    | Key for RBAC (authorization)                     |

---

## ⚖️ User vs ServiceAccount
| 🧩 **Aspect**             | 👤 **User**                                            | 🤖 **ServiceAccount**                           | 🧠 **Explanation**                                             |
| ------------------------- | ------------------------------------------------------- | ----------------------------------------------- | -------------------------------------------------------------- |
| 👥 **Who**                | 👨‍💻 Humans (Developers, Admins, DevOps Engineers)       | 🤖 Pods, Applications, Controllers              | Users = External Identity, ServiceAccounts = Internal Identity |
| 🌍 **Scope**              | 🌐 Outside Kubernetes Cluster                          | 📦 Kubernetes Cluster                       | Users connect from outside; Pods run inside                    |
| 🔑 **Authentication**     | 🪪 kubeconfig, Certificates, OIDC, IAM                 | 🔑 ServiceAccount Token (auto-mounted in Pod)      | Different authentication mechanisms                            |
| 📦 **Used By**            | 🛠️ kubectl, CI/CD Pipelines, Admin Tools               | ⚙️ Pods, Deployments, Jobs, Controllers            | Depends on who is calling the API                              |
| 🔐 **Purpose**            | 🧭 Manage and operate the cluster                      | 🔗 Allow Pods to access Kubernetes API             | Separate identities for humans and workloads                   |
| ⚙️ **Lifecycle**          | 🌍 Managed outside Kubernetes                          | 📦 Managed inside Kubernetes                       | ServiceAccount is a Kubernetes resource                        |
| 🏷️ **Kubernetes Object** | ❌ No                                                  | ✅ Yes                                           |                                                                |
| 📄 **Created With**       | IAM, OIDC, Certificates                                | `kind: ServiceAccount`                          | Different creation methods                                     |
| 🛡️ **RBAC Binding**      | User ↔ RoleBinding/ClusterRoleBinding                  | ServiceAccount ↔ RoleBinding/ClusterRoleBinding   | Both use RBAC for authorization                                |

---

## ☁️ AWS EKS (IRSA)
  * 👉 To access AWS services:
     * ❌ Don’t use `IAM user credentials`
     * ✅ Use IRSA (`IAM Role for ServiceAccount`)
     * Secure & No secrets needed
  
### 🧾 Common Mistakes
   * ❌ Giving `admin access` to default ServiceAccount
   * ❌ Not using `RBAC`
   * ❌ `Hardcoding credentials` in Pods

### 💡 Best Practices
  * ✅ Create `custom` ServiceAccounts
  * ✅ Use minimal permissions (`least privilege`)
  * ✅ Avoid using `default ServiceAccount`
  * ✅ Rotate `tokens` if needed

### 🧠 Quick Revision
| 🧩 Component      | 💡 Meaning                           |
| ----------------- | ------------------------------------ |
| 👤 ServiceAccount | 🆔 Pod identity                      |
| 🔑 Token          | 🔐 Authentication (who you are)      |
| 🛡️ RBAC           | 🎯 Authorization (what you can do)   |
| 🔗 RoleBinding    | 🔑 Links role to `user/ServiceAccount` |

## 🎯 One-Line Answer
  👉 ServiceAccount is an identity used by Pods to securely access the `Kubernetes API` with permissions controlled by `RBAC`.


## BASIC COMMANDS
```hcl
 kubectl get serviceaccounts / kubectl get sa         # 📋 List ServiceAccounts
 kubectl get sa -n dev                                # List ServiceAccounts in a specific namespace
 kubectl create serviceaccount my-sa                  # ➕ Create ServiceAccount
 kubectl describe serviceaccount my-sa -n dev          # 🔍 Describe ServiceAccount
 kubectl delete sa my-sa -n default                    # ❌ Delete ServiceAccount
 kubectl get sa -o wide                               # List ServiceAccounts with wide output
 kubectl exec <pod> -- cat /var/run/secrets/kubernetes.io/serviceaccount/token        # 🔑 View token inside Pod
 kubectl exec <pod> -- ls /var/run/secrets/kubernetes.io/serviceaccount               # 📂 Verify token mount
```

## 🧾 Summary
  - Every namespace has a `default ServiceAccount`
  - Pods use `ServiceAccounts` for identity
  - Tokens are automatically `mounted`
  - RBAC controls `permissions`
  - If not specified ServiceAccount, `default` is used

---

## 🚀 Kubernetes ServiceAccount – Rapid Fire Interview Questions & Answers
| 🔢     | ❓ Question                                                | ✅ Answer                                                                             |
| ------ | --------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 1️⃣    | What is a ServiceAccount?                                 | 🤖 An identity used by Pods to communicate with the Kubernetes API Server.           |
| 2️⃣    | Why do we need a ServiceAccount?                          | 🔐 To provide authentication and authorization for applications running inside Pods. |
| 3️⃣    | Who uses a ServiceAccount?                                | 📦 Pods, Deployments, StatefulSets, DaemonSets, Jobs, and CronJobs.                  |
| 4️⃣    | Is a ServiceAccount a user?                               | ❌ No, it is a non-human identity for workloads.                                      |
| 5️⃣    | Default ServiceAccount name?                              | 🎯 `default`                                                                         |
| 6️⃣    | Does every namespace have a default ServiceAccount?       | ✅ Yes                                                                                |
| 7️⃣    | What happens if no ServiceAccount is specified?           | 🔄 Pod uses the `default` ServiceAccount.                                            |
| 1️⃣3️⃣ | Is ServiceAccount namespace-scoped?                       | ✅ Yes                                                                                |
| 1️⃣4️⃣ | Can two namespaces have the same ServiceAccount name?     | ✅ Yes                                                                                |
| 1️⃣5️⃣ | Can Pods share a ServiceAccount?                          | ✅ Yes                                                                                |
| 1️⃣6️⃣ | Does every Pod need its own ServiceAccount?               | ❌ No, multiple Pods can use the same ServiceAccount.                                 |
| 1️⃣7️⃣ | Where is ServiceAccount specified in Pod YAML?            | `serviceAccountName:`                                                                |
| 1️⃣8️⃣ | What is mounted inside Pods?                              | 🔑 ServiceAccount token                                                              |
| 1️⃣9️⃣ | Path of ServiceAccount token?                             | `/var/run/secrets/kubernetes.io/serviceaccount/`                                     |
| 2️⃣0️⃣ | What does the token contain?                              | 🎫 Identity used to authenticate to API Server.                                      |
| 2️⃣1️⃣ | Can ServiceAccount access API Server automatically?       | ✅ Yes, using the mounted token.                                                      |
| 2️⃣2️⃣ | Does ServiceAccount provide permissions?                  | ❌ No, permissions come from RBAC.                                                    |
| 2️⃣3️⃣ | What provides permissions to ServiceAccounts?             | 🛡️ Role & RoleBinding or ClusterRole & ClusterRoleBinding.                          |
| 2️⃣4️⃣ | What is RBAC?                                             | 🔐 Role-Based Access Control.                                                        |
| 2️⃣5️⃣ | What is the relationship between ServiceAccount and RBAC? | 🤝 RBAC grants permissions to ServiceAccounts.                                       |
| 2️⃣6️⃣ | Can a ServiceAccount create Pods by default?              | ❌ No                                                                                 |
| 2️⃣7️⃣ | Can a ServiceAccount read Pods?                           | ❌ Not unless RBAC permissions are granted.                                           |
| 2️⃣8️⃣ | Can one Role be bound to multiple ServiceAccounts?        | ✅ Yes                                                                                |
| 2️⃣9️⃣ | Can one ServiceAccount have multiple Roles?               | ✅ Yes                                                                                |
| 3️⃣0️⃣ | What is the format of a ServiceAccount identity?          | `system:serviceaccount:<namespace>:<sa-name>`                                        |
| 3️⃣1️⃣ | What is a Role?                                       | 📜 Defines permissions within a namespace.                     |
| 3️⃣2️⃣ | What is a ClusterRole?                                | 🌍 Defines cluster-wide permissions.                           |
| 3️⃣3️⃣ | What is a RoleBinding?                                | 🔗 Connects a Role to a User, Group, or ServiceAccount.        |
| 3️⃣4️⃣ | What is a ClusterRoleBinding?                         | 🔗 Connects a ClusterRole to a User, Group, or ServiceAccount. |
| 3️⃣5️⃣ | Can a ServiceAccount use ClusterRole?                 | ✅ Yes                                                          |
| 3️⃣9️⃣ | What does `apiGroup: rbac.authorization.k8s.io` mean? | 🛡️ RBAC API group used for Roles and Bindings.                |
| 4️⃣0️⃣ | Which resource grants pod read access?                | 📜 Role with `get`, `list`, `watch` permissions.               |
| 4️⃣1️⃣ | Why are ServiceAccounts important in EKS?  | ☁️ Used with IAM Roles for Service Accounts (IRSA).     |
| 4️⃣2️⃣ | What is IRSA?                              | 🔐 IAM Roles for Service Accounts.                      |
| 4️⃣3️⃣ | Why use IRSA?                              | Secure AWS API access without storing `AWS keys in Pods`. |
| 4️⃣4️⃣ | What is required before IRSA?              | 🌐 OIDC Provider. (allows you to` grant temporary, secure access` to AWS resources from external platforms without using long-lived static credentials like `IAM access keys`.) |
| 4️⃣5️⃣ | Which AWS service uses IRSA commonly?      | 💾 EBS CSI Driver, Cluster Autoscaler, ALB Controller.  |
| 4️⃣6️⃣ | Can Pods access AWS APIs without IRSA?     | ⚠️ Possible using node IAM role, but not recommended.   |
| 4️⃣7️⃣ | ServiceAccount used by EBS CSI Driver?     | `ebs-csi-controller-sa`                                 |
| 4️⃣8️⃣ | ServiceAccount used by Cluster Autoscaler? | `cluster-autoscaler`                                    |
| 4️⃣9️⃣ | Why not use node IAM role for everything?  | 🔒 Violates least privilege principle.                  |
| 5️⃣0️⃣ | Best practice in EKS?                      | ✅ Use IRSA with dedicated ServiceAccounts.              |

