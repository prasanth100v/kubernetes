# 🌐 Kubernetes Ingress & LoadBalancer -- Simple Guide

## 📌 Overview

In Kubernetes, a **combination of a LoadBalancer Service and an Ingress
Controller** is commonly used to expose applications to external users
efficiently.

We use **Ingress** to: - 💰 Reduce cost - 🧭 Simplify routing - 🚦
Enable smart traffic control

But we still need **one LoadBalancer** to connect the **internet to the
Kubernetes cluster**.

------------------------------------------------------------------------

# ⚙️ Core Components

## 🔹 LoadBalancer Service

A **LoadBalancer service** automatically provisions a cloud provider
load balancer.

Examples: - AWS ELB - Azure Load Balancer - Google Cloud Load Balancer

**Key Features** - 🌍 Provides a **public IP address** - 🔁 Forwards
traffic to the **Ingress Controller** - ☁️ Managed by cloud provider

------------------------------------------------------------------------

## 🔹 Ingress Controller

The **Ingress Controller** watches for **Ingress resources** and routes
traffic accordingly.

Examples: - NGINX Ingress Controller - Istio Ingress Gateway - Traefik

**Capabilities** - Host-based routing - Path-based routing - SSL/TLS
termination - Header-based routing

------------------------------------------------------------------------

# 🌐 Network Layers in Kubernetes

## 🔹 Layer 7 -- Application Layer (HTTP/HTTPS)

Used by **Ingress Controllers**

Features: - Understands HTTP & HTTPS - Routing based on: - URL paths -
Hostnames - Headers

📌 **Ingress works only with HTTP/HTTPS**

Example:

    myapp.example.com/api

------------------------------------------------------------------------

## 🔹 Layer 4 -- Transport Layer (TCP/UDP)

Used by: - LoadBalancer Services - NodePort Services

Features: - Operates on **IP + Port** - Does **not understand HTTP** -
Routes based on **TCP/UDP traffic**

Example Use Cases: - MySQL - Redis - Databases - Custom TCP apps

------------------------------------------------------------------------

# 🚀 Request Flow

    User Request
          │
          ▼
    🌍 myapp.example.com/api
          │
          ▼
    ☁️ Cloud LoadBalancer (AWS / Azure / GCP)
          │
          ▼
    🚦 Ingress Controller (NGINX / Istio)
          │
          ▼
    📜 Ingress Resource (Routing Rules)
          │
          ▼
    🔗 Kubernetes Service (ClusterIP)
          │
          ▼
    📦 Pods (Application Containers)

------------------------------------------------------------------------

# 🧰 Basic Ingress Commands

### View All Ingress

    kubectl get ingress

### View Ingress in Namespace

    kubectl get ingress -n my-namespace

### Describe Ingress

    kubectl describe ingress my-ingress

### View YAML

    kubectl get ingress my-ingress -o yaml

### Create Ingress

    kubectl apply -f ingress.yaml

### Delete Ingress

    kubectl delete ingress my-ingress

### Edit Ingress

    kubectl edit ingress my-ingress

### Test Ingress

    curl -H "Host: myapp.example.com" http://<INGRESS-IP>

### Check Ingress Controller Service

    kubectl get svc -n ingress-nginx

### Check Controller Pods

    kubectl get pods -n ingress-nginx

------------------------------------------------------------------------

# 🔍 Common Errors & Fixes

## 🔴 404 Not Found

Cause: - Path incorrect - Service name mismatch

Fix: - Verify ingress path - Check backend service

------------------------------------------------------------------------

## 🔴 503 Service Unavailable

Cause: - Backend pods not reachable

Fix: - Check pod status - Verify service selector

------------------------------------------------------------------------

## 🔴 No External IP

Cause: - LoadBalancer pending

Fix: - Check cloud provider integration

------------------------------------------------------------------------

## 🔴 SSL Errors

Cause: - TLS secret missing or invalid

Fix: - Verify TLS secret configuration

------------------------------------------------------------------------

# ☁️ AWS LoadBalancer with Istio Ingress Gateway

Recommended architecture for **advanced microservices**.

Benefits: - Fully managed AWS Load Balancer - High scalability - Secure
traffic management - Advanced traffic policies

------------------------------------------------------------------------

## 🔹 Advanced Features

Istio provides:

-   🔁 Traffic shifting
-   🔄 Retries
-   ⚡ Circuit breaking
-   🔐 mTLS security
-   📊 Observability

------------------------------------------------------------------------

# 🎯 AWS Target Groups

If annotation is used:

    alb.ingress.kubernetes.io/target-type: ip

Then: - **Pods are registered directly in Target Group**

If:

    alb.ingress.kubernetes.io/target-type: instance

Then: - **EC2 nodes are registered**

Target Groups are automatically managed by the **AWS Load Balancer
Controller**.

------------------------------------------------------------------------

# 🔗 Service Mesh Communication

## Internal Communication

Inside the cluster: - Managed by **Istio Envoy sidecars** - No external
load balancer required

## External Communication

External traffic:

    User
      │
      ▼
    AWS LoadBalancer
      │
      ▼
    Istio Ingress Gateway
      │
      ▼
    Kubernetes Services
      │
      ▼
    Pods

------------------------------------------------------------------------

# 🌍 Real World Example

1️⃣ User visits:

    www.example.com

2️⃣ DNS resolves to:

    AWS Load Balancer IP

3️⃣ LoadBalancer forwards to:

    Istio Ingress Gateway

4️⃣ Istio routes request to:

    Correct Kubernetes Service

5️⃣ Service forwards to:

    Application Pods

------------------------------------------------------------------------

# 🧠 Key Takeaway

  Component      Layer   Purpose
  -------------- ------- ----------------------------
  LoadBalancer   L4      Expose cluster to internet
  Ingress        L7      Smart routing
  Service        L4      Internal load balancing
  Pods           App     Run applications

------------------------------------------------------------------------

# 🚀 Summary

✔ **LoadBalancer connects internet to cluster**\
✔ **Ingress provides intelligent routing**\
✔ **Services expose pods internally**\
✔ **Istio enables advanced traffic control**

------------------------------------------------------------------------

💡 **Modern Kubernetes production architectures often use:**

    AWS LoadBalancer → Ingress / Istio Gateway → Services → Pods
