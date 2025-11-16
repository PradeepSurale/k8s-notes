## What is PersistentVolumeClain (PVC)?

A **PersistentVolumeClaim (PVC)** is a request made by a Pod (or user) for storage.

It is the **bridge** between a Pod and a PersistentVolume (PV).

📌 Pods → PVC → PV → Storage Backend

PVC allows the Pod to ask for:
  - Storage size (e.g., 5Gi)
  - Access mode (RWO/RWX/ROX)
  - StorageClass (optional)
Kubernetes then finds a matching PV OR dynamically creates one through StorageClass.

## Key Characteristics

**1. Acts as a "storage request"**
PVC specifies:

  - Storage size (example: 5Gi)
  - Access mode (RWO / RWX / ROX)
  - Optional storage class (SSD, HDD, Ceph, EBS, etc.)

PVC does **not** define the actual backend — it only requests it.

**2. Binds to a matching PersistentVolume (PV)**

After PVC is created:
  - Kubernetes searches for a PV that matches the request
  - If found → PVC gets bound to that PV
  - If not found → it waits, or provisioning is done dynamically
Once bound, a PVC is **exclusive** to that PV.


**3. Supports dynamic provisioning**

If a **StorageClass** is specified:

- Kubernetes automatically creates a PV using the storage provider
- No admin involvement needed
Example:
```
storageClassName: fast-ssd
```

**4. Decouples app from storage tech**
PVC users (pods) don’t care if storage is:
- NFS
- AWS EBS
- Azure Disk
- GCE PD
- Ceph RBD
- CSI drivers
PVC abstracts the backend completely.


**5. Namespace-scoped**

PVCs live inside namespaces, unlike PVs (cluster-level).
Pods must be in **the same namespace** to use PVC.


## PVC YAML Definition
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

```

## Summary

| Property         | Description                                               |
| ---------------- | --------------------------------------------------------- |
| **What is PVC?** | A request for persistent storage                          |
| **Purpose**      | Allow Pods to consume storage without knowing the backend |
| **Scope**        | Namespace-scoped                                          |
| **Binds to**     | A PV (cluster-scoped)                                     |
| **Used For**     | Databases, StatefulSets, CNFs                             |
| **Supports**     | RWO, RWX, ROX                                             |
| **Provisioning** | Static or dynamic (via StorageClass)                      |
