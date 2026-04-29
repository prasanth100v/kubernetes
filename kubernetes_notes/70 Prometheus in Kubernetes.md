# 📊 Prometheus in Kubernetes 
## 🔍 What is Prometheus?
 * Prometheus is an **open-source monitoring & alerting tool** designed for Kubernetes widely used in Kubernetes.
 * 👉 It collects and stores `metrics` (time-series data) from `systems` and `applications`.
 * 💡 It helps monitor:
    - CPU usage 🖥️  
    - Memory usage 💾  
    - Request rate 📡
    - Disk & network 📈
    - Pod restarts 🔄  
    - Errors & latency ⚡

## 🚀 How Prometheus Works
1️⃣ Applications expose metrics:
```yaml
http://myapp:8080/metrics
```
2️⃣ Prometheus **scrapes** metrics periodically  
3️⃣ Stores data in **time-series database**  
4️⃣ Query using **PromQL**  
5️⃣ Visualize using **Grafana**  
6️⃣ Set alerts via Alertmanager 🚨  

## 📈 Prometheus in Kubernetes
| 🧩 Feature            | 💡 Description                                                |
| ---------------------- | ------------------------------------------------------------- |
| 📈 Metrics Collection | 📊 Scrapes metrics from `pods`, `nodes`, and `K8s components` |
| 🧠 Service Discovery  | 🔍 Automatically discovers services to monitor                |
| 🔔 Alerting           | 🚨 Sends alerts using Alertmanager                            |
| 🧪 Query              | 🔎 Uses `PromQL` to query data for dashboards                   |


