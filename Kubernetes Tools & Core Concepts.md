
# 🧰 Kubernetes Tools & Core Concepts

## Minikube
**Minikube** is a tool that allows you to run a **single-node Kubernetes cluster locally** on your laptop or desktop. 
Developers use Minikube to test applications in a Kubernetes environment before deploying them to real clusters.

---

## kubeadm
**kubeadm** is a tool that helps you set up a Kubernetes cluster quickly and easily.  
It is commonly used to set up Kubernetes on:

- Physical servers
- Virtual machines
- Local data centers
- Cloud VMs such as:
  - AWS EC2
  - Google Cloud Compute Engine
  - Azure Virtual Machines

---

## Kubernetes API Server Port
**Port 6443** is the default port used by the Kubernetes API Server.  
When any component (such as kubelet, kubectl, or kube-proxy) needs to communicate with the control plane, it connects to the API Server on **port 6443**.
> 📌 If port 6443 is blocked, the cluster will not function properly.

---

## 🕸️ Calico (CNI Plugin) (Container Network Interface)
**CNI** is responsible for pod-to-pod networking in Kubernetes.

What CNI Does:
- Assigns IP addresses to Pods
- Enables Pod-to-Pod communication

**Calico** is a simple, fast, and powerful networking plugin for Kubernetes.  
📌 Calico is often chosen when security and performance are top priorities.
It is widely used in production environments because it provides:

- Pod-to-pod networking
- High-performance networking
- Network Policies for security
- Works on cloud and on-prem environments

