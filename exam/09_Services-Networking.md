#### Team Pluto needs a new cluster internal Service. Create a ClusterIP Service named project-plt-6cc-svc in Namespace pluto. This Service should expose a single Pod named project-plt-6cc-api of image nginx:1.17.3-alpine, create that Pod as well. The Pod should be identified by label project: plt-6cc-api. The Service should use tcp port redirection of 3333:80. Finally use for example curl from a temporary nginx:alpine Pod to get the response from the Service. Write the response into /opt/course/10/service_test.html on ckad9043. Also check if the logs of Pod project-plt-6cc-api show the request and write those into /opt/course/10/service_test.log on ckad9043.
`k -n pluto run project-plt-6cc-api --image=nginx:1.17.3-alpine --labels project=plt-6cc-api`
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    project: plt-6cc-api
  name: project-plt-6cc-api
spec:
  containers:
  - image: nginx:1.17.3-alpine
    name: project-plt-6cc-api
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
`k -n pluto expose pod project-plt-6cc-api --name project-plt-6cc-svc --port 3333 --target-port 80
Expose will create a yaml where everything is already set for our case and no need to change anything:`
```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    project: plt-6cc-api
  name: project-plt-6cc-svc   # good
  namespace: pluto            # great
spec:
  ports:
  - port: 3333                # awesome
    protocol: TCP
    targetPort: 80            # nice
  selector:
    project: plt-6cc-api      # beautiful
status:
  loadBalancer: {}
```
**Verify from host**

`k get svc -n <namespace>`
- Got the IP

`curl <svc-IP:port>`

**Verify from Pod(tmp)**

`k run tmp1 --image=nginx:alpine -n pluto -- curl project-plt-6cc-svc:3333`

OR

`k run tmp --restart=Never --rm --image=nginx:alpine -i -- curl http://project-plt-6cc-svc.pluto:3333`

#### In Namespace venus you'll find two Deployments named api and frontend. Both Deployments are exposed inside the cluster using Services. Create a NetworkPolicy named np1 which restricts outgoing tcp connections from Deployment frontend and only allows those going to Deployment api. Make sure the NetworkPolicy still allows outgoing traffic on UDP/TCP ports 53 for DNS resolution. Test using: wget www.google.com and wget api:2222 from a Pod of Deployment frontend.
```
# Test services work
k -n venus run tmp --restart=Never --rm -i --image=busybox -- wget -O- frontend:80
k -n venus run tmp --restart=Never --rm -i --image=busybox -- wget -O- api:2222

# Test frontend pod can reach external and internal
k -n venus exec <frontend-pod-name> -- wget -O- www.google.com
k -n venus exec <frontend-pod-name> -- wget -O- api:2222
```
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np1
  namespace: venus
spec:
  podSelector:
    matchLabels:
      id: frontend          # Apply policy to frontend pods
  policyTypes:
  - Egress                  # Control outgoing traffic only
  egress:
  - to:                     # Rule 1: Allow egress to api pods
    - podSelector:
        matchLabels:
          id: api
  - ports:                  # Rule 2: Allow DNS resolution
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```

#### Create a pod with image nginx called nginx and expose its port 80 with service
`kubectl run nginx --image=nginx --restart=Never --port=80 --expose: --expose is is used to create svc directly`

#### Create a service that exposes the deployment on port 6262. Verify its existence, check the endpoints
```
kubectl expose deploy foo --port=6262 --target-port=8080
kubectl get service foo # you will see ClusterIP as well as port 6262
kubectl get endpoints foo # you will see the IPs of the three replica pods, listening on port 8080
```