## 📦 Install Prometheus (Helm) Easiest way to install:
```hcl
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
```hcl
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090
```
👉 http://localhost:9090  

### 🔹 Grafana UI
```hcl
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
```
👉 http://localhost:3000  

🔑 Default login:
   - Username: `admin  `
   - Password: `admin / prom-operator  `

## 🔐 Get Grafana Password
```hcl
kubectl get secret -n monitoring prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d
```

## 🔔 Alerting Flow
 * 👉 Alerts defined using PrometheusRule (CRDs)
 * 🔄 Flow:
   - Prometheus evaluates rules
   - Condition met → Alert triggered 
   - Sent to **Alertmanager**  
   - Forwarded to `Slack / Email` 📩

## 📊 Storage & Model
 - Pull-based model 🔄  
 - Data stored locally 💾  
 - Default retention: **15 days**  

## 📡 Pull Model (Important)
 * 👉 Prometheus uses pull-based monitoring
   * ✔️ It scrapes targets
   * ❌ Not push-based

## 🚀 Use Cases
### 1️⃣ Cluster & Node Monitoring
  - CPU, memory, disk usage  
  - Alert if CPU `> 90%` for 5 minutes

### 2️⃣ Pod & Container Monitoring
  - Track pod resource usage  
  - Alert on CrashLoopBackOff  

### 3️⃣ Real-Time Alerts
  - Detect failures early  
  - Notify via `Slack/Email  `

### 4️⃣ Service Mesh Monitoring
  - Monitor Istio traffic  
  - Track latency & errors
  - Observability for microservices

## 🔌 Exporters in Prometheus
 * 👉 Exporters = Bridge tools that expose `metrics for Prometheus`.

### Common Exporters:
| 🧩 Exporter           | 🎯 Purpose         | 💡 What it Monitors                          |
| --------------------- | ------------------ | -------------------------------------------- |
| 🖥️ node-exporter     | 📊 Node metrics    | CPU, memory, disk, network usage             |
| ☸️ kube-state-metrics | 📦 K8s objects     | Pods, Deployments, ReplicaSets, Nodes status |
| 🌐 blackbox-exporter  | 🌍 External checks | HTTP endpoints, ping, DNS, uptime            |


## ☸️ Kubernetes Monitoring
 * Prometheus scrapes metrics from:
  - kubelet  
  - API server  
  - etcd  
  - cAdvisor
  - Pods & services 

### 🧠 PromQL (Query Language)
 * 👉 Used to query metrics
 * Example:
```yaml
rate(http_requests_total[5m])    # Shows request rate
```

## 🧠 Summary

Prometheus is:
 - 📊 Powerful monitoring tool  
 - 🔔 Real-time alerting system  
 - ☸️ Native Kubernetes support  
 - 📈 Works with Grafana dashboards
 - 🔥 Flexible queries (`PromQL`) & Strong alerting system & `Stores time-series data`
 - 💡 Uses pull model (/metrics endpoint)

 👉 Essential for DevOps & SRE 🚀

## ⚡ Prometheus in Kubernetes — Rapid Fire Q&A

| 🔢 Q# | ❓ Question                                 | 💡 Answer                                                                                 |
| ----- | ------------------------------------------ | ----------------------------------------------------------------------------------------- |
| 🔹 Q1 | What is Prometheus?                        | 👉 An open-source monitoring & alerting toolkit designed for reliability and scalability. |
| 🔹 Q2 | What type of database does Prometheus use? | 👉 `Time-Series Database `(TSDB).                                                           |
| 🔹 Q3 | What is a metric in Prometheus?            | 👉 A `numerical value` with `timestamp `(e.g., `CPU usage`).                                    |
| 🔹 Q4 | What are labels?                           | 👉 Key-value pairs used to identify metrics (e.g., `pod="nginx"`).                          |
| 🔹 Q5 | What is scraping?                          | 👉 Pulling metrics from targets at regular intervals.                                     |
| 🔹 Q6  | How does Prometheus discover pods in Kubernetes? | 👉 Using Kubernetes service discovery (`API server`).                          |
| 🔹 Q7  | What is a ServiceMonitor?                        | 👉 A custom resource used by Prometheus Operator to define scraping targets. |
| 🔹 Q8  | What is PodMonitor?                              | 👉 Similar to ServiceMonitor but directly `targets pods`.                      |
| 🔹 Q9  | What is kube-state-metrics?                      | 👉 Exposes Kubernetes object state metrics (`deployments`, `pods`, `nodes`).   |
| 🔹 Q10 | What is node-exporter?                           | 👉 Exposes node-level metrics like `CPU, memory, disk.`                        |
| 🔹 Q11 | Core components of Prometheus? | 👉 `Prometheus server`, `exporters`, `Alertmanager`, `Grafana`. |
| 🔹 Q12 | What is an exporter?           | 👉 Converts application metrics into Prometheus format. |
| 🔹 Q13 | What is Alertmanager?          | 👉 Handles alerts (`routing`, `grouping`, `silencing`).       |
| 🔹 Q14 | What is Prometheus Operator?   | 👉 Kubernetes-native way to `deploy/manage Prometheus`.   |
| 🔹 Q15 | Types of metrics?     | 👉 Counter, Gauge, Histogram, Summary.                                   |
| 🔹 Q16 | What is a Counter?    | 👉 Monotonically increasing value (e.g., `requests count`).                |
| 🔹 Q17 | What is a Gauge?      | 👉 Value that goes up/down (e.g., `memory usage`).                         |
| 🔹 Q18 | What is Histogram?    | 👉 Measures distribution (`latency buckets`).                              |
| 🔹 Q19 | Summary vs Histogram? | 👉 Summary = `client-side quantiles`, Histogram = `server-side aggregation`. |
| 🔹 Q20 | What is PromQL?              | 👉 Query language to retrieve and analyze `metrics`. |
| 🔹 Q21 | Example query for CPU usage? | 👉 `rate(container_cpu_usage_seconds_total[5m])`     |
| 🔹 Q22 | What is rate()?              | 👉 Calculates per-second average increase.         |
| 🔹 Q23 | What is sum()?               | 👉 `Aggregates metrics`.                             |
| 🔹 Q24 | What is `avg_over_time()`?     | 👉 Average over a time range.                      |
| 🔹 Q25 | What is an alert rule?             | 👉 Condition that triggers alerts.                    |
| 🔹 Q26 | Where are alert rules defined?     | 👉 In Prometheus configuration (or `CRDs in Operator`). |
| 🔹 Q27 | What happens after alert triggers? | 👉 Sent to Alertmanager.                              |
| 🔹 Q28 | Alertmanager features?             | 👉 Deduplication, grouping, routing.                  |
| 🔹 Q29 | Where does Prometheus store data? | 👉 Local disk (`TSDB`).                    |
| 🔹 Q30 | Default retention period?         | 👉 `~15 days` (configurable).              |
| 🔹 Q31 | What is remote storage?           | 👉 External storage like Thanos, Cortex. |
| 🔹 Q32 | Is Prometheus horizontally scalable? | 👉 Not natively (use Thanos/Cortex for scaling).       |
| 🔹 Q33 | What is Thanos?                      | 👉 Extends Prometheus for `long-term storage & HA`.      |
| 🔹 Q34 | What is federation?                  | 👉 Prometheus pulling metrics from another Prometheus. |
| 🔹 Q35 | How to expose app metrics?     | 👉 Add `/metrics endpoint` + `annotations.`                         |
| 🔹 Q36 | Example annotations?           | 👉 prometheus.io/scrape: `"true" <br> prometheus.io/port: "8080" `|
| 🔹 Q37 | Why use Helm for Prometheus?   | 👉 Simplifies deployment (`kube-prometheus-stack`).               |
| 🔹 Q38 | What is `kube-prometheus-stack`? | 👉 Helm chart bundling `Prometheus + Grafana + Alertmanager`.     |
| 🔹 Q39 | Metrics not showing?             | 👉 Check: `ServiceMonitor labels`, Target status (`/targets`), `Network policies` |
| 🔹 Q40 | High memory usage in Prometheus? | 👉 Causes: High cardinality, Too many metrics                               |
| 🔹 Q41 | What is high cardinality?        | 👉 Too many unique label combinations.                                      |
| 🔹 Q42 | What is relabeling?                 | 👉 Modify labels before scraping.                    |
| 🔹 Q43 | What is recording rule?           | 👉 Precompute queries for performance.               |
| 🔹 Q44 | Push vs Pull model?               | 👉 Prometheus uses pull; Pushgateway for push cases. |
| 🔹 Q45 | What is Pushgateway?             | 👉 Allows short-lived jobs to push metrics.          |
| 🔹 Q46 | Why is Prometheus better than traditional monitoring? | 👉 `Pull model`, `powerful queries`, `Kubernetes-native`. |
| 🔹 Q47 | How do you monitor multi-cluster Kubernetes?          | 👉 Use `Thanos/Cortex + federation`.                  |
| 🔹 Q48 | How to reduce Prometheus load?                        | 👉 Reduce `scrape interval`, avoid `high cardinality`.  |
| 🔹 Q49 | Why alerts are delayed?                               | 👉 Check `evaluation interval & scrape interval`.     |
| 🔹 Q50 | How to secure Prometheus?                             | 👉 `RBAC`,` TLS`, `authentication`.                      |
