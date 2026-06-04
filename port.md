### 🏗 Think like this (mental picture)
 * 🧑 User talks to Service
 * 🧑‍💼 Service talks to Pod
```
User → Service:80 → Pod:80 → App
```

### 🔥 Different ports example (very common)
```hcl
ports:
- port: 80
  targetPort: 8080
```

### What is port?
```
port: 80    👉 Port number on the Service   This is the front door  ( Clients connect to this port )
```
### What is targetPort?
```
targetPort: 80     👉 Port number inside the Pod (containerPort)  Service forwards traffic here  (This is the inside door)
```

### Important notes
 * targetPort must match containerPort  ❌ `If mismatch → app unreachable`
 * containerPort is optional but recommended

### 🧩 Full minimal example
Pod
```hcl
containers:
- name: app
  image: nginx
  ports:
  - containerPort: 80
```
Service
```hcl
apiVersion: v1
kind: Service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```
🧠 Final memory trick
```hcl
port = Service door
targetPort = Pod door
```

🧩 Traffic Flow Explanation (Very impressive)
```hcl
Traffic flows as:
Client → NodeIP:nodePort → Service:port → PodIP:targetPort → containerPort.
```
```hcl
containerPort: 8080
targetPort: 8080
port: 80
nodePort: 30080
```

### 🧠 ONE PICTURE (inside → outside)
```hcl
APP
 ↑
containerPort
 ↑
targetPort
 ↑
Service port
 ↑
nodePort
 ↑
USER (browser / curl)
```

Read it bottom → top when traffic comes in.

### 🧠 ONE SENTENCE (say this)
 * User enters through `nodePort`, Service receives on port,  👉 “Service port is the door of the Service, not the app.”
 * Service forwards to `targetPort`, App listens on `containerPort`.

All services use port 80.
```hcl
#service-A
port: 80
targetPort: 8080

#service-B
port: 80
targetPort: 8081

#service-C
port: 80
targetPort: 3000
```
### how IP addresses are created in Kubernetes

1️⃣ Node IP — comes from your infrastructure (Cloud provider (`AWS, GCP, Azure`)
```hcl
IP of the VM / EC2 / server          Node IP: 192.168.1.10
Kubernetes does not create this
```
2️⃣ Pod IP — created by CNI plugin ((Container Network Interface)) Calico, AWS VPC CNI
What is a Pod IP?
```hcl
Every Pod gets its own unique IP             Pod IP: 10.244.1.5
This IP is inside the cluster network
```
### Important rule
```hcl
Pod IPs are temporary
Pod deleted → IP released    👉    New Pod → new IP
That’s why we need Services.
```
3️⃣ Service IP — created by Kubernetes itself
What is a Service IP?
```hcl
Also called ClusterIP  👉  A virtual IP (not attached to any pod or node)
Service IP: 10.96.10.25          Kubernetes has a Service CIDR
Each Service gets one unique IP       IP stays stable until Service is deleted
```
### Who assigns Service IP?   ➡️ Kubernetes API Server ( Not the CNI )

* 🧠 Why same ports don’t conflict (important)
* Because Each Pod → unique Pod IP
* Each Service → unique Service IP
* Same port on different IPs = no conflict

✅ Final memory lines
 * Node IP → Infra  ➡️  Pod IP → CNI   ➡️  Service IP → Kubernetes
```hcl
❌ Kubernetes DNS does NOT route traffic    ✅ Kubernetes DNS only resolves names to IPs  ➡️ Routing traffic is done by kube-proxy, not DNS.
```
```hcl
🔹 Network Policies : Enforced by CNI (Calico)  ➡️  Control pod-to-pod traffic
```
