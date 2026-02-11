# 🧰 Kubernetes Tools & Core Concepts

## Minikube
**Minikube** is a tool that allows you to run a **single-node Kubernetes cluster locally** on your laptop or desktop. 
Developers use Minikube to test applications in a Kubernetes environment before deploying them to real clusters.

------------------------------------------------------------------------

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

------------------------------------------------------------------------

## Kubernetes API Server Port
**Port 6443** is the default port used by the Kubernetes API Server.  
When any component (such as kubelet, kubectl, or kube-proxy) needs to communicate with the control plane, it connects to the API Server on **port 6443**.
> 📌 If port 6443 is blocked, the cluster will not function properly.

------------------------------------------------------------------------

## 🕸️ Calico (CNI Plugin) (Container Network Interface)
**CNI** is responsible for pod-to-pod networking in Kubernetes.

What CNI Does:
- Assigns IP addresses to Pods
- Enables Pod-to-Pod communication

------------------------------------------------------------------------

**Calico** is a simple, fast, and powerful networking plugin for Kubernetes.  
📌 Calico is often chosen when security and performance are top priorities.
It is widely used in production environments because it provides:

- Pod-to-pod networking
- High-performance networking
- Network Policies for security
- Works on cloud and on-prem environments

------------------------------------------------------------------------

## K3s
K3s is a lightweight Kubernetes distribution for edge devices and IoT (Internet of Things) devices.
##### Why K3s is used
- Lightweight – Very small binary size compared to standard Kubernetes
- Low resource usage – Runs on devices with low CPU/RAM (edge devices, IoT)
- Easy installation – Single-command install
- The single-command installation for K3s on Linux is: ****curl -sfL https://get.k3s.io | sh -***

  ------------------------------------------------------------------------

## 1. Managed Kubernetes Services (Cloud Providers)
| Cloud Provider  | Kubernetes Service Name                 |
| --------------- | --------------------------------------- |
| AWS             | Amazon EKS (Elastic Kubernetes Service) |
| Microsoft Azure | Azure AKS (Azure Kubernetes Service)    |
| Google Cloud    | Google GKE (Google Kubernetes Engine)   |
| Oracle Cloud    | Oracle OKE (Oracle Kubernetes Engine)   |
| IBM Cloud       | IBM Cloud Kubernetes Service            |

## 2. Kubernetes Installers / Tools (Self-Hosting)
| Tool      | Description                                  |
| --------- | -------------------------------------------- |
| kubeadm   | Official Kubernetes tool to set up clusters  |
| kops      | Automates Kubernetes cluster setup on AWS    |
| Rancher   | GUI-based management for Kubernetes clusters |
| Kubespray | Ansible-based Kubernetes deployment tool     |
| MicroK8s  | Lightweight Kubernetes for local/dev use     |
| Minikube  | Runs Kubernetes locally for testing          |
| Kind      | Kubernetes in Docker for testing clusters    |

## 3. Enterprise / Commercial Distributions
| Distribution            | Description                                         |
| ----------------------- | --------------------------------------------------- |
| Red Hat OpenShift       | Enterprise Kubernetes with CI/CD, security features |
| SUSE Rancher Kubernetes | Enterprise Kubernetes management platform           |

## 4. Lightweight / Edge Kubernetes
| Distribution | Description                                    |
| ------------ | ---------------------------------------------- |
| k3s          | Lightweight Kubernetes by Rancher for edge/IoT |

## Top 3 Most Popular Kubernetes Flavors in Production
| Service    | Cloud           | Key Advantages                                        |
| ---------- | --------------- | ----------------------------------------------------- |
| Amazon EKS | AWS             | Fully managed, scalable, integrates with AWS services |
| Google GKE | Google Cloud    | Feature-rich, stable, created by Kubernetes founders  |
| Azure AKS  | Microsoft Azure | Easy Azure integration, enterprise-friendly           |
| OpenShift  | Enterprise      | Built-in CI/CD, security, enterprise support          |

------------------------------------------------------------------------

## KOPS

KOPS is used for automating cluster creation on cloud environments
(mainly AWS). It automates the installation and management process.
kOps, short for Kubernetes Operations.

Instead of manually launching EC2 instances and configuring Kubernetes,
KOPS handles everything automatically. It sets up the infrastructure
like VPCs, subnets, EC2 nodes, IAM roles, and installs Kubernetes on
top. KOPS also stores the cluster configuration in an S3 bucket.

------------------------------------------------------------------------

## 🚀 What is Red Hat OpenShift?

Red Hat OpenShift is a Kubernetes-based container platform built for
enterprises. Developed by Red Hat, it is a platform-as-a-service (PaaS).

It provides a more secure, user-friendly, and production-ready version
of Kubernetes with extra tools and support. OpenShift is a
developer-friendly and secure environment to build, deploy, and manage
applications using containers.

It adds additional features like: - Built-in CI/CD pipeline - Web-based
dashboard - Role-Based Access Control (RBAC) - Image management -
Tighter security policies - Built-in monitoring based on Prometheus and
Grafana

## 🧱 In simple words

**OpenShift = Kubernetes + Developer Tools + Security + Red Hat Support**

It is popular in industries like banking, healthcare, and other enterprise sectors.

------------------------------------------------------------------------

