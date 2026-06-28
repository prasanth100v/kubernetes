# 🌈 Kubernetes CRD & Operator
### 📌 What is a CRD (Custom Resource Definition)?
 * CRD (`CustomResourceDefinition`) allows you to **extend the Kubernetes API**
 * by creating your own custom resource types.
 * 👉 Just like:
     - Pods
     - Services
     - Deployments
 * 🎯 CRD = `Create your own Kubernetes resource types`

## 🎯 Why CRD?
  * Kubernetes built-in default resources are not always enough.
     - Pod
     - Service
     - Deployment
  * 👉 But real-world needs require:
       - Database management 🗄️
       - Traffic routing 🌐
       - Certificates 🔐
       - GitOps applications
  * CRDs solve this by allowing `custom objects`.
 
### 📌 CRD = Extending Kubernetes API
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
```
 * Adds new resource types to Kubernetes

## 🔄 CRD vs CR
| 🧩 Term                               | 📖 Meaning                   | 🧠 How It Works                               | 💡 Example                           |
| ------------------------------------- | ---------------------------- | ---------------------------------------------- | ------------------------------------ |
| 🧩 **CRD (CustomResourceDefinition)** | 📐 Defines new resource type | 👉 Extends Kubernetes API with custom objects | Define `MyApp`, `Database`, `Backup` |
| 📦 **CR (Custom Resource)**           | 📦 Instance of that type     | 👉 Actual object created using that CRD       | `myapp-prod`, `db-instance-1`        |

## 📘 Example
👉 Argo CD:
    - CRD → Application  
    - CR → my-app (actual deployment)

### How CRDs Work
```yaml
Apply CRD 📦
   ↓
Kubernetes learns new type 🧠
   ↓
Create Custom Resource (CR) 📄
   ↓
Kubernetes manages it like native resource 🚀
```

## 🌍 Real-World CRDs

| 🧩 CRD                | 🛠 Tool         | 🎯 Purpose        | 🧠 How It Works                                          | 💡 Real-World Insight       |
| --------------------- | --------------- | ----------------- | -------------------------------------------------------- | --------------------------- |
| 📦 **Application**    | 🚀 Argo CD      | GitOps deployment | 👉 Defines desired state from Git → auto sync to cluster | 🔄 Automates deployments    |
| 🌐 **VirtualService** | 🕸 Istio        | Traffic routing   | 👉 Controls routing (paths, headers, canary releases)    | ⚖️ Advanced traffic control |
| 🔐 **Certificate**    | 🔑 Cert-Manager | TLS management    | 👉 Issues & renews certificates automatically            | 🔒 HTTPS automation         |
| 🔑 **SealedSecret**   | 📦 Bitnami      | Encrypted secrets | 👉 Encrypts secrets → safe to store in Git               | 🛡 Secure GitOps workflows  |

### ⚙️ CRD Details

  - apiVersion: `apiextensions.k8s.io/v1`
  - Stored in etcd Just like: `Pods`, `Services`
  - Managed like normal Kubernetes objects

### 🔍 Useful Commands

```bash
kubectl get crds                   #📋 List CRDs
kubectl get <resource-name>        #📄 List Custom Resources
```

### ⚠️ What Happens If You Delete a CRD?
 * ❗ Important behavior:
   - ❌ All custom resources are deleted
   - ❌ Kubernetes stops recognizing that resource 

# 🤖 What is an Operator?
  * An **Operator** automates application management using:
    - CRDs
    - Controllers
  * Operator = CRD + Controller (`automation engine`)

## 🤖 Analogy
  * Think of Operator as:
     * 🧑‍💻 Kubernetes = OS
     * 🤖 Operator = Smart automation robot
     * Handles complex tasks automatically

### 🔑 Key Components
| 🧩 **Component**                      | 🎯 **Role**            | 🧠 **How It Works**                                                 | 💡 **Real-World Example**            |
| ------------------------------------- | ---------------------- | -------------------------------------------------------------------- | ------------------------------------ |
| 🧩 **CRD (CustomResourceDefinition)** | Defines resource       | 👉 Extends Kubernetes API with new resource type                    | `MyApp`, `Database`, `Backup`        |
| 📦 **CR (Custom Resource)**           | Actual object          | 👉 Instance created from CRD                                        | `myapp-prod`, `db-prod`              |
| 🤖 **Controller**                     | Watches & acts         | 👉 Continuously monitors CR and takes action to match desired state | Creates Pods, Services automatically |
| 🚀 **Operator**                       | Full automation system | 👉 Combines CRDs + Controllers + domain logic                       | Automates DB setup, scaling, backup  |

### ⚙️ How Operator Works
```yaml
Create CR 📄
   ↓
Controller watches 👀
   ↓
Takes action ⚙️
   ↓
