# 🚨 Kubernetes Error: Service Not Accessible 
## 📌 What does "Service Not Accessible" mean?
 * A Kubernetes Service is used to `expose an application running inside pod`. If the service is not accessible, users or other applications cannot reach the application.
 * Common symptoms:
   * ❌ Unable to access the application
   * ❌ curl returns `Connection Refused`
   * ❌ Browser shows 𝟰𝟬𝟰, 𝟱𝟬𝟯, or 𝗧𝗶𝗺𝗲𝗼𝘂𝘁
   * ❌ LoadBalancer EXTERNAL-IP is not responding
  
## ☸️ Kubernetes Service Not Accessible — Common Reasons & Solutions
| 🚨 **Cause**                        | 📖 **Description**                                        | 🛠️ **Solution**                                                  | 💻 **Useful Commands**                                |
| ----------------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------- | ----------------------------------------------------- |
| 🎯 **Selector Mismatch**            | Service selector does not match Pod labels                 | Verify the Service selector and Pod labels                        | `kubectl get svc`<br>`kubectl get pods --show-labels` |
| 🔌 **Incorrect `targetPort`**       | `targetPort` does not match the container's listening port | Update `targetPort` to match the application's port               | `kubectl describe svc <service-name>`                 |
| 🚪 **Wrong Service Port**           | Service `port` is configured incorrectly                   | Verify the Service `port` and client connection port              | `kubectl get svc`                                     |
| 📦 **Pods Not Running / Not Ready** | Backend Pods are not running or not Ready                  | Fix Pod issues and ensure readiness probes succeed                | `kubectl get pods`                                    |
| 🌐 **Network Policy**               | NetworkPolicy blocks traffic to the Pods                   | Update NetworkPolicy to allow the required traffic                | `kubectl get networkpolicy`                           |
| 🔥 **Firewall / Security Group**    | External traffic to NodePort or LoadBalancer is blocked    | Open the required ports in the firewall or cloud security group   | Check firewall or cloud security group rules          |
| ⚖️ **No Endpoints**                 | Service has no backend Pods                                | Ensure Pods are running and labels match the Service selector     | `kubectl get endpoints <service-name>`                |
| 🧭 **DNS Resolution Issue**         | Service name cannot be resolved                            | Verify CoreDNS is healthy and use the correct Service name        | `kubectl get pods -n kube-system`                     |
| ☁️ **LoadBalancer Pending**         | External IP has not been assigned                          | Verify the cloud controller manager and LoadBalancer provisioning | `kubectl get svc`                                     |

## 🚀 Service Not Accessible Troubleshooting Flow
```hcl
Application Not Accessible
          ↓
kubectl get svc
          ↓
kubectl describe svc <service-name>
          ↓
Selector Matches Pod Labels?
          ↓
Yes
          ↓
Check Endpoints
          ↓
Endpoints Available?
          ↓
Yes
          ↓
Verify targetPort = containerPort
          ↓
Test Using BusyBox Pod
          ↓
Works Inside Cluster?
      ↓              ↓
     Yes             No
      ↓              ↓
Check Firewall,     Check Pods,
LoadBalancer,       Labels,
Ingress             Application Logs
          ↓
Application Accessible ✅
```

## 🔍 Useful Troubleshooting Commands
| 💻 **Command**                                             | 🎯 **Purpose**                                    |
| ----------------------------------------------------------- | ------------------------------------------------- |
| `kubectl get svc`                                           | View Service details                              |
| `kubectl describe svc <service-name>`                       | Inspect Service configuration                     |
| `kubectl get pods --show-labels`                            | Verify Pod labels                                 |
| `kubectl get endpoints <service-name>`                      | Check backend endpoints                           |
| `kubectl get pods`                                          | Verify Pod status                                 |
| `kubectl describe pod <pod-name>`                           | Check Pod readiness and events                    |
| `kubectl get networkpolicy`                                 | List NetworkPolicies                              |
| `kubectl exec -it <pod-name> -- curl <service-name>:<port>` | Test Service connectivity from inside the cluster |
| `kubectl get events --sort-by=.lastTimestamp`               | Review recent cluster events                      |
| `kubectl logs <pod-name>`                                   | Check application logs                            |
| `kubectl get networkpolicy`                                 | Check for network restrictions                    |

## 🎯 Interview Answer
### Q: How do you troubleshoot a Kubernetes Service that is not accessible?
 * Check the Service configuration using `kubectl describe svc`.
 * Verify that the Service selector matches the Pod labels.
 * Ensure the backend Pods are running and in the Ready state.
 * Confirm the Service has endpoints using `kubectl get endpoints`.
 * Verify the `port` and `targetPort` values.
 * Check for NetworkPolicy restrictions.
 * For NodePort or LoadBalancer Services, verify `firewall` or cloud `security group rules`.

## 🎯 Interview One-Liner
 * ☸️ A Kubernetes Service is usually inaccessible because of 𝘀𝗲𝗹𝗲𝗰𝘁𝗼𝗿 𝗺𝗶𝘀𝗺𝗮𝘁𝗰𝗵𝗲𝘀, 𝗶𝗻𝗰𝗼𝗿𝗿𝗲𝗰𝘁 𝘁𝗮𝗿𝗴𝗲𝘁𝗣𝗼𝗿𝘁 or 𝗽𝗼𝗿𝘁 𝗰𝗼𝗻𝗳𝗶𝗴𝘂𝗿𝗮𝘁𝗶𝗼𝗻, 𝘂𝗻𝗵𝗲𝗮𝗹𝘁𝗵𝘆 𝗯𝗮𝗰𝗸𝗲𝗻𝗱 𝗣𝗼𝗱𝘀, 𝗺𝗶𝘀𝘀𝗶𝗻𝗴 𝗲𝗻𝗱𝗽𝗼𝗶𝗻𝘁𝘀, 𝗡𝗲𝘁𝘄𝗼𝗿𝗸𝗣𝗼𝗹𝗶𝗰𝘆 𝗿𝗲𝘀𝘁𝗿𝗶𝗰𝘁𝗶𝗼𝗻𝘀, or 𝗯𝗹𝗼𝗰𝗸𝗲𝗱 𝗡𝗼𝗱𝗲𝗣𝗼𝗿𝘁/𝗟𝗼𝗮𝗱𝗕𝗮𝗹𝗮𝗻𝗰𝗲𝗿 𝘁𝗿𝗮𝗳𝗳𝗶𝗰.
 * 🚀 The first step is to inspect the Service with `kubectl describe svc` and verify its endpoints using `kubectl get endpoints`. 
