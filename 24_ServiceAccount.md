## What is ServiceAccount?
- A ServiceAccount (SA) is an identity used by Pods to authenticate with the Kubernetes API server.
- Just like users authenticate with usernames/passwords or certificates, Pods use ServiceAccounts to prove “who they are” and what permissions they have.

📌 In short:
ServiceAccount = Identity + Credentials for Pods to access Kubernetes APIs.

## Key Characteristics
**1. Default identity for Pods**
- Every Pod automatically gets a default ServiceAccount unless another is specified:
  ```
  default serviceaccount in the namespace
  ```
  `This provides basic access to the API server.`

**2. Used for Pod-to-Kubernetes authentication**
- Pods can use a ServiceAccount to:
  - Read ConfigMaps/Secrets
  - Watch other Pods
  - Access the API server
  - Interact with Kubernetes resources

Permissions depend on RBAC roles assigned to the ServiceAccount.

**3. Provides API credentials to Pods**
- When a Pod runs with a ServiceAccount, Kubernetes injects:
  - A JWT token (or projected service token)
  - CA certificates
  - Namespace info

These are mounted into the Pod at:
  ```
  /var/run/secrets/kubernetes.io/serviceaccount/
  ```
  `(Unless automount is disabled.)`

**4. Works with RBAC (Role-Based Access Control)**
- A ServiceAccount is useful only when combined with:
  - Role / ClusterRole
  - RoleBinding / ClusterRoleBinding
- Example:
  ```
  Pod → ServiceAccount → RoleBinding → Role → Permissions
  ```
  `This determines what the Pod can do.`

**5. Namespace-scoped**
- ServiceAccounts exist inside namespaces.
- A Pod can only use a ServiceAccount from the same namespace.

**6. Used by controllers & system components**
- Examples:
  - Deployment controller
  - CronJob controller
  - Horizontal Pod Autoscaler
  - Ingress controller
  - Service mesh sidecars (Istio)

They have their own ServiceAccounts for secure interactions.

**7. Supports projected service account tokens**
- Kubernetes offers enhanced security via:
  - Expiring tokens
  - Audience restrictions
  - Scoped permissions

These are heavily used in modern systems and service meshes.


## Use Cases
- Pods needing access to Kubernetes API
- CI/CD pipelines running in Pods
- Operators / Controllers
- Ingress / Service Mesh / CSI Drivers


## ServiceAccount YAML Definition
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
```

## Using ServiceAccount in a Pod YAML Definition
```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  serviceAccountName: app-sa
  containers:
  - name: app
    image: nginx
```

## Summary

| Feature                     | Description                                  |
| --------------------------- | -------------------------------------------- |
| **What is ServiceAccount?** | Identity for Pods to access Kubernetes API   |
| **Scope**                   | Namespace-scoped                             |
| **Credentials**             | Token + CA certs mounted automatically       |
| **Permissions**             | Controlled via RBAC                          |
| **Default SA**              | Every namespace has a default ServiceAccount |
| **Purpose**                 | Secure Pod-to-API authentication             |
| **Used For**                | Controllers, operators, CI/CD, CNFs          |
