# 📘 Kubernetes Cluster Upgrade (kubeadm - On-Prem)
## 🚀 What is kubeadm Upgrade?

* 👉 In self-managed (on-prem) Kubernetes, you upgrade clusters manually using kubeadm.
* 💡 Unlike managed services (EKS), you control everything : Control plane upgrade & Worker node upgrade

```
--------------------------------------🚀🚀master node 🚀🚀-------------------------------------
# 🚫 Step 1: Drain master node 
                     
      kubectl cordon master-1                                               # Prevent new pods scheduling
      kubectl drain master-1 --ignore-daemonsets --delete-emptydir-data     # 🧹 Evict existing pods safely (except daemonsets)

# 📦 Step 2: Upgrade kubeadm
                     
      apt-mark unhold kubeadm                     # Unhold kubeadm so you can upgrade it
      apt-get update                              #Update package list and install target version of kubeadm
      apt-get install -y kubeadm=1.24.17-00  
      apt-mark hold kubeadm                        # Lock version (avoid auto upgrade)

 # 🚀 Step 3: Upgrade control plane
            
      kubeadm upgrade plan                   # Check upgrade plan
      kubeadm upgrade apply v1.24.17         # Apply upgrade

                 # 🔄 This upgrades control plane components lik: kube-apiserver, controller-manager & scheduler

 # ⚙ Step 4: Upgrade node components (kubelet and kubectl)
          
      apt-mark unhold kubelet kubectl
      apt-get install -y kubelet=1.24.17-00 kubectl=1.24.17-00                  #Install the same target version
      apt-mark hold kubelet kubectl

# 🔄 Step 5: Restart services
                      
      systemctl daemon-reexec           # Reload systemd to pick up changes (safe practice)
      systemctl restart kubelet         # Restart kubelet to apply the upgrade

# ✅ Step 6: Uncordon the master node (Enable scheduling)
                       
      kubectl uncordon master-1         # Uncordon the master node to allow scheduling

-----------------------------------------👷👷Worker Node👷👷--------------------------------------
                        
### 👷 B. Worker Node Upgrade
# 🚫 Step 1: Drain worker node
                        
      kubectl cordon worker-1
      kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data          # 🧹 Evict workloads safely

 # 📦 Step 2: Upgrade kubeadm
                   
      apt-mark unhold kubeadm
      apt-get update
      apt-get install -y kubeadm=1.24.17-00
      apt-mark hold kubeadm

# 🔄 Step 3: Apply worker node upgrade  (Update kubelet config)           

       kubeadm upgrade node               # This updates the local kubelet config and validates the version.

# ⚙ Step 4: Upgrade kubelet & kubectl
          
      apt-mark unhold kubelet kubectl
      apt-get install -y kubelet=1.24.17-00 kubectl=1.24.17-00     # Install the target version
      apt-mark hold kubelet kubectl

# 🔄 Step 5: Restart services
                  
      systemctl daemon-reexec                         #Reload systemd and restart kubelet
      systemctl restart kubelet

# ✅ Step 6:  Uncordon the Worker Node ( Resume scheduling )
               
      kubectl uncordon worker-1                # pods to be scheduled
```


## 💡 Quick Flow (Easy to Remember)
```
upgrade_flow Interview shortcut:
                        
  - Cordon                             # 🚫 Stop scheduling
  - Drain                              # 🧹 Evict pods
  - Upgrade kubeadm                    # 📦 Tool upgrade
  - Upgrade control plane / node       # 🚀 Apply upgrade
  - Upgrade kubelet & kubectl          # ⚙ Match versions
  - Restart services                   # 🔄 Apply changes
  - Uncordon                           # ✅ Resume scheduling
```



