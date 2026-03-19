# 🌐 Kubernetes Networking Guide

## 📌 Overview
Kubernetes Networking manages how **Pods, Services, and external clients communicate**.

---

# 📦 What is Kubernetes Networking?

Kubernetes networking defines how:

👉 Pods communicate with each other  
👉 Services expose applications  
👉 External users access applications  

---

## 🚀 Kubernetes Networking Model

- 🟢 Every Pod gets its **own IP address**
- 🔄 No NAT required between Pods
- 🔗 All Pods can communicate with each other
- 🖥️ Nodes ↔ Pods communication is allowed
- 🎯 Services provide **stable access (IP + DNS)**

---

## 🧩 Network Components
# 🏗 Networking Architecture

```
Pod ↔ Pod ↔ Service ↔ Ingress ↔ External User
```

### 🔹 Pod Network
- Each Pod has a unique IP
- Containers inside Pod share network namespace

### 🔹 Service
- Stable IP + DNS name
- Load balances traffic to Pods

### 🔹 Cluster Network
- Connects all Nodes & Pods internally

### 🔹 CNI Plugin
- Manages Pod networking  
- Examples:
  - 🟡 Calico (NetworkPolicy support)
  - 🔵 Flannel (Simple overlay)
  - 🟣 Cilium (eBPF based)
  - 🟠 Weave (Secure & simple)

### 🔹 kube-proxy
- Manages Service networking (routing) 
- Uses:
  - iptables
  - IPVS

### 🔹 Ingress Controller
✔ Handles external HTTP(S) traffic 
- Manages HTTP/HTTPS traffic from outside
- Examples:
  - NGINX
  - Traefik
  - HAProxy

### 🔹 NetworkPolicy
✔ Acts like a **firewall for Pods**  
✔ Controls allowed traffic  

---

## 🌍 Real-World Scenarios

| Scenario | Solution |
|----------|---------|
| 🌐 Expose app to internet | Ingress + LoadBalancer |
| 🔒 Secure service communication | NetworkPolicy |
| 📊 Monitor traffic | Service Mesh (Istio) |
| ⚖️ Scale services | ClusterIP + DNS |
| 🏢 Multi-tenancy | Namespaces + NetworkPolicy |

---

## 🔗 Pod-to-Pod Communication

- Direct communication using Pod IP
- No NAT required
- Managed by CNI plugins

📌 Example:  
Pod A (Node1) → Pod B (Node2) using IP directly

---

## 🧰 Tools & Plugins

- 🌐 **CNI Plugins**: Calico, Flannel, Cilium   | Pod networking |
- 🔍 **DNS**: CoreDNS   | Service discovery |
- 🚪 **Ingress Controllers**: NGINX, Traefik  | External access |
- 🧠 **Service Mesh**: Istio, Linkerd  | Advanced traffic control |

---

## 🌍 Egress Traffic (Outbound)

- Pods can access internet by default
- Restrict using:
  - 🔒 NetworkPolicy
  - 🚪 Egress Gateway (Istio/Calico)

---

## 🔒 Network Policies (Pod Firewall)
### 📌 What is NetworkPolicy?

👉 Controls traffic between: - Pods, Namespaces and External endpoints  
- Control **allow/deny traffic**
- Works with labels & selectors
- Implemented by CNI (e.g., Calico)

### ⚠️ Default Behavior
- All Pods can talk to all Pods

---

### 🧾 Example NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```
---

## 📌 Explanation

✔ Only `frontend` pods can access `backend` pods  
✔ Others are blocked  

---

# 🔗 Pod-to-Service Communication

✔ Services provide stable access 

Services provide: Stable IP (ClusterIP) and DNS name 📌 Example: http://backend-service

## 📌 Example DNS format:

```
backend-service.default.svc.cluster.local
```

✔ Pods use DNS instead of IP  

---

# 📊 Communication Summary

| Communication | How It Works | Tools |
|------|------|------|
|🔗 Pod ↔ Pod | Direct via CNI | CNI, Routing |
|🔄 Pod ↔ Service | kube-proxy | iptables/IPVS |
|🌐 External ↔ Service | Ingress/LoadBalancer | Ingress Controller |
|🔍 DNS | CoreDNS | Service discovery (DNS lookup) |
|🔒 Security | NetworkPolicy | Labels |

---

# ⚡ Service Types Overview

| Type | Use Case |
|------|------|
| ClusterIP | Internal access |
| NodePort | External via node |
| LoadBalancer | Cloud external access |
| Ingress | HTTP routing |

---

# 🚀 Best Practices

✔ 🟢 Use CNI with NetworkPolicy support (Calico)
✔ 🌐 Use Ingress instead of NodePort
✔ 📊 Monitor latency & bandwidth 
✔ 🔒 Secure traffic using **NetworkPolicy or Service Mesh (mTLS)**  
✔ Use DNS instead of hardcoded IPs  

---

# 💡 Pro Tip

👉 In production:

- Use **Ingress + TLS**  
- Apply **NetworkPolicies**  
- Use **Service Mesh (Istio)** for observability  

---

# 🎯 Quick Summary

| Component | Role |
|------|------|
| Pod | Basic unit with IP |
| Service | Stable endpoint |
| CNI | Networking layer |
| kube-proxy | Routing |
| Ingress | External access |
| NetworkPolicy | Security |

---

# ⭐ Final Thought

Kubernetes networking is the backbone of:

✔ Microservices communication  
✔ Security  
✔ Scalability  

