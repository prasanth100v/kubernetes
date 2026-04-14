# 🌐 🚀 Kubernetes Ingress -- Complete Guide

---

## ✅ 📘 What is Ingress in Kubernetes?

Ingress is a Kubernetes object that manages external access to services
in your cluster --- usually over HTTP/HTTPS.  
It acts as a reverse proxy and provides L7 (Application Layer) routing.

Unlike NodePort or LoadBalancer, Ingress provides path-based and
host-based routing.

Load balancers handle the external IP address and forwarding rules,
making it easier to expose your Ingress controller to the outside world.

---

## ✅ 🧩 Ingress Architecture -- Simple Explanation

### 📄 Ingress Resource

A YAML configuration that defines routing rules --- e.g.,
`/api → backend-service`.  
It's just a set of rules.

### ⚙️ Ingress Controller (Must be Deployed)

A pod (e.g., NGINX, ALB) that reads the Ingress rules and configures the
actual routing.

### 🔗 Service

Exposes a set of Pods inside the cluster. (ClusterIP / NodePort)

### 📦 Pods

The actual containers running your app (Deployment-managed)

---

## 🔁 🌊 How Traffic Flows

Client → LoadBalancer (external IP) → Ingress Controller → Ingress
Resource → Service → Pods

---

## ✅ 🎯 Benefits of Using Ingress

- 🎛 Centralized routing (1 LoadBalancer for multiple services)
- 🌐 Host-based routing (app1.example.com, app2.example.com)
- 🛣 Path-based routing (/api, /frontend)
- 🔒 SSL/TLS support via Kubernetes Secret
- ⚙️ Custom configurations via annotations
- 💰 Cost-effective
- ⚡ L7 routing and load balancing

---

## 🔧 💡 Real-Time Use Case

Instead of exposing 5 services with 5 LoadBalancers ($$$), use 1
NGINX Ingress Controller and route traffic smartly with domain/path
rules.

Ingress makes this simple and cost-effective.

---

## ✅ 🛠 Installation Methods

### 1️⃣ ☁️ Standard Installation (Cloud Latest Stable)

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml
```

### 2️⃣ 📦 Helm Installation (Recommended for Production)

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx
```

### 3️⃣ 🖥 Bare-Metal Installation

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/baremetal/deploy.yaml
```

---

## 🧾 ⚙️ What is MetalLB?

MetalLB is a load balancer implementation for bare-metal Kubernetes
clusters.  
It provides external IPs for `Service type: LoadBalancer`.

In cloud environments (AWS, GCP, Azure), LoadBalancer services
automatically get public IPs.  
In bare-metal environments, MetalLB provides this functionality.

---

## ✅ ⚙️ How It Works

1. 🚀 Deploy NGINX Ingress Controller (pod).
2. 🧾 Define Ingress resources (routing rules).
3. 👀 NGINX controller watches those rules.
4. 🔁 Traffic is forwarded to correct services.

---

## 🔁 🔄 Reverse Proxy in Kubernetes

A reverse proxy acts as an intermediary between clients and internal
services.

Ingress Controller commonly acts as a reverse proxy.

### 🌟 Popular Reverse Proxies:

- NGINX Ingress (Most popular)
- Istio Gateway
- Envoy

---

## ❓ 🧠 What Problem Does Ingress Solve?

LoadBalancer requires one LB per application.

Ingress provides:  
- 🌐 Host/path-based routing (multiple services under one IP)  
- 🔒 Centralized TLS termination  
- ⚙️ Traffic rules (redirects, rewrites, rate limiting)

For non-HTTP services (databases, gRPC), LoadBalancer or NodePort is
sufficient.

---

# 🔐 🌐 Kubernetes Ingress -- TLS & Security Guide

## 🔐 🔑 What is TLS?

Transport Layer Security (TLS) is a security protocol that provides
secure communication over a network. It is primarily used for web
browsing, APIs, and email.

TLS uses SSL/TLS certificates to:  
- 🔒 Encrypt communication between clients and applications  
- 🛡 Protect sensitive data  
- ✅ Verify server identity  

SSL/TLS certificates are digital documents that enable encrypted
communication.

---

## 🔒 ⚙️ TLS Termination (NGINX Ingress Example)

```yaml
spec:
  tls:
  - hosts:
    - example.com
    secretName: example-tls
```

TLS setup requires:  
- 📄 `tls.crt`  
- 🔑 `tls.key`  
Stored inside a Kubernetes Secret.

---

## 🔑 🛠 How to Generate TLS Secret

### 1️⃣ 📜 Generate Certificate

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=example.com"
```

### 2️⃣ 🔐 Create Kubernetes Secret

```bash
kubectl create secret tls example-tls --cert=tls.crt --key=tls.key
```

---

## ☁️ 🌐 SSL/TLS in AWS ALB Ingress

```yaml
annotations:
  alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-west-2:123456789012:certificate/xxxxxx
```

---

## 🚀 📊 Canary Deployment (Traffic Splitting)

```yaml
annotations:
  nginx.ingress.kubernetes.io/canary: "true"
  nginx.ingress.kubernetes.io/canary-weight: "20"
```

---

## 📛 🚫 Restrict Access (IP Whitelisting)

```yaml
annotations:
  nginx.ingress.kubernetes.io/whitelist-source-range: "192.168.1.0/24"
```

---

## 🔁 ⚙️ Popular NGINX Ingress Annotations

### 🔄 Rewrite Target

```yaml
nginx.ingress.kubernetes.io/rewrite-target: /
```

---

### 🔒 SSL Redirect

```yaml
nginx.ingress.kubernetes.io/ssl-redirect: "true"
```

---

### ⏱ Proxy Read Timeout

```yaml
nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
```

---

### 🔐 Backend Protocol

```yaml
nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

---

### 🚦 Rate Limiting

```yaml
nginx.ingress.kubernetes.io/limit-rpm: "30"
```

---

## 🌐 ☁️ ALB Ingress Popular Annotations

```yaml
alb.ingress.kubernetes.io/backend-protocol: HTTP
alb.ingress.kubernetes.io/scheme: internal
alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
alb.ingress.kubernetes.io/ssl-redirect: '443'
```

---

## 🧩 ⚙️ Istio Ingress Gateway

Istio does not require traditional Kubernetes Ingress. It uses:

- Gateway
- VirtualService

---

### 🔄 Rewrite in Istio

```yaml
rewrite:
  uri: /new-path
```

---

### 🔁 Sidecar Injection

```yaml
sidecar.istio.io/inject: "true"
```

---

### 🔌 Control Inbound Ports

```yaml
traffic.sidecar.istio.io/includeInboundPorts: "8080,9090"
```

---

### 🚫 Exclude Outbound IP Ranges

```yaml
traffic.sidecar.istio.io/excludeOutboundIPRanges: "10.10.0.0/16"
```

---

## ✅ 🎯 Conclusion

- 🔒 TLS encrypts communication between clients and services.
- ⚙️ NGINX, ALB, and Istio support TLS configuration.
- 🧩 Use annotations for traffic control, security, and routing.
- 🌐 Istio uses Gateway and VirtualService instead of traditional Ingress.

---

## 🌍 🚀 Popular Ingress Controllers

- NGINX Ingress Controller
- HAProxy
- Istio Ingress Gateway
- AWS ALB Ingress Controller

An Ingress Controller acts like a traffic controller, routing external
requests to the correct services inside your Kubernetes cluster.

