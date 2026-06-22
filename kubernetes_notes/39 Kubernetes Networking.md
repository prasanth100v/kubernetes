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

| 🧩 **Component**  | 🎯 **Responsibility** | 🧠 **How It Works**                                                              | 💡 **Real-World Insight**                     |
| ----------------- | --------------------- | ---------------------------------------------------------------------------------- | --------------------------------------------- |
| 🌐 **CNI Plugin** | Pod networking        | 👉 Assigns IP addresses to Pods and enables Pod-to-Pod communication across nodes | Examples: Calico, Cilium, Flannel             |
| 🔄 **kube-proxy** | Service routing       | 👉 Routes traffic from Service to Pods using `iptables or IPVS `                   | Ensures load balancing and stable access      |
| 🔍 **CoreDNS**    | DNS resolution        | 👉 Resolves `service names` to `IP addresses` in` cluster                          | Enables communication using DNS instead of IP |


## 🔌✨ What is a CNI Plugin?
 * CNI (Container Network Interface) plugins handle Pod networking.
 * ✨ Popular CNI Plugins:
     * Calico/WeaveNet (most used, supports NetworkPolicy)
     * Amazon VPC CNI  (used in `EKS` and supports NetworkPolicy. (version above 1.25))

## 🔄✨ How CNI Works in Kubernetes
* 1️⃣ Pod is scheduled on a node
* 2️⃣ Kubelet calls `CNI plugin `
* 3️⃣ CNI creates network interface
* 4️⃣ Assigns `IP address`
* 5️⃣ Updates `routing tables ` 

---

## 🔀✨ What is kube-proxy?
 * kube-proxy runs on each node and manages networking rules.
 * ✨ Responsibilities:
     - Routes traffic to backend Pods
     - Handles Service endpoint
     - kube-proxy translates: 👉 `Service IP → Pod IPs`
  * ⚡ Modes:
     - iptables (simple)
     - IPVS (scalable, faster)

---

## 🔐✨ What is a NetworkPolicy?
 * A **NetworkPolicy** acts like a firewall for Pods.
 * 🔒 Controls:
     - Ingress traffic
     - Egress traffic
  * 🎯 Based on:
      - Labels
      - Namespaces
      - IP blocks
      - Ports

## 🚦✨ Default Behavior
  - All Pods can communicate with each other (no restrictions)
  - After applying NetworkPolicy:
      - Only allowed traffic is permitted

---

## 🔒✨ Types of NetworkPolicies
| 🔐 **Type**    | 📖 **Description**                          | 🧠 **How It Works**                                                             | 💡 **Real-World Use Case**                  |
| -------------- | ------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------- |
| ⬇️ **Ingress** | Controls incoming traffic to Pods           | 👉 Defines **who can send traffic to a Pod** (based on labels, namespaces, IPs) | Allow only frontend → backend communication |
| ⬆️ **Egress**  | Controls outgoing traffic from Pods         | 👉 Defines **where a Pod can send traffic**                                     | Restrict backend from calling external APIs |
| 🔄 **Both**    | Controls both incoming and outgoing traffic | 👉 Combines Ingress + Egress rules for full security                            | `Zero-trust security model` in production    |

 * 🎯 Interview Tip :
      * NetworkPolicies act like a firewall for Pods.
      * Ingress controls `incoming traffic`, Egress controls `outgoing traffic`, and combining both ensures complete `network security`.
      * 💡 Egress Use Case  : Backend can only `talk to DB` → blocks everything else.
      * 💡 Ingress Use Case : Allow only `frontend → backend` communication
---

## 🌐✨ Kubernetes DNS (CoreDNS)
 * ☸️ CoreDNS is the `DNS server` in Kubernetes that provides service discovery by resolving `service names` such as `my-service.default.svc.cluster.local` into `Service ClusterIP` addresses
 * 🚀 Enabling Pods to communicate using `DNS names` instead of `IP addresses`.

| 🧩 **Concept**           | 💡 **Details**                                      | 📖 **Description**                           |
| ------------------------ | --------------------------------------------------- | -------------------------------------------- |
| 🌐 **Component**         | CoreDNS                                             | Default DNS server in Kubernetes             |
| 🎯 **Purpose**           | Service Discovery                                   | Resolves service names to IP addresses       |
| 📌 **DNS Format**        | `<service-name>.<namespace>.svc.cluster.local`      | Standard Kubernetes DNS naming convention    |
| 📦 **Example**           | `my-service.default.svc.cluster.local`              | Fully Qualified Domain Name (FQDN)           |
| 🔗 **Resolution**        | `my-service.default.svc.cluster.local → 10.96.25.3` | Converts DNS name to Service ClusterIP       |
| 🏠 **Short Name Access** | `my-service`                                        | Works within the same namespace              |
| 📂 **Namespace Access**  | `my-service.default`                                | Access service from another namespace        |
| ⚙️ **Runs As**           | Deployment in `kube-system` namespace               | Usually 2 CoreDNS Pods for high availability |

* 🔄 Traffic Flow : `User → LoadBalancer → Ingress → Service → Pod`
* 🚫 Restrict Communication
   * Using NetworkPolicy:
     - podSelector
     - namespaceSelector
     - ipBlock

## ☁️✨ EKS Networking
  - Uses AWS VPC
  - Pods get IPs from subnet
  - No overlay network

---

## 🧠✨ Additional Deep Concepts
| 🧩 Concept          | 💡 Description                                   |
| ------------------- | ------------------------------------------------ |
| 🔹 **Pod CIDR**     | 📦 Range of IPs assigned to Pods                 |
| 🔹 **Service CIDR** | 🔗 Range of IPs assigned to Services             |
| 🔹 **NodePort**     | 🌍 Exposes service externally via node IP + port |
| 🔹 **ClusterIP**    | 🔒 Internal communication within cluster         |
| 🔹 **LoadBalancer** | ⚖️ External access via cloud provider LB         |

## 🧠✨ Interview Tips
| 🧩 **Area**            | 💡 **Component**      | 📖 **Purpose**                | 🎯 **Responsibility**                   |
| ---------------------- | --------------------- | ----------------------------- | ----------------------------------------- |
| 📦 **Pod Networking**  | 🌐 **CNI**            | Connects Pods to the network  | Assigns Pod IPs, routing, and networking |
| 🔗 **Service Routing** | 🔀 **kube-proxy**     | Enables Service communication | Routes Service traffic to backend Pods   |
| 🌍 **DNS**             | 🧠 **CoreDNS**        | Service discovery             | Resolves Service names to IP addresses   |
| 🔐 **Security**        | 🛡️ **NetworkPolicy** | Controls Pod communication    | Allows or denies `ingress/egress traffic` |

## 🔥✨ Advanced Notes
 - IPVS is preferred for production
 - Calico/WeaveNet supports network policies
 - Cilium uses eBPF (Extended Berkeley Packet Filter) to provide `high-performance networking`, `security`, and `observability`. (next-gen networking)
 - NetworkPolicies require supported CNI

---

## 📌✨ Final Summary
| 🧩 Component     | 💡 Role                                  |
| ---------------- | ---------------------------------------- |
| 📦 Pods          | 🌐 Each Pod has a unique IP              |
| 🔗 Services      | 🎯 Provide stable access to Pods         |
| 🚪 Ingress       | 🌍 Handles external routing (HTTP/HTTPS) |
| 🧠 DNS           | 🔍 Enables service discovery (name → IP) |
| 🔐 NetworkPolicy | 🛡️ Controls traffic & security rules    |

## 🌐 Kubernetes Networking Debug Cheat Sheet 
```yaml
                                               # 🔍 Pod Checks
  kubectl get pods -o wide                        # 📌 Check Pod IPs and nodes
  kubectl get pods -A                             # 📌 Check all Pods status (all namespaces)
  kubectl get pods --show-labels                  # 📌 Verify Pod labels

                                                    # 🔗 Service Checks
  kubectl get svc                                     # 📌 List all Services

                                                 # 🔐 Network Policies
  kubectl get networkpolicy                         # 📌 List NetworkPolicies (current namespace)
  kubectl get networkpolicy -A                      # 📌 List all NetworkPolicies
  kubectl describe networkpolicy <np-name>          # 📌 Inspect policy rules

                                                   # ⚙️ CNI Plugin Checks
  kubectl get pods -n kube-system                    # 📌 Check CNI (calico/flannel/aws-node)
  kubectl logs -n kube-system <cni-pod>              # 📌 View CNI logs

                                                                  # 🌍 DNS / CoreDNS
  kubectl get pods -n kube-system -l k8s-app=kube-dns               # 📌 Check CoreDNS Pods
  kubectl exec -it <pod-name> -- nslookup <service-name>            # 📌 Test DNS resolution

                                                         # 🔎 Pod Details
  kubectl describe pod <pod-name>                           # 📌 Check Pod network settings

                                                       # 🔗 Connectivity Testing
  kubectl exec -it <pod-name> -- /bin/sh                  # 📌 Enter Pod shell
  ping <target-pod-ip>                                    # 📌 Test Pod-to-Pod connectivity
  curl <service-name>:<port>                              # 📌 Test Service access
  wget <service-name>:<port>                              # 📌 Alternative to curl

                                                    # 🖥️ Node Networking
  kubectl get nodes -o wide                           # 📌 Check node internal IPs
                                                       
  kubectl exec -it <pod-name> -- curl <service-name>:<port>              # 🧪 Service Testing from Pod
  kubectl exec -it <pod-name> -- wget <service-name>:<port>

                                                                         # 🔄 Port Forwarding (Local Test)
  kubectl port-forward svc/<service-name> 8080:<port>                    # 📌 Access service locally
