# What is a Kubernetes-Native API Gateway?
 * ☸️ A Kubernetes-Native API Gateway is a traffic management layer that acts as a `single entry point for applications running in Kubernetes`.
 * 🛡️ Modern Kubernetes environments commonly use `Gateway API implementations` such as `Istio`, `Kong` and `NGINX Gateway Fabric` to manage external traffic efficiently.

### What additional features does Istio Gateway API provide beyond routing?
 * ⚡ Istio Gateway API provides `advanced traffic management` features such as `path` and `host-basedrouting`, `SSL/TLS` termination, `JWT authentication`, `authorization policies`, `rate limiting`, `load balancing`, `traffic splitting` for canary deployments.
 * 💥 Observability through `Prometheus/Grafana/Jaeger/Kiali`.
 * 🛠️ Istio Gateway API supports resiliency features such as `retries`, `timeouts`, `circuit breaking`, and `fault injection`. These features improve application reliability.
 * 🎯 It acts as an `enterprise-grade API gateway` for Kubernetes applications...

### 🚀 Istio Gateway API Features
| 🧩 **Feature**               | 📖 **Description**                                                           | 🎯 **Use Case**                                        |
| ---------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------ |
| 🌐 **Routing**               | Route traffic based on `hostnames`, `paths`,` headers`                      | `Host-based`, `path-based`, and `header-based` routing       |
| 🔐 **Authentication**        | Validate `JWT tokens` and integrate with `OAuth/OIDC` providers             | User authentication and API security                   |
| 🛡️ **Authorization**         | Allow or deny requests using `security policies`                             | RBAC and access control                                |
| 🚦 **Rate Limiting**         | Restrict `requests per user`, `IP`, `API key`, or `service `                | Protect APIs from abuse and `excessive traffic`          |
| 🔒 **SSL/TLS Termination**   | Terminate HTTPS at the gateway and forward traffic internally                | Secure external communication                          |
| ⚖️ **Load Balancing**        | Distribute traffic across multiple pods or services                          | High availability and scalability                      |
| 🔄 **Traffic Splitting**     | Support canary deployments, blue-green deployments, and A/B testing          | Safer application releases                             |
| 📊 **Observability**         | Provides `metrics`, `logs`, and `distributed tracing `                     | Monitoring with `Prometheus`, `Grafana`, `Jaeger`, and `Kiali` |
| 🛠️ **Resiliency**           | Supports `retries`, `timeouts`, `circuit breaking`, and `fault injection `   | Improve application reliability                        |
| 🌍 **Multi-Cluster Support** | Route traffic across multiple Kubernetes clusters                            | Disaster recovery and global applications              |

### 🚀 Istio Resiliency Features
| 🧩 **Feature**         | 🎯 **Purpose**                                | 📖 **Description**                                                    | 💡 **Example**                                                                     |
| ---------------------- | --------------------------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 🔄 **Retries**         | Automatically retry failed requests           | Envoy retries requests when transient (temporary) failures occur       | Retry 3 times if service returns **503 Service Unavailable**                          |
| ⏱️ **Timeouts**        | Stop waiting after a `specified duration` (Fail request after **5 seconds**) | timeout specifies the `maximum amount of time` Istio will wait for a response from a backend service.| Istio terminates the request and returns an `error`. |
| ⚡ **Circuit Breaking** | Prevent sending traffic to unhealthy services | Stops requests when a service becomes overloaded or unhealthy | Istio Limiting the maximum number of connections ( `maxConnections` ) and the number of requests per connection  |
| 💥 **Fault Injection** | Simulate failures for testing                 |  Tests application `resilience` and `error handling`       | Inject **5-second delay** or **HTTP 500 errors** for how applications behave under failure conditions.  |

---

## 🚀 Gateway API Deployment Strategies
 * Gateway API can be used to implement `modern deployment strategies` such as:
    * ✅ Rolling Update
    * ✅ Canary Deployment
    * ✅ Blue-Green Deployment
    * ✅ Traffic Splitting (`A/B Testing`)

