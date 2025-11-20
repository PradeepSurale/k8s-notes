#### Create a configmap named config with values foo=lala,foo2=lolo
```
kubectl create configmap config --from-literal=foo=lala --from-literal=foo2=lolo
```

#### Display its values
```
kubectl get cm config -o yaml
# or
kubectl describe cm config
```

#### Create and display a configmap from a file
```
kubectl create cm configmap2 --from-file=config.txt
kubectl get cm configmap2 -o yaml
```

#### Create and display a configmap from a .env file
```
kubectl create cm configmap3 --from-env-file=config.env
kubectl get cm configmap3 -o yaml
```

#### Create and display a configmap from a file, giving the key 'special'
```
kubectl create cm configmap4 --from-file=special=config4.txt
kubectl describe cm configmap4
kubectl get cm configmap4 -o yaml
```

#### Create a configMap called 'options' with the value var5=val5. Create a new nginx pod that loads the value from variable 'var5' in an env variable called 'option'
```
kubectl create cm options --from-literal=var5=val5
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod.yaml
vi pod.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    env:
    - name: option # name of the env variable
      valueFrom:
        configMapKeyRef:
          name: options # name of config map
          key: var5 # name of the entity in config map
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

#### Create a configMap 'anotherone' with values 'var6=val6', 'var7=val7'. Load this configMap as env variables into a new nginx pod
```
k create cm anotherone --from-literal=var6=val6 --from-litearal=var7=val7
```
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx1
spec:
  containers:
    - name: nginx1
      image: nginx
      envFrom:
        - configMapRef:
            name: anotherone
```

#### Create a configMap 'cmvolume' with values 'var8=val8', 'var9=val9'. Load this as a volume inside an nginx pod on path '/etc/lala'. Create the pod and 'ls' into the '/etc/lala' directory.
```
k create cm cmvolume --from-literal=var8=val9 --from-literal=var9=val9
```
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx9
spec:
  containers:
    - name: nginx9
      image: nginx
      volumeMounts:
      - name: foo
        mountPath: "/etc/lala"
        readOnly: true
  volumes:
  - name: foo
    configMap:
      name: cmvolume
```

#### Problem: Team Sun needs a new Deployment named sunny with 4 replicas of image nginx:1.17.3-alpine in Namespace sun. The Deployment and its Pods should use the existing ServiceAccount sa-sun-deploy. Expose the Deployment internally using a ClusterIP Service named sun-srv on port 9999. The nginx containers should run as default on port 80. The management of Team Sun would like to execute a command to check that all Pods are running on occasion. Write that command into file /opt/course/p2/sunny_status_command.sh.
`k create ns sun`
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-sun-deploy
  namespace: sun
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sunny
  namespace: sun
  labels:
    app: nginx
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      serviceAccountName: sa-sun-deploy
      containers:
      - name: nginx
        image: nginx:1.17.3-alpine
        ports:
        - containerPort: 80
```
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: sun-srv
  namespace: sun
spec:
  ports:
  - name: 9999
    port: 9999
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  type: ClusterIP
```

#### You need to make changes on an existing Pod in Namespace moon called secret-handler. Create a new Secret secret1 which contains user=test and pass=pwd. The Secret's content should be available in Pod secret-handler as environment variables SECRET1_USER and SECRET1_PASS. The yaml for Pod secret-handler is available at /opt/course/14/secret-handler.yaml. There is existing yaml for another Secret at /opt/course/14/secret2.yaml, create this Secret and mount it inside the same Pod at /tmp/secret2. Your changes should be saved under /opt/course/14/secret-handler-new.yaml on ckad9043. Both Secrets should only be available in Namespace moon.
**Command to encode value into base64 value**
```
echo -n "<value>" | base64 -w0
echo -n "<value>" | base64 -w0
```
```
apiVersion: v1
kind: Secret
metadata:
  name: secret1
  namespace: moon
type: Opaque
data:
  pass: cHdk
  user: dGVzdA==
```
```
apiVersion: v1
kind: Pod
metadata:
  name: secret-handler
  namespace: moon
spec:
  containers:
  - name: secret-handler-container
    image: nginx
    env:
    - name: SECRET1_USER
      valueFrom:
        secretKeyRef:
          name: secret1
          key: user
    - name: SECRET1_PASS
      valueFrom:
        secretKeyRef:
          name: secret1
          key: pass
