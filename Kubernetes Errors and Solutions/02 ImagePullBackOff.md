# ☸️ Kubernetes Error 2: ImagePullBackOff
### 🚨 What is ImagePullBackOff?
 * ImagePullBackOff means Kubernetes tried to `download the container image from the container registry but failed`.
 * The kubelet keeps retrying with increasing delays (`backoff`), so the Pod remains in the `ImagePullBackOff state`.
 ```hcl
 Developer
     │
     ▼
 Deployment Created
     │
     ▼
 Scheduler assigns Pod to Node
     │
     ▼
 Kubelet tries to pull Docker image
     │
     ▼
 Pull Successful? ───► Yes ───► Container Starts
        │
        ▼
       No
        │
        ▼
 ImagePullBackOff
 ```

## ☸️ Kubernetes ImagePullBackOff — Common Causes
| 🚨 **Cause**                | 📖 **Description**                          | 🔍 **How to Verify**                             | 🛠️ **Solution**                                        |
| --------------------------- | ------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------- |
| 🏷️ **Wrong Image Name**    | Image name is incorrect                     | Check the Deployment/Pod YAML                    | Correct the image name                                  |
| 🔖 **Wrong Tag**            | Specified tag doesn't exist                 | Verify the image tag in the registry             | Use a valid image tag                                   |
| 🔐 **Private Registry**     | Authentication is missing                   | Check `imagePullSecrets` or registry credentials | Configure `imagePullSecrets` or registry authentication |
| 🗑️ **Image Deleted**       | Image no longer exists in the registry      | Verify the repository                            | Push or restore the image                               |
| 🌐 **Network Issue**        | Worker node cannot reach the registry       | Test internet/DNS connectivity                   | Fix networking or DNS issues                            |
| 🚫 **Registry Unavailable** | Docker Hub, ECR, ACR, or GCR is unavailable | Check registry status                            | Retry after the service is restored                     |
| ⏳ **Rate Limit**            | Docker Hub anonymous pull limit exceeded    | Check Pod events                                 | Authenticate to Docker Hub or use another registry      |

---

## ☸️ Kubernetes ImagePullBackOff — Common Error Messages & Solutions
| 🚨 **Error Message**        | 🔍 **Cause**                                  | 🛠️ **Solution**                                             | 💡 **Interview Tip**                                  |
| --------------------------- | --------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| `manifest unknown`          | Image tag does not exist                      | Use a valid image tag                                        | Verify the tag exists in Docker Hub, ECR, ACR, or GCR |
| `pull access denied`        | Repository is private or inaccessible         | Configure `imagePullSecrets` or authenticate to the registry | Common when pulling private images                    |
| `unauthorized`              | Invalid or expired registry credentials       | Recreate or update the Docker registry secret                | Check username, password, or access token             |
| `repository does not exist` | Incorrect repository or image name            | Correct the image name                                       | Verify the repository path                            |
| `dial tcp: i/o timeout`     | Network connectivity issue                    | Check internet access, DNS, proxy, firewall, or VPC routing  | Verify the node can reach the registry                |
| `TLS handshake timeout`     | Registry connection timeout                   | Retry later and verify network connectivity                  | Often caused by slow or unstable networks             |
| `Back-off pulling image`    | Kubernetes is repeatedly retrying image pulls | Fix the root cause, then restart or recreate the Pod         | This is a symptom, not the root cause                 |
| `ErrImagePull`              | Initial image pull failed                     | Inspect the detailed error message in Pod events             | Usually appears before `ImagePullBackOff`             |

---

## 🔍 Useful Commands
| 💻 **Command**                               | 🎯 **Purpose**                          |
| --------------------------------------------- | --------------------------------------- |
| `kubectl describe pod <pod-name>`             | View Pod events and image pull errors   |
| `kubectl get events --sort-by=.lastTimestamp` | Display recent cluster events           |
| `kubectl get secret`                          | Verify registry secrets exist           |
| `kubectl describe secret <secret-name>`       | Inspect image pull secret details       |
| `kubectl delete pod <pod-name>`               | Recreate the Pod after fixing the issue |

---

## 🎯 Interview Approach
 * When asked "How do you troubleshoot ImagePullBackOff?", explain this order:
   * ✅ Check the Pod events using `kubectl describe pod`.
   * ✅ Verify the `image name` and `tag`.
   * ✅ Confirm registry authentication (`imagePullSecrets for private registries`).
   * ✅ Check whether the image `exists in the registry`.
   * ✅ Verify node connectivity to the registry.
   * ✅ Check if the registry is available or if `rate limits` have been reached.

## 🎯 Interview One-Liner
 * ImagePullBackOff occurs when Kubernetes cannot pull a container image.
 * The most common causes are an `incorrect image name` or `tag`, missing authentication for a `private registry`, a `deleted image`, network connectivity issues, registry outages, or registry rate limits (ex., `200 pulls per 6 hours`.).
 * The first troubleshooting step is to `inspect the Pod events` using `kubectl describe pod`. ☸️🐳🚀
