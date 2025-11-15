## What is Ingress?
Ingress is a Kubernetes API object that manages external access to services, typically over HTTP and HTTPS.

It acts as an application-layer (Layer 7) router, controlling:

- URLs / paths
- Hostnames
- SSL/TLS termination
- Load balancing
- Traffic rules

Ingress allows you to expose multiple Services **through a single external IP.**

Think of it like an NGINX / Envoy reverse proxy managed by Kubernetes.

## Key Characteristics

**1. L7 (HTTP/HTTPS) routing**

Ingress routes traffic based on:
  - URL path
  - Hostname
  - Subdomain

Examples:
```
/api → service A  
/ui → service B  
shop.example.com → shop-service  
```

**2. Requires an Ingress Controller**
- Ingress is just a rule set. To actually route traffic, you need an Ingress Controller, such as:
  - NGINX
  - HAProxy
  - Traefik
  - Istio (as gateway)
  - AWS/GCP/Azure L7 LB controllers
- Without a controller, Ingress does nothing.

**3. Provides a single entrypoint**

Instead of exposing each Service as a NodePort/LoadBalancer, you can use:
- ONE external IP
- Handling MANY services
This reduces costs and simplifies management.

**4. Supports TLS / SSL termination**
- Ingress can perform HTTPS termination using certificates:
```
tls:
- hosts:
  - example.com
  secretName: tls-secret
```
- The Ingress controller handles TLS handshake.

**5. Centralized traffic routing rules**

Ingress acts as a policy layer where you define:
- Rate limits
- Security rules
- Rewrite rules
- Redirects
- Load-balancing behavior


## Use Cases
- ✔ Expose multiple microservices behind one public IP
- ✔ Host-based routing (api.example.com, app.example.com)
- ✔ Path-based routing (/auth, /db, /ui)
- ✔ TLS/HTTPS termination
- ✔ Reverse proxy behavior
- ✔ API gateway for microservices
- ✔ Telecom CNF northbound/southbound interfaces
- ✔ Ingress for dashboards, UIs, APIs


## YAML Definition
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: demo.example.com
    http:
      paths:
      - path: /app
        pathType: Prefix
        backend:
          service:
            name: app-service
            port:
              number: 80
```
