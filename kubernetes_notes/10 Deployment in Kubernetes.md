# Kubernetes Deployment

## ✅ Deployment in Kubernetes
A **Deployment** is a higher-level controller in Kubernetes used to manage and maintain a desired number of Pods running a specific container image.

---

## 💥 Key Features of Deployment
- Automatically creates and manages Pods using a ReplicaSet
- Supports **rolling updates** and **rollback** to previous versions
- Allows easy **scaling** (up or down)
- Ensures **high availability** and **zero-downtime deployments**
- Automatically replaces Pods when updated
- Mainly used for **stateless applications** (web frontend/backend)
- **Self-healing:** automatically restarts crashed Pods

**Deployment = Version-controlled, scalable, and self-healing application management**

---

# Kubernetes Deployment Strategies

## 1. Rolling Update (Default)
**How it works:** Gradually replaces old Pods with new ones  
**Benefit:** No downtime  
**Configuration:** `strategy.type: RollingUpdate`  
Controlled by:
- `maxSurge`
- `maxUnavailable`

**Best for:**
- Production environments requiring zero downtime
- Web and stateless applications

---

## 2. Recreate
**How it works:** Deletes all old Pods first, then creates new ones  
**Disadvantage:** Downtime occurs  
**Configuration:** `strategy.type: Recreate`

**Best for:**
- Development/testing environments
- When old and new versions cannot coexist
- Stateful applications

---

## 3. Blue-Green Deployment
**How it works:**  
- Run new version (Green) alongside current version (Blue)
- After testing, switch traffic to Green
- Blue remains available for rollback

**Benefits:**
- Quick rollback
- No risk to live traffic

**Implementation:**
- Two Deployments: `myapp-blue`, `myapp-green`
- One Service pointing to either version

---

## 4. Canary Deployment
**Why “Canary”?**  
Inspired by canary birds used in mines to detect danger early.

**How it works:**
- Deploy new version alongside old version
- Send small percentage (e.g., 10%) of traffic to new version
- Gradually increase traffic if stable
- Rollback immediately if issues occur

**Benefits:**
- Early bug detection
- Gradual rollout with monitoring

**Requirements:**
- Advanced traffic control (Ingress, Istio, Service Mesh)

**Best for:**
- Testing new features with real users
- High-risk updates

---

## 5. A/B Testing
**How it works:**  
Run two versions simultaneously and route traffic based on:
- Location
- Device type
- Random selection (e.g., 50/50)

**Benefits:**
- Useful for experiments and analytics

**Requirements:**
- Istio / Linkerd (Service Mesh)
- Advanced Ingress routing rules

---

## 6. Shadow Deployment (Traffic Mirroring)
**How it works:**
- Copy live production traffic to a new version silently
- Users receive response only from old version
- New version processes requests for testing

**Benefits:**
- Test new version using real traffic
- Compare performance safely

**Requirements:**
- Istio traffic mirroring
- NGINX Ingress custom configuration

---

## Summary
Kubernetes Deployments provide **scalable, version-controlled, and self-healing** application management.  
Different deployment strategies help balance **risk, downtime, and testing needs** depending on the production scenario.
