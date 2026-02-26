# Kubernetes Annotations & Labels Guide

## ✅ Annotations in Kubernetes

Annotations are used to store extra (non-identifying) information about
a Kubernetes object.

-   Used by tools, controllers, and plugins\
-   Store additional metadata or configuration\
-   Commonly used for monitoring, ingress, and external integrations\
-   NOT used for selecting or filtering resources

------------------------------------------------------------------------

## 🏷️ Labels in Kubernetes

Labels are key-value pairs attached to Kubernetes objects like:

-   Pods\
-   Deployments\
-   Services\
-   ReplicaSets

### 🔎 Purpose of Labels

-   Identify resources\
-   Filter resources\
-   Group resources\
-   Used by selectors to match Pods

Example:

``` yaml
labels:
  app: my-app
  env: production
```

------------------------------------------------------------------------

## 🌍 Real-World Analogy

-   **Labels** → Like barcodes on products (used for scanning and
    sorting)\
-   **Annotations** → Like product manuals (extra details not used for
    sorting)

------------------------------------------------------------------------

## 📝 What is an Ingress Annotation?

Ingress annotations are extra configuration settings that modify how
your Ingress behaves.

They help define:

-   Traffic routing rules\
-   SSL/TLS configuration\
-   Rewrite rules\
-   Rate limiting\
-   Authentication settings\
-   Custom controller-specific configurations

Example:

``` yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
```

------------------------------------------------------------------------

## 🔥 Key Difference Summary

 | Feature                   | Labels      | Annotations |
|---------------------------|------------|------------|
| Used for Selection        | ✅ Yes     | ❌ No      |
| Used by Selectors         | ✅ Yes     | ❌ No      |
| Store Extra Metadata      | ❌ No      | ✅ Yes     |
| Used by Tools/Controllers | ❌ Rarely  | ✅ Yes     |

------------------------------------------------------------------------

## 🚀 Conclusion

-   Use **Labels** for identifying and grouping Kubernetes objects.
-   Use **Annotations** for storing additional metadata or configuration
    details.
-   Use **Ingress Annotations** to control advanced traffic behavior.