Maintains desired state 🔄
```

### 🔄 How Operator Works

| 🔢 Step | 📖 Action             | 🧠 What Happens Internally                                  | 💡 Why It Matters     |
| ------- | --------------------- | ------------------------------------------------------------- | --------------------- |
| 1️⃣     | 📦 User creates CR    | 👉 User Defines desired state using Custom Resource (YAML) | 🧾 Declarative config    |
| 2️⃣     | 👀 Controller watches | 👉 Controller continuously watches for CR changes (watch API)  | ⚡ Automation trigger  |
| 3️⃣     | 🤖 Operator acts      | 👉 Operator executes logic (create Pods, DB, backups, etc.)  | 🛠 Handles complexity    |
| 4️⃣     | 🔄 Maintains state    | 👉 Reconciliation loop ensures actual = desired state        | ♻️ Self-healing         |


### 🛠 Popular Operators

| 🧩 Operator                    | 🎯 Purpose        | 🧠 What It Automates                                                 | 💡 Real-World Use Case         |
| ------------------------------ | ----------------- | --------------------------------------------------------------------- | ----------------------------------- |
| 📦 **Argo CD Operator**       | GitOps automation | 👉  Manages deployment lifecycle from Git (sync, rollback, self-heal) | 🚀 Continuous delivery using Argo CD |
| 📊 **Prometheus Operator**    | Monitoring setup  | 👉 Deploys Prometheus, Alertmanager, ServiceMonitors                  | 📈 Observability stack         |
| 🔍 **Elasticsearch Operator** | Logging stack     | 👉 Manages Elasticsearch clusters (scaling, recovery)                 | 📦 Centralized logging (ELK)   |
| 🗄 **Database Operators**     | DB lifecycle      |  👉 Handles DB Provisioning, backups, scaling, failover                | 🐘 MySQL/PostgreSQL automation |

### 💡 Benefits
  - ✅ Automates complex tasks  
  - ✅ Extends Kubernetes capabilities  
  - ✅ Reduces manual work  
  - ✅ Improves reliability  

### ⚠️ Limitations
  - ❌ Requires learning CRDs & controllers  
  - ❌ More complexity than basic YAML  

## 🔄 Full Flow (CRD + Operator)
```yaml
CRD 📦 → Define type
   ↓
CR 📄 → Create object
   ↓
Controller 👀 → Watch changes
   ↓
Operator 🤖 → Take action
   ↓
System managed automatically 🚀
```
---

## 🧠 Final Summary
  * CRD = Extend Kubernetes API
  * CR = Instance of CRD  ]
  * Operator = Automates `app lifecycle` using `CRDs` + `controllers`
  * Used in real-world tools like `Argo CD`, `Istio  `

 * 🧩 Ultimate One-Line
    * CRDs extend Kubernetes, and Operators automate complex application management using those extensions.

---

## 🚀 Kubernetes CRD (Custom Resource Definition) – Rapid Fire Interview Questions & Answers
| 🔢  | ❓ Question                               | ✅ Answer                                                                                                 |
| --- | ------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| 1️⃣ | What is a CRD?                              | 🚀 **Custom Resource Definition (CRD)** extends the Kubernetes API by creating new `custom resource types`. |
| 2️⃣ | Why do we use CRDs?                         | 🛠️ To `add custom resources` beyond built-in Kubernetes objects.                                           |
| 3️⃣ | What does CRD stand for?                    | 📖 Custom Resource Definition                                                                             |
| 4️⃣ | Is a CRD a built-in Kubernetes resource?    | ❌ No                                                                                                      |
| 5️⃣ | Does CRD extend the Kubernetes API?         | ✅ Yes                                                                                                     |
| 6️⃣ | What is a Custom Resource (CR)?             | 📦 An instance created from a CRD.                                                                        |
| 7️⃣ | CRD creates what?                           | 📄 A new API resource.                                                                                    |
| 8️⃣ | Can Kubernetes understand a CRD by default? | ❌ Only after the CRD is installed.                                                                        |
| 9️⃣ | Which API server stores CRDs?               | 🌐 Kubernetes API Server                                                                                  |
| 🔟 | Where are CRDs stored?                      | 💾 etcd                                                                                                   |
| 1️⃣1️⃣ | Difference between CRD and CR?           | 📋 CRD defines a new resource type; 📦 CR is an object created from that type. |
| 1️⃣2️⃣ | Can multiple CRs use one CRD?            | ✅ Yes                                                                          |
| 1️⃣3️⃣ | Is CRD cluster-scoped?                   | ✅ Yes                                                                          |
| 1️⃣3️⃣ | Which command verifies CRDs?             | ⭐ `kubectl get crd`                                                     |
| 1️⃣3️⃣ | Does Argo CD use CRDs?                   | ✅ Yes (`Application`, `AppProject`, etc.).                     |
| 1️⃣3️⃣ | Does Prometheus Operator use CRDs?       | ✅ Yes (`ServiceMonitor`, `PrometheusRule`, etc.).              |
| 1️⃣3️⃣ | Does Istio use CRDs?                     | ✅ Yes (`VirtualService`, `DestinationRule`, etc.).             |
| 1️⃣3️⃣ | Does Gateway API use CRDs?               | ✅ Yes (`Gateway`, `GatewayClass`, `HTTPRoute`, etc.).          |
| 1️⃣3️⃣ | Does External Secrets Operator use CRDs? | ✅ Yes                                                          |
| 1️⃣3️⃣ | Can Helm install CRDs?                   | ✅ Yes                                                          |
| 1️⃣3️⃣ | Can Argo CD deploy CRDs?                 | ✅ Yes                                                          |
| 1️⃣3️⃣ | Biggest advantage of CRDs?               | 🚀 Extend Kubernetes without modifying Kubernetes source code.  |
