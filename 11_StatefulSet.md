## What is StatefulSet?
A StatefulSet is a Kubernetes controller used to manage stateful applications—those that require persistent storage, stable network identities, and ordered deployment/termination.

It ensures each Pod has:
- A unique, stable name (pod-0, pod-1, pod-2…)
- A dedicated persistent volume that stays even after Pod restarts
- Ordered operations (create, update, scale, delete in a defined sequence)

``StatefulSet = Deployment + Stability + Identity + Persistence``

## Key Characteristics:
**1. Stable, Unique Pod Names**
- StatefulSet pods follow a deterministic naming pattern:

**2. Ordered Deployment and Scaling**
StatefulSets follow strict order:
  - Create Pods in ascending order (0 → 1 → 2)
  - Delete Pods in reverse order (2 → 1 → 0)
This is critical for clustered databases.

**3. Persistent Storage with Stable Volume Binding**
Each Pod gets its own PersistentVolumeClaim:
```
mypod-0 —> PV0  
mypod-1 —> PV1  
```
Even if the Pod is rescheduled to a new node, its volume stays attached.
This enables true persistence unlike Deployments.

**4. Ordered Updates**
- Rolling updates occur one Pod at a time, maintaining consistency.

**5. Best suited for Stateful / Distributed Systems**

Use StatefulSet when the application needs:
- Stable identity
- Stable storage
- Ordered startup/shutdown
- Peer-to-peer communication

## Use Cases of StatefulSet
- Databases
  - MySQL
  - PostgreSQL
  - MongoDB
  - Cassandra

- Distributed Systems
  - Kafka
  - Zookeeper
  - RabbitMQ
  - ElasticSearch

## YAML Definition

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web-headless"
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: web-data
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: web-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```
