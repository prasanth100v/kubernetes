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

|  🔢#    | ❓ Question                            | 💡 Answer                                                                                        |
| ------- | -------------------------------------- | ------------------------------------------------------------------------------------------------- |
| 🔹 1  | What is a Kubernetes API Resource?     | 👉 Any object you interact with via the Kubernetes API (`Pods`, `Deployments`, `Services`, etc.). |
| 🔹 2  | Where are API resources defined?       | 👉 In the Kubernetes `API server`.                                                                |
| 🔹 3  | Command to list all API resources?     | 👉 `kubectl api-resources`                                                                       |
| 🔹 4  | Command to get API versions?           | 👉 `kubectl api-versions  `                                                                      |
| 🧠 5  | What is an API Group?                  | 👉 Logical grouping of resources (e.g., `apps`, `batch`).                                       |
| 🧠 6  | What is API Version?                   | 👉 Version of resource schema (e.g., `v1`, `apps/v1`).                                          |
| 🧠 7  | What is Kind?                          | 👉 Type of resource (e.g., `Pod`, `Deployment`).                                                |
| 🧠 8  | What is Resource vs Kind?              | 👉 Resource = `API endpoint` (pods), Kind = `object type` (Pod).                                |
| 📦 9  | Core group examples?                   | 👉 `Pods`, `Services`, `ConfigMaps`, `Secrets (v1)`                                            |
| 📦 10 | apps/v1 group?                         | 👉 `Deployments`, `StatefulSets`, `DaemonSets`                                                |
| 📦 11 | batch/v1 group?                        | 👉 `Jobs`, `CronJobs `                                                                        |
| 📦 12 | networking.k8s.io/v1?                  | 👉 `Ingress`, `NetworkPolicy `                                                                |
| 📄 13 | Main sections of a resource YAML?      | 👉 `apiVersion, kind, metadata, spec, status `                                                |
| 📄 14 | What is metadata?                      | 👉 Info like `name`, `labels`, `namespace`.                                                   |
| 📄 15 | What is spec?                          | 👉 Desired state.                                                                             |
| 📄 16 | What is status?                        | 👉 `Current state` (managed by Kubernetes).                                                   |
| 🌐 17 | Are all resources namespaced?          | 👉 ❌ No                                                                                     |
| 🌐 18 | Examples of cluster-wide resources?    | 👉 `Nodes`, `PersistentVolumes`, `Namespaces  `                                               |
| ⚙️ 19 | How to create a resource?              | 👉 `kubectl apply -f file.yaml `                                                              |
| ⚙️ 20 | How to delete a resource?              | 👉 `kubectl delete -f file.yaml  `                                                            |
| ⚙️ 21 | How to edit a resource?                | 👉 `kubectl edit `                                                                            |
| 🔍 22 | How to inspect a resource definition?  | 👉 `kubectl explain <resource> `                                                              |
| 🔍 23 | How to get detailed info?              | 👉 `kubectl get <resource> -o yaml `                                                          |
| 🧩 24 | What is CRD?                           | 👉 Custom Resource Definition — `extends Kubernetes API`.                                     |
| 🧩 25 | Why use CRDs?                          | 👉 To define custom objects (e.g., `monitoring`, `databases`).                                |
| 🧩 26 | Example CRD usage?                     | 👉 Istio resources like `VirtualService `                                                     |
| 🔄 27 | What is a controller?                  | 👉 Watches `resources` and ensures `desired state`.                                           |
| 🔄 28 | Example?                               | 👉 Deployment controller ensures `pods match replica count`.                                  |
| 🔐 29 | How does kubectl interact with API?    | 👉 Via `API server using REST`.                                                               |
| 🔐 30 | What is `kubeconfig`?                  | 👉 `Config file for cluster access`.                                                          |
| ⚖️ 31 | What are API stability levels?         | 👉 `Alpha`, `Beta`, `Stable (GA)`                                                              |
| ⚖️ 32 | Can API versions be deprecated?        | 👉 `✅ Yes   `                                                                                 |
| 🚀 33 | What is resource quota?                | 👉 Limits `resource usage in namespace`.                                                      |
| 🚀 34 | What is limit range?                   | 👉 `Default/min/max` resource constraints.                                                    |
| 🚀 35 | What is field selector?                | 👉 `Filter resources by fields`.                                                              |
| 🎯 36 | Why is apiVersion important?           | 👉 Ensures `compatibility` with `cluster version`.                                            |
| 🎯 37 | What happens if wrong apiVersion used? | 👉 `Resource creation fails`.                                                                 |
| 🎯 38 | How to find correct API version?       | 👉 `kubectl api-resources`                                                                    |
| 🎯 39 | Why CRDs are powerful?                 | 👉 `Extend Kubernetes into platform` (operators).                                             |