```
**Command to know that secret key has been passed/exposed in container**
` k exec -it secret-handler -- env`

#### Team Neptune needs 3 Pods of image httpd:2.4-alpine, create a Deployment named neptune-10ab for this. The containers should be named neptune-pod-10ab. Each container should have a memory request of 20Mi and a memory limit of 50Mi. Team Neptune has it's own ServiceAccount neptune-sa-v2 under which the Pods should run. The Deployment should be in Namespace neptune.
```
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    kubernetes.io/enforce-mountable-secrets: "true"
  name: neptune-sa-v2
  namespace: neptune
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: neptune-10ab
  namespace: neptune
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
      serviceAccountName: neptune-sa-v2
      containers:
      - name: neptune-pod-10ab
        image: httpd:2.4-alpine
        resources:
          requests:
            memory: "20Mi"
          limits:
            memory: "50Mi"
```

#### Create a namespace named limitrange with a LimitRange that limits pod memory to a max of 500Mi and min of 100Mi
```
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-resource-constraint
  namespace: limitrange
spec:
  limits:
   - max: # max and min define the limit range
       cpu: "500Mi"
       memory: "500Mi"
     min:
       cpu: "100Mi"
       memory: "100Mi"
     type: Pod
```

#### Create an nginx pod that requests 250Mi of memory in the limitrange namespace
```
apiVersion: v1
kind: Pod
metadata:
  name: example-conflict-with-limitrange-cpu
  namespace: limitrange
spec:
  containers:
  - name: demo
    image: nginx
    resources:
      requests:
        memory: "250Mi"
        cpu: "100Mi"
      limits:
        cpu: "250Mi"
        memory: "250Mi"
```

#### Create a secret called mysecret2 that gets key/value from a file
**Create a file called username with the value admin:**
`echo -n admin > username`
```
kubectl create secret generic mysecret2 --from-file=username
```

#### Get the value of mysecret2
```
kubectl get secret mysecret2 -o yaml
echo -n YWRtaW4= | base64 -d # on MAC it is -D, which decodes the value and shows 'admin'
```

#### Create an nginx pod that mounts the secret mysecret2 in a volume on path /etc/foo
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  volumes: # specify the volumes
  - name: foo # this name will be used for reference inside the container
    secret: # we want a secret
      secretName: mysecret2 # name of the secret - this must already exist on pod creation
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    volumeMounts: # our volume mounts
    - name: foo # name on pod.spec.volumes
      mountPath: /etc/foo #our mount path
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
```
kubectl create -f pod.yaml
kubectl exec -it nginx -- /bin/bash
ls /etc/foo  # shows username
cat /etc/foo/username # shows admin
```

#### Delete the pod you just created and mount the variable 'username' from secret mysecret2 onto a new nginx pod in env variable called 'USERNAME'
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    env: # our env variables
    - name: USERNAME # asked name
      valueFrom:
        secretKeyRef: # secret reference
          name: mysecret2 # our secret's name
          key: username # the key of the data in the secret
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
`kubectl create -f pod.yaml
kubectl exec -it nginx -- env | grep USERNAME | cut -d '=' -f 2 # will show 'admin'`

#### Create a Secret named 'ext-service-secret' in the namespace 'secret-ops'. Then, provide the key-value pair API_KEY=LmLHbYhsgWZwNifiqaRorH8T as literal.
```
 k create secret generic ext-service-secret -n secret-ops --from-literal=API_KEY=LmLHbYhsgWZwNifiqaRorH8T
```

#### Create a Secret named 'my-secret' of type 'kubernetes.io/ssh-auth' in the namespace 'secret-ops'. Define a single key named 'ssh-privatekey', and point it to the file 'id_rsa' in this directory.
```
#Tips, export to variable
export do="--dry-run=client -oyaml"  #alias
export ns="-n secret-ops"  #alias & export are same

#if id_rsa file didn't exist.
ssh-keygen

k create secret generic my-secret $ns --type="kubernetes.io/ssh-auth" --from-file=ssh-privatekey=id_rsa $do > sc.yaml
k apply -f sc.yaml
```

#### Generate an API token for the service account 'myuser'
```
k create serviceaccount <serviceAccountName>
k create token <serviceAccountName>
```
