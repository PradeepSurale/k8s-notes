## What is Kubernetes?
Kubernetes (often called K8s) is an **open-source container orchestration platform** designed to automate the deployment, scaling, and management of containerized applications.

**Think of it as the operating system for your cloud/data center** that keeps all your containers running properly across many machines.

## Why Kubernetes?

When you run applications inside containers (like Docker), managing them manually becomes difficult when the number of containers grows. You need to handle:

- How many copies (replicas) of an app should run
- Restarting apps if they crash
- Distributing load across containers
- Updating apps without downtime
- Placing containers on best-fit servers
- Managing storage, configs, secrets
- Auto-scaling based on load

``Kubernetes automates all of this above.``

## Simple Definition

Kubernetes is a system that:

  - Runs your containers
  - Keeps them healthy
  - Scales them automatically
  - Load-balances traffic
  - Handles deployments & rollbacks
  - Manages infrastructure resources


## What Problems Does Kubernetes Solve?

| Problem                | Without K8s           | With K8s                   |
| ---------------------- | --------------------- | -------------------------- |
| App crashes            | Manual restart        | Auto-heals                 |
| Traffic increase       | Manual scaling        | Auto-scaling               |
| Deployment             | Risky, manual process | Rolling updates, rollbacks |
| Load balancing         | Manual                | Automatic                  |
| Multi-node deployments | Hard                  | Built-in                   |
| Secrets & config       | Hard-coded            | ConfigMap/Secrets          |
| Storage mgmt           | Manual                | PV/PVC                     |
