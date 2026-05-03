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


## ⚡ Kubernetes API Resources — Rapid Fire Q&A

| 🔢 Q#  | ❓ Question                            | 💡 Answer                                                                                        |
| ------ | --------------------------------------- | ------------------------------------------------------------------------------------------------- |
| 🔹 Q1  | What is a Kubernetes API Resource?     | 👉 Any object you interact with via the Kubernetes API (`Pods`, `Deployments`, `Services`, etc.). |
| 🔹 Q2  | Where are API resources defined?       | 👉 In the Kubernetes `API server`.                                                                |
| 🔹 Q3  | Command to list all API resources?     | 👉 `kubectl api-resources`                                                                       |
| 🔹 Q4  | Command to get API versions?           | 👉 `kubectl api-versions  `                                                                      |
| 🧠 Q5  | What is an API Group?                  | 👉 Logical grouping of resources (e.g., `apps`, `batch`).                                       |
| 🧠 Q6  | What is API Version?                   | 👉 Version of resource schema (e.g., `v1`, `apps/v1`).                                          |
| 🧠 Q7  | What is Kind?                          | 👉 Type of resource (e.g., `Pod`, `Deployment`).                                                |
| 🧠 Q8  | What is Resource vs Kind?              | 👉 Resource = `API endpoint` (pods), Kind = `object type` (Pod).                                |
| 📦 Q9  | Core group examples?                   | 👉 `Pods`, `Services`, `ConfigMaps`, `Secrets (v1)`                                            |
| 📦 Q10 | apps/v1 group?                         | 👉 `Deployments`, `StatefulSets`, `DaemonSets`                                                |
| 📦 Q11 | batch/v1 group?                        | 👉 `Jobs`, `CronJobs `                                                                        |
| 📦 Q12 | networking.k8s.io/v1?                  | 👉 `Ingress`, `NetworkPolicy `                                                                |
| 📄 Q13 | Main sections of a resource YAML?      | 👉 `apiVersion, kind, metadata, spec, status `                                                |
| 📄 Q14 | What is metadata?                      | 👉 Info like `name`, `labels`, `namespace`.                                                   |
| 📄 Q15 | What is spec?                          | 👉 Desired state.                                                                             |
| 📄 Q16 | What is status?                        | 👉 `Current state` (managed by Kubernetes).                                                   |
| 🌐 Q17 | Are all resources namespaced?          | 👉 ❌ No                                                                                     |
| 🌐 Q18 | Examples of cluster-wide resources?    | 👉 `Nodes`, `PersistentVolumes`, `Namespaces  `                                               |
| ⚙️ Q19 | How to create a resource?              | 👉 `kubectl apply -f file.yaml `                                                              |
| ⚙️ Q20 | How to delete a resource?              | 👉 `kubectl delete -f file.yaml  `                                                            |
| ⚙️ Q21 | How to edit a resource?                | 👉 `kubectl edit `                                                                            |
| 🔍 Q22 | How to inspect a resource definition?  | 👉 `kubectl explain <resource> `                                                              |
| 🔍 Q23 | How to get detailed info?              | 👉 `kubectl get <resource> -o yaml `                                                          |
| 🧩 Q24 | What is CRD?                           | 👉 Custom Resource Definition — `extends Kubernetes API`.                                     |
| 🧩 Q25 | Why use CRDs?                          | 👉 To define custom objects (e.g., `monitoring`, `databases`).                                |
| 🧩 Q26 | Example CRD usage?                     | 👉 Istio resources like `VirtualService `                                                     |
| 🔄 Q27 | What is a controller?                  | 👉 Watches `resources` and ensures `desired state`.                                           |
| 🔄 Q28 | Example?                               | 👉 Deployment controller ensures `pods match replica count`.                                  |
| 🔐 Q29 | How does kubectl interact with API?    | 👉 Via `API server using REST`.                                                               |
| 🔐 Q30 | What is `kubeconfig`?                  | 👉 `Config file for cluster access`.                                                          |
| ⚖️ Q31 | What are API stability levels?         | 👉 `Alpha`, `Beta`, `Stable (GA)`                                                              |
| ⚖️ Q32 | Can API versions be deprecated?        | 👉 `✅ Yes   `                                                                                 |
| 🚀 Q33 | What is resource quota?                | 👉 Limits `resource usage in namespace`.                                                      |
| 🚀 Q34 | What is limit range?                   | 👉 `Default/min/max` resource constraints.                                                    |
| 🚀 Q35 | What is field selector?                | 👉 `Filter resources by fields`.                                                              |
| 🎯 Q36 | Why is apiVersion important?           | 👉 Ensures `compatibility` with `cluster version`.                                            |
| 🎯 Q37 | What happens if wrong apiVersion used? | 👉 `Resource creation fails`.                                                                 |
| 🎯 Q38 | How to find correct API version?       | 👉 `kubectl api-resources`                                                                    |
| 🎯 Q39 | Why CRDs are powerful?                 | 👉 `Extend Kubernetes into platform` (operators).                                             |
