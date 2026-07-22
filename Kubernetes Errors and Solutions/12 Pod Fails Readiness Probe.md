# 🚨 Kubernetes Error: Pod Fails Readiness Probe
## 📌 What is a Readiness Probe?
 * A Readiness Probe tells Kubernetes whether a container is ready to receive traffic.
 * ✅ If the readiness probe succeeds, the pod is added to the 𝗦𝗲𝗿𝘃𝗶𝗰𝗲 𝗲𝗻𝗱𝗽𝗼𝗶𝗻𝘁𝘀 and 𝘀𝘁𝗮𝗿𝘁𝘀 𝗿𝗲𝗰𝗲𝗶𝘃𝗶𝗻𝗴 𝘁𝗿𝗮𝗳𝗳𝗶𝗰.
 * ❌ If the readiness probe fails, the pod continues running but does not receive any traffic.
 * Unlike a Liveness Probe, 𝙖 𝙛𝙖𝙞𝙡𝙚𝙙 𝙍𝙚𝙖𝙙𝙞𝙣𝙚𝙨𝙨 𝙋𝙧𝙤𝙗𝙚 𝙙𝙤𝙚𝙨 𝙣𝙤𝙩 𝙧𝙚𝙨𝙩𝙖𝙧𝙩 𝙩𝙝𝙚 𝙘𝙤𝙣𝙩𝙖𝙞𝙣𝙚𝙧.
 * Example:
   ```hcl
   READY   STATUS    RESTARTS
   0/1     Running   0             # The pod is Running, but not Ready.
   ```

## 🚀 Readiness Probe Troubleshooting Flow
```hcl
Pod Running but Not Ready
          ↓
kubectl describe pod
          ↓
Readiness Probe Failed?
          ↓
Check Probe Path & Port
          ↓
Test Endpoint Inside Pod
          ↓
Endpoint Responds with HTTP 200?
      ↓               ↓
     Yes              No
      ↓               ↓
Adjust Timing     Fix Application/
Parameters        Endpoint Configuration
          ↓
Check Service Endpoints
          ↓
Pod Becomes Ready ✅
```

## ☸️ Kubernetes Readiness Probe Failure — Common Reasons & Solutions
| 🚨 **Cause**                            | 📖 **Description**                                             | 🛠️ **Solution**                                                  | 💻 **Useful Commands**                                  |
| --------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------- | -------------------------------------------------------- |
| 🌐 **Incorrect Endpoint**               | Probe path (for example, `/health` or `/ready`) does not exist | Configure the correct readiness endpoint                          | `kubectl describe pod <pod-name>`                        |
| 🔌 **Wrong Port**                       | Probe checks the wrong container port                          | Match the readiness probe port with the application port          | `kubectl get pod <pod-name> -o yaml`                     |
| ⏳ **Application Still Starting**        | Application is not ready when the probe begins                 | Increase `initialDelaySeconds` or use a `startupProbe`            | `kubectl describe pod <pod-name>`                       |
| ⚙️ **Slow Initialization**              | Database, cache, or external service takes time to connect     | Increase `timeoutSeconds`, `periodSeconds`, or `failureThreshold` | Review application startup logs                          |
| ❌ **Application Error**                 | Application returns HTTP 500 or crashes internally             | Check application logs and fix the underlying issue               | `kubectl logs <pod-name>`                               |
| 🔒 **Dependency Unavailable**           | Database, Redis, Kafka, or external API is unavailable         | Ensure dependent services are healthy and reachable               | Test connectivity to dependencies                        |
| 📦 **Container Not Listening**          | Application is not listening on the configured port            | Verify the application is listening on the expected port          | `kubectl exec -it <pod-name> -- netstat -tuln`           |
| 🌍 **DNS / Service Resolution Failure** | Application cannot resolve service names                       | Verify CoreDNS and Kubernetes Services                            | `kubectl exec -it <pod-name> -- nslookup <service-name>` |

