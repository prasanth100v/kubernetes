# 🧩 Kubernetes Pod Creation Flow

## What Happens on a Master Node: Pod Creation Flow in Kubernetes

1. **User sends a request to the API Server**  
   Example:  
   ```bash
   kubectl apply -f nginx-pod.yaml
   ```

2. **API Server receives the request**, validates it, and stores pod details in **etcd** (a key-value database).

3. **etcd holds the pod data**, but no node is assigned yet.

4. **Scheduler detects the new pod** and selects the best node based on:
   - Resource availability
   - Taints and tolerations
   - Affinity/Anti-affinity rules
   - Other scheduling policies

5. **API Server updates etcd** with the selected node information.

6. **Kubelet on the assigned node** pulls the container image and starts the pod.

7. **Controllers continuously check cluster state**, for example:
   - "Are there 5 replicas of my application running?"
   - "Did any nodes fail?"
   - "Do Services have the correct endpoints?"


---

## What Happens on a Worker Node?

1. **Scheduler assigns a pod** to the worker node.
2. **Kubelet receives the assignment** from the API Server.
3. **Kubelet requests the container runtime** to:
   - Pull the container image
   - Start the container
4. **Kube-proxy configures networking** to allow communication with the pod.
