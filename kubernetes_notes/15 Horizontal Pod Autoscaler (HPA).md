# 🚀 1. Horizontal Pod Autoscaler (HPA)

 * 📈 Scales number of pods in a `Deployment/ReplicaSet` based on resource utilization.
 * ⚙️ What it does - Automatically adds or removes Pod replicas based on `CPU`, `memory`, or `custom metrics` (like request count).
 * 🌍 Example Use Case:
      * A web app getting more traffic increases pods to handle load.

# 🔍 Real-Time Use Cases
| 📌 Scenario                 | ⚙️ Autoscaler Used                 | 🎯 Result                   |
| --------------------------- | ---------------------------------- | --------------------------- |
| 🌐 Website traffic surges   | 📦 HPA (Horizontal Pod Autoscaler) | 📈 More pods added          |
| 🧠 ML pod needs more memory | 📊 VPA (Vertical Pod Autoscaler)   | 💾 Memory request increased |
| 🏗️ Cluster full             | 🖥️ Cluster Autoscaler              | ➕ New node added            |

## 📊 metrics-server in Kubernetes

`metrics-server` is a Kubernetes component that collects live resource metrics like `CPU` and `memory` usage of pods and nodes.

**🔑 Key Points**
 - 📊 Supports commands like `kubectl top`
 - ⚙️ Enables auto-scaling features like `HPA`
 - 🖥️ Collects resource metrics from each node's `Kubelet`
 - ❗ Not for long-term metrics (use `Prometheus + Grafana` for that)

**🛠️ Command**
```hcl
    kubectl top nodes
    kubectl top pods -A
```
   * If output shows `CPU` and `Memory usage`, Metrics Server is working. Install Metrics Server (`If Not Installed`)

## 🚀 Install Metrics Server (Required for HPA)
```hcl
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
**✅ Verify**
```hcl
    kubectl get deployment metrics-server -n kube-system
    kubectl get pods -n kube-system | grep metrics-server
```

# ⚙️ How HPA Works

1. 📊 **Metrics Collection** -- HPA uses metrics-server to get live usage.
2. 🎯 **Target Utilization** -- Example:  `targetCPUUtilizationPercentage: 70`
3. 🔄 **Autoscaling**
     - 📈 Usage > target → `Adds pods`
     - 📉 Usage < target → `Removes pods`
4. 🔁 **Controller Loop** -- Runs every `15 seconds` (default).

---

# 📈 Horizontal Pod Autoscaler (HPA)

 * 🎯 Purpose: Automatically scale the number of Pods based on `CPU/memory` usage.
 * ⚙️ What it does:
     * Works on top of a `Deployment` or `ReplicaSet`.
     * Adjusts replica count `dynamically`.
 * 🌍 Example :
     * 📈 `Scale up` when CPU > 70%
     * 📉 `Scale down` when below target

## 🔹 Sample HPA YAML
``` yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

## 💥 Requirements
  - 📦 Deployment must define CPU requests.
  - ⚙️ metrics-server must be installed.

## 🧩 HPA Workflow

1. 🎯 Define target `CPU/memory` utilization.
2. ⏱️ Kubernetes checks every `15` seconds.
3. ⚙️ Requires `metrics-server`.
4. 📈 Above target → `scale up`.
5. 📉 Below target → `scale down`.

**📊 Metrics supported**
  - CPU (default)
  - Memory (optional)
  - Custom/external metrics via Prometheus adapter

---

# ✅ Real-World Use Case
## 🛒 Web Application Scaling

 * Suppose you have an e-commerce `frontend Deployment`. During sales events, traffic increases rapidly.
 * 🎯 Result - HPA `increases pod replicas` automatically when `CPU crosses 70%.`

## 🎯 Why Requests and Limits Matter

Allocating resources using requests and limits:
  - ⚖️ Ensures fair usage
  - 📍 Helps scheduler place Pods correctly
  - 🚫 Prevents resource overuse
  - 🚀 Improves performance and stability
  - 🔄 Enables effective autoscaling

---

 * ✅ `CPU` = Processing power used to `execute application code`.
 * ✅ `Memory` = RAM used to store application data while running.
 * ✅ `CPU overuse` → Container is `throttled`. ( its CPU usage has been `restricted` by the system )
 * ✅ `Memory overuse` → Container is `OOMKilled` and `restarted`.
 * ✅ HPA commonly scales pods based on `CPU` or `Memory utilization`.

---

# 🚀 HPA + Cluster Autoscaler Together in EKS

 * 🔹 HPA (Horizontal Pod Autoscaler) and Cluster Autoscaler (CA) work together to provide complete autoscaling in Kubernetes.

## 🎯 What HPA Does
 * 🔹Scales Pods based on `CPU`, `memory`, or `custom metrics`.
 * 🔹Example:
      * 🔸CPU usage exceeds `70%`
      * 🔸HPA increases replicas from `2 → 5 pods`

## ⚙️ What Cluster Autoscaler Does
 * 🔹Scales Nodes when there is `not enough capacity to run new pods`.
 * 🔹Example:
     * 🔸HPA creates `5 pods`
     * 🔸Existing nodes can run only `3 pods`
     * 🔸2 pods remain in `Pending state`
     * 🔸Cluster Autoscaler `adds a new EC2 node`
     * 🔸Pending pods get `scheduled on the new node`.

### 🌟 This is the most common production setup.

```text
📈 User Traffic Increases
         │
         ▼
🔥 CPU Usage > 70%
         │
         ▼
📦 HPA Scales Pods
(2 → 5 Pods)
         │
         ▼
⚠️ Not Enough Node Capacity
         │
         ▼
⏳ Pods Pending
         │
         ▼
🔍 Cluster Autoscaler Detects Pending Pods
         │
         ▼
🖥️ Adds New EC2 Node
         │
         ▼
✅ Pending Pods Scheduled
```

 * 🔹 Here:
     * 🔸HPA scales Pods
     * 🔸Cluster Autoscaler scales Nodes

| 🧩 Component                       | 📈 Scales | ⚡ Trigger                     |
| ---------------------------------- | -------- | ----------------------------- |
| Horizontal Pod Autoscaler (HPA)    | Pods     | CPU, Memory, Custom Metrics   |
| Cluster Autoscaler                 | Nodes    | Pending/Unschedulable Pods    |

---

## 🎤 Interview Answer

 * 🔹 HPA scales application pods based on metrics such as CPU or memory utilization, while Cluster Autoscaler scales worker nodes when pods cannot be scheduled due to insufficient resources.
 * 🔹 HPA increases or decreases the number of pods, and Cluster Autoscaler ensures enough node capacity to run those pods...
