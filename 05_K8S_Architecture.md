## Kubernetes Architecture

Kubernetes has **a Master (Control Plane)** that makes decisions and Worker Nodes that run your applications.

You can think of it like:
 - **Control Plane = Brain of the cluster**
 - **Worker Nodes = Muscles that run your workloads**

## Architecture Diagram
![Alt text](./resources/kubernetes-cluster-architecture.svg "Docker Architecture")

## Control Plane Components (Master Node)

### API Server (kube-apiserver) — The Gateway
- All communication happens through the API Server
- kubectl commands go here
- UI dashboards, controllers, schedulers all talk to it
- Acts like the "front desk" of Kubernetes

### Scheduler (kube-scheduler) — Decides where pods run
- Looks at all nodes
- Matches a Pod to the best Node
- Based on:
  - CPU/memory availability
  - Node taints & tolerations
  - Pod affinity/anti-affinity
  - Resource limits
- Example: "Pod needs 2 CPU — Node 3 has space → schedule here"


### Controller Manager (kube-controller-manager) — Brains of Automation
Manages background controllers such as:

- Node Controller — detects node health
- ReplicaSet Controller — maintains desired replicas
- Deployment Controller — rolling updates
- Endpoint Controller — service endpoints
- Job/CronJob Controller
Ensures desired state = actual state.

### etcd — The Cluster Database

- Distributed key-value store
- Stores the entire cluster state:
  - Pods
  - ConfigMaps
  - Nodes
  - Secrets
  - Network policies
- Highly available and fast
- Think of it like the "source of truth"


## Worker Node Components

### Kubelet — Node’s Supervisor
- Installed on every worker node
- Ensures containers are running
- Talks to the API server
- Reports health, status
- If a container dies → kubelet restarts it

### Kube-Proxy — Node Networking
- Maintains networking rules
- Handles service IPs, load balancing
- Implements cluster networking (iptables or IPVS)

### Container Runtime
Examples:
  - Docker engine
  - containerd
  - CRI-O
  - Mirantis/Podman
  - (Docker is not mandatory!)
Its job:
  - Pull container images
  - Start/stop containers
  - Allocate resources


### How Kubernetes Works Internally (Step-by-Step)
```
kubectl apply -f deployment.yaml
```
What happens?
- API Server receives the request
- etcd stores new desired state
- Controller Manager sees difference → creates pods
- Scheduler picks best nodes for pods
- Kubelet receives instructions → starts containers
- Kube-Proxy updates network rules
- Monitoring loop continues until:
  - Pods are running
  - Health checks pass
