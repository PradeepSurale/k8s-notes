## What is ReplicaSet?
A ReplicaSet (RS) is a Kubernetes object that ensures a **specified number of identical Pods are always running.**

Think of it as the auto-healing layer that keeps your application running even if Pods crash or nodes fail.

Example:
If you say "I want 3 replicas", ReplicaSet always maintains exactly 3 Pods.
  - If 1 Pod dies → ReplicaSet creates a new one
  - If 1 Pod is added manually → ReplicaSet deletes the extra Pod


## Key Characteristics

**1. Ensures desired number of replicas**

**2. Uses label selectors to identify Pods**
  - ReplicaSet manages Pods using labels and match selectors.
  ```
  selector:
      matchLabels:
        app: nginx

  ```
  - Any Pod with this label becomes part of the ReplicaSet.

**3. Supports rolling updates (when used via Deployment)**

While ReplicaSets can be used directly, Deployments use ReplicaSets to:
  - Rollout new versions
  - Roll back
  - Update pods safely
You rarely create ReplicaSets manually— **Deployment is preferred.**


**4. Self-healing mechanism**

When a Pod crashes or a node goes down:
  - ReplicaSet recreates the missing Pod automatically
  - Gives you high availability.

## YAML Definition Example
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest

```
