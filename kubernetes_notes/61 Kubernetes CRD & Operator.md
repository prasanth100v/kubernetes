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
| 1️⃣1️⃣ | Difference between CRD and CR?         | 📋 CRD defines a new resource type; 📦 CR is an object created from that type. |
| 1️⃣2️⃣ | Can multiple CRs use one CRD?          | ✅ Yes                                                                          |
| 1️⃣3️⃣ | Is CRD cluster-scoped?                 | ✅ Yes                                                                          |
| 1️⃣4️⃣ | Is a Custom Resource namespace-scoped? | 📍 Usually yes, but it can also be cluster-scoped depending on the CRD.        |
| 1️⃣5️⃣ | YAML kind for CRD?                     | `CustomResourceDefinition`                                                     |
| 1️⃣6️⃣ | API group for CRDs?                    | `apiextensions.k8s.io`                                                         |
| 1️⃣7️⃣ | Current stable API version?            | `apiextensions.k8s.io/v1`                                                      |
| 1️⃣8️⃣ | Can CRDs have multiple versions?       | ✅ Yes                                                                        |
| 1️⃣9️⃣ | Example version names?                 | `v1alpha1`, `v1beta1`, `v1`                                                    |
| 2️⃣0️⃣ | Can CRDs define schemas?               | ✅ Yes (OpenAPI v3 Schema).                                                    |
| 2️⃣1️⃣ | Can CRDs validate fields?           | ✅ Yes                                                                            |
| 2️⃣2️⃣ | Can CRDs define required fields?    | ✅ Yes                                                                            |
| 2️⃣3️⃣ | Can CRDs have default values?       | ✅ Yes                                                                            |
| 2️⃣4️⃣ | Can CRDs support multiple versions? | ✅ Yes                                                                            |
| 2️⃣5️⃣ | Can CRDs be namespaced?             | ✅ The CRD defines whether its custom resources are namespaced or cluster-scoped. |
| 2️⃣6️⃣ | Can CRDs use labels?                | ✅ Yes                                                                            |
| 2️⃣7️⃣ | Can CRDs use annotations?           | ✅ Yes                                                                            |
| 2️⃣8️⃣ | Can CRDs have a status field?       | ✅ Yes                                                                            |
| 2️⃣9️⃣ | Can CRDs have a spec field?         | ✅ Yes                                                                            |
| 3️⃣0️⃣ | Can CRDs support `kubectl get`?     | ✅ Yes                                                                            |
| 3️⃣1️⃣ | What is an Operator?                   | 🤖 A controller that watches Custom Resources and performs automated actions.    |
| 3️⃣2️⃣ | Do CRDs work without an Operator?      | ✅ Yes, but only as stored custom objects unless another controller acts on them. |
| 3️⃣3️⃣ | Relationship between CRD and Operator? | 🤝 Operator watches CRs created from a CRD.                                      |
| 3️⃣4️⃣ | Does CRD contain business logic?       | ❌ No                                                                            |
| 3️⃣5️⃣ | Where is business logic implemented?   | 🤖 In a Controller or Operator.                                                  |
| 3️⃣6️⃣ | Which component watches CRs?           | 👀 Controller/Operator                                                           |
| 3️⃣7️⃣ | Can Operators update CR status?        | ✅ Yes                                                                           |
| 3️⃣8️⃣ | Example Operator?                      | 🚀 Argo CD Operator, Prometheus Operator, Cert-Manager.                          |
| 3️⃣9️⃣ | Why use Operators?                     | ⚙️ Automate application lifecycle management.                                    |
| 4️⃣0️⃣ | Can one Operator manage multiple CRDs? | ✅ Yes                                                                           |
| 4️⃣1️⃣ | List all CRDs?                  | `kubectl get crd`                                                            |
| 4️⃣2️⃣ | Describe a CRD?                 | `kubectl describe crd <crd-name>`                                            |
| 4️⃣3️⃣ | Delete a CRD?                   | `kubectl delete crd <crd-name>`                                              |
| 4️⃣4️⃣ | View Custom Resources?          | `kubectl get <resource-name>`                                                |
| 4️⃣5️⃣ | Explain a CRD?                  | `kubectl explain <resource-name>`                                            |
| 4️⃣6️⃣ | Apply a CRD?                    | `kubectl apply -f crd.yaml`                                                  |
| 4️⃣7️⃣ | Create a Custom Resource?       | `kubectl apply -f custom-resource.yaml`                                      |
| 4️⃣8️⃣ | What happens if CRD is deleted? | ⚠️ The CRD is removed, and its associated Custom Resources are also removed. |
| 4️⃣9️⃣ | Can you edit a CRD?             | ✅ Yes (`kubectl edit crd <name>`).                                          |
| 5️⃣0️⃣ | Which command verifies CRDs?    | ⭐ `kubectl get crd`                                                     |
| 5️⃣1️⃣ | Does Argo CD use CRDs?                   | ✅ Yes (`Application`, `AppProject`, etc.).                     |
| 5️⃣2️⃣ | Does Cert-Manager use CRDs?              | ✅ Yes                                                          |
| 5️⃣3️⃣ | Does Prometheus Operator use CRDs?       | ✅ Yes (`ServiceMonitor`, `PrometheusRule`, etc.).              |
| 5️⃣4️⃣ | Does Istio use CRDs?                     | ✅ Yes (`VirtualService`, `DestinationRule`, etc.).             |
| 5️⃣5️⃣ | Does Gateway API use CRDs?               | ✅ Yes (`Gateway`, `GatewayClass`, `HTTPRoute`, etc.).          |
| 5️⃣6️⃣ | Does Crossplane use CRDs?                | ✅ Yes                                                          |
| 5️⃣7️⃣ | Does External Secrets Operator use CRDs? | ✅ Yes                                                          |
| 5️⃣8️⃣ | Can Helm install CRDs?                   | ✅ Yes                                                          |
| 5️⃣9️⃣ | Can Argo CD deploy CRDs?                 | ✅ Yes                                                          |
| 6️⃣0️⃣ | Biggest advantage of CRDs?               | 🚀 Extend Kubernetes without modifying Kubernetes source code.  |
