# 🚀 What is Kubernetes?

## ✅ Introduction

**Kubernetes (K8s)** is an **open-source container orchestration platform** used to **automate deployment, scaling, and management of containerized applications**.

Kubernetes is the **2nd largest open-source project in GitHub history**, after Linux.

---

## 📜 History of Kubernetes

- In **2014**, **Google** open-sourced Kubernetes, making this powerful technology available to everyone.
- Kubernetes is now maintained by the **Cloud Native Computing Foundation (CNCF)**.
- Before Kubernetes, Google used an internal system called **Borg** to manage applications.
- Borg is considered the **parent or inspiration** for Kubernetes.
- Google successfully ran **billions of containers** using Borg, and later built Kubernetes using the same core concepts.

---

## 🌍 Companies Using Kubernetes

Many large organizations use Kubernetes to manage millions of containers daily, including:

- Netflix  
- Twitter  
- Adobe  
- Walmart  

### 📈 Scalability
A single Kubernetes cluster can scale up to:
- **5,000 nodes**
- **150,000 pods**
- **300,000 containers**

---

## 🖥️ Kubernetes Cluster

- A **Kubernetes cluster** is a group of computers (called **nodes**) that work together to run containerized applications.
- Kubernetes acts as a **self-healing system**:
  - Automatically restarts failed containers
  - Reschedules dead pods
  - Replaces unhealthy nodes

---

## 🔄 Deployments & Updates

Kubernetes supports modern deployment strategies:
- Rolling updates
- Blue-green deployments
- Canary releases

Applications can be updated **without downtime**, and if something goes wrong, Kubernetes can **roll back to the previous version in seconds**.

---

## ☁️ Where Kubernetes Runs

Kubernetes is highly flexible and can run on:
- Bare-metal servers
- Cloud platforms like **AWS**, **Azure**, and **GCP**

Even **NASA** uses Kubernetes for space missions and satellite data processing 🚀

---

## ⚠️ Kubernetes Power & Risks

⚠️ A single command can be dangerous if misused:

```bash
kubectl delete --all
```
- (if misused) can delete an entire cluster in seconds 😬
👉 Accidentally deleting a namespace will permanently remove all resources inside it (pods, services, configs, secrets, etc.).







