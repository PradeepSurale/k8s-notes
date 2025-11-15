## What is Pod?
A Pod in Kubernetes is the smallest deployable unit that you can create and manage.
It represents one or more containers that work together as a single application unit.

### Simple Definition
- Pod = Container(s) + Shared Environment (network, storage, configuration)

## Key Characteristics

**1. Smallest unit of deployment**

Kubernetes never runs containers directly — it runs them inside Pods.
- Example:
  - You deploy a Pod
  - The Pod runs your NGINX container inside it

**2. Usually contains one container**

Most Pods contain one container (best practice).
But you can have multiple containers if they must run together, such as:
  1. Init Containers
  2. Sidecar containers (logging agent, proxy)

Example: Envoy + Application container

**3. Containers in a Pod share:**
- Same network namespace
  - Same IP address
  - Can talk on localhost
- Shared storage
  - Same mounted volumes
  - Same lifecycle
- If Pod dies → all containers inside die

**4. Pods are ephemeral**
Pods are **not permanent**:
  - If a node fails → Pods are recreated on another node
  - If containers crash → Kubernetes restarts the Pod

This is why we use **Deployments, ReplicaSets, StatefulSets** to manage Pods.

**5. Pod Networking**
- Every Pod gets:
  - Unique IP in the cluster
  - Containers inside Pod share that IP
- This means:
  - Between containers → communicate as `localhost`
  - Between Pods → communicate via Pod IP
  - For stable access → use Kubernetes **Services**
