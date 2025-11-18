#### Team Neptune needs a Job template located at /opt/course/3/job.yaml. This Job should run image busybox:1.31.0 and execute sleep 2 && echo done. It should be in namespace neptune, run a total of 3 times and should execute 2 runs in parallel. Start the Job and check its history. Each pod created by the Job should have the label id: awesome-job. The job should be named neb-new-job and the container neb-new-job-container.
```
apiVersion: batch/v1
kind: Job
metadata:
  name: neb-new-job
  namespace: Neptune
  label:
    id: awesome-job
spec:
  parallelism: 2
  completion: 3
  template:
    spec:
      containers:
      - name: neb-new-job-container
        image: busybox:1.31.0
        command: ['sh', '-c', 'sleep 2 && echo done!']
      restartPolicy: Never
  backoffLimit: 10
```
`Monitor the Job execution - you should see two pods running in parallel at most, but three total:
k -n neptune get pod,job | grep neb-new-job`

`Check the Job history:  k -n neptune describe job neb-new-job`

#### Deployment changes/rollout history
` kubectl rollout history deployment/<deployment-name>`

####  Deployment changes/rollout history to revert to a specific revision(version)
`kubectl rollout undo deployment/<deployment-name> --to-revision=<target-revision-no.>`


#### In addition, the new Deployment should set allowPrivilegeEscalation: false and privileged: false for the security context on container level. Please create the Deployment and save its yaml under /opt/course/9/holy-api-deployment.yaml on ckad9043.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
        image: nginx
        ports:
        - containerPort: 80
        securityContext:
          allowPrivilegeEscalation: false
          privileged: false
```

#### Create 3 pods with names nginx1,nginx2,nginx3. All of them should have the label app=v1
`k run nginx1 --image=nginx`

` kubectl label pod nginx1 app=v1`

#### Show all labels of the pods
`k get pods --show-labels`

#### Change the labels of pod 'nginx2' to be app=v2 overwrite
`kubectl label pod nginx2 app=v2 --overwrite`

#### Get the label 'app' for the pods (show a column with APP labels)
`kubectl get po -L app`
OR
`kubectl get po --label-columns=app`

#### Get 'app=v2' and not 'tier=frontend' pods
`kubectl get po -l app=v2,run!=nginx2`

#### Check the annotations for pod nginx1
`kubectl annotate pod nginx1 --list`

`kubectl describe po nginx1 | grep -i 'annotations'`

#### Create a pod that will be deployed to a Node that has the label 'kubernetes.io/hostname=node01
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    kubernetes.io/hostname: node01
```
OR Use node affinity
```
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: accelerator
            operator: In
            values:
            - nvidia-tesla-p100
  containers:
    ...
```


#### Taint a node with key tier and value frontend with the effect NoSchedule. Then, create a pod that tolerates this taint.
`kubectl taint node node1 tier=frontend:NoSchedule # key=value:Effect`

`kubectl describe node node1 # view the taints on a node`

```
And to tolerate the taint:
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "tier"
    operator: "Equal"
    value: "frontend"
    effect: "NoSchedule"
```

#### Create a pod that will be placed on node controlplane. Use nodeSelector and tolerations.
`vi pod.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    kubernetes.io/hostname: controlplane
  tolerations:
  - key: "node-role.kubernetes.io/control-plane"
    operator: "Exists"
    effect: "NoSchedule"
```
`kubectl create -f pod.yaml`

#### Check how the deployment rollout is going
`kubectl rollout status deploy nginx`

#### Undo the latest rollout and verify that new pods have the old image (nginx:1.18.0)
`kubectl rollout undo deployment <deployment-name>`

#### Check the details of the fourth revision (number 4)
`kubectl rollout history deployment <deployment-name> --revision=<revision-no.>`

#### Autoscale the deployment, pods between 5 and 10, targeting CPU utilization at 80%
`kubectl autoscale deployment deployment --min=5 --max=10 --cpu=80%`

#### Pause the rollout of the deployment
`kubectl rollout pause deployment/<deployment-name>`

#### Delete the deployment and the horizontal pod autoscaler you created
`k get hpa`

`k delete hpa <deployment-name>`

#### Check logs output of Job and wait till successful
`kubectl get jobs -w`

#### Create a job but ensure that it will be automatically terminated by kubernetes if it takes more than 30 seconds to execute
```
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  activeDeadlineSeconds: 30 # add this line
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: busybox
    spec:
      containers:
      - args:
        - /bin/sh
        - -c
        - while true; do echo hello; sleep 10;done
        image: busybox
        name: busybox
        resources: {}
      restartPolicy: OnFailure
status: {}
```

#### Create the same cron job again, and watch the status. Once it ran, check which job ran by the created cron job. Check the log, and delete the cron job
```
kubectl get cj
kubectl get jobs --watch
kubectl get po --show-labels # observe that the pods have a label that mentions their 'parent' job
kubectl logs busybox-1529745840-m867r
# Bear in mind that Kubernetes will run a new job/pod for each new cron job
kubectl delete cj busybox
```

#### Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time (i.e. the job missed its scheduled time).
```
apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: time-limited-job
spec:
  startingDeadlineSeconds: 17 # add this line
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: time-limited-job
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
            image: busybox
            name: time-limited-job
            resources: {}
          restartPolicy: Never
  schedule: '* * * * *'
status: {}
```


#### Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it successfully starts but takes more than 12 seconds to complete execution.
```
apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: time-limited-job
spec:
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: time-limited-job
    spec:
      activeDeadlineSeconds: 12 # add this line
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
            image: busybox
            name: time-limited-job
            resources: {}
          restartPolicy: Never
  schedule: '* * * * *'
status: {}
```

#### Create a job from cronjob.
`kubectl create job --from=cronjob/<cronjob-name> <job-name>`

#### Get the pods with label env=dev and also output the labels
`kubectl get pods -l env=dev --show-labels`

#### Remove the labels for the pods that we created now and verify all the labels are removed
`kubectl label pod nginx-dev{1..3} env-`

#### Let’s add the label app=nginx for all the pods and verify
```
kubectl label pod nginx-dev{1..3} app=nginx
kubectl label pod nginx-prod{1..2} app=nginx

kubectl get po --show-labels
```
