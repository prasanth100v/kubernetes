# Namespace in Kubernetes

A **Namespace** in Kubernetes is used to logically divide a cluster into
multiple virtual environments.\
It helps manage resources, separate teams, and apply different policies
like resource limits or RBAC (access control).

## Key Points

-   Pod names must be unique within a namespace.
-   **ResourceQuota** is applied per namespace to limit resource usage
    (CPU, memory, number of pods, etc.).
-   Namespaces provide logical isolation only; they share the same
    physical cluster.
-   `metadata.name` -- The name of your namespace (e.g.,
    `my-namespace`).
-   `labels` -- Optional; used for organizing and selecting resources.

## Default Namespaces in Kubernetes

1.  **default** -- Used when no namespace is specified. ***(If you don't specify a namespace, Kubernetes uses default namespace.)***
2.  **kube-system** -- Holds Kubernetes internal components like API
    server, scheduler, etc.
3.  **kube-public** -- Readable by all users; used for cluster-wide
    info.
4.  **kube-node-lease** -- Stores node heartbeat information for node
    health detection.

Example:

``` bash
kubectl get pods -n kube-system
```
If you run ***kubectl get pods -n kube-system,*** you’ll see – kube-dns, coredns, kube-proxy, etcd

## Why Use Namespaces

-   Create separate environments (dev, staging, prod)
-   Deploy same application in different stages
-   Apply different resource limits
-   Restrict access using Role and RoleBinding
-   Developer only access resources in the dev namespace, not in prod. Use Role and RoleBinding in specific namespaces to restrict access. So one team/user can't affect another team's resources.
-   Filter monitoring tools (Prometheus, Grafana, Fluentd)

## Example Commands
You want to deploy the same application in different stages, Create 3 namespaces: dev, stag, prod.

``` bash
kubectl create ns dev
kubectl create ns stag
kubectl create ns prod
```

## Example YAML

### dev-namespace.yaml

``` yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

### multi-namespace.yaml

``` yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev-frontend
  labels:
    environment: dev
    app: frontend
```
> Apply it: kubectl apply -f dev-namespace.yaml

## Common Namespace Commands
```bash
# Create a namespace
kubectl create namespace dev

# List namespaces
kubectl get namespaces
kubectl get ns

# Create resources in a namespace
kubectl apply -f deployment.yaml -n dev

# View resources in a namespace
kubectl get pods -n dev

# WARNING: Deletes ALL resources in the namespace!
kubectl delete namespace dev
```
