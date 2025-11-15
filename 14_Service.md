## What is Service?

A Service in Kubernetes is an abstract object that provides **a stable network endpoint** to access a set of Pods.

Because Pods are ephemeral (they die, restart, move between nodes), a Service ensures your application always has a:

1. consistent IP
2. consistent DNS name
3. consistent port
4. built-in load balancing

Even if Pods behind it are constantly changing.

Think of a Service as a **network gateway / load balancer** for a group of Pods.

## Key Characteristics

**1. Stable IP address**

Pods get new IPs when recreated, but a Service gets a fixed cluster IP that does not change.
This allows other Pods/services to communicate reliably.

**2. Selects Pods using labels**
- A Service automatically finds the Pods it routes to using a selector:
```
selector:
  app: myapp
```
- Any Pod with this label becomes part of the Service.

**3. Load balances traffic**

Service distributes traffic **across all healthy Pods**, providing:
- Round-robin
- Session-affinity (optional)

Works inside the cluster and externally (depending on type).

**4. Different types of Services**

Kubernetes supports multiple service types:

1. ClusterIP (default)
   - Internal-only access inside cluster.
2. NodePort
  - Exposes the service on each node’s port.
3. LoadBalancer
  - Creates a cloud load balancer (AWS, GCP, Azure).
4. ExternalName
  - Maps service to an external DNS name.
5. Ingress

**5. Provides abstraction**

Backends can change (add/remove Pods), but clients always use the same endpoint.

This decouples microservices cleanly.  

## Use Cases
- ✔ Expose Pods inside the cluster
- ✔ Expose applications externally
- ✔ Load balancing multiple Pods
- ✔ Service discovery for microservices


## YAML Definition
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```
