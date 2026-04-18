# 👤 IAM User + RBAC in Amazon EKS 
## 🌟 What is IAM User in EKS?
  * In Amazon EKS, authentication and authorization are handled in two layers:
      * 🔑 AWS IAM → Authentication (`Who are you?`)
      * 🔐 Kubernetes RBAC → Authorization (`What can you do?`)
      * 👉 Flow: `IAM User → aws-auth ConfigMap → RBAC → Access granted`

## 🎯 AWS IAM User Access to Kubernetes (EKS)
| 🔢 Step | 📖 What You Do                     | 🧠 How It Works                                   | 💡 Real-World Insight          |
| ------- | ---------------------------------- | --------------------------------------------------- | ------------------------------ |
| 1️⃣     | 👤 Create IAM User                 | 👉 Create user with programmatic access            | 🔑 Used for CLI authentication |
| 2️⃣     | ⚙️ Configure AWS CLI               | 👉 Run `aws configure` with (access key & secret)  | 🌐 Enables AWS API access      |
| 3️⃣     | 🔗 Map IAM in `aws-auth` ConfigMap | 👉 Maps IAM user to Kubernetes identity             | 🔐 Required for EKS authentication |
| 4️⃣     | 🛡️ Apply RBAC (Role + Binding)    | 👉 Define what user can do in cluster                 | 🎯 Controls access (authorization)  |
| 5️⃣     | 🖥️ Access via `kubectl`           | 👉 User authenticates via AWS IAM → accesses cluster  | 🚀 Manage cluster resources    |



## 🟢 Step 1: Create IAM User

1. 📍Go to AWS Console → `IAM` → `Users` → `Add user`  
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
```
aws configure
```
Enter:
- AWS Access Key ID  
- AWS Secret Access Key  
- Region (e.g., us-east-1)  
- Output format (json)  


## 🟢 Step 3: Map IAM User in aws-auth ConfigMap (Bridge Between AWS & Kubernetes)
### 🔧 Why this step?
  * Kubernetes does NOT understand IAM users.
  * 👉 So You must map IAM → Kubernetes identity using `aws-auth`

### ✏️ Edit ConfigMap (Map IAM User)
```
kubectl edit configmap aws-auth -n kube-system
```

### 🧾 ✍️ Add IAM User Mapping
```
mapUsers: |
- userarn: arn:aws:iam::123456789012:user/eks-admin  
  username: dev-user  
  groups:
    - system:masters  
    - dev-team  
```

### ⚠️ Important
   - 🚨 `system:masters` = Cluster Admin (`root access`) (like `root`)  
   - dev-team = custom `RBAC group`  

### 💾 Apply changes
```bash
kubectl apply -f aws-auth.yaml -n kube-system
```

## 🟢 Step 4: Update kubeconfig
Run:
```bash
aws eks --region <region> update-kubeconfig --name <cluster-name>
```

## 🟢 Step 5: Test Access
```bash
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
### 🚫 What They CANNOT Do
   - ❌ Create pods
   - ❌ Delete deployments
   - ❌ Modify services
   - 👉 Only view access

💡 Result:
    - ✅ Can deploy apps in `dev`
    - ❌ Cannot access `other namespaces`

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

### 🔹 IAM Role vs IAM User
| 🧩 Aspect              | 👤 IAM User                         | 🤖 IAM Role                             | 🧠 Explanation                    |
| ----------------------- | ----------------------------------- | ---------------------------------------- | --------------------------------- |
| 👥 Used For            | 👨‍💻 Humans (developers, admins)      | ⚙️ Applications / services               | Users = people, Roles = workloads |
| 🔑 Credentials         | 🔐 Static (Access Key + Secret Key) | ⏳ Temporary (STS tokens)                | Roles avoid long-term  credentials  |
| 🧰 Usage               | 🖥️ CLI, Console access              | ☁️ Used by services (EC2, EKS, Lambda)   | Roles preferred for automation    |
| ☁️ Integration         | ⚠️ Manual setup                     | 🔗 Works with IRSA, EC2 instance roles   | Seamless cloud integration        |
| 🔒 Security            | ⚠️ Higher risk (key exposure)       | ✅ More secure (short-lived credentials)  | best security practices = use roles  |
| 🔄 Credential Rotation | 🔁 Manual                           | 🔄 Automatic                              | Less operational overhead         |

 IRSA (IAM Roles for Service Accounts) 👉 Used when pods need AWS access 🔹Example : `Access S3`, `Access DynamoDB`


## 🧠 Quick Revision

| 🧩 Component   | 💡 Meaning                                    |
| -------------- | --------------------------------------------- |
| ☁️ IAM         | 🔑 Authentication (who you are in AWS)        |
| 🔗 `aws-auth`  | 🔄 Maps IAM identity → Kubernetes user/role   |
| 🛡️ RBAC       | 🎯 Authorization (what you can do in cluster) |
| 📄 Role        | 📦 Defines permissions (namespace-level)      |
| 🔗 RoleBinding | 🔑 Assigns Role to user/ServiceAccount        |


## 🎯 One-Line Answer
   In EKS, IAM handles `authentication`, aws-auth maps `users to Kubernetes`, and RBAC defines `permissions` inside Kubernetes...
