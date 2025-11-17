#### Create Pod
`kubectl run pod pod1 --image='httpd:2.4.41-alpine -n default`

#### Dry Run and redirect output to files
`kubectl run pod <pod-name> --image=<image-name> --dry-run=client -o yaml > <file-name>.yml`

`kubectl run pod pod1 --image=nginx --dry-run=client -o yaml > q2-pod.yml`

#### Parse JSON Output and Print status
`kubectl get pod pod1 -n default -o jsonpath='{.status.phase}'`

#### Redirecting Existing/Running Pod Output
`kubectl get pod <pod-name> -n <namespace> -o yaml > <file-name>.yaml`

#### Forcefully Delete Pod without Wasting Time
`kubectl delete pod [pod_name] --grace-period=0 --force`

#### Get Pod Status with all Labels in a Namespace
`kubectl get pods --show-labels`

#### To list pod with given label and add labels at once
`kubectl label pods -l type=worker yourlabel=yourvalue`

#### To list pod with given label and add annotation at once
`kubectl annotate pods -l type=worker your-annotation-key=your-annotation-value`

#### Add multiple annotations
`kubectl annotate pods -l type=worker key1=value1 key2=value2`

#### Search pods with multiple label values and Add label
`k -n sun label pod -l "type in (worker,runner)" protected=true`

####  Create Pod and Run command without showing the output
`k run nginx-4 --image=nginx --namespace=mynamespace --command -- env`

#### Create Pod and Run command with output
`k run nginx-3 --image=nginx --namespace=mynamespace --command -it -- env`

#### Create Resource Quota
`kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run=client -o yaml`

#### Update image of Pod and Container
`k set image pod/<pod-name> <container-name>=<new-image>`

`k set image pod/nginx nginx=nginx:1.24.0`

#### Get Ip of a Pod
`k get pod -o wide`

#### Run wget command
` k run bbx --image=busybox --command -it wget 192.168.1.4:80`

#### Get Pod Logs
`kubectl logs <pod-name>`

#### Get Pod Logs of Previous Instance
`kubectl logs <pod-name> -p`

#### Execute a simple shell on the pod
`kubectl exec -it <pod-name> -- /bin/sh`

#### Create a busybox pod that echoes 'hello world' and then exits
`kubectl run busybox --image=busybox -it --restart=Never -- echo 'hello world'`

#### Pod deleted automatically when it's completed
`kubectl run busybox --image=busybox -it --rm --restart=Never -- /bin/sh -c 'echo hello world'`

#### List all the pods showing name and namespace with a json path expression
`kubectl get pods -o=jsonpath="{.items[*]['metadata.name', 'metadata.namespace']}"`

#### List the pod with different levels of verbosity
`// List the pod with different verbosity
kubectl get po nginx --v=7
kubectl get po nginx --v=8
kubectl get po nginx --v=9`

#### List the nginx pod with custom columns POD_NAME and POD_STATUS
`kubectl get po -o=custom-columns="POD_NAME:.metadata.name, POD_STATUS:.status.containerStatuses[].state"`

#### List all the pods sorted by name
`kubectl get pods --sort-by=.metadata.name`

#### List all the pods sorted by created timestamp
`kubectl get pods--sort-by=.metadata.creationTimestamp`
