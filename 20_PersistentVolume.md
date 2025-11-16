## What is PersistentVolume?

A **PersistentVolume (PV)** is a piece of storage in a Kubernetes cluster that has been **provisioned by an administrator or dynamically created** using a StorageClass.

It is a **cluster-level resource**, not tied to any specific Pod or namespace.

Think of it as a **storage disk** that exists independently of Pods.

Pods use PVs through **PersistentVolumeClaims (PVCs).**

## Key Characteristics:

**1. Cluster-wide storage resource**

- PV lives at the **cluster scope**, not inside a namespace.
- It is created and managed independently of Pods.

**2. Decoupled from Pod lifecycle**

- PVs outlive Pods
- When a Pod dies or is rescheduled, its data remains intact
- Ideal for stateful applications like databases

PVCs allow Pods to request storage without knowing the underlying details.

**3. Supports multiple storage backends**

PVs can represent many types of storage:
- NFS
- CephFS / RBD
- AWS EBS
- GCE Persistent Disk
- Azure Disk
- iSCSI
- Local storage
- GlusterFS
- CSI drivers (Container Storage Interface)

**4. Uses access modes**

Each PV declares supported access modes:

- ReadWriteOnce (RWO)
  - Only one node can mount it at a time (most common).
- ReadOnlyMany (ROX)
  - Multiple nodes can read it.
- ReadWriteMany (RWX)
  - Multiple nodes can read/write at the same time (for shared storage).

**5. Capacity**
PV specifies the amount of storage, e.g.:

```
resources:
  requests:
    storage: 10Gi
```

**6. Reclaim policies**

Controls what happens when PVC is deleted:

- `Retain`
  - Keep PV and data (manual cleanup required).
- `Delete`
  - Delete PV and underlying storage.
- `Recycle (Deprecated)`
  - Simple scrub & reuse.

**7. Static or Dynamic Provisioning**
- Static PV
  - Admin creates PVs manually.
- Dynamic PV
  - StorageClass auto-creates PV on demand.


## Use Cases

- Databases (MySQL, PostgreSQL, MongoDB, Cassandra)
- StatefulSet applications
- Log storage
- Persistent cache
- File uploads / shared file systems

## YAML Definition
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-example
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
```


## Summary

| Feature            | Description                                   |
| ------------------ | --------------------------------------------- |
| **What is PV?**    | Cluster-wide storage resource                 |
| **Purpose**        | Provide persistent, reliable storage for Pods |
| **Lifecycle**      | Independent of Pods                           |
| **Backends**       | NFS, EBS, Ceph, CSI, local storage            |
| **Access Modes**   | RWO, ROX, RWX                                 |
| **Reclaim Policy** | Retain/Delete                                 |
| **Provisioning**   | Static or dynamic (StorageClass)              |
| **Use Cases**      | Databases, StatefulSets, CNFs needing state   |
