# ✅ Service in Kubernetes

Service in Kubernetes is a stable way to access our Pods, no matter how
many times they restart or get replaced.\
Pods can change IP addresses. A Service ensures that communication stays
stable.

------------------------------------------------------------------------

## 🚀 Why Services?

Pods are ephemeral --- they can die and get recreated with a new IP.\
So, you can't rely on Pod IPs directly.

👉 Services give a stable IP and DNS name to access the Pods.

------------------------------------------------------------------------

## 📌 Useful kubectl Commands

``` bash
kubectl get services
kubectl get svc
kubectl describe service <service-name>
kubectl apply -f service.yaml
kubectl delete service <service-name>
kubectl get svc <service-name>
```

Inside the pod:

``` bash
nslookup <service-name>
wget <service-name>:<port>
```

------------------------------------------------------------------------

# 🧩 Types of Services in Kubernetes

1.  ClusterIP (Default)
2.  NodePort
3.  LoadBalancer
4.  ExternalName
5.  Headless Service

------------------------------------------------------------------------

## 1️⃣ ClusterIP (Default)

-   Internal communication only
-   Accessible only inside the cluster
-   Default service type

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```

Access inside cluster:

    http://backend-service:80

------------------------------------------------------------------------

## 2️⃣ NodePort

-   Exposes service outside the cluster
-   Opens static port (30000--32767)
-   Accessible using: http://`<NodeIP>`{=html}:`<NodePort>`{=html}

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30036
  type: NodePort
```

------------------------------------------------------------------------

## 3️⃣ LoadBalancer

-   Used in cloud providers (AWS, GCP, Azure)
-   Automatically provisions external load balancer
-   Used for production applications

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer
```

------------------------------------------------------------------------

## 4️⃣ ExternalName

-   Maps service to external DNS name
-   No proxying or load balancing inside cluster
-   Creates DNS CNAME record

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-db-service
spec:
  type: ExternalName
  externalName: mydb.external.com
```

------------------------------------------------------------------------

## 5️⃣ Headless Service

-   No ClusterIP (clusterIP: None)
-   Returns Pod IPs directly via DNS
-   Mostly used with StatefulSets

### Example YAML

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

------------------------------------------------------------------------

# 📚 Summary

  Type           Internal   External   Use Case
  -------------- ---------- ---------- ------------------------
  ClusterIP      ✅ Yes     ❌ No      Internal microservices
  NodePort       ✅ Yes     ✅ Yes     Development/testing
  LoadBalancer   ✅ Yes     ✅ Yes     Production (cloud)
  ExternalName   ✅ Yes     ✅ Yes     External DB/API
  Headless       ✅ Yes     ❌ No      Stateful apps

------------------------------------------------------------------------

✅ Kubernetes Services ensure stable networking for dynamic Pods.