## 🔍 Useful Troubleshooting Commands
| 💻 Command                                             | 🎯 Purpose                               |
| ------------------------------------------------------ | ---------------------------------------- |
| `kubectl describe pod <pod-name>`                      | View readiness probe failures            |
| `kubectl logs <pod-name>`                              | Check application logs                   |
| `kubectl exec -it <pod-name> -- /bin/sh`               | Access the container                     |
| `curl http://localhost:<port>/<path>`                  | Test the readiness endpoint              |
| `kubectl get endpoints`                                | Verify Service endpoints                 |
| `kubectl edit deployment <deployment-name>`            | Update readiness probe settings          |
| `kubectl rollout restart deployment <deployment-name>` | Restart pods after configuration changes |
| `kubectl logs <pod-name> --previous`                          | View logs from the previous container                    |
| `kubectl get pod <pod-name> -o yaml`                          | Inspect readiness probe configuration                    |
| `kubectl exec -it <pod-name> -- curl localhost:<port>/<path>` | Test the readiness endpoint from inside the container    |
| `kubectl exec -it <pod-name> -- netstat -tuln`                | Verify the application is listening on the expected port |
| `kubectl get events --sort-by=.lastTimestamp`                 | Review recent cluster events                             |


## 🎯 How do you troubleshoot a Readiness Probe failure?
 * Inspect the Pod events using 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝗼𝗱.
 * Verify the 𝙧𝙚𝙖𝙙𝙞𝙣𝙚𝙨𝙨 𝙚𝙣𝙙𝙥𝙤𝙞𝙣𝙩 and 𝙘𝙤𝙣𝙩𝙖𝙞𝙣𝙚𝙧 𝙥𝙤𝙧𝙩.
 * Ensure the application has enough startup time.
 * Check application logs for 𝙃𝙏𝙏𝙋 500 𝙚𝙧𝙧𝙤𝙧𝙨 or 𝙘𝙧𝙖𝙨𝙝𝙚𝙨.
 * Verify database and external service connectivity.
 * Tune probe parameters such as 𝗶𝗻𝗶𝘁𝗶𝗮𝗹𝗗𝗲𝗹𝗮𝘆𝗦𝗲𝗰𝗼𝗻𝗱𝘀, 𝘁𝗶𝗺𝗲𝗼𝘂𝘁𝗦𝗲𝗰𝗼𝗻𝗱𝘀, and 𝗳𝗮𝗶𝗹𝘂𝗿𝗲𝗧𝗵𝗿𝗲𝘀𝗵𝗼𝗹𝗱.
 * Consider using a 𝙨𝙩𝙖𝙧𝙩𝙪𝙥𝙋𝙧𝙤𝙗𝙚 𝙛𝙤𝙧 𝙖𝙥𝙥𝙡𝙞𝙘𝙖𝙩𝙞𝙤𝙣𝙨 𝙬𝙞𝙩𝙝 𝙨𝙡𝙤𝙬 𝙨𝙩𝙖𝙧𝙩𝙪𝙥 𝙩𝙞𝙢𝙚𝙨.

## 🎯 Interview Answer
 * ☸️ A Readiness Probe failure means Kubernetes considers the application not ready to receive traffic.
 * 🚀 Common causes include an incorrect endpoint or port, slow application startup, internal application errors, unavailable dependencies, or DNS/service resolution issues.
 * 🚀 The first troubleshooting step is to inspect the Pod with 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝗼𝗱 and 𝙫𝙚𝙧𝙞𝙛𝙮 𝙩𝙝𝙚 𝙧𝙚𝙖𝙙𝙞𝙣𝙚𝙨𝙨 𝙚𝙣𝙙𝙥𝙤𝙞𝙣𝙩 and 𝙖𝙥𝙥𝙡𝙞𝙘𝙖𝙩𝙞𝙤𝙣 𝙡𝙤𝙜𝙨.
