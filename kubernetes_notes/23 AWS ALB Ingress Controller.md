# 🌐 AWS ALB Ingress Controller & NGINX Ingress Guide

## 🚀 AWS ALB Ingress Controller (Application Load Balancer)

Example configuration for **AWS ALB Ingress Controller** using
**Path-Based Routing**.

------------------------------------------------------------------------

## 🔹 Path-Based Routing Ingress Example

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
  annotations:

    # Core ALB Settings
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip

    # Security
    alb.ingress.kubernetes.io/security-groups: sg-0123456789abcdef
    alb.ingress.kubernetes.io/manage-backend-security-group-rules: "true"

    # TLS
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-west-2:123456789012:certificate/xxxxxx
    alb.ingress.kubernetes.io/ssl-policy: ELBSecurityPolicy-TLS-1-2-2017-01

    # Health Checks
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP
    alb.ingress.kubernetes.io/healthcheck-path: /health
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: "15"

    # Timeouts
    alb.ingress.kubernetes.io/idle-timeout-seconds: "60"

spec:
  rules:
  - host: myapp.example.com
    http:
      paths:

      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80

      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 3000
```

------------------------------------------------------------------------

## 📌 Path Types in Kubernetes Ingress

  Path Type    Meaning
  ------------ ------------------------------------------
  **Prefix**   Matches the path and everything after it
  **Exact**    Matches only the exact path

Example:

    /web → /web, /web/app, /web/test
    Exact /web → only /web

------------------------------------------------------------------------

# 🛡️ AWS WAF Integration with ALB

You can attach **AWS Web Application Firewall (WAF)** to protect your
applications.

``` yaml
metadata:
  annotations:
    alb.ingress.kubernetes.io/waf-acl-id: "arn:aws:wafv2:us-east1:123456789012:global/webacl/MyWebACL/xxxxxx"
    alb.ingress.kubernetes.io/wafv2-acl-arn: "arn:aws:wafv2:us-east1:123456789012:regional/webacl/MyRegionalWebACL/xxxxxx"
```

### Benefits

-   Protect against **SQL Injection**
-   Prevent **XSS attacks**
-   IP rate limiting
-   Bot protection

------------------------------------------------------------------------

# 🌍 NGINX Ingress Controller -- Host Based Routing

Example configuration for **multiple domains using NGINX Ingress**.

------------------------------------------------------------------------

## 🔹 Host-Based Routing Example

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-host-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"

spec:
  tls:
  - hosts:
    - api.example.com
    - admin.example.com
    secretName: example-tls

  rules:

  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80

  - host: admin.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 80
```

------------------------------------------------------------------------

# 🔐 TLS Configuration

Create TLS secret:

``` bash
kubectl create secret tls example-tls --cert=tls.crt --key=tls.key
```

Requirements:

-   Valid TLS certificate
-   Kubernetes secret
-   DNS pointing to ingress

------------------------------------------------------------------------

# 📊 Ingress Routing Types

  Routing Type   Example
  -------------- -----------------
  Path Based     /api , /web
  Host Based     api.example.com
  TLS Routing    HTTPS

------------------------------------------------------------------------
