#### In Namespace pluto there is a Deployment named project-23-api. It has been working okay for a while but Team Pluto needs it to be more reliable. Implement a liveness-probe which checks the container to be reachable on port 80. Initially the probe should wait 10, periodically 15 seconds. The original Deployment yaml is available at /opt/course/p1/project-23-api.yaml. Save your changes at /opt/course/p1/project-23-api-new.yaml and apply the changes.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: project-23-api
  namespace: pluto
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        livenessProbe:                  # ADD THIS SECTION
          tcpSocket:                    # Use TCP socket check
            port: 80                    # Check port 80
          initialDelaySeconds: 10       # Wait 10 seconds before first probe
          periodSeconds: 15
```
`Look for Liveness inside container to check that connection has been done: k describe pod <pod-name> -n <namespace>`

#### Create a single Pod named pod6 in Namespace default of image busybox:1.31.0. The Pod should have a readiness-probe executing cat /tmp/ready. It should initially wait 5 and periodically wait 10 seconds. This will set the container ready only if the file /tmp/ready exists. The Pod should run the command touch /tmp/ready && sleep 1d, which will create the necessary file to be ready and then idles. Create the Pod and confirm it starts.
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: busybox:1.31.0
    args:
    - /bin/sh
    - -c
    - touch /tmp/ready; sleep 1d; rm -f /tmp/ready; sleep 600
    readinessProbe:
      exec:
        command:
        - cat
        - /tmp/ready
      initialDelaySeconds: 5
      periodSeconds: 10
```

#### Create an nginx pod with a liveness probe that just runs the command 'ls'. Save its YAML in pod.yaml. Run it, check its probe status, delete it.
#### Modify the pod.yaml file so that liveness probe starts kicking in after 5 seconds whereas the interval between probes would be 5 seconds. Run it, check the probe, delete it.
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod7
  name: pod7
spec:
  containers:
  - image: nginx
    name: pod7
    resources: {}
    livenessProbe:
      exec:
        command:
        - ls
      initialDelaySeconds: 5
      periodSeconds: 5
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

#### List all the events sorted by timestamp and put them into file.log and verify
```
kubectl get events --sort-by=.metadata.creationTimestamp

// putting them into file.log
kubectl get events --sort-by=.metadata.creationTimestamp > file.log

cat file.log
```
