## What is Deployment?

A **Deployment** is a high-level Kubernetes object used to manage **stateless applications** by controlling ReplicaSets and Pods.

- You can think of it as the brain that manages application lifecycle:
- Creates Pods (through ReplicaSets)
- Scales Pods up/down
- Performs rolling updates (zero-downtime)
- Performs rollbacks (return to previous version)
- Ensures the desired number of Pods are always running

In real-world production, **you always use Deployments** instead of creating ReplicaSets manually.


## Key Characteristics

**1. Uses ReplicaSets to manage Pods**
- Deployment automatically creates and manages ReplicaSets.
- You rarely interact with ReplicaSets directly.

**2. Supports rolling updates**
  - Deployment enables zero-downtime upgrades using:
  - RollingUpdate strategy
    - Max surge / max unavailable settings
  - Example: Deploy new version of app without stopping the old one.

**3. Supports rollback**

- If something goes wrong in a rollout:
  - You can instantly revert to the previous ReplicaSet using kubectl rollout undo.

## YAML Definition

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
        image: nginx:1.25
        ports:
        - containerPort: 80
```
