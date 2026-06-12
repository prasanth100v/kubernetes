# 🌐 ✅ Service in Kubernetes
## 🚀 📌 Service in Kubernetes
 * Service in Kubernetes is a stable way to access our Pods, no matter how many times they `restart` or get replaced.
 * Pods can change IP addresses. but a Service ensures communication remains `stable`.

## 🚀 Why Services❓

 * Pods are ephemeral --- they can terminate and be recreated with a `new IP address`, Because of this, you cannot rely directly on `Pod IP addresses`.
 * 👉 Services provide a `stable IP address` and `DNS name` to access Pods reliably.

---

## 📌 🛠 Useful kubectl Commands

```bash
kubectl get services                              # List all services
kubectl get svc

kubectl describe service <service-name>           # Get details of a specific service
kubectl apply -f service.yaml                     # Create a service from YAML

kubectl delete service <service-name>             # Delete a service

kubectl get svc <service-name>                    # Get external IP of a service
```

Inside a Pod, you can test DNS or port access:
```bash
nslookup <service-name>
wget <service-name>:<port>
```

---

# 🧩 📦 Types of Services in Kubernetes

1️⃣ ClusterIP (Default)  
2️⃣ NodePort  
3️⃣ LoadBalancer  
4️⃣ ExternalName  
5️⃣ Headless Service  

## 1️⃣ 🏠 ClusterIP (Default)

 - 🔒 ClusterIP is the `default` Service type in Kubernetes, It is used for internal communication only between Pods.
 - 🧩 Accessible only inside the cluster, It provides a `stable IP` and `DNS` name within the cluster but is not accessible from outside. 
 - 📌 Use Case: Internal microservices communication (e.g., `frontend accessing backend`).


### 📄 Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```

### How It Works
 -   Backend Pod listens on port 8080.
 -   Service exposes port 80 inside the cluster.
 -   Traffic to port 80 is forwarded to port 8080 on the backend Pod.

Access from another Pod: `http://backend-service:80`

---

## 2️⃣ 🌍 NodePort

 * 🌐 NodePort exposes your application outside the cluster by opening a static port (30000--32767) on each node's IP address.
 * **Use Case:** Development or testing environments.
      * Automatically creates a `ClusterIP` service.
      * 🔗 External access format:  `http://<NodeIP>:<NodePort>`
      * 🔐 NodePort Range: 30000 -- 32767

### 📄 Example YAML
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30036
  type: NodePort
```
### How It Works

  -   Pods run containers on port `8080`.
  -   Service maps port `80 → 8080`.
  -   NodePort opens port `30036` on every node.
  -   Access using: `http://<NodeIP>:30036`

---

## 3️⃣ ☁️ LoadBalancer

 * ☁️ LoadBalancer is used to expose an application externally using a cloud provider's load balancer (`AWS`, `GCP`, `Azure`).
 * 🌍 It is more advanced than `NodePort` because it `automatically provisions an external load balancer`.
 * 🚀 Used for production applications 
     * Automatically creates `ClusterIP` and `NodePort`.
     * External IP is assigned by the `cloud provider`.

### 📄 Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer
```
### How It Works

1.  Kubernetes communicates with the cloud provider.
2.  The cloud provider provisions a load balancer.
3.  A `public IP` or `DNS name` is assigned.
4.  Traffic is distributed to Pods.

  * Example:  `http://203.0.113.10`
  * In AWS, a Classic ELB may provide a DNS name like: `a1b2c3d4e5f6g7h8.elb.amazonaws.com`
  * If you need an Application Load Balancer (`ALB`), use the AWS Load Balancer Controller (`Ingress-based`).

---

## 4️⃣ 🔗 ExternalName

- 🌐 ExternalName maps a Kubernetes Service to an external DNS name. 
- ❌ No proxying or load balancing inside cluster  
- 📛 It only creates a DNS CNAME record.
- **Use Case:** Connecting to `external databases` or `APIs`.

### 📄 Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-db-service
spec:
  type: ExternalName
  externalName: mydb.external.com