```

## ☸️ Kubernetes Network Troubleshooting Tools Inside a Pod
| 🧰 **Tool**         | 📌 **Purpose**         | 🧠 **What It Checks**                                        | 💡 **Example Use Case**                    |
| ------------------- | ---------------------- | ------------------------------------------------------------ | ------------------------------------------ |
| 🌐 **curl**         | HTTP Testing           | Tests APIs endpoints, web applications, HTTP response, and headers | `curl http://my-service`                   |
| 📡 **ping**         | Connectivity Check     | Verifies basic network reachability using ICMP               | Check Pod-to-Pod connectivity              |
| 🛣️ **traceroute**  | Network Path Tracing   | Shows the route packets take to the destination              | Identify routing delays or network hops    |
| 🔍 **dig**          | DNS Query Tool         | Provides detailed DNS resolution information                 | `dig my-service.default.svc.cluster.local` |
| 🔎 **nslookup**     | DNS Lookup             | Simple DNS resolution verification                           |Verify service name resolves to IP  `nslookup my-service`    |
| 📊 **tcpdump**      | Packet Capture         | Captures and analyzes network packets                        | Debug network traffic issues               |
| 🔗 **wget**         | HTTP Download/Test     | Tests web endpoints and downloads content                    | `wget http://my-service`                   |
| 🌐 **netstat / ss** | Socket Inspection      | Displays listening ports and active connections              | Verify application ports                   |
| 🧪 **telnet / nc**  | Port Connectivity Test | Checks if a specific port is reachable                       | `nc -zv mysql-service 3306`                |

## 🚀 Common Kubernetes Debug Commands
| Problem                | Tool           | Example                                    |
| ---------------------- | -------------- | ------------------------------------------ |
| DNS Issue              | 🔎 nslookup    | `nslookup kubernetes.default`              |
| DNS Issue (Detailed)   | 🔍 dig         | `dig my-service.default.svc.cluster.local` |
| Service Not Responding | 🌐 curl        | `curl http://my-service`                   |
| Port Connectivity      | 🧪 nc          | `nc -zv my-service 80`                     |
| Network Reachability   | 📡 ping        | `ping 10.244.1.5`                          |
| Routing Issue          | 🛣️ traceroute | `traceroute google.com`                    |
| Packet Analysis        | 📊 tcpdump     | `tcpdump -i eth0`                          |


## ✨ Tips:
   * Check labels match between Pods & Services
   * Verify NetworkPolicies are not blocking traffic
   * Always test DNS before debugging connectivity
   * Check CNI health if networking fails
