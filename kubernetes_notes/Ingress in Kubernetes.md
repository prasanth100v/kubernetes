# Kubernetes Ingress -- Complete Guide

## ✅ What is Ingress in Kubernetes?

Ingress is a Kubernetes object that manages external access to services
in your cluster --- usually over HTTP/HTTPS.\
It acts as a reverse proxy and provides L7 (Application Layer) routing.

Unlike NodePort or LoadBalancer, Ingress provides path-based and
host-based routing.

Load balancers handle the external IP address and forwarding rules,
making it easier to expose your Ingress controller to the outside world.

------------------------------------------------------------------------

## ✅ Ingress Architecture -- Simple Explanation

### Ingress Resource

A YAML configuration that defines routing rules --- e.g.,
`/api → backend-service`.\
It's just a set of rules.

### Ingress Controller (Must be Deployed)

A pod (e.g., NGINX, ALB) that reads the Ingress rules and configures the
actual routing.

### Service

Exposes a set of Pods inside the cluster. (ClusterIP / NodePort)

### Pods

The actual containers running your app (Deployment-managed)

------------------------------------------------------------------------

## 🔁 How Traffic Flows

Client → LoadBalancer (external IP) → Ingress Controller → Ingress
Resource → Service → Pods

------------------------------------------------------------------------

## ✅ Benefits of Using Ingress

-   Centralized routing (1 LoadBalancer for multiple services)
-   Host-based routing (app1.example.com, app2.example.com)
-   Path-based routing (/api, /frontend)
-   SSL/TLS support via Kubernetes Secret
-   Custom configurations via annotations
-   Cost-effective
-   L7 routing and load balancing

------------------------------------------------------------------------

## 🔧 Real-Time Use Case

Instead of exposing 5 services with 5 LoadBalancers (\$\$\$), use 1
NGINX Ingress Controller and route traffic smartly with domain/path
rules.

Ingress makes this simple and cost-effective.

------------------------------------------------------------------------

## ✅ Installation Methods

### 1. Standard Installation (Cloud Latest Stable)

``` bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml
```

### 2. Helm Installation (Recommended for Production)

``` bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx
```

### 3. Bare-Metal Installation

``` bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/baremetal/deploy.yaml
```

------------------------------------------------------------------------

## 🧾 What is MetalLB?

MetalLB is a load balancer implementation for bare-metal Kubernetes
clusters.\
It provides external IPs for `Service type: LoadBalancer`.

In cloud environments (AWS, GCP, Azure), LoadBalancer services
automatically get public IPs.\
In bare-metal environments, MetalLB provides this functionality.

------------------------------------------------------------------------

## ✅ How It Works

1.  Deploy NGINX Ingress Controller (pod).
2.  Define Ingress resources (routing rules).
3.  NGINX controller watches those rules.
4.  Traffic is forwarded to correct services.

------------------------------------------------------------------------

## 🔁 Reverse Proxy in Kubernetes

A reverse proxy acts as an intermediary between clients and internal
services.

Ingress Controller commonly acts as a reverse proxy.

### Popular Reverse Proxies:

-   NGINX Ingress (Most popular)
-   Istio Gateway
-   Envoy

------------------------------------------------------------------------

## ❓ What Problem Does Ingress Solve?

LoadBalancer requires one LB per application.

Ingress provides: - Host/path-based routing (multiple services under one
IP) - Centralized TLS termination - Traffic rules (redirects, rewrites,
rate limiting)

For non-HTTP services (databases, gRPC), LoadBalancer or NodePort is
sufficient.
