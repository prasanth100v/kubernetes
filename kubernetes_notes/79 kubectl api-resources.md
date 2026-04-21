## 🌐 What is kubectl api-resources?
 * `kubectl api-resources ` This command lists all available resource types in your cluster.
 * API resources are the object types available in Kubernetes
 * It lists all available resource types supported by the Kubernetes API server.
 * 👉 It shows:
   * Resource name
   * Short name
   * API group
   * Namespaced or not
   * Kind

❓ 1. What is the importance of API resources in Kubernetes?

Answer:
They are the foundation of Kubernetes.

👉 Everything in Kubernetes is an API object:

You create resources
Kubernetes maintains desired state
Controllers manage them

❓ 14. Can we extend Kubernetes API?

Answer:
Yes, using:

CRDs

❓ 3. Difference between Resource and Kind?
| Term     | Meaning                           |
| -------- | --------------------------------- |
| Resource | API endpoint (e.g., `pods`)       |
| Kind     | Object type in YAML (e.g., `Pod`) |


❓ 4. What are namespaced vs non-namespaced resources?
 * 👉 Check:  `kubectl api-resources --namespaced=true`
| Type         | Description              | Examples          |
| ------------ | ------------------------ | ----------------- |
| Namespaced   | Exist within a namespace | Pods, Deployments |
| Cluster-wide | Exist at cluster level   | Nodes, PVs        |


❓ 5. What is an API Group?

Answer:
API groups organize resources logically.

👉 Examples:

v1 → Core group
apps/v1 → Deployments
batch/v1 → Jobs

❓ 7. What are Custom Resource Definitions (CRDs)?

Answer:
CRDs allow you to create your own API resources in Kubernetes.

👉 Example:

Prometheus creates ServiceMonitor
ArgoCD creates Application


🎯 Scenario 5: You need to list only cluster-level resources

❓ Question:
How do you find resources that are NOT namespaced?

✅ Answer:

kubectl api-resources --namespaced=false

👉 Examples:

Nodes
PersistentVolumes
Namespaces




🎯 Scenario 11: Too many resources in cluster

❓ Question:
Your cluster shows 200+ resources. Why?

✅ Answer:

Many CRDs installed
Tools like:
Prometheus
Istio
ArgoCD

👉 Each tool adds custom resources
