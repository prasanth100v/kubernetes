# 🧩 DaemonSet in Kubernetes
## 📘 🔍 What is a DaemonSet?

 * A **DaemonSet** ensures that a copy of a specific Pod runs on every node (or selected nodes) in your Kubernetes cluster.
 * It is commonly used for background services like `log collectors`, network tools, or monitoring agents like `Prometheus Node Exporter`.
 * When new nodes are added to the cluster, the DaemonSet automatically deploys the pod to them.

## ✅ ✨ Key Features of DaemonSet

 - 🧬 **One pod per node** -- Automatically runs 1 pod on each node in the cluster  
 - ♻️ **Auto-add/remove**
   - ➕ When a new node is added → Pod is automatically scheduled  
   - ➖ When a node is removed → Pod is deleted  
 - 🧹 **System-level tasks** -- Used for monitoring, logging, networking agents  
 - 🎯 **Node selector support** -- Can target specific nodes using labels, taints, and tolerations  

## 📘 🚀 Common Use Cases

 - 🪵 Log collection agents (Fluentd, Logstash)  
 - 📈 Monitoring tools (Prometheus Node Exporter)  
 - 🔌 Network plugins (Calico, CNI plugins)  
 - 🔐 Security agents (Falco)  

---

## 🔧 🛠 Useful Commands

```bash
kubectl apply -f fluentd-daemonset.yaml                      # Apply
kubectl get daemonsets                                       # List DaemonSets
kubectl get pods -o wide                                     # List Pods with node details
kubectl delete daemonset <daemonset-name> -n <namespace>     # Delete DaemonSet
kubectl get pods -l name=fluentd-logging -o wide              # View pod placement
```

---

## 🏗 🧠 Real-World Production Setup (Fluentd DaemonSet)

| 🧩 **Component**                 | 📖 **What It Does**                                                                  | 💡 **Why It’s Important (Real World)**                                                                                |
| -------------------------------- | ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| 🔐 **ServiceAccount**            | Allows Fluentd pods to authenticate with the Kubernetes API                          | 👉 Needed to securely access cluster information like pod metadata without using admin credentials                    |
| 🛡 **RBAC (Role & RoleBinding)** | Grants permissions (read access) to resources like pods, namespaces, metadata        | 👉 Follows **least privilege principle** → only required permissions are given for security                           |
| 📦 **ConfigMap**                 | Stores `fluent.conf` (Fluentd configuration file)                                    | 👉 Enables dynamic updates without rebuilding images<br>👉 Easy to manage configs across environments (dev/test/prod) |
| 💾 **Volumes**                   | Mounts host paths like 📂`/var/log` and 📂`/var/lib/docker/containers` into Fluentd pods | 👉 Required to collect container logs from nodes<br>👉 Without this, Fluentd cannot access logs                       |


---

## 📄 🧾 Example DaemonSet YAML

```yaml
apiVersion: apps/v1
kind: DaemonSet                       # Ensures one pod runs on every node

metadata:
  name: fluentd
  namespace: kube-system               # Common namespace for system services
  labels:
    app: fluentd                       # Label to identify the DaemonSet

spec:
  selector:
    matchLabels:
      app: fluentd                     # DaemonSet manages pods with this label

  template:
    metadata:
      labels:
        app: fluentd                    # Pod label (must match selector)

    spec:
      tolerations:
        - key: "node-role.kubernetes.io/master"                    # Allow scheduling on master/control-plane nodes
          operator: "Exists"
          effect: "NoSchedule"

      containers:
        - name: fluentd
          image: fluent/fluentd-kubernetes-daemonset:v1.16

          resources:
            limits:
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi

          volumeMounts:
            - name: varlog                                                # Mount host /var/log inside container
              mountPath: /var/log
            - name: dockercontainers                                       # Mount Docker container logs (read-only)
              mountPath: /var/lib/docker/containers
              readOnly: true
            - name: fluentd-config                                          # Mount ConfigMap for Fluentd configuration
              mountPath: /fluentd/etc

      terminationGracePeriodSeconds: 30                        # Allow graceful shutdown

      volumes:
        - name: varlog                                         # Host system log directory
          hostPath:
            path: /var/log
        - name: dockercontainers                                 # Docker container logs directory
          hostPath:
            path: /var/lib/docker/containers
        - name: fluentd-config                                   # ConfigMap containing fluent.conf
          configMap:
            name: fluentd-config
```

## 🚀 📊 What Happens After Deployment?

 - 📦 One Fluentd pod runs on every node  
 - 📥 Collects logs from:
    - 📂 `/var/log` (system logs)
    - 📂 `/var/lib/docker/containers` (container logs)
 - 📤 Forwards logs to Elasticsearch  

## 🔄 ⏱ terminationGracePeriodSeconds: 30
   Gives Fluentd 30 seconds to shut down gracefully and flush logs before being forcefully terminated.

## 📦 🧠 Understanding Volumes vs VolumeMounts

 - 📦 **volumes** → Define host paths (outside container)  
 - 🔗 **volumeMounts** → Define where those paths appear inside the container  

### 🏠 Analogy
  - 🏠 Node = Your house  
  - 👤 Fluentd container = Guest in a room  
  - 🔑 volumeMount = Key that allows access to the house  

## 🧪 🔍 Example Log Flow
 Node logs:  
  - 📂 `/var/log/syslog`  
  - 📂 `/var/lib/docker/containers/container-id/container.log`
  - Fluentd reads these logs and forwards them to Elasticsearch.

---

## ❗ ⚠️ Important Notes

 - ➕ You can run multiple DaemonSets (e.g., one for `logging`, one for `monitoring`)  
 - 🔄 If a DaemonSet pod fails, Kubernetes automatically restarts it  
 - 🚫 Cordoned node → Existing pod stays, no new scheduling  
 - 🔁 Drained node → Pod is terminated and rescheduled when node returns  

## 🛠 🔧 Troubleshooting DaemonSet Issues

```bash
kubectl describe node <nodename>                      # Check node taints
kubectl describe daemonset <name>                     # Check DaemonSet events
kubectl get pods -o wide                              # View scheduling details
```

---

## 📌 🎯 Summary
 * DaemonSets are essential for running cluster-wide node-level services.
 * They ensure every node runs exactly one copy of a required system pod such as `Fluentd` for `log collection`.
