# Kubernetes Error: Pending Pods
## Cause
 * A Pod remains in the Pending state because Kubernetes is `unable to schedule it onto any node`.
 * A Pod enters the Pending state when Kubernetes cannot schedule it on any node.

## Common Reasons
 * Insufficient CPU or memory on the cluster
 * No available nodes
 * Unbound PersistentVolumeClaim (PVC)
 * Node Selector or Node Affinity mismatch
 * Taints and Tolerations mismatch
 * Image pull secrets or storage provisioning issues

## ☸️ Kubernetes Pending Pods — Common Reasons & Solutions
| 🚨 **Cause**                                   | 📖 **Description**                                      | 🛠️ **Solution**                                                     | 💻 **Useful Commands**                                     |
| ---------------------------------------------- | ------------------------------------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------- |
| 📍 **Pod Scheduling Failure**                  | Scheduler cannot place the Pod on any node              | Check Pod events to identify the scheduling reason                   | `kubectl describe pod <pod-name>`                          |
| 💾 **Insufficient CPU or Memory**              | Nodes don't have enough available resources             | Verify node capacity and resource usage; add nodes or free resources | `kubectl get nodes`<br>`kubectl describe node <node-name>` |
| 📊 **Resource Requests Exceed Capacity**       | CPU/Memory requests are larger than available resources | Reduce resource requests/limits or scale the cluster                 | `kubectl edit deployment <deployment-name>`                |
| 🏷️ **Node Selector / Node Affinity Mismatch** | No node matches the Pod's scheduling rules              | Update or remove `nodeSelector` or affinity rules                    | `kubectl describe pod <pod-name>`                          |
| 💽 **PersistentVolumeClaim (PVC) Pending**     | Required storage is not available                       | Verify PVC, StorageClass, PV, and CSI Driver                         | `kubectl get pvc`<br>`kubectl describe pvc <pvc-name>`     |
| 🚧 **Taints and Tolerations Mismatch**         | Node taints prevent Pod scheduling                      | Add matching tolerations or use another node                         | `kubectl describe node <node-name>`                        |
| 🚫 **Unschedulable Node**                      | Node is cordoned or marked unschedulable                | Uncordon the node or schedule on another node                        | `kubectl get nodes`                                        |
| 📦 **Cluster Autoscaler Delay**                | New nodes have not been provisioned yet                 | Wait for autoscaling or add nodes manually                           | `kubectl get nodes`                                        |
| 🔌 **CSI Driver / Storage Issues**             | Storage provisioner cannot create a volume              | Check CSI driver status and StorageClass                             | `kubectl get storageclass`<br>`kubectl get csidrivers`     |
| ⚠️ **Resource Quota / LimitRange**             | Namespace quota or limits prevent scheduling            | Check namespace quotas and adjust limits                             | `kubectl describe resourcequota`                           |













