1. Concepts Overview + Notes.
    a.
2. Labs

----
3. GitRepositories:
   a. https://github.com/SafeEHA/CKAD-Practice-Questions
   b. https://github.com/dgkanatsios/CKAD-exercises
   c. https://github.com/bbachi/CKAD-Practice-Questions
   d. https://github.com/jamesbuckett/ckad-questions
   e. https://github.com/marcusvieira88/CKAD-kubernetes-certification-commands
   f.
4. Certified Kubernetes Application Developer - Full Mock Exam Series
----


- StatefulSet – For stateful apps (stable identity, storage)
- Job – Runs a task to completion
- CronJob – Scheduled Jobs
- Service
  ClusterIP
  NodePort
  LoadBalancer
  ExternalName
- Ingress – HTTP/HTTPS routing
- NetworkPolicy – Pod-level network rules

Configuration

  ConfigMap – Non-sensitive configuration values
  Secret – Sensitive data (passwords, tokens, keys)

Storage
  PersistentVolume (PV) – Actual storage
  PersistentVolumeClaim (PVC) – Request for storage
  StorageClass – Dynamic provisioning rules
  Volume – Attached storage inside Pods

Security.
  ServiceAccount – Identity for Pods
  Role – RBAC permissions within a namespace
  ClusterRole – RBAC permissions cluster-wide
  RoleBinding – Attach Role to users/accounts
  ClusterRoleBinding – Attach ClusterRole to users/accounts
