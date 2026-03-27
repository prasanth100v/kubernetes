# 📊 Prometheus in Kubernetes 
## 🔍 What is Prometheus?
Prometheus is an **open-source monitoring & alerting tool** designed for Kubernetes widely used in Kubernetes.

👉 It collects and stores metrics (time-series data) from systems and applications.

💡 It helps monitor:
- CPU usage 🖥️  
- Memory usage 💾  
- Request rate 📡
- Disk & network 📈
- Pod restarts 🔄  
- Errors & latency ⚡  

## 🚀 How Prometheus Works
1️⃣ Applications expose metrics:
```
http://myapp:8080/metrics
```
2️⃣ Prometheus **scrapes** metrics periodically  
3️⃣ Stores data in **time-series database**  
4️⃣ Query using **PromQL**  
5️⃣ Visualize using **Grafana**  
6️⃣ Set alerts via Alertmanager 🚨  

## 📈 Prometheus in Kubernetes
| 🧩 Feature            | 💡 Description                                          |
| --------------------- | ------------------------------------------------------- |
| 📈 Metrics Collection | 📊 Scrapes metrics from pods, nodes, and K8s components |
| 🧠 Service Discovery  | 🔍 Automatically discovers services to monitor          |
| 🔔 Alerting           | 🚨 Sends alerts using Alertmanager                      |
| 🧪 Query              | 🔎 Uses PromQL to query data for dashboards             |


## 📦 Install Prometheus (Helm) Easiest way to install:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```

### 🎁 What Gets Installed?
  - ✅ Prometheus
  - ✅ Grafana
  - ✅ Node Exporter
  - ✅ kube-state-metrics
  - ✅ Alertmanager


## 🌐 Access Prometheus & Grafana
### 🔹 Prometheus UI
```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090
```
👉 http://localhost:9090  

### 🔹 Grafana UI
```bash
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
```
👉 http://localhost:3000  

🔑 Default login:
- Username: admin  
- Password: admin / prom-operator  

## 🔐 Get Grafana Password
```bash
kubectl get secret -n monitoring prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d
```


## 🔔 Alerting Flow
👉 Alerts defined using PrometheusRule (CRDs)

🔄 Flow:
- Prometheus evaluates rules
- Condition met → Alert triggered 
- Sent to **Alertmanager**  
- Forwarded to Slack / Email 📩  

## 📊 Storage & Model
- Pull-based model 🔄  
- Data stored locally 💾  
- Default retention: **15 days**  

## 📡 Pull Model (Important)
👉 Prometheus uses pull-based monitoring

- ✔️ It scrapes targets
- ❌ Not push-based

## 🚀 Use Cases
### 1️⃣ Cluster & Node Monitoring
- CPU, memory, disk usage  
- Alert if CPU > 90% for 5 minutes

### 2️⃣ Pod & Container Monitoring
- Track pod resource usage  
- Alert on CrashLoopBackOff  

### 3️⃣ Real-Time Alerts
- Detect failures early  
- Notify via Slack/Email  

### 4️⃣ Service Mesh Monitoring
- Monitor Istio traffic  
- Track latency & errors
- Observability for microservices


## 🔌 Exporters in Prometheus
👉 Exporters = Bridge tools that expose metrics for Prometheus.

### Common Exporters:
| 🧩 Exporter           | 🎯 Purpose         | 💡 What it Monitors                          |
| --------------------- | ------------------ | -------------------------------------------- |
| 🖥️ node-exporter     | 📊 Node metrics    | CPU, memory, disk, network usage             |
| ☸️ kube-state-metrics | 📦 K8s objects     | Pods, Deployments, ReplicaSets, Nodes status |
| 🌐 blackbox-exporter  | 🌍 External checks | HTTP endpoints, ping, DNS, uptime            |


## ☸️ Kubernetes Monitoring
Prometheus scrapes metrics from:
- kubelet  
- API server  
- etcd  
- cAdvisor
- Pods & services 

### 🧠 PromQL (Query Language)
👉 Used to query metrics

Example:
```
rate(http_requests_total[5m])    # Shows request rate
```


## 🧠 Summary

Prometheus is:
- 📊 Powerful monitoring tool  
- 🔔 Real-time alerting system  
- ☸️ Native Kubernetes support  
- 📈 Works with Grafana dashboards
- 🔥 Flexible queries (PromQL) & Strong alerting system & Stores time-series data
- 💡 Uses pull model (/metrics endpoint)

👉 Essential for DevOps & SRE 🚀
