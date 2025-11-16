## What is ClusterRoleBinding?
- A ClusterRoleBinding is an RBAC object that assigns a ClusterRole (or Role) to a user, group, or ServiceAccount at the cluster level.
- It grants the permissions defined in a ClusterRole across all namespaces (cluster-wide).

📌 ClusterRole = What permissions
📌 ClusterRoleBinding = Who gets those permissions cluster-wide

## Key Characteristics
**1. Cluster-scoped**
- Unlike RoleBinding (namespace-scoped), a ClusterRoleBinding is not tied to a namespace.

It grants permissions across the entire Kubernetes cluster.

**2. Binds ClusterRoles (or Roles) to subjects**
- ClusterRoleBinding connects:
  - A ClusterRole (permissions)
  - To one or more subjects:
    - User
    - Group
    - ServiceAccount
- Example:
  ```
  subjects:
  - kind: User
    name: admin-user
  - kind: ServiceAccount
    name: app-sa
    namespace: default
  ```

**3. Grants cluster-wide access**
- When used with ClusterRole, it provides access to:
  - Cluster-scoped resources (Nodes, PVs, Namespaces, CRDs)
  - Namespaced resources across all namespaces
Example:
Grant “view” access across the entire cluster.

**4. Can bind Roles too (less common)**
- A ClusterRoleBinding may bind a namespaced Role, but it gives no additional power, so it’s rarely used.
- ClusterRoleBinding is primarily used for ClusterRoles.

**5. Used by system components and controllers**
- ClusterRoleBindings are essential for:
  - Kubelet
  - Controllers (deployment, HPA, scheduler)
  - Service mesh (Istio, Linkerd)
  - Monitoring tools (Prometheus)
  - GitOps tools (ArgoCD/Flux)
  - CSI storage drivers
  - Telecom CNFs that need cross-namespace API access

**6. Enables multi-namespace or global privileges**
- Examples:
  - Auditor needs read access across all namespaces
  - DevOps team needs cluster-wide admin
  - Monitoring service needs to scrape all Pods
  - Operators need cluster-level permissions

**7. Must be used carefully (security-sensitive)**
- ClusterRoleBindings can grant very broad access, so misconfigurations can lead to:
  - Security breaches
  - Privilege escalation
  - Accidental cluster-wide destruction

Always follow least privilege.


## Use Case
- Grant cluster-wide permissions to administrators
- Allow Prometheus to list/watch Pods across all namespaces
- Allow ArgoCD to deploy to all namespaces
- Enable system controllers (HPA, scheduler, etc.)
- Access to cluster-scoped resources (Nodes, PVs, StorageClasses)

## ClusterRoleBinding YAML Example
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: admin-user
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```
`What this does? - Gives admin-user full cluster admin permissions across the cluster.`

## ClusterRoleBinding for ServiceAccount\
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus-binding
subjects:
- kind: ServiceAccount
  name: prometheus-sa
  namespace: monitoring
roleRef:
  kind: ClusterRole
  name: view
  apiGroup: rbac.authorization.k8s.io
```
`What this does? - Allows Prometheus to read all resources in all namespaces.`

## Summary
| Feature                         | Description                                     |
| ------------------------------- | ----------------------------------------------- |
| **What is ClusterRoleBinding?** | Assigns a ClusterRole’s permissions to subjects |
| **Scope**                       | Cluster-wide                                    |
| **Binds**                       | Users, groups, or ServiceAccounts               |
| **Permissions**                 | Apply across all namespaces                     |
| **Used For**                    | Admins, controllers, service mesh, CNFs         |
| **Security**                    | Very powerful — must be used carefully          |
