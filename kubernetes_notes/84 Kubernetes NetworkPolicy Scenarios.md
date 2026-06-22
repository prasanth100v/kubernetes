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
