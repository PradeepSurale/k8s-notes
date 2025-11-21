## What is a Liveness Probe in Kubernetes?
- A Liveness Probe is a diagnostic mechanism used by Kubernetes to check whether a container is still running properly.
- If the liveness probe fails, Kubernetes assumes the container is unhealthy and automatically restarts it.
- It ensures your application recovers from deadlocks, hangs, or stuck states without manual intervention.

## Characteristics of Liveness Probe (5 key points)
1. Detects Application Health (Not Just Running Process)
  - It checks if the application inside the container is alive, not just if the container is running.
  - Helps recover when apps hang or get stuck.

2. Supports Multiple Probe Types
- Kubernetes supports three probe mechanisms:
    - HTTP GET probe → Hits an endpoint (e.g., /healthz)
    -  Command probe (exec) → Runs a command inside the container
    -  TCP Socket probe → Checks if a port is open
`These allow flexibility based on your application’s design.`

3. Automatically Restarts Unhealthy Containers
    - If the probe fails repeatedly (based on failureThreshold), kubelet restarts the container.
    -  Helps achieve self-healing.

4. Configurable Timing Parameters
- You can tune how & when the probe runs:
    - initialDelaySeconds → Wait time before first check
    - periodSeconds → Frequency of checks
    - failureThreshold → Failures allowed before restart
    - timeoutSeconds → How long to wait before considering it failed
    - successThreshold → Minimum consecutive successes (usually 1 for liveness)

5. Improves Application Reliability & Stability
    - Ensures pods recover from deadlocks, memory leaks, hung threads, etc.
    - Reduces downtime without human action.

**HTTP GET Liveness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: liveness-http
spec:
  containers:
  - name: myapp
    image: nginx
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 10     # wait 10s before first check
      periodSeconds: 5            # check every 5s
      timeoutSeconds: 2           # fail if no response in 2s
      failureThreshold: 3         # after 3 failures, restart container
      successThreshold: 1         # 1 success needed (default)
```

**Exec (Command) Liveness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: liveness-exec
spec:
  containers:
  - name: myapp
    image: busybox
    args: ["sh", "-c", "touch /tmp/healthy; sleep 3600"]
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

**TCP Socket Liveness Probe**
```
apiVersion: v1
kind: Pod
metadata:
  name: liveness-tcp
spec:
  containers:
  - name: myapp
    image: nginx
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 5
```
