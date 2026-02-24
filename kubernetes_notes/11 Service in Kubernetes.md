# ✅ Service in Kubernetes

Service in Kubernetes is a stable way to access our Pods, no matter how
many times they restart or get replaced.\
Pods can change IP addresses. A Service ensures that communication stays
stable.

------------------------------------------------------------------------

## 🚀 Why Services?

Pods are ephemeral --- they can die and get recreated with a new IP.\
So, you can't rely on Pod IPs directly.

👉 Services give a stable IP and DNS name to access the Pods.

------------------------------------------------------------------------

## 📌 Useful kubectl Commands

``` bash
kubectl get services
kubectl get svc
kubectl describe service <service-name>
kubectl apply -f service.yaml
kubectl delete service <service-name>
kubectl get svc <service-name>
```

Inside the pod:

``` bash
nslookup <service-name>
wget <service-name>:<port>
```

------------------------------------------------------------------------

# 🧩 Types of Services in Kubernetes

1.  ClusterIP (Default)
2.  NodePort
3.  LoadBalancer
4.  ExternalName
5.  Headless Service

------------------------------------------------------------------------

## 1️⃣ ClusterIP (Default)

-   Internal communication only
-   Accessible only inside the cluster
-   Default service type

### Example YAML

``` yaml
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

Access inside cluster:

    http://backend-service:80

------------------------------------------------------------------------

## 2️⃣ NodePort

-   Exposes service outside the cluster
-   Opens static port (30000--32767)
-   Accessible using: http://`<NodeIP>`{=html}:`<NodePort>`{=html}

### Example YAML

``` yaml
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

------------------------------------------------------------------------

## 3️⃣ LoadBalancer

-   Used in cloud providers (AWS, GCP, Azure)
-   Automatically provisions external load balancer
-   Used for production applications

### Example YAML

``` yaml
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

------------------------------------------------------------------------

## 4️⃣ ExternalName

-   Maps service to external DNS name
-   No proxying or load balancing inside cluster
-   Creates DNS CNAME record

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-db-service
spec:
  type: ExternalName
  externalName: mydb.external.com
```

------------------------------------------------------------------------

## 5️⃣ Headless Service

-   No ClusterIP (clusterIP: None)
-   Returns Pod IPs directly via DNS
-   Mostly used with StatefulSets

### Example YAML

``` yaml
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

------------------------------------------------------------------------

# 📚 Summary

  Type           Internal   External   Use Case
  -------------- ---------- ---------- ------------------------
  ClusterIP      ✅ Yes     ❌ No      Internal microservices
  NodePort       ✅ Yes     ✅ Yes     Development/testing
  LoadBalancer   ✅ Yes     ✅ Yes     Production (cloud)
  ExternalName   ✅ Yes     ✅ Yes     External DB/API
  Headless       ✅ Yes     ❌ No      Stateful apps

------------------------------------------------------------------------

✅ Kubernetes Services ensure stable networking for dynamic Pods.


# ✅ Service in Kubernetes

A Service in Kubernetes is a stable way to access Pods, no matter how
many times they restart or get replaced.\
Pods can change IP addresses, but a Service ensures communication
remains stable.

------------------------------------------------------------------------

## 🚀 Why Services?

Pods are ephemeral --- they can terminate and be recreated with a new IP
address.\
Because of this, you cannot rely directly on Pod IP addresses.

👉 Services provide a stable IP address and DNS name to access Pods
reliably.

------------------------------------------------------------------------

## 📌 Useful kubectl Commands

``` bash
# List all services
kubectl get services
kubectl get svc

# Get details of a specific service
kubectl describe service <service-name>

# Create a service from YAML
kubectl apply -f service.yaml

# Delete a service
kubectl delete service <service-name>

