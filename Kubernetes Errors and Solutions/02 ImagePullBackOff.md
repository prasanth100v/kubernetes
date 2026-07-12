# Kubernetes Error: ImagePullBackOff
### What is ImagePullBackOff?
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

