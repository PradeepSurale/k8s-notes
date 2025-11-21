## What is a Readiness Probe?
- A Readiness Probe checks whether a container is ready to receive traffic.
- If the probe fails, the pod is removed/stopped from Service endpoints, but not restarted.

## Five Characteristics of Readiness Probe
1. Controls traffic flow to the pod
- Pod is added to or removed from Service load balancer depending on probe result.

2. Prevents sending traffic to unready applications
- Useful during:
    - Long startup time
    - App warming
    - Database connection wait
    - Caches loading

3. Does NOT restart containers
- Failure does not kill the pod (unlike Liveness Probe).

4. Supports three probe types
- httpGet
- exec
- tcpSocket

5. Fully configurable with timing parameters
- initialDelaySeconds
- periodSeconds
- timeoutSeconds
- failureThreshold
- successThreshold (often >1 for readiness)

**1. HTTP GET Readiness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: readiness-http
spec:
  containers:
  - name: myapp
    image: nginx
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5       # wait before first probe
      periodSeconds: 5             # how often to check
      timeoutSeconds: 2
      successThreshold: 1
      failureThreshold: 3          # after 3 failures, mark as NotReady
```

**2. Exec Readiness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: readiness-exec
spec:
  containers:
  - name: myapp
    image: busybox
    args: ["sh", "-c", "sleep 3600"]
    readinessProbe:
      exec:
        command:
        - cat
        - /tmp/is_ready
      initialDelaySeconds: 3
      periodSeconds: 5
```

**3. TCP Readiness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: readiness-tcp
spec:
  containers:
  - name: myapp
    image: nginx
    readinessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```
