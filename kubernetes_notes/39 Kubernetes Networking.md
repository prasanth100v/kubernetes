# 🌐 Kubernetes Networking
## ✨ What is the Pod Network?

 * The **Pod Network** refers to how Pods communicate with each other in a Kubernetes cluster.
 * Each Pod:
     - Gets its own **unique IP address**
     - Can communicate with any other Pod **directly**
     - Does **NOT require NAT** (Network Address Translation)

### 🔑✨ Important:
   - Pod IPs are assigned by **CNI plugins**
   - Pods behave like **VMs on a flat network**

## 🌍✨ What is the Cluster Network?

  * The **Cluster Network** is the internal communication backbone between:
    - Pods
    - Services
    - Nodes
  * ✨Purpose:
    - Enable **secure communication**
    - Provide **service discovery**
    - Maintain **high performance networking**

## ⚙️✨ Who Manages the Cluster Network?

| 🧩 **Component**  | 🎯 **Responsibility** | 🧠 **How It Works**                                                               | 💡 **Real-World Insight**                     |
| ----------------- | --------------------- | --------------------------------------------------------------------------------- | --------------------------------------------- |
| 🌐 **CNI Plugin** | Pod networking        | 👉 Assigns IP addresses to Pods and enables Pod-to-Pod communication across nodes | Examples: Calico, Cilium, Flannel             |
| 🔄 **kube-proxy** | Service routing       | 👉 Routes traffic from Service to Pods using `iptables or IPVS `                   | Ensures load balancing and stable access      |
| 🔍 **CoreDNS**    | DNS resolution        | 👉 Resolves `service names` to `IP addresses` in` cluster                          | Enables communication using DNS instead of IP |


---

## 🔌✨ What is a CNI Plugin?

CNI (Container Network Interface) plugins handle Pod networking.

### 🔹✨ Popular CNI Plugins:
- Calico (most used, supports NetworkPolicy)
- Cilium (eBPF-based, high performance)
- Flannel (simple networking)
- Amazon VPC CNI (used in EKS)

---

## 🔄✨ How CNI Works in Kubernetes

1️⃣ Pod is scheduled on a node  
2️⃣ Kubelet calls CNI plugin  
3️⃣ CNI creates network interface  
4️⃣ Assigns IP address  
5️⃣ Updates routing tables  

---

## 🔀✨ What is kube-proxy?

kube-proxy runs on each node and manages networking rules.

### 🧠✨ Responsibilities:
- Routes traffic to backend Pods
- Handles Service abstraction

### ⚡✨ Modes:
- iptables (simple)
- IPVS (scalable, faster)

---

## 🔐✨ What is a NetworkPolicy?

A **NetworkPolicy** acts like a firewall for Pods.

### 🔒 Controls:
- Ingress traffic
- Egress traffic

### 🎯 Based on:
- Labels
- Namespaces
- IP blocks
- Ports

---

## 🚦✨ Default Behavior

- All Pods can communicate with each other (no restrictions)

After applying NetworkPolicy:
- Only allowed traffic is permitted

---

## 🔒✨ Types of NetworkPolicies

| Type | Description |
|------|------------|
| Ingress | Incoming traffic control |
| Egress | Outgoing traffic control |
| Both | Full security |

---

## 🌐✨ Kubernetes DNS (CoreDNS)

CoreDNS resolves service names to IPs.

### 📌 Format:
<service-name>.<namespace>.svc.cluster.local

### 📌✨ Example:
my-service.default.svc.cluster.local → 10.96.25.3

---

## 🔄✨ Traffic Flow

User → LoadBalancer → Ingress → Service → Pod

---

## 🚫✨ Restrict Communication

Using NetworkPolicy:
- podSelector
- namespaceSelector
- ipBlock

---

## ☁️✨ EKS Networking

- Uses AWS VPC
- Pods get IPs from subnet
- No overlay network

---

## 🧠✨ Additional Deep Concepts

### 🔹 Pod CIDR
Range of IPs assigned to Pods

### 🔹 Service CIDR
Range of IPs assigned to Services

### 🔹 NodePort
Expose service externally via node IP

### 🔹 ClusterIP
Internal service communication

### 🔹 LoadBalancer
External traffic via cloud provider

---

## 🧠✨ Interview Tips

- Pod networking = CNI
- Service routing = kube-proxy
- DNS = CoreDNS
- Security = NetworkPolicy

---

## 🔥✨ Advanced Notes

- IPVS is preferred for production
- Calico supports network policies
- Cilium uses eBPF (next-gen networking)
- NetworkPolicies require supported CNI

---

## 📌✨ Final Summary

- Pods → unique IP
- Services → stable access
- Ingress → external routing
- DNS → service discovery
- NetworkPolicy → security

---

# 🌐✨ Kubernetes Networking Debug Cheat Sheet ✨🌐

commands:
```
  # 🔍 Pod Checks
  - kubectl get pods -o wide        # 📌 Check Pod IPs and nodes
  - kubectl get pods -A             # 📌 Check all Pods status (all namespaces)
  - kubectl get pods --show-labels  # 📌 Verify Pod labels

  # 🔗 Service Checks
  - kubectl get svc                 # 📌 List all Services

  # 🔐 Network Policies
  - kubectl get networkpolicy       # 📌 List NetworkPolicies (current namespace)
  - kubectl get networkpolicy -A    # 📌 List all NetworkPolicies
  - kubectl describe networkpolicy <np-name>  # 📌 Inspect policy rules

  # ⚙️ CNI Plugin Checks
  - kubectl get pods -n kube-system # 📌 Check CNI (calico/flannel/aws-node)
  - kubectl logs -n kube-system <cni-pod>  # 📌 View CNI logs

  # 🌍 DNS / CoreDNS
  - kubectl get pods -n kube-system -l k8s-app=kube-dns  # 📌 Check CoreDNS Pods
  - kubectl exec -it <pod-name> -- nslookup <service-name>  # 📌 Test DNS resolution

  # 🔎 Pod Details
  - kubectl describe pod <pod-name>  # 📌 Check Pod network settings

  # 🔗 Connectivity Testing
  - kubectl exec -it <pod-name> -- /bin/sh  # 📌 Enter Pod shell
  - ping <target-pod-ip>                   # 📌 Test Pod-to-Pod connectivity
  - curl <service-name>:<port>             # 📌 Test Service access
  - wget <service-name>:<port>             # 📌 Alternative to curl

  # 🖥️ Node Networking
  - kubectl get nodes -o wide  # 📌 Check node internal IPs

  # 🧪 Service Testing from Pod
  - kubectl exec -it <pod-name> -- curl <service-name>:<port>
  - kubectl exec -it <pod-name> -- wget <service-name>:<port>

  # 🔄 Port Forwarding (Local Test)
  - kubectl port-forward svc/<service-name> 8080:<port>  # 📌 Access service locally
```
  # 🛠️ Tools inside Pod
  - curl        # 📌 HTTP testing
  - ping        # 📌 Connectivity check
  - traceroute  # 📌 Network path
  - dig         # 📌 DNS query
  - nslookup    # 📌 DNS lookup
  - tcpdump     # 📌 Packet capture

# ✅✨ Tips:
-  ✔ Check labels match between Pods & Services
-  ✔ Verify NetworkPolicies are not blocking traffic
-  ✔ Always test DNS before debugging connectivity
-  ✔ Check CNI health if networking fails

  