```
### What Happens

  - Kubernetes DNS resolves `my-db-service` to `mydb.external.com`.
  - Kubernetes does NOT perform proxying or load balancing.
  - Traffic routing and failover are handled externally (e.g., `AWS Route 53` or `external load balancer`).
  - Example for AWS RDS:  `my-aurora-cluster.cluster-1234567890.us-west-2.rds.amazonaws.com`


---

## 5️⃣ 🧩 Headless Service

- 🚫 Headless Service is No ClusterIP (`clusterIP: None`)  
- 📡 It does not provide load balancing. Instead, it returns `individual Pod IP addresses` via `DNS`.
- 🗄 **Mostly used with:** `StatefulSets` for stable network identities.

### 📄 Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

### StatefulSet Reference 

``` yaml
spec:
  serviceName: "my-headless-service"                   # In StatefulSet yaml Reference 
```

### Example DNS Records
  * `web-0.web-headless.default.svc.cluster.local`
  * `web-1.web-headless.default.svc.cluster.local`

---

## 📌 🛠 Useful kubectl Commands

```bash
kubectl get services                              # List all services
kubectl get svc

kubectl describe service <service-name>           # Get details of a specific service

kubectl apply -f service.yaml                     # Create a service from YAML

kubectl delete service <service-name>             # Delete a service

kubectl get svc <service-name>                    # Get external IP of a service
```

### Inside a Pod:
```bash
nslookup <service-name>
wget <service-name>:<port>
```

---

### 🌐 Kubernetes Service Types (Detailed)
| 🧩 **Service Type**     | 🌐 **Accessibility**      | 📖 **What It Does**                                         | ⚙️ **How It Works**                                      | 💡 **Real-World Use Case**                         |
| ----------------------- | ------------------------- | ----------------------------------------------------------- | -------------------------------------------------------- | -------------------------------------------------- |
| 🏠 **ClusterIP**        | 🔒 Internal only          | Default service type for Pod-to-Pod communication           | Assigns an internal IP accessible only inside the cluster  | 🔗 Backend communication (API ↔ DB, microservices) |
| 🌍 **NodePort**         | 🌐 External (via Node IP) | Exposes service on a static port (30000–32767) on each node | Opens same port on all nodes → routes traffic to service | 🧪 Testing or simple external access without LB    |
| ☁️ **LoadBalancer**     | 🌍 Public internet        | Exposes service using cloud provider LB                     | Automatically provisions external IP (AWS ELB, GCP LB)   | 🚀 Production apps needing public access           |
| 🔗 **ExternalName**     | 🌍 External DNS           | Maps service to an external DNS name                        | Returns a CNAME record instead of proxying traffic       | 🌐 Connect to external DB or API                   |
| 🧩 **Headless Service** | 🔒 Internal (no IP)      | Directly exposes Pod IPs (no load balancing)                | `clusterIP: None` → DNS returns Pod IPs                  | ⚡ Stateful apps (Kafka, MongoDB, Redis clusters)   |

### 🔍 Key Differences
| Feature         | ClusterIP | NodePort   | LoadBalancer | ExternalName      | Headless      |
| --------------- | --------- | ---------- | ------------ | ----------------- | ------------- |
| Default         | ✅ Yes     | ❌ No       | ❌ No         | ❌ No              | ❌ No          |
| External Access | ❌ No      | ⚠️ Limited | ✅ Yes        | ✅ Yes (DNS)       | ❌ No          |
| Load Balancing  | ✅ Yes     | ✅ Yes      | ✅ Yes        | ❌ No              | ❌ No          |
| Use Case        | Internal  | Testing    | Production   | External services | Stateful apps |

---

## 📚 📊 Final Summary

| 📦 Type | 🔒 Internal | 🌐 External | 🎯 Common Use Case |
|--------|------------|------------|---------------------|
| ClusterIP | ✅ Yes | ❌ No | Internal microservices |
| NodePort | ✅ Yes | ✅ Yes | Development/testing |
| LoadBalancer | ✅ Yes | ✅ Yes | Production (cloud) |
| ExternalName | ✅ Yes | ✅ Yes | External DB/APIs |
| Headless | ✅ Yes | ❌ No | Stateful applications |

✅ Kubernetes Services provide stable networking for dynamic and ephemeral Pods.

---
