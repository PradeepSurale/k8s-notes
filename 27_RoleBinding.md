## What is RoleBinding?
- A RoleBinding is an RBAC (Role-Based Access Control) object that assigns a Role (permissions) to a user, group, or ServiceAccount within a specific namespace.

📌 Role = Permissions
📌 RoleBinding = Who gets those permissions

- A RoleBinding tells Kubernetes:

  `“Give this user/ServiceAccount the permissions defined in this Role.”`

## Key Characteristics
**1. Connects Roles to Identities**
- A RoleBinding links:
  - Role (permission set)
  - to a subject (user / group / ServiceAccount)
Example subjects:
  - User
  - Group
  - ServiceAccount (most common for Pods)

**2. Namespace-scoped**
- A RoleBinding applies only within the namespace it is created in.
Example:
  - A RoleBinding in dev DOES NOT apply in prod.
If you need cluster-wide bindings → use ClusterRoleBinding.

**3. Can bind either Roles or ClusterRoles**
- RoleBinding can bind:
  - Role → for namespace-specific permissions
  - ClusterRole → give clusterRole permissions but only inside the namespace

- Example:
  - Bind a ClusterRole (view) to a user only in dev namespace.

**4. Allows fine-grained access control**
- RoleBinding helps enforce least privilege by granting users only the access they need.
- Example:
  - Let app Pods read ConfigMaps
  - Allow Devs to manage Deployments in only one namespace
  - Prevent accidental access to cluster-wide resources

**5. Essential for Pod access to the API**
- Most Pods use ServiceAccounts.
- A RoleBinding assigns API permissions to that ServiceAccount.
- Used heavily by:
  - Controllers
  - Operators
  - CNFs
  - Monitoring tools
  - GitOps tools
  - Ingress / Storage drivers

**6. Multiple subjects can be bound**
- You can assign the same Role to several identities:
  ```
  subjects:
  - kind: User
    name: dev1
  - kind: User
    name: dev2
  - kind: ServiceAccount
    name: app-sa
  ```


## Use Case
- Give a Pod limited access to Kubernetes API
- Allow an app to list Pods or ConfigMaps
- Allow developers read/write access to one namespace only
- Allow CI/CD pipeline to deploy inside a particular namespace
- Give telecom CNFs access to CRDs or NF registration APIs
- Bind a ClusterRole for namespace-scoped permissions
- Implement least-privilege RBAC for multi-team clusters

## Simple RoleBinding YAML Example
- Bind a Role to a ServiceAccount
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

## Bind a ClusterRole (example) YAML
- This gives view permissions only in the demo namespace.
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: view-demo
    namespace: demo
  subjects:
  - kind: User
    name: dev-user
  roleRef:
    kind: ClusterRole
    name: view
    apiGroup: rbac.authorization.k8s.io
  ```

## Summary

| Feature                  | Description                                             |
| ------------------------ | ------------------------------------------------------- |
| **What is RoleBinding?** | Assigns a Role’s permissions to a user/group/SA         |
| **Scope**                | Namespace-scoped                                        |
| **Binds**                | Roles or ClusterRoles                                   |
| **Purpose**              | Apply least-privilege RBAC rules                        |
| **Used For**             | Pod API access, developer permissions, CI/CD, operators |
| **Key Role**             | Connects “who” → “what permissions”                     |
