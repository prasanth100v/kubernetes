# 🌐 Kubernetes Networking – Complete Guide (Extended)

---

## 🌟 What is the Pod Network?

The **Pod Network** refers to how Pods communicate with each other in a Kubernetes cluster.

Each Pod:
- Gets its own **unique IP address**
- Can communicate with any other Pod **directly**
- Does **NOT require NAT**

### 🔑 Important:
- Pod IPs are assigned by **CNI plugins**
- Pods behave like **VMs on a flat network**

---

## 🌍 What is the Cluster Network?

The **Cluster Network** is the internal communication backbone between:
- Pods
- Services
- Nodes

### 🎯 Purpose:
- Enable **secure communication**
- Provide **service discovery**
- Maintain **high performance networking**

---

## ⚙️ Who Manages the Cluster Network?

| Component       | Responsibility |
|----------------|---------------|
| **CNI Plugin** | Pod networking |
| **kube-proxy** | Service routing |
| **CoreDNS**    | DNS resolution |

---

## 🔌 What is a CNI Plugin?

CNI (Container Network Interface) plugins handle Pod networking.

### 🔹 Popular CNI Plugins:
- Calico (most used, supports NetworkPolicy)
- Cilium (eBPF-based, high performance)
- Flannel (simple networking)
- Amazon VPC CNI (used in EKS)

---

## 🔄 How CNI Works in Kubernetes

1. Pod is scheduled on a node  
2. Kubelet calls CNI plugin  
3. CNI creates network interface  
4. Assigns IP address  
5. Updates routing tables  

---

## 🔀 What is kube-proxy?

kube-proxy runs on each node and manages networking rules.

### 🧠 Responsibilities:
- Routes traffic to backend Pods
- Handles Service abstraction

### ⚡ Modes:
- iptables (simple)
- IPVS (scalable, faster)

---

## 🔐 What is a NetworkPolicy?

A **NetworkPolicy** acts like a firewall for Pods.

### Controls:
- Ingress traffic
- Egress traffic

### Based on:
- Labels
- Namespaces
- IP blocks
- Ports

---

## 🚦 Default Behavior

- All Pods can communicate with each other (no restrictions)

After applying NetworkPolicy:
- Only allowed traffic is permitted

---

## 🔒 Types of NetworkPolicies

| Type | Description |
|------|------------|
| Ingress | Incoming traffic control |
| Egress | Outgoing traffic control |
| Both | Full security |

---

## 🌐 Kubernetes DNS (CoreDNS)

CoreDNS resolves service names to IPs.

### Format:
<service-name>.<namespace>.svc.cluster.local

### Example:
my-service.default.svc.cluster.local → 10.96.25.3

---

## 🔄 Traffic Flow

User → LoadBalancer → Ingress → Service → Pod

---

## 🚫 Restrict Communication

Using NetworkPolicy:
- podSelector
- namespaceSelector
- ipBlock

---

## ☁️ EKS Networking

- Uses AWS VPC
- Pods get IPs from subnet
- No overlay network

---

## 🧠 Additional Deep Concepts

### Pod CIDR
Range of IPs assigned to Pods

### Service CIDR
Range of IPs assigned to Services

### NodePort
Expose service externally via node IP

### ClusterIP
Internal service communication

### LoadBalancer
External traffic via cloud provider

---

## 🧠 Interview Tips

- Pod networking = CNI
- Service routing = kube-proxy
- DNS = CoreDNS
- Security = NetworkPolicy

---

## 🔥 Advanced Notes

- IPVS is preferred for production
- Calico supports network policies
- Cilium uses eBPF (next-gen networking)
- NetworkPolicies require supported CNI

---

## 📌 Final Summary

- Pods → unique IP
- Services → stable access
- Ingress → external routing
- DNS → service discovery
- NetworkPolicy → security

---
