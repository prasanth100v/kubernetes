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




