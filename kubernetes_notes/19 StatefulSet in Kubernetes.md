# 🧩 StatefulSet in Kubernetes

 * A **StatefulSet** is a Kubernetes controller used to manage stateful applications --- apps that need `stable network identities`, `persistent storage`, and `ordered deployment`, `scaling`, or `deletion`.
 * Unlike Deployments, StatefulSets provide `unique network identities` and dedicated volumes for each pod, making them ideal for applications like `databases` and `message queues`.


## ✅ ✨ Key Features of StatefulSet
 * 🔐 **Stable pod names** -- Pods have persistent names
   * Example (StatefulSet name: `mysql`, replicas: `3`) :
     * 📦 mysql-0
     * 📦 mysql-1
     * 📦 mysql-2  

 * 🌐 **Stable network identity** -- Each pod gets a `stable` DNS hostname
     * Example:
       *  `mysql-0.mysql.default.svc.cluster.local`
       *  `mysql-1.mysql.default.svc.cluster.local`   
 * 💾 **Persistent storage** -- Each pod can have its own `PersistentVolumeClaim (PVC)` that is never deleted when the pod is rescheduled.
 * 🔄 **Ordered deployment & scaling** -- Pods are started, updated, and deleted `one-by-one` in order.

---

## 📌 🚀 When to Use StatefulSets

Use StatefulSets when:
 - 💾 Your app needs stable storage  
 - 🌐 Pods need stable hostnames  
 - 🔢 Pods must start/stop in order  

### 🧠 Common Use Cases:
 - 🗄️ Databases (MySQL, PostgreSQL, MongoDB clusters)
 - 📩 Message queues
 - 📦 Any application requiring persistent storage

❗ ⚠️ `Avoid` it if your app is stateless (like frontend or API). Use Deployment instead.

---

## 🧱 Step 1: Headless Service YAML
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  clusterIP: None      # Headless service for stable DNS
  selector:
    app: nginx         # Targets pods with this label
  ports:
    - port: 80
      name: web
```

### ⚙️ Apply Command:
```bash
kubectl apply -f headless-service.yaml
```

### 🛠️ Useful Commands:
```bash
kubectl get statefulsets
kubectl get sts
kubectl describe statefulset <statefulset-name>
kubectl delete statefulset <statefulset-name>
```

### 🔍 Explanation:
 - 🧩 `clusterIP: None` makes it a headless service (required for StatefulSet).
 - 🌐 Each pod gets a DNS like :  `nginx-0.nginx.default.svc.cluster.local`
 - 🔗 Allows direct access to individual pods (important for stateful apps).


# 🧱 Step 2: StatefulSet YAML
```yaml
apiVersion: apps/v1
kind: StatefulSet            # Ensures unique, ordered pods with stable storage
metadata:
  name: nginx                # Name of the StatefulSet

spec:
  serviceName: nginx               # Must match the Headless Service name
  replicas: 3                 # Number of NGINX pods to create

  selector:
    matchLabels:
      app: nginx               # Matches labels on the pod template

  template:
    metadata:
      labels:
        app: nginx            # Label used by service to select pods

    spec:
      terminationGracePeriodSeconds: 10           # Wait time before forcibly killing pod
      containers:
      - name: nginx                    # Container name
        image: nginx                   # Container image to use
        ports:
        - containerPort: 80            # Expose port 80
          name: web
        volumeMounts:
        - name: www                              # Mount volume named 'www'
          mountPath: /usr/share/nginx/html        # Mount path inside the container

  volumeClaimTemplates:                           # Template to request storage for each pod
  - metadata:
      name: www                                  # Volume name used in volumeMounts
    spec:
      accessModes:
      - ReadWriteOnce                            # Can be mounted as read-write by a single node
      resources:
        requests:
          storage: 1Gi                        # Size of storage requested
```

### ⚙️ Apply Command:
```bash
kubectl apply -f statefulset.yaml
```

---

## 📂 💡 How Volumes Work
Each pod in the StatefulSet gets its own PersistentVolumeClaim:
 - 📦 nginx-0 → PVC: `www-nginx-0`
 - 📦 nginx-1 → PVC: `www-nginx-1`
 - 📦 nginx-2 → PVC: `www-nginx-2`

These volumes remain even if the pod is deleted and recreated --- making `StatefulSets perfect for stateful applications`.
