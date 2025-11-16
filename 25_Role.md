## What is Role?
- A Role is an RBAC (Role-Based Access Control) object in Kubernetes that defines a set of permissions within a specific namespace.
- A Role does not grant access itself — instead, it defines what actions are allowed, such as:
  - Reading Pods
  - Updating ConfigMaps
  - Listing Deployments

Then it must be bound to a user, group, or ServiceAccount using a RoleBinding.

📌 In short:
Role = Permission Set (namespace-based)
RoleBinding = Assign those permissions to someone

## Key Characteristics
**1. Namespace-scoped**
- A Role only applies inside the namespace where it is created.
- Example:
  - A Role in dev namespace cannot control resources in prod.

**2. Defines permissions using rules**
- A Role contains rules that specify:
- **Resources**
  - pods
  - services
  - secrets
  - configmaps
  - deployments

- **Verbs**
Actions allowed:
  - get
  - list
  - watch
  - create
  - update
  - delete

Example rule:
  ```
  rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
  ```

**3. Requires a RoleBinding**
- A Role alone does nothing.
- To apply permissions, it must be attached to:
  - User
  - Group
  - ServiceAccount
via a **RoleBinding.**

**4. Fine-grained access control**
- Role allows you to define very specific access:
  - Read-only access to pods
  - Write access to ConfigMaps only
  - Allow listing Pods but not Secrets
  - Allow updating Deployments but not deleting them
RBAC = Least privilege.

**5. Works only within a single namespace**
- If you need cluster-wide permissions, use ClusterRole instead.
- Roles cannot:
  - Manage nodes
  - Manage namespaces
  - Access cluster-scoped resources

**6. Improves security**
- Roles ensure that workloads (Pods) or users only get the permissions they need.
- Important for:
  - Multi-tenant clusters
  - Microservices calling Kubernetes API
  - Operators and controllers
  - GitOps tools (ArgoCD, Flux)
  - Telecom CNFs accessing K8s API securely


## Use Cases
- Give a Pod read-only access to ConfigMaps
- Allow an app to list Pods in its own namespace
- Give a CI/CD pipeline permissions only in one namespace
- Allow Dev team to manage Deployments but not Secrets
- Kubernetes operators needing limited API access
- Telecom CNFs needing registration or configuration access


## Simple Role Example (Read-only Pod Access) YAML Definition:
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: demo
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

## RoleBinding Example (assign Role to ServiceAccount) YAML Definition;
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: demo
subjects:
- kind: ServiceAccount
  name: app-sa
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Summary

| Feature           | Description                                         |
| ----------------- | --------------------------------------------------- |
| **What is Role?** | A set of permissions within a namespace             |
| **Scope**         | Namespace-scoped                                    |
| **Defines**       | What actions (verbs) are allowed on which resources |
| **Needs**         | RoleBinding to apply permissions                    |
| **Use For**       | Fine-grained, least-privilege access                |
| **Not For**       | Cluster-wide access (use ClusterRole instead)       |
