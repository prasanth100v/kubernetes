## 🌐 What is kubectl api-resources❓
 * `kubectl api-resources ` This command lists all available resource types in your cluster.
 * API resources are the object types available in Kubernetes
 * It lists all available resource types supported by the Kubernetes API server.
 * 👉 It shows:
   * Resource name
   * Short name
   * API group
   * Namespaced or not
   * Kind

 ## What is the importance of API resources in Kubernetes❓

  * They are the foundation of Kubernetes.
  * 👉 Everything in Kubernetes is an API object:
      * You create resources
      * Kubernetes maintains `desired state`
      * Controllers manage them

 ## Can we extend Kubernetes API❓
  * Yes, using: `CRDs`

## Difference between Resource and Kind❓
| 🧩 Term         | 💡 Meaning                                      |
| --------------- | ----------------------------------------------- |
| 📡 **Resource** | API endpoint (e.g., `pods`, `deployments`)      |
| 📦 **Kind**     | Object type in YAML (e.g., `Pod`, `Deployment`) |

## What are namespaced vs non-namespaced resources❓
 * 👉 Check:  `kubectl api-resources --namespaced=true`

| 🧩 **Type**         | 📖 **Description**       | 🧠 **How It Works**                                                            | 💡 **Examples**                               |
| ------------------- | ------------------------ | ------------------------------------------------------------------------------ | --------------------------------------------- |
| 📦 **Namespaced**   | Exist within a namespace | 👉 Created and accessed inside a specific namespace (`default`, `dev`, `prod`) | Pods, Deployments, Services, ConfigMaps       |
| 🌐 **Cluster-wide** | Exist at cluster level   | 👉 Not tied to any namespace → available across cluster                        | Nodes, PersistentVolumes (PV), StorageClasses |

## What is an API Group❓
  * API groups organize resources logically.
  * 👉 Examples:
      * `v1` → Core group
      * `apps/v1` → Deployments
      * `batch/v1` → Jobs

 ## What are Custom Resource Definitions (CRDs)❓
  * CRDs allow you to create your `own API resources` in Kubernetes.
  * 👉 Example:
      * Prometheus creates ServiceMonitor
      * ArgoCD creates Application


## 🎯 Scenario : You need to list only cluster-level resources
 ## How do you find resources that are NOT namespaced❓

  * kubectl api-resources --namespaced=false
  * 👉 Examples: `PersistentVolumes`, `StorageClasses`

## 🎯 Scenario : Too many resources in cluster
 ## Your cluster shows 200+ resources. Why❓

  * ✅ Many CRDs installed
  * Tools like : `Prometheus`, `Istio`, `ArgoCD`
  * 👉 Each tool adds custom resources
