# 🔄 Kubernetes Autoscalers

Autoscaling in Kubernetes is the process of automatically adjusting
resources (like Pods or Nodes) based on metrics such as CPU usage,
memory, or custom metrics.

Kubernetes supports three types of autoscalers:

------------------------------------------------------------------------

## 🚀 1. Horizontal Pod Autoscaler (HPA)

Scales number of pods in a Deployment/ReplicaSet based on resource
utilization.

**What it does** - Automatically adds or removes Pod replicas based on
CPU, memory, or custom metrics (like request count).

**Example Use Case** A web app getting more traffic increases pods to
handle load.

------------------------------------------------------------------------

## 2. Vertical Pod Autoscaler (VPA)

**What it does** - Adjusts CPU/memory requests/limits for your pods.

**Example Use Case** Useful when your app doesn't need more pods, just
more power per pod.\
Your app starts consuming more memory over time. VPA will increase the
pod's memory requests.

------------------------------------------------------------------------

## 3. Cluster Autoscaler

**What it does** - Works with cloud providers like AWS, GCP, Azure to
add/remove nodes in your cluster based on pod scheduling needs.

**Example Use Case** If your app pods can't be scheduled due to resource
limits, new nodes will be added automatically.

------------------------------------------------------------------------

