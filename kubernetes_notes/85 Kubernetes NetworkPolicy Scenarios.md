# 🌐 NetworkPolicy in Kubernetes
### What is NetworkPolicy?
 * 🛡️ A NetworkPolicy is a Kubernetes resource used to `control network traffic between Pods`.
 * 👉 Similar to a `firewall` for Pods.
 * By default, Kubernetes allows:
    * Pod A  ---> Pod B ✅
    * Pod B  ---> Pod C ✅
    * Pod C  ---> Pod A ✅
 * With NetworkPolicy, you can restrict:
    * ➡️ Incoming traffic (`Ingress`)
    * ➡️ Outgoing traffic (`Egress`)

### ✅ Amazon VPC CNI
* Amazon VPC CNI supports Kubernetes `NetworkPolicy` in modern EKS versions.
* Modern EKS clusters using `recent Amazon VPC CNI versions` support NetworkPolicies natively.

| 🕒 **Period**                          | 🌐 **Networking Solution** | 🛡️ **NetworkPolicy Support**  |                      💡 **Notes**                                                  |
| -------------------------------------- | -------------------------- | ------------------------------- | ---------------------------------------------------------------------------------- |
| 📜 **Earlier Amazon VPC CNI Versions** | AWS VPC CNI                | ❌ Not Supported               | Kubernetes NetworkPolicy resources existed, but `AWS VPC CNI could not enforce them` |
| 🔄 **Calico + Amazon VPC CNI**         | AWS VPC CNI + Calico       | ✅ Supported via Calico        | `Calico` enforced NetworkPolicies while `AWS VPC CNI handled networking`           |
| 🚀 **Recent Amazon VPC CNI (EKS)**     | AWS VPC CNI                | ✅ Native Support              | AWS VPC CNI can now enforce Kubernetes NetworkPolicies `without requiring Calico`    |

### 🛡️ Without NetworkPolicy vs With NetworkPolicy
| 🧩 **Feature**                  | ❌ **Without NetworkPolicy**             | ✅ **With NetworkPolicy**                  |
| ------------------------------- | --------------------------------------- | -------------------------------------------- |
| 📦 **Pod Communication**        | Any Pod can communicate with any Pod    | Only explicitly allowed Pods can communicate |
| 🔐 **Security**                 | Less secure                             | More secure                                  |
| 🚦 **Traffic Control**          | No traffic control                      | Fine-grained ingress and egress control      |
| ⚠️ **Attack Surface**           | Higher risk of lateral movement attacks | Limits attack surface                        |
| 🏗️ **Microservices Isolation** | Difficult to enforce                    | Easy to enforce                              |
| 🗄️ **Database Protection**     | Any Pod may reach the database          | Only authorized Pods can access the database |
| 🏢 **Multi-Tenant Clusters**    | Poor isolation                          | Strong `namespace/workload` isolation          |
| 🎯 **Compliance & Governance**  | Harder to meet requirements             | Easier to enforce security policies          |
| 🛡️ **Zero Trust Model**         | Not possible                             | Supported                                    |

## 🛡️ Kubernetes NetworkPolicy Scenarios
| #   | 🎯 **Scenario**                 | 🔒 **Policy Goal**                            | 💡 **Interview Explanation**                                                             |
| --- | ------------------------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------------- |
| 1️⃣ | 🚫 **Deny All Traffic**         | Block all `ingress` and `egress `             |  Used as Baseline security policy. Pods cannot send or receive traffic unless explicitly allowed. |
| 2️⃣ | 🌐➡️⚙️ **Frontend → Backend**   | Only `frontend can access backend`            | Prevents unauthorized Pods from calling backend APIs.                                    |
| 3️⃣ | ⚙️➡️🗄️ **Backend → Database**  | Only backend can access database               | Protects databases from direct access by `frontend` or `other Pods`.                     |
| 4️⃣ | 🔓 **Allow Specific Port**      | Open only required ports (e.g., 8080)          | Reduces attack surface by `blocking unnecessary ports`.                                    |
| 5️⃣ | 🏢 **Namespace Isolation**      | Restrict communication between namespaces      | Useful for `multi-team` or `multi-tenant` clusters.                                          |
| 6️⃣ | 🌍 **Allow DNS Traffic**        | Permit UDP/TCP port 53 traffic                  | Required for Pods to resolve domain names using CoreDNS.                                 |
| 7️⃣ | ☁️ **Allow Internet Access**    | Permit outbound traffic to external services   | Applications can access `external APIs`, SaaS platforms, and `external databases`.          |
| 8️⃣ | 🔐 **Restrict Internet Access** | Block all outbound internet traffic            | Common for highly secure workloads handling `sensitive data`.                              |
| 9️⃣ | 📊 **Monitoring Access**        | Allow only monitoring tools (e.g., Prometheus) | Prevents unauthorized access to application metrics endpoints.                             |
| 🔟  | 🏗️ **Production 3-Tier App**   | Frontend → Backend → Database only             | Implements least-privilege communication in production. (`Real-world microservices security model`) |

---

### 🎯 Scenario 1: Deny All Traffic to a Namespace (No Pod should send or receive traffic.)
 * 🎯 Requirement :
 * No Pod in the production namespace should:
      * Receive traffic (Ingress) ❌
      * Send traffic (Egress) ❌
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: production
spec:
  podSelector: {}              # 👉 Select all Pods in the production namespace.
  policyTypes:
  - Ingress
  - Egress
```
| Policy Type | Action                     |
| ----------- | -------------------------- |
| Ingress     | 🚫 Block all incoming traffic |
| Egress      | 🚫 Block all outgoing traffic |

#### Result:
|      Source  = Destination      | Status    |
| ------------------------------- | --------- |
| frontend    ─────►  backend     | ❌ Blocked |
| backend     ─────►  mysql       | ❌ Blocked |
| frontend    ─────►  Internet    | ❌ Blocked |
| DNS Server  ─────►  frontend    | ❌ Blocked |

### Why use a Deny-All NetworkPolicy?
 * 🛡️ To implement a `Zero Trust Security Model`.
 * 🚫 First `block all traffic`, then explicitly allow only the required communication between Pods..

---

### ✅ Scenario 2: Allow Traffic Only from Frontend to Backend
 * 🎯 Requirement :
     * Only the `Frontend Pod` should be able to communicate with the `Backend Pod`.
     * All other Pods must be `denied access`.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend            # ✅ This policy applies only to: Backend Pod 

  policyTypes:
  - Ingress                   # ✅ Controls incoming traffic to Backend Pods.

  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend       # ✅ Allow traffic only from Pods with: app: frontend
```
Result Table :
| Source Pod    | Label          | Backend Access |
| ------------- | -------------- | -------------- |
| Frontend      | app=frontend   | ✅ Allowed      |
| Database      | app=mysql      | ❌ Denied       |
| Prometheus    | app=prometheus | ❌ Denied       |
| Any Other Pod | Any Label      | ❌ Denied       |

 * 👉 This NetworkPolicy selects backend Pods and `allows ingress traffic only` from Pods labeled `app=frontend`; all other Pods are `denied access` to the backend.

---



