# 👤 IAM User + RBAC in Amazon EKS 
## 🌟 What is IAM User in EKS?
  * In Amazon EKS, authentication and authorization are handled in two layers:
      * 🔑 AWS IAM → Authentication (`Who are you?`)
      * 🔐 Kubernetes RBAC → Authorization (`What can you do?`)
      * 👉 Flow: `IAM User → aws-auth ConfigMap → RBAC → Access granted`

## 🎯 AWS IAM User Access to Kubernetes (EKS)
| 🔢 **Step** | 📖 **What You Do**     | 🧠 **How It Works**                                                              | 💡 **Real-World Insight**       |
| ----------- | ---------------------- | -------------------------------------------------------------------------------- | ------------------------------- |
| 1️⃣         | 👤 Create IAM User     | Create an IAM user with EKS permissions                                          |🔑  Used for AWS authentication     |
| 2️⃣         | ⚙️ Configure AWS CLI   | Run `aws configure` with Access Key and Secret Key                               |🌐 Enables AWS API access         |
| 3️⃣         | 🔗 Map IAM User to EKS | Map IAM identity to Kubernetes identity (traditionally via `aws-auth` ConfigMap) |🔐 Required for EKS authentication |
| 4️⃣         | 🛡️ Apply RBAC         | Create Role/ClusterRole and Binding                                              | 🎯 Controls what the user can do   |
| 5️⃣         | 🖥️ Access via `kubectl`  | User authenticates through IAM and accesses EKS cluster                          | 🚀 Manage cluster resources        |


## 🟢 Step 1: Create IAM User
1. 📍 Go to AWS Console → `IAM` → `Users` → `Add user`  
2. Enter username → eks-admin-user  
3. Select access type:
    - ✅ `Programmatic access  `

4.📦 Set permissions:
   - Attach policies directly  
   - Example:
      - `AmazonEKSClusterAccess `
      - (or `AdministratorAccess` for ⚠️ full access)

5. Create user  
6. Save:
    - `Access Key ID  `
    - `Secret Access Key  `

🔐 These are used for `AWS CLI authentication` .


## 🟢 Step 2: Configure AWS CLI
Run:
```yaml
aws configure
```
Enter:
 - AWS Access Key ID  
 - AWS Secret Access Key  
 - Region (e.g., `us-east-1`)  
 - Output format (`json`)  


## 🟢 Step 3: Map IAM User in aws-auth ConfigMap (Bridge Between AWS & Kubernetes)
### 🔧 Why this step?
  * Kubernetes does NOT understand IAM users.
  * 👉 So You must map IAM → Kubernetes identity using `aws-auth`

### ✏️ Edit ConfigMap (Map IAM User)
```yaml
kubectl edit configmap aws-auth -n kube-system
```

### 🧾 ✍️ Add IAM User Mapping
### What is aws-auth ConfigMap?
  * aws-auth = Bridge between `AWS IAM` and Kubernetes `RBAC`.
  * The `aws-auth ConfigMap` maps `AWS IAM users/roles` to Kubernetes RBAC `users/groups`.
  * 📌 Without this mapping, `EKS cannot recognize IAM identities` inside Kubernetes.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system

data:
  mapRoles: |
    - rolearn: arn:aws:iam::953146141152:role/eksctl-eks-cluster2-nodegroup-work-NodeInstanceRole-v9G9QHEKU3xo
      username: system:node:{{EC2PrivateDNSName}}
      groups:
      - system:bootstrappers
      - system:nodes

  mapUsers: |                                                     # ✍️ add this mapUsers only       
    - userarn: arn:aws:iam::953146141152:user/devops-user         # ✍️ user arn id
      username: devops-user
      groups:                                                     # ✍️ Inside Kubernetes group name
      - developers
```
(OR) Cluster Admin user add :
```yaml
mapUsers: |
- userarn: arn:aws:iam::123456789012:user/eks-admin  
  username: dev-user  
  groups:
    - system:masters               # ✍️ system:masters = Kubernetes cluster-admin group.
    - dev-team  
```

### ⚠️ Important
   - 🚨 `system:masters` = Cluster Admin (`root access`) (like `root`)  
   - dev-team = custom `RBAC group`  

### 💾 Apply changes
```yaml
kubectl apply -f aws-auth.yaml -n kube-system
```

## 🟢 Step 4: Update kubeconfig
Run:
```yaml
aws eks --region <region> update-kubeconfig --name <cluster-name>
```

## 🟢 Step 5: Test Access
```yaml
kubectl get nodes
```
   ✅ If successful → IAM user can access cluster

---

# 🔐 Kubernetes RBAC - Read-Only Access (dev namespace)
## 📦 1. ROLE (Namespace-Scoped Permissions)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: dev-namespace-readonly-role               # 🏷️ Role name
  namespace: dev                                  # 📦 Applies ONLY to 'dev' namespace

rules:
  - apiGroups: [""]              # 🔹 Core API group ("" = core resources)
    resources:
      - pods
      - services
      - configmaps
    verbs:
      - get                          # 👁️ Read a single resource
      - list                         # 📋 List multiple resources
      - watch                        # 👀 Watch for changes
  - apiGroups: ["apps"]        # 🔹 Apps API group (deployments)
    resources:
      - deployments
    verbs:
      - get
      - list
      - watch
```
 * Apply: `kubectl apply -f role.yaml`

