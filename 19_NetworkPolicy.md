## What is NetworkPolicy?

A **NetworkPolicy** is a Kubernetes object that **controls network traffic flow** at the IP/Pod level.
It defines how Pods communicate with each other and with external endpoints based on rules.

Think of it as a firewall for Pods inside the cluster.

NetworkPolicies apply to:

	-	Ingress traffic (incoming to Pod)
	-	Egress traffic (outgoing from Pod)

They work only if your cluster has a CNI plugin that supports NetworkPolicies
(e.g., Calico, Cilium, Weave Net, Kube-router).


## Key Characteristics of NetworkPolicy

**1. Default Deny Behavior (implicit)**

	-	When you create a NetworkPolicy selecting a Pod, **all traffic is denied except what is explicitly allowed.**
	-	Without any NetworkPolicy, all Pods can talk to each other.

**2. Pod-Selector Based**

- Rules apply to Pods identified using labels.
- Example
```
podSelector:
  matchLabels:
    app: backend
```
- This makes policies dynamic — when a Pod gains/loses labels, rules apply automatically.

**3. Controls Ingress and Egress Separately**
Policies define one or both:

	- ingress: incoming allowed traffic
	- egress: outgoing allowed traffic

A policy may include:
```
policyTypes:
  - Ingress
  - Egress
```

**4. Based on Five Types of Selectors**
- Traffic can be allowed based on:

| Type                  | Meaning                                    |
| --------------------- | ------------------------------------------ |
| **podSelector**       | Allow from specific Pods                   |
| **namespaceSelector** | Allow from Pods in selected namespaces     |
| **ipBlock**           | Allow from IP CIDRs (external or internal) |
| **ports**             | Allow traffic on specific ports            |
| **policyTypes**       | Whether policy applies to ingress / egress |


**5. Layer-3 / Layer-4 Only**

- Works at IP and Port level
- Does not inspect HTTP, DNS, or application-level info

