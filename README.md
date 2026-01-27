## 📘 Kubernetes Glossary
| Term                            | What it Means                    | Simple Explanation / Example                           |
| ------------------------------- | -------------------------------- | ------------------------------------------------------ |
| **Kubernetes**                  | Container orchestration platform | Manages containers automatically (deploy, scale, heal) |
| **Cluster**                     | Group of machines                | One Kubernetes setup = multiple servers                |
| **Node**                        | Worker machine                   | A server where pods run                                |
| **Control Plane**               | Brain of cluster                 | Decides *what runs where*                              |
| **Worker Node**                 | Execution machine                | Runs application containers                            |
| **Pod**                         | Smallest deployable unit         | One or more containers running together                |
| **Container**                   | App runtime                      | Runs your app (Docker container)                       |
| **Image**                       | App blueprint                    | Used to create containers                              |
| **Namespace**                   | Logical isolation                | Separate environments like `dev`, `prod`               |
| **Deployment**                  | App manager                      | Ensures correct number of pods                         |
| **ReplicaSet**                  | Pod counter                      | Keeps desired pod count                                |
| **Service**                     | Network access                   | Exposes pods internally or externally                  |
| **ClusterIP**                   | Internal service                 | Access only inside cluster                             |
| **NodePort**                    | External service                 | Exposes app using node IP + port                       |
| **LoadBalancer**                | Cloud service                    | Exposes app with public IP                             |
| **Ingress**                     | HTTP routing                     | Routes traffic using domain/path                       |
| **Ingress Controller**          | Traffic handler                  | Implements ingress rules                               |
| **ConfigMap**                   | Non-secret config                | Stores env variables, configs                          |
| **Secret**                      | Sensitive data                   | Stores passwords, tokens                               |
| **Volume**                      | Storage                          | Data shared with containers                            |
| **PersistentVolume (PV)**       | Actual storage                   | Disk provided by admin/cloud                           |
| **PersistentVolumeClaim (PVC)** | Storage request                  | Pod asks for storage                                   |
| **StatefulSet**                 | Stateful apps                    | For DBs like MySQL                                     |
| **DaemonSet**                   | One pod per node                 | Log agents, monitoring                                 |
| **Job**                         | One-time task                    | Runs once (backup, batch job)                          |
| **CronJob**                     | Scheduled job                    | Runs jobs at fixed time                                |
| **Label**                       | Key-value tag                    | Used to select objects                                 |
| **Selector**                    | Matching rule                    | Connects service to pods                               |
| **Taint**                       | Node restriction                 | Prevents pod scheduling                                |
| **Toleration**                  | Taint override                   | Allows pod on tainted node                             |
| **Affinity**                    | Pod placement                    | Controls where pods run                                |
| **HPA**                         | Auto scaling                     | Scales pods based on CPU                               |
| **Rolling Update**              | Zero downtime deploy             | Updates pods gradually                                 |
| **kubectl**                     | CLI tool                         | Command line for Kubernetes                            |
| **etcd**                        | Cluster database                 | Stores cluster state                                   |
| **Scheduler**                   | Pod placer                       | Chooses node for pod                                   |
| **Controller Manager**          | Maintainer                       | Keeps desired state                                    |
| **API Server**                  | Entry point                      | All requests go through this                           |
| **Helm**                        | Package manager                  | Installs apps using charts                             |
| **Chart**                       | Helm package                     | Predefined Kubernetes resources                        |
| **CRD**                         | Custom resource                  | Add your own resource type                             |
| **Operator**                    | Smart controller                 | Manages complex apps                                   |
| **Probe (Liveness)**            | Health check                     | Restarts unhealthy pod                                 |
| **Probe (Readiness)**           | Traffic check                    | Controls traffic flow                                  |
| **Probe (Startup)**             | Boot check                       | For slow-starting apps                                 |

## 📗 Advanced Kubernetes Glossary
| Term                            | What it Is            | Simple Explanation / Real Use          |
| ------------------------------- | --------------------- | -------------------------------------- |
| **Kubernetes API Group**        | Resource category     | Example: `apps/v1`, `batch/v1`         |
| **API Version**                 | Resource version      | Example: `apps/v1` for Deployment      |
| **Manifest**                    | YAML file             | Describes Kubernetes objects           |
| **Desired State**               | Target configuration  | What you *want* Kubernetes to maintain |
| **Current State**               | Actual condition      | What is currently running              |
| **Reconciliation Loop**         | Continuous check      | K8s keeps fixing differences           |
| **Admission Controller**        | Request validator     | Allows or denies API requests          |
| **Mutating Webhook**            | Modifies requests     | Adds labels automatically              |
| **Validating Webhook**          | Validates rules       | Blocks wrong configs                   |
| **CNI**                         | Networking plugin     | Calico, Flannel                        |
| **CSI**                         | Storage plugin        | Connects storage systems               |
| **kube-proxy**                  | Network manager       | Enables service networking             |
| **Endpoint**                    | Pod IP list           | Actual backend pods                    |
| **EndpointSlice**               | Scalable endpoints    | Improved endpoint handling             |
| **Service Mesh**                | Traffic control       | Istio, Linkerd                         |
| **Sidecar**                     | Helper container      | Logging, proxy container               |
| **Init Container**              | Pre-run container     | Runs before app starts                 |
| **Eviction**                    | Pod removal           | Due to memory/disk pressure            |
| **OOMKilled**                   | Memory crash          | Pod killed due to RAM limit            |
| **Resource Request**            | Minimum resource      | Guaranteed CPU/memory                  |
| **Resource Limit**              | Max resource          | Prevents overuse                       |
| **QoS Class**                   | Priority level        | Guaranteed / Burstable / BestEffort    |
| **Node Pressure**               | Resource shortage     | Memory, disk, PID pressure             |
| **Pod Disruption Budget (PDB)** | Availability rule     | Limits pod down during updates         |
| **Leader Election**             | Master selection      | Used by controllers                    |
| **Finalizer**                   | Cleanup hook          | Runs before delete                     |
| **Garbage Collection**          | Auto cleanup          | Deletes unused resources               |
| **Custom Controller**           | Custom logic          | Watches CRDs                           |
| **Operator Pattern**            | App automation        | DB lifecycle management                |
| **Blue-Green Deployment**       | Zero-risk release     | Switch traffic instantly               |
| **Canary Deployment**           | Gradual rollout       | Test with few users                    |
| **Node Drain**                  | Safe node maintenance | Moves pods before shutdown             |
| **Cordoning**                   | Block scheduling      | Prevent new pods on node               |
| **Static Pod**                  | Node-level pod        | Managed by kubelet only                |
| **Mirror Pod**                  | API reflection        | Static pod visible in API              |
| **RBAC**                        | Access control        | Who can do what                        |
| **Role**                        | Namespace permission  | Limited access                         |
| **ClusterRole**                 | Cluster-wide access   | Admin-level permissions                |
| **ServiceAccount**              | Pod identity          | Used for API access                    |
| **Token**                       | Authentication key    | Used by pods                           |
| **Audit Log**                   | Activity log          | Tracks API actions                     |
| **NodeSelector**                | Simple placement      | Run pod on specific node               |
| **Pod Anti-Affinity**           | Separation rule       | Spread pods across nodes               |