## 🚦 What is Traffic Splitting (A/B Testing) in Gateway API ?
 * 💡 Traffic splitting allows you to send `different percentages of traffic` to different backend services (Versions) .
  * 🚀 Example: `90%` Traffic to `v1`, `10%` Traffic to `v2`
  * HTTPRoute YAML
  ```yaml
  apiVersion: gateway.networking.k8s.io/v1
  kind: HTTPRoute
  metadata:
    name: app-route
  spec:
    parentRefs:
    - name: my-gateway
    rules:
    - backendRefs:
      - name: app-v1-service
        port: 80
        weight: 90                  # 90% ---> app-v1-service
      - name: app-v2-service
        port: 80
        weight: 10                  # 10% ---> app-v2-service
   ```
  * 🎯 Services
  * For `🟦🟩 Blue-Green` and `🐤 Canary deployments` in Gateway API, you typically need 2 Services because each version of the application should have its `own Service`.
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: app-v1-service
  spec:
    selector:
      version: v1
    ports:
    - port: 80
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: app-v2-service
  spec:
    selector:
      version: v2
    ports:
    - port: 80
  ```

### 🚀 Gateway API Deployment Strategies
| 🧩 **Deployment Type**         | 🎯 **One-Line Answer**                                                                | 💡 **Traffic Example**          | ✅ **Best Use Case**      |
| ------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------- | ------------------------ |
| 🚦 **Traffic Splitting / A/B Testing** | Distributes traffic across multiple backend services (versions) using `backendRefs.weight`. | 50% → v1 + 50% → v2          | A/B Testing  ⭐⭐⭐   |
| 🟦🟩 **Blue-Green Deployment** | Maintains two environments and switches 100% traffic from old version to new version. | 100% Blue → 100% Green          | Major Releases  ⭐⭐⭐⭐  |
| 🐤 **Canary Deployment**       | Gradually shifts a small percentage of traffic to a new version before full rollout.  | 90% → v1 + 10% → v2 → 100% → v2 | Safe Production Rollouts ⭐⭐⭐⭐⭐ |


| 🧩 **Feature**              | 🚦 **Traffic Splitting**                     | 🟦🟩 **Blue-Green Deployment**                 | 🐤 **Canary Deployment**                        |
| --------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------------------------------------------- |
| ⏱️ **Downtime**             | ❌ No                                         | ❌ No                                           | ❌ No                                            |
| ⚠️ **Risk Level**           | 🟡 Medium                                    | 🟢 Low                                         | 🟢 Very Low                                     |
| 🔄 **Rollback**             | ✅ Easy                                       | ✅ Very Easy                                    | ✅ Easy                                          |
| 🌐 **Gateway API Feature**  | `backendRefs.weight`                         | `backendRefs.weight`                           | `backendRefs.weight`                            |
| 💡 **Example**              | 50% v1, 50% v2                               | 100% Blue → 100% Green                         | 90% v1, 10% v2                                  |
| 🎯 **Best Use Case**        | A/B Testing                                  | Major Releases                                 | Safe Production Rollouts                        |
| 📊 **User Exposure**        | Users see different versions simultaneously  | Users see only one environment at a time       | Small percentage of users see new version first |
| 🧪 **Testing Capability**   | Excellent for experiments                    | Excellent for full-environment validation      | Excellent for production validation             |


### 🚀 Deployment Traffic Flow Examples
| 🧩 **Deployment Strategy**             | 📖 **Traffic Flow Example**                                                               | 🎯 **Purpose**                           | 💡 **Key Benefit**            |
| -------------------------------------- | -------------------------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------- |
| 🚦 **Traffic Splitting (A/B Testing)** | **100% Traffic** → `50% → v1`, `50% → v2`                                                  | Compare multiple versions simultaneously | User behavior testing         |
| 🟦🟩 **Blue-Green Deployment**         | **Before:** 100% → Blue<br>**After:** 100% → Green                                           | Switch entire workload to new version    | Fast rollback & zero downtime |
| 🐤 **Canary Deployment**               | **Phase 1:** `90% → v1`, `10% → v2`<br>**Phase 2:**` 70% → v1`, `30% → v2`<br>**Phase 3:** `100% → v2` | Gradually release new version     | Lowest deployment risk        |
| 🔄 **Rolling Update (Default)**        | **Before:** 100% → v1<br>**During:** Gradually replace v1 Pods with v2 Pods<br>**After:** 100% → v2    | Update application with zero downtime  | Default Kubernetes strategy, Replace Pods Gradually |

---

## 🎯 Interview One-Liner
| Strategy             | Answer                                                                               |
| -------------------- | ------------------------------------------------------------------------------------ |
| 🔄 Rolling Update    | Gradually replaces old Pods with new Pods without downtime.  `v1 Pods → gradually replaced by v2 Pods `  |
| 🚦 Traffic Splitting | Distributes traffic between multiple versions using weighted routing. `Test Both Versions`         |
| 🟦🟩 Blue-Green      | Switches all traffic between two identical environments.                             |
| 🐤 Canary            | Gradually shifts a small percentage of traffic to a new version before full rollout. |

---

### ☸️ Kubernetes Rollout vs Rollback
| 🧩 **Feature**          | 🚀 **Rollout**                                                                            | 🔙 **Rollback**                         |
| ----------------------- | ----------------------------------------------------------------------------------------- | --------------------------------------- |
| 🎯 **Purpose**          | Deploy a new version of an application   ("Move Forward")                                 | Revert to a previous stable version ("Move Backward") |
| 📖 **Meaning**          | Update Pods with a new image/configuration                                                | Undo a failed or problematic deployment |
| 🔄 **Direction**        | Old Version ➜ New Version                                                                 | New Version ➜ Previous Version          |
| ⚠️ **When Used**        | Application upgrade                                                                       | Deployment failure or bug               |
| ⏱️ **Downtime**         | ❌ No (Rolling Update)                                                                     | ❌ No                                    |
| 🛠️ **Command**         | `kubectl rollout restart deploy/myapp`<br>`kubectl set image deploy/myapp app=nginx:1.27` | `kubectl rollout undo deploy/myapp`     |
| 📜 **History Required** | Creates a new revision                                                                    | Uses previous revision                  |
| 💡 **Example**          | v1 ➜ v2                                                                                   | v2 ➜ v1                                 |
| 🎯 **Goal**             | Release new features                                                                      | Restore service quickly                 |

