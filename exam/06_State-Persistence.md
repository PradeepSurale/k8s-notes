####Create a new PersistentVolume named earth-project-earthflower-pv. It should have a capacity of 2Gi, accessMode ReadWriteOnce, hostPath /Volumes/Data and no storageClassName defined. Next create a new PersistentVolumeClaim in Namespace earth named earth-project-earthflower-pvc. It should request 2Gi storage, accessMode ReadWriteOnce and should not define a storageClassName. The PVC should bound to the PV correctly. Finally create a new Deployment project-earthflower in Namespace earth which mounts that volume at /tmp/project-data. The Pods of that Deployment should be of image httpd:2.4.41-alpine.
**PV:**
```
kind: PersistentVolume
apiVersion: v1
metadata:
 name: earth-project-earthflower-pv
spec:
 capacity:
  storage: 2Gi
 accessModes:
  - ReadWriteOnce
 hostPath:
  path: "/Volumes/Data"
```

**PVC**
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: earth-project-earthflower-pvc
  namespace: earth
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
     storage: 2Gi
```

**Deployment**
```
# 12_dep.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: project-earthflower
  name: project-earthflower
  namespace: earth
spec:
  replicas: 1
  selector:
    matchLabels:
      app: project-earthflower
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: project-earthflower
    spec:
      volumes:                                      # add
      - name: data                                  # add
        persistentVolumeClaim:                      # add
          claimName: earth-project-earthflower-pvc  # add
      containers:
      - image: httpd:2.4.41-alpine
        name: container
        volumeMounts:                               # add
        - name: data                                # add
          mountPath: /tmp/project-data              # add
```


#### Team Moonpie, which has the Namespace moon, needs more storage. Create a new PersistentVolumeClaim named moon-pvc-126 in that namespace. This claim should use a new StorageClass moon-retain with the provisioner set to moon-retainer and the reclaimPolicy set to Retain. The claim should request storage of 3Gi, an accessMode of ReadWriteOnce and should use the new StorageClass. The provisioner moon-retainer will be created by another team, so it's expected that the PVC will not boot yet. Confirm this by writing the event message from the PVC into file /opt/course/13/pvc-126-reason on ckad9043.
**StorageClass**
```
# 13_sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: moon-retain
provisioner: moon-retainer
reclaimPolicy: Retain
```

**PVC**
```
# 13_pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: moon-pvc-126            # name as requested
  namespace: moon               # important
spec:
  accessModes:
    - ReadWriteOnce             # RWO
  resources:
    requests:
      storage: 3Gi              # size
  storageClassName: moon-retain # uses our new storage class
```

**Write event message to file**
```
# /opt/course/13/pvc-126-reason
Waiting for a volume to be created either by the external provisioner 'moon-retainer' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
```

#### Create busybox pod with two containers, each one will have the image busybox and will run the 'sleep 3600' command. Make both containers mount an emptyDir at '/etc/foo'. Connect to the second busybox, write the first column of '/etc/passwd' file to '/etc/foo/passwd'. Connect to the first busybox and write '/etc/foo/passwd' file to standard output. Delete pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
  - name: bbx1
    image: busybox
    command: [ "sh", "-c", "sleep 3600"]
    volumeMounts:
    - name: workdir1
      mountPath: /etc/foo
  - name: bbx2
    image: busybox
    command: [ "sh", "-c", "sleep 3600"]
    volumeMounts:
    - name: workdir1
      mountPath: /etc/foo
  restartPolicy: Never
  volumes:
  - name: workdir1
    emptyDir: {}
```

**Connect to the second container:**
```
kubectl exec -it busybox -c busybox2 -- /bin/sh
cat /etc/passwd | cut -f 1 -d ':' > /etc/foo/passwd # instead of cut command you can use awk -F ":" '{print $1}'
cat /etc/foo/passwd # confirm that stuff has been written successfully
exit
```

**Connect to the first container:**
```
kubectl exec -it busybox -c busybox -- /bin/sh
mount | grep foo # confirm the mounting
cat /etc/foo/passwd
exit
kubectl delete po busybox
```

#### Create a PersistentVolume of 10Gi, called 'myvolume'. Make it have accessMode of 'ReadWriteOnce' and 'ReadWriteMany', storageClassName 'normal', mounted on hostPath '/etc/foo'. Save it on pv.yaml, add it to the cluster. Show the PersistentVolumes that exist on the cluster
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: myvolume
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
    - ReadWriteMany
  storageClassName: normal
  hostPath:
    path: /etc/foo  
```

#### Create a PersistentVolumeClaim for this PersistentVolume, called 'mypvc', a request of 4Gi and an accessMode of ReadWriteOnce, with the storageClassName of normal, and save it on pvc.yaml. Create it on the cluster. Show the PersistentVolumeClaims of the cluster. Show the PersistentVolumes of the cluster
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: normal
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 4Gi
```

#### Create a busybox pod with command 'sleep 3600', save it on pod.yaml. Mount the PersistentVolumeClaim to '/etc/foo'. Connect to the 'busybox' pod, and copy the '/etc/passwd' file to '/etc/foo/passwd'
```
apiVersion: v1
kind: Pod
metadata:
  name: bbx1
spec:
  containers:
  - name: bbx1-container
    image: busybox
    command: ['sh', '-c', 'sleep 3600']
    volumeMounts:
    - name: data
      mountPath: /etc/foo
  volumes:                                      # add
  - name: data                                  # add
    persistentVolumeClaim:                      # add
      claimName: my-pvc
```
**Connect to the pod and copy '/etc/passwd' to '/etc/foo/passwd':**
`kubectl exec busybox -it -- cp /etc/passwd /etc/foo/passwd`

#### Create a busybox pod with 'sleep 3600' as arguments. Copy '/etc/passwd' from the pod to your local folder
```
kubectl run busybox --image=busybox --restart=Never -- sleep 3600
kubectl cp busybox:/etc/passwd ./passwd # kubectl cp command
# previous command might report an error, feel free to ignore it since copy command works
cat passwd
```

#### Create a Pod with an image Redis and configure a volume that lasts for the lifetime of the Pod
```
// emptyDir is the volume that lasts for the life of the pod

apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    emptyDir: {}

kubectl create -f redis-storage.yaml
```
