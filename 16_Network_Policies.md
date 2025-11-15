## What is Network Policy?
- A **NetworkPolicy** is a Kubernetes resource that **controls which Pods can communicate with each other** (ingress and egress traffic).
  - Think of it as a **firewall for Pods**.


- Without a NetworkPolicy:
  - **All Pods can communicate with all other Pods** (allow-all).


- With NetworkPolicies:
  - You can **restrict** communication between Pods, Namespaces, or external endpoints.


- NetworkPolicies let you enforce **Zero Trust Network Security** inside Kubernetes.

## Key Characteristics
**1. Controls Pod-to-Pod communication**

NetworkPolicy decides:
  - Which Pods can send traffic (egress)
  - Which Pods can receive traffic (ingress)

Example:
  - Only allow traffic from frontend to backend

  - Block other Pods from accessing backend


**2. Uses label selectors to match Pods**
- NetworkPolicies do NOT apply to nodes or IPs directly.
- They apply to Pods identified using labels:
  ```
  podSelector:
    matchLabels:
      app: backend

  "Then you specify rules for allowed sources/destinations."      
  ```

**3. Deny-by-default (when applied)**
- Once any NetworkPolicy selects a Pod:
  - All traffic is blocked by default unless explicitly allowed.

`This provides strong security isolation.`

**4. Supports both ingress & egress rules**
- You can define:
1. Ingress rules
  - Control what traffic is allowed into Pods.


2. Egress rules
  - Control what Pod is allowed to send out.

`Not all CNI plugins support egress rules.`

**5. Requires a network plugin (CNI) that supports NetworkPolicy**
- NetworkPolicies only work if your CNI supports it, such as:
  - Calico
  - Cilium
  - WeaveNet
  - Kube-router
  - Antrea

`Flannel does NOT support NetworkPolicies.`

**6. Namespace-level rules**
- You can allow/deny traffic from:
  - Pods in the same namespace
  - Pods in other namespaces
  - Named CIDR blocks / IP ranges

**7. L3/L4 Security**
- NetworkPolicies operate at:
  - Layer 3 (IP)
  - Layer 4 (TCP/UDP ports)

- They do not inspect application-layer data (no L7).

`For L7 rules → you need a Service Mesh (Istio/Envoy).`


## Use Cases
- ✔ Enforce Zero Trust between microservices
- ✔ Block all inter-Pod communication except explicitly allowed
- ✔ Allow only frontend → backend and block everything else
- ✔ Restrict egress traffic (security, compliance)
- ✔ Allow only monitoring/logging components to reach Pods
- ✔ Telecom CNF security (e.g., SMF ↔ UPF only)
- ✔ PCI / banking cluster compliance
- ✔ Isolating dev/test/prod namespaces


## YAML Definition
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      role: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80

```
