## What is Sidecar Container?

A **Sidecar Container** is an additional container inside the **same Pod** as your main application container.
It runs alongside the main app to provide supporting functionality.

Think of it as a “helper” container that extends or enhances the main container without modifying your application code.

Both containers share:
- Pod network (same IP, localhost communication)
- Volumes
- Lifecycle (run together as long as the Pod is running)

## Key Characteristics

**1. Runs in parallel with main container**

Unlike Init Containers (which run before), Sidecars run continuously along with the main application container.

**2. Shares the same Pod environment**
  - Same IP address
  - Can communicate via localhost
  - Shares volumes for file exchange

**3. Decouples responsibilities**
  - The sidecar handles “extra” tasks, so your main container stays clean and focused.

**4. Independent image and configuration**
- Can use different container images
- Can have different permissions
- Can be updated independently (via Deployment updates)

## Use Cases of Sidecar Containers

**1. Logging / Monitoring Sidecars**
  - Fluentd / Filebeat sidecar collecting logs from main container
  - Sends logs to ELK / Loki / Prometheus
Use case: If your main app writes logs to a shared volume, the sidecar reads and ships them.

**2. Configuration or File Updater**
  - Sidecar regularly:
    - Updates config files
    - Downloads new certificates
    - Updates models or rule files
  - Main container reads these shared files.


## YAML Definition
```
apiVersion: v1
kind: Pod
metadata:
  name: app-with-sidecar
spec:
  containers:
  - name: main-app
    image: myapp:latest
    command: ["sh", "-c", "echo App running...; while true; do echo 'Hello' >> /var/log/app.log; sleep 2; done"]
    volumeMounts:
    - name: log-volume
      mountPath: /var/log

  - name: log-collector
    image: fluentd
    volumeMounts:
    - name: log-volume
      mountPath: /var/log

  volumes:
  - name: log-volume
    emptyDir: {}
```
