## What is Init Container(s)?

An **Init Container** is a special type of container in a Pod that **runs before the main application** containers start.

It is used to perform **initialization tasks**, such as:

  - Preparing environment
  - Waiting for dependencies
  - Running setup scripts
  - Fetching configuration

Only **after all Init Containers finish successfully**, the main containers start.  

## Key Characteristics

**1. Run sequentially**
- They run one after another in the order defined.
- The next Init Container starts only when the previous one succeeds.

**2. Must succeed before app containers start**

- If an Init Container fails, Kubernetes restarts the Pod until it passes.
- Ensures the environment is fully ready.

**3. No concurrent execution**
- Init Containers do not run in parallel with main application containers.
- They exit once complete.

**4. Separate from main containers**

- They can have different:
  - Images
  - Security settings
  - Tools/utilities
- Useful for running utilities you don't want in the main container.

**5. They share the Pod environment**
- Init Containers share:
  - Storage volumes
  - Network namespace
  - Configuration
with main containers.


## Use Cases

**1. Wait for a service to be ready**

Example:
  - Wait for a database before the app starts
  - Wait for an external API to respond

**2. Preload configuration or secrets**

Examples:
  - Fetch config files from Git/S3
  - Download certificates
  - Generate config templates

## YAML Definition

```
apiVersion: v1
kind: Pod
metadata:
  name: app-with-init
spec:
  initContainers:
  - name: init-db-wait
    image: busybox
    command: ['sh', '-c', 'echo Waiting for MySQL...; until nc -z mysql 3306; do sleep 2; done']

  - name: init-generate-config
    image: busybox
    command: ['sh', '-c', 'echo "env=production" > /config/app.conf']
    volumeMounts:
    - name: config-volume
      mountPath: /config

  containers:
  - name: main-app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/appconfig

  volumes:
  - name: config-volume
    emptyDir: {}
```
