#### Add sidecar container as initContainer

`Modern Kubernetes supports sidecar containers as initContainers with`

`restartPolicy: Always`

```
spec:
      volumes:
      - name: logs
        emptyDir: {}
      initContainers:
      - name: init
        image: bash:5.0.11
        command: ['bash', '-c', 'echo init > /var/log/cleaner/cleaner.log']
        volumeMounts:
        - name: logs
          mountPath: /var/log/cleaner
      - name: logger-con                                                # add
        image: busybox:1.31.0                                           # add
        restartPolicy: Always                                           # add
        command: ["sh", "-c", "tail -f /var/log/cleaner/cleaner.log"]   # add
        volumeMounts:                                                   # add
        - name: logs                                                    # add
          mountPath: /var/log/cleaner                                   # add
      containers:
      - name: cleaner-con
        image: bash:5.0.11
        args: ['bash', '-c', 'while true; do echo `date`: "remove random file" >> /var/log/cleaner/cleaner.log; sleep 1; done']
        volumeMounts:
        - name: logs
          mountPath: /var/log/cleaner
```

#### Create a Pod with two containers, both with image busybox and command "echo hello; sleep 3600". Connect to the second container and run 'ls'

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: bbx1
    image: busybox:1.28
    command: ['sh', '-c', 'echo Hello! && sleep 3600']
  - name: bbx2
    image: busybox:1.28
    command: ['sh', '-c', 'echo Hello! && sleep 3600']
```

`k exec -it <pod-name> -c <container-name> -- <command>`

` k exec -it myapp-pod -c bbx2 -- ls`

#### Create a pod with an nginx container exposed on port 80. Add a busybox init container which downloads a page using 'echo "Test" > /work-dir/index.html'. Make a volume of type emptyDir and mount it in both containers. For the nginx container, mount it on "/usr/share/nginx/html" and for the initcontainer, mount it on "/work-dir". When done, get the IP of the created pod and create a busybox pod and run "wget -O- IP"

- Create pod YAML Output using imperative command(dry-run)
- Add Init Container
- Add Volume
- Mount Volume to both the container

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: box
  name: box
spec:
  initContainers:
  - args:
    - /bin/sh
    - -c
    - echo "Test" > /work-dir/index.html
    image: busybox
    name: box
    volumeMounts:
    - name: vol
      mountPath: /work-dir
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: vol
      mountPath: /usr/share/nginx/html
  volumes:
  - name: vol
    emptyDir: {}
```

#### Show metrics of the above pod containers and puts them into the file.log and verify
`kubectl top pod busybox --containers`

```
// putting them into file
kubectl top pod busybox --containers > file.log
cat file.log
```

#### Create a Pod with main container busybox and which executes this “while true; do echo ‘Hi I am from Main container’ >> /var/log/index.html; sleep 5; done” and with sidecar container with nginx image which exposes on port 80. Use emptyDir Volume and mount this volume on path /var/log for busybox and on path /usr/share/nginx/html for nginx container. Verify both containers are running.
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: bbx7
    image: busybox
    command: ['sh', '-c', "while true; do echo 'Hi I am from Main container' >> /var/log/index.html; sleep 5; done"]
    volumeMounts:
    - name: shared-data
      mountPath: /var/log

  initContainers:
  - name: nginx7
    image: nginx
    restartPolicy: Always
    ports:
      - containerPort: 80
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html

  volumes:
  - name: shared-data
    emptyDir: {}
```