## 🔗 2. ROLE BINDING (Attach Role to Group)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-namespace-readonly-binding            # 🏷️ Binding name
  namespace: dev                                  # ⚠️ Must match Role namespace

subjects:
  - kind: Group                                    # 👥 Group that gets access
    name: dev-group                                # 👤 All users in this group
    apiGroup: rbac.authorization.k8s.io

roleRef:
  kind: Role
  name: dev-namespace-readonly-role                # 🔗 Must match Role name
  apiGroup: rbac.authorization.k8s.io
```
 * Apply: `kubectl apply -f rolebinding.yaml`

### 🚫 What They CANNOT Do
   - ❌ Create pods
   - ❌ Delete deployments
   - ❌ Modify services
   - 👉 Only view access

💡 Result:
    - ✅ Can deploy apps in `dev`
    - ❌ Cannot access `other namespaces`

### Useful Commands
```hcl
kubectl get configmap aws-auth -n kube-system -o yaml
kubectl edit configmap aws-auth -n kube-system
kubectl auth can-i get pods --as=devops-user
```

---

 * 🔸 Role         →  Defines permissions inside `ONE namespace`
 * 🔸 RoleBinding  →  Assigns that Role to `users/groups`
 * 🔸 get   →  Read single object
 * 🔸 list  →  List all objects
 * 🔸 watch →  Monitor changes in real-time
 * ⚠️ Important: This setup gives ONLY `READ access` (no `create/update/delete`)
 * 🔐 Best Practice: Always use `read-only roles` for developers unless write access is needed
 * 💡 Tip :
     * You can replace `Group` with `User` or `ServiceAccount`
     * depending on your use case

## ✅ Final Result
   - IAM user (`eks-user`) → mapped to dev-group  
   - dev-group → `bound to Role`  
   - Permissions:
      - ✅ Can `view pods`, `services`, `configmaps`, `deployments`  
      - ❌ Cannot `create`, `update`, `delete`  

## 🔄 Real-World Example
### 🎯 Scenario:
   * You want a DevOps engineer to deploy workloads in dev namespace
   * 🪜 Steps:
      * 1. Create IAM user → dev-user
        2. Map user in aws-auth
        3. Create Role (deployment permissions)
        4. Create RoleBinding  

## ⚠️ Common Mistakes
   - Giving `system:masters` to everyone  
   - Skipping `RBAC` (only using IAM)  
   - Wrong group name `mismatch  `
   - Not updating `kubeconfig  `

## ✅ Best Practices
   - Use groups instead of individual users  
   - Follow least privilege principle  
   - Avoid full admin access  
   - Separate dev, staging, prod access  

---

### 🔹 IAM Role :
 * IRSA (IAM Roles for Service Accounts) 👉 Used when pods need AWS access 🔹Example : `Access S3`, `Access DynamoDB`

## 🧠 Quick Revision
| 🧩 Component   | 💡 Meaning                                    |
| -------------- | --------------------------------------------- |
| ☁️ IAM         | 🔑 Authentication (who you are in AWS)        |
| 🔗 `aws-auth`  | 🔄 Maps IAM identity → Kubernetes user/role   |
| 🛡️ RBAC       | 🎯 Authorization (what you can do in cluster) |
| 📄 Role        | 📦 Defines permissions (namespace-level)      |
| 🔗 RoleBinding | 🔑 Assigns Role to user/ServiceAccount        |

### ☸️ How to Provide EKS Access to an IAM User
| 🔢 **Step** | 📖 **What You Do**                         | 🧠 **How It Works**                                 | 💡 **Purpose**                          |
| ----------- | ------------------------------------------ | ----------------------------------------------------- | --------------------------------------- |
| 1️⃣         | 👤 Create IAM User                         | Create an IAM user in AWS                             | Identity for EKS authentication         |
| 2️⃣         | 🔗 Map User in EKS                         | Add IAM User ARN to EKS authentication mapping        | Connects AWS IAM identity to Kubernetes |
| 3️⃣         | 👥 Map to Kubernetes Group                 | Assign the user to a Kubernetes group                 | Easier RBAC management                  |
| 4️⃣         | 🛡️ Create Role / ClusterRole              | Define permissions on resources                        | Specifies allowed actions               |
| 5️⃣         | 🔗 Create RoleBinding / ClusterRoleBinding | Bind group to role                                    | Grants permissions                      |
| 6️⃣         | 🖥️ Access Cluster                         | User authenticates with IAM and is authorized by RBAC  | Secure cluster access                   |

## 🎯 One-Line Answer
   In EKS, IAM handles `authentication`, aws-auth maps `users to Kubernetes`, and RBAC defines `permissions` inside Kubernetes...
