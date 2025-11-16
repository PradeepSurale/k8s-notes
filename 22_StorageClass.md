## What is StorageClass?

- A StorageClass defines how storage is dynamically provisioned in Kubernetes.

It tells Kubernetes:

- **What storage backend to use** (EBS, NFS, Ceph, Azure Disk, CSI drivers, etc.)
- **What parameters to use** (type, disk speed, filesystem)
- **What reclaim policy to apply** (Retain/Delete)

When a **PVC Requests storage**, Kubernetes looks at the PVC’s storageClassName and automatically creates a matching PersistentVolume (PV) using the StorageClass.

📌 StorageClass = Blueprint for dynamic storage creation

## Key Characteristics
**1. Enables Dynamic Provisioning**

Without StorageClass → PVs must be created manually (static provisioning).
With StorageClass → PVs are automatically created when PVCs request them.

Highly useful for cloud and scalable setups.

**2. Abstracts underlying storage technology**

StorageClass hides backend details such as:
 - AWS EBS
 - Azure Disk
 - GCE Persistent Disk
 - vSphere
 - Ceph / RBD / CephFS
 - NFS
 - NetApp
 - CSI driver plugins
PVC users don’t need to know which backend is used.

**3. Defines storage parameters**
StorageClass defines how volumes should be created:
example:
```
parameters:
  type: gp3
  fsType: ext4
  iops: "3000"
```

**4. Controls the reclaim policy**

Sets what happens to the PV when PVC is deleted:
  - **Delete** → delete underlying storage
  - **Retain** → keep storage for manual cleanup

```
reclaimPolicy: Delete
```

**5. Sets volume binding mode**

Defines when PV is provisioned:

- Immediate
  - PV created right after PVC creation (default).
- WaitForFirstConsumer
  - PV only created when Pod is scheduled, ensuring:
    - Correct node placement
    - High availability
    - Zonal/local storage placement
Useful for zonal storage like AWS EBS.

**6. Supports expansion (if enabled)**

Some StorageClasses allow resizing PVCs
```
allowVolumeExpansion: true
```

## StorageClass YAML Example (AWS EBS)
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp3-storage
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
  fsType: ext4
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```


## Use Cases:

- Automatic storage allocation for PVCs
- Cloud-native workloads needing dynamic scaling
- Databases requiring fast SSD or high IOPS
- StatefulSet Pods (each Pod auto-gets PV)


## Summary

| Feature                   | Description                                           |
| ------------------------- | ----------------------------------------------------- |
| **What is StorageClass?** | Template describing dynamic storage provisioning      |
| **Purpose**               | Automatically create PVs for PVCs                     |
| **Defines**               | Provisioner, parameters, reclaim policy, binding mode |
| **Backend**               | Any storage provider (cloud, CSI, Ceph, NFS, etc.)    |
| **Modes**                 | Immediate / WaitForFirstConsumer                      |
| **Expansion**             | Can allow PVC resizing                                |
| **Common Use**            | Databases, StatefulSets, cloud storage automation     |