# Get external IP of a service
kubectl get svc <service-name>
```

Inside a Pod, you can test DNS or port access:

``` bash
nslookup <service-name>
wget <service-name>:<port>
```

------------------------------------------------------------------------

# 🧩 Types of Services in Kubernetes

1.  ClusterIP (Default)
2.  NodePort
3.  LoadBalancer
4.  ExternalName
5.  Headless Service

------------------------------------------------------------------------

## 1️⃣ ClusterIP (Default)

ClusterIP is the default Service type in Kubernetes. It is used for
internal communication between Pods.\
It provides a stable IP and DNS name within the cluster but is not
accessible from outside.

**Use Case:** Internal microservices communication (e.g., frontend
accessing backend).

### Example YAML

``` yaml
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

Access from another Pod:

    http://backend-service:80

------------------------------------------------------------------------

## 2️⃣ NodePort

NodePort exposes your application outside the cluster by opening a
static port (30000--32767) on each node's IP address.

**Use Case:** Development or testing environments.

-   Automatically creates a ClusterIP service.
-   External access format:\
    `http://<NodeIP>:<NodePort>`

### NodePort Range

🔐 30000 -- 32767

### Example YAML

``` yaml
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

-   Pods run containers on port 8080.
-   Service maps port 80 → 8080.
-   NodePort opens port 30036 on every node.
-   Access using:

```{=html}
<!-- -->
```
    http://<NodeIP>:30036

------------------------------------------------------------------------

## 3️⃣ LoadBalancer

LoadBalancer is used to expose an application externally using a cloud
provider's load balancer (AWS, GCP, Azure).\
It is more advanced than NodePort because it automatically provisions an
external load balancer.

**Use Case:** Production web applications.

-   Automatically creates ClusterIP and NodePort.
-   External IP is assigned by the cloud provider.

### Example YAML (AWS NLB)

``` yaml
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
3.  A public IP or DNS name is assigned.
4.  Traffic is distributed to Pods.

Example:

    http://203.0.113.10

In AWS, a Classic ELB may provide a DNS name like:

    a1b2c3d4e5f6g7h8.elb.amazonaws.com

If you need an Application Load Balancer (ALB), use the AWS Load
Balancer Controller (Ingress-based).

------------------------------------------------------------------------

## 4️⃣ ExternalName

ExternalName maps a Kubernetes Service to an external DNS name.\
It does not create a proxy or load balancer --- it only creates a DNS
CNAME record.

**Use Case:** Connecting to external databases or APIs.

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-db-service
spec:
  type: ExternalName
  externalName: mydb.external.com
```

### What Happens

-   Kubernetes DNS resolves `my-db-service` to `mydb.external.com`.
-   Kubernetes does NOT perform proxying or load balancing.
-   Traffic routing and failover are handled externally (e.g., AWS Route
    53 or external load balancer).

Example for AWS RDS:

    my-aurora-cluster.cluster-1234567890.us-west-2.rds.amazonaws.com

------------------------------------------------------------------------

## 5️⃣ Headless Service

A Headless Service is created by setting:

    clusterIP: None

It does not provide load balancing. Instead, it returns individual Pod
IP addresses via DNS.

**Commonly used with:** StatefulSets for stable network identities.

### Features

-   No ClusterIP
-   Direct Pod-to-Pod communication
-   DNS returns Pod IPs

### Example YAML

``` yaml
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
  serviceName: "my-headless-service"
```

### Example DNS Records

    web-0.web-headless.default.svc.cluster.local
    web-1.web-headless.default.svc.cluster.local

------------------------------------------------------------------------

# 📚 Summary

  ----------------------------------------------------------------------------
  Type             Internal Access    External Access    Common Use Case
  ---------------- ------------------ ------------------ ---------------------
  ClusterIP        ✅ Yes             ❌ No              Internal
                                                         microservices

  NodePort         ✅ Yes             ✅ Yes             Development/testing

  LoadBalancer     ✅ Yes             ✅ Yes             Production (cloud)

  ExternalName     ✅ Yes             ✅ Yes             External DB/API

  Headless         ✅ Yes             ❌ No              Stateful applications
  ----------------------------------------------------------------------------

------------------------------------------------------------------------

✅ Kubernetes Services provide stable networking for dynamic and
ephemeral Pods.
