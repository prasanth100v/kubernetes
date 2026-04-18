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
| 🎯 **Purpose**             | 📖 **Explanation**                                                 | 💡 **Real-World Value**                   |
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
| 🔑 **Secrets Management** | `HashiCorp Vault`, `AWS IAM` | Securely manage secrets           | 👉 Authenticates Pods to external systems        |

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

| 🧩 Aspect         | 👤 User                             | 🤖 ServiceAccount                     | 🧠 Explanation                                        |
| ----------------- | ----------------------------------- | --------------------------------------- | ----------------------------------------------------- |
| 👥 Who            | 👨‍💻 Humans  (developers, admins)   | 🤖 Pods / applications                  | Users = external,  ServiceAccounts = internal identity |
| 🌍 Scope          | 🌐 Outside cluster                  | 📦 Inside Kubernetes                  | Users access cluster from outside                      |
| 🔑 Authentication | 🪪 kubeconfig (certs, tokens, OIDC) | 🔑 Token (auto-mounted in Pod)        | Different auth methods                                 |
| 📦 Used By        | 🛠️ kubectl, CI/CD, admins          | ⚙️ Pods, controllers, automation tools | Depends on who is making API calls                     |
| 🔐 Purpose        | 🧭 Manage cluster                   | 🔗 Allow Pod to → API communication   | Separation of identities                               |
| ⚙️ Lifecycle      | 🌍 Managed outside K8s              | 📦 Managed inside cluster             | ServiceAccounts are Kubernetes objects                 |


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
| 🛡️ RBAC          | 🎯 Authorization (what you can do)   |
| 🔗 RoleBinding    | 🔑 Links role to user/ServiceAccount |

## 🎯 One-Line Answer
  👉 ServiceAccount is an identity used by Pods to securely access the `Kubernetes API` with permissions controlled by `RBAC`.


## BASIC COMMANDS
```hcl
 kubectl get serviceaccounts                     # 📋 List ServiceAccounts
 kubectl create serviceaccount my-sa             # ➕ Create ServiceAccount
 kubectl describe serviceaccount my-sa           # 🔍 Describe ServiceAccount
 kubectl delete sa my-sa -n default              # ❌ Delete ServiceAccount

 kubectl exec <pod> -- cat /var/run/secrets/kubernetes.io/serviceaccount/token        # 🔑 View token inside Pod
 kubectl exec <pod> -- ls /var/run/secrets/kubernetes.io/serviceaccount               # 📂 Verify token mount

```

## 🧾 Summary

  - Every namespace has a `default ServiceAccount`
  - Pods use `ServiceAccounts` for identity
  - Tokens are automatically `mounted`
  - RBAC controls `permissions`
  - If not specified ServiceAccount, `default` is used
