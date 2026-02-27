# DaemonSet in Kubernetes

## What is a DaemonSet?

A **DaemonSet** ensures that a copy of a specific Pod runs on every node
(or selected nodes) in your Kubernetes cluster.

It is commonly used for background services like log collectors, network
tools, or monitoring agents like Prometheus Node Exporter.

When new nodes are added to the cluster, the DaemonSet automatically
deploys the pod to them.

------------------------------------------------------------------------

## ✅ Key Features of DaemonSet

-   🧬 **One pod per node** -- Automatically runs 1 pod on each node in
    the cluster\
-   ♻️ **Auto-add/remove**
    -   When a new node is added → Pod is automatically scheduled\
    -   When a node is removed → Pod is deleted\
-   🧹 **System-level tasks** -- Used for monitoring, logging,
    networking agents\
-   🎯 **Node selector support** -- Can target specific nodes using
    labels, taints, and tolerations

------------------------------------------------------------------------

## 📘 Common Use Cases

-   🪵 Log collection agents (Fluentd, Logstash)\
-   📈 Monitoring tools (Prometheus Node Exporter)\
-   🔌 Network plugins (Calico, CNI plugins)\
-   🔐 Security agents (Falco)

------------------------------------------------------------------------

## 🔧 Useful Commands

``` bash
# Apply
kubectl apply -f fluentd-daemonset.yaml

# List DaemonSets
kubectl get daemonsets

# List Pods with node details
kubectl get pods -o wide

# Delete DaemonSet
kubectl delete daemonset <daemonset-name> -n <namespace>

# View pod placement
kubectl get pods -l name=fluentd-logging -o wide
```

------------------------------------------------------------------------

## 🏗 Real-World Production Setup (Fluentd DaemonSet)

### 1️⃣ ServiceAccount

Allows Fluentd to communicate with the Kubernetes API.

### 2️⃣ RBAC (Role & RoleBinding)

Grants permissions to read pods, namespaces, and metadata.

### 3️⃣ ConfigMap

Stores `fluent.conf` configuration file. - Allows dynamic
configuration - Supports different environments (dev/test/prod)

### 4️⃣ Volumes

Required to access host log files: - `/var/log` -
`/var/lib/docker/containers`

------------------------------------------------------------------------

## 📄 Example DaemonSet YAML

``` yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      tolerations:
      - key: "node-role.kubernetes.io/master"
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
        - name: varlog
          mountPath: /var/log
        - name: dockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
        - name: fluentd-config
          mountPath: /fluentd/etc
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: dockercontainers
        hostPath:
          path: /var/lib/docker/containers
      - name: fluentd-config
        configMap:
          name: fluentd-config
```

------------------------------------------------------------------------

## 🚀 What Happens After Deployment?

-   One Fluentd pod runs on every node\
-   Collects logs from:
    -   `/var/log` (system logs)
    -   `/var/lib/docker/containers` (container logs)
-   Forwards logs to Elasticsearch

------------------------------------------------------------------------

## 🔄 terminationGracePeriodSeconds: 30

Gives Fluentd 30 seconds to shut down gracefully and flush logs before
being forcefully terminated.

------------------------------------------------------------------------

## 📦 Understanding Volumes vs VolumeMounts

-   **volumes** → Define host paths (outside container)\
-   **volumeMounts** → Define where those paths appear inside the
    container

### 🏠 Analogy

-   Node = Your house\
-   Fluentd container = Guest in a room\
-   volumeMount = Key that allows access to the house

------------------------------------------------------------------------

## 🧪 Example Log Flow

Node logs: - `/var/log/syslog` -
`/var/lib/docker/containers/container-id/container.log`

Fluentd reads these logs and forwards them to Elasticsearch.

------------------------------------------------------------------------

## ❗ Important Notes

-   You can run multiple DaemonSets (e.g., one for logging, one for
    monitoring)
-   If a DaemonSet pod fails, Kubernetes automatically restarts it
-   Cordoned node → Existing pod stays, no new scheduling
-   Drained node → Pod is terminated and rescheduled when node returns

------------------------------------------------------------------------

## 🛠 Troubleshooting DaemonSet Issues

``` bash
# Check node taints
kubectl describe node <nodename>

# Check DaemonSet events
kubectl describe daemonset <name>

# View scheduling details
kubectl get pods -o wide
```

------------------------------------------------------------------------

## 📌 Summary

DaemonSets are essential for running cluster-wide node-level services.\
They ensure every node runs exactly one copy of a required system pod
such as Fluentd for log collection.
