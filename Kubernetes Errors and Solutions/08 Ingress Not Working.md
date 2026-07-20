# 🚨 Kubernetes Error: Ingress Not Working
## 📌 What does "Ingress Not Working" mean?
 * An Ingress manages external 𝗛𝗧𝗧𝗣/𝗛𝗧𝗧𝗣𝗦 𝗮𝗰𝗰𝗲𝘀𝘀 to services in a Kubernetes cluster. If the Ingress is not working, traffic from users does not reach the backend application.
 * Common symptoms:
   * ❌ Website returns 𝟒𝟎𝟒 Not Found
   * ❌ 𝟓𝟎𝟐 Bad Gateway
   * ❌ 𝟓𝟎𝟑 Service Unavailable
   * ❌ Connection Timed Out
   * ❌ Domain name is not accessible

## ✅ Check the Ingress Resource
```hcl
kubectl get ingress
---
NAME            CLASS   HOSTS             ADDRESS
nginx-ingress   nginx   example.com       35.201.xx.xx
```

## 🚀 Ingress Not Working Troubleshooting Flow
```hcl
Ingress Not Accessible
          ↓
kubectl get ingress
          ↓
kubectl describe ingress
          ↓
Ingress Controller Running?
      ↓            ↓
     Yes           No
      ↓            ↓
Check Backend     Start/Restart
Service           Controller
      ↓
Service Exists?
      ↓
Endpoints Available?
      ↓
DNS Points to Ingress IP?
      ↓
TLS Secret Valid?
      ↓
Application Accessible ✅
```

## ☸️ Kubernetes Ingress Not Working — Common Reasons & Solutions
| 🚨 **Cause**                            | 📖 **Description**                                                   | 🛠️ **Solution**                                                   | 💻 **Useful Commands**                    |
| --------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------- |
| 🚦 **Ingress Controller Not Running**   | NGINX, Traefik, HAProxy, or another Ingress controller is unavailable | Verify the Ingress controller Pods are running and healthy         | `kubectl get pods -n ingress-nginx`       |
| 📝 **Incorrect Ingress Rules**          | Host, path, or backend configuration is incorrect                     | Review the Ingress rules and correct the host, path, or path type  | `kubectl describe ingress <ingress-name>` |
| 🎯 **Backend Service Misconfiguration** | Service name or port is incorrect                                     | Verify the Service exists and the port matches the Ingress backend | `kubectl get svc`                         |
| 🌐 **DNS Misconfiguration**             | Domain does not resolve to the Ingress external IP                    | Update the DNS record to point to the LoadBalancer or Ingress IP   | `nslookup <domain>`<br>`dig <domain>`     |
| 🔒 **TLS / Certificate Issues**         | TLS Secret is missing, invalid, or expired                            | Verify the TLS Secret and certificate configuration                | `kubectl describe secret <tls-secret>`    |
| ⚖️ **No Service Endpoints**             | Backend Service has no healthy Pods                                   | Ensure Pods are running, Ready, and selected by the Service        | `kubectl get endpoints <service-name>`    |
| 🚫 **Network Policy / Firewall**        | Traffic is blocked by NetworkPolicy or firewall rules                 | Allow HTTP/HTTPS traffic and update NetworkPolicies                | `kubectl get networkpolicy`               |
| 🏷️ **Wrong Ingress Class**              | Ingress is using an incorrect or missing `ingressClassName`           | Set the correct Ingress class supported by the controller          | `kubectl get ingressclass`                |
| 📦 **Pods Not Ready**                   | Backend Pods are failing readiness checks                             | Fix Pod health issues so endpoints become available                | `kubectl get pods`                        |

## 🔍 Useful Troubleshooting Commands
| 💻 Command                                       | 🎯 Purpose                      |
| ------------------------------------------------ | ------------------------------- |
| `kubectl get ingress`                            | List Ingress resources          |
| `kubectl describe ingress <ingress-name>`        | View Ingress rules and events   |
| `kubectl get pods -n ingress-nginx`              | Check Ingress controller status |
| `kubectl logs -n ingress-nginx <controller-pod>` | View controller logs            |
| `kubectl get svc`                                | Verify backend services         |
| `kubectl describe svc <service-name>`            | Verify service configuration    |
| `kubectl get secret`                             | Verify TLS secrets              |
| `kubectl describe secret <tls-secret>`           | Inspect TLS certificate Secret      |
| `kubectl get ingressclass`                       | Verify available Ingress classes    |
| `kubectl get svc`                                | Check backend Service configuration |
| `kubectl get endpoints <service-name>`           | Verify backend endpoints            |
| `kubectl logs -n ingress-nginx <controller-pod>` | Review controller logs              |
| `nslookup <domain>` / `dig <domain>`             | Verify DNS resolution               |

## 🎯 Interview Answer
### Q: How do you troubleshoot an Ingress that is not working?
 * Verify the Ingress controller is running.
 * Inspect the Ingress resource using 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙞𝙣𝙜𝙧𝙚𝙨𝙨.
 * Check that the backend `Service name` and `port` are correct.
 * Ensure the Service has healthy endpoints.
 * Verify DNS points to the 𝙄𝙣𝙜𝙧𝙚𝙨𝙨 𝙚𝙭𝙩𝙚𝙧𝙣𝙖𝙡 𝙄𝙋 or 𝙇𝙤𝙖𝙙𝘽𝙖𝙡𝙖𝙣𝙘𝙚𝙧.
 * Validate the `TLS Secret` and `certificate` configuration.
 * Check for `NetworkPolicy` or `firewall rules` blocking `HTTP/HTTPS traffic`.

## 🎯 Interview One-Liner
 * ☸️ An Ingress usually fails because the Ingress controller is unavailable, the Ingress rules are incorrect, the backend Service or endpoints are misconfigured, DNS does not resolve to the Ingress IP, TLS is invalid, or traffic is blocked by NetworkPolicies or firewalls.
 * 🚀 The first troubleshooting step is to inspect the Ingress using 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙞𝙣𝙜𝙧𝙚𝙨𝙨 and verify the controller, Service, and endpoints. 

