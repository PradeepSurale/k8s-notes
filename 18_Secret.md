## What is Secret?
- A Secret is a Kubernetes object used to store sensitive/confidential data, such as:
  - Passwords
  - Tokens
  - API keys
  - Certificates
  - SSH keys
  - TLS keys

`Secrets keep confidential data out of Pod specs and container images, providing security and separation of concerns.`

## Key Characteristics
**1. Store sensitive data (base64-encoded)**
- Secret values are stored as base64-encoded strings.
- Example:
  ```
  data:
    password: cGFzc3dvcmQ=
  ```
- Note: Base64 is NOT encryption — just encoding.
- Additional security depends on Kubernetes, etcd encryption, RBAC, and environment.

**2. Multiple ways to use Secrets in Pods**
- As environment variables
  ```
  env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
  ```

- As mounted files (most secure)
  ```
  /etc/secret/password
  ```

- For imagePullSecrets
  ```
Used to authenticate to private Docker registries.
  ```

**3. More secure than ConfigMaps**
- Unlike ConfigMaps, Secrets:
  - Are hidden in logs/UI
  - Can be encrypted at rest in etcd
  - Are mounted as tmpfs (not written to disk)
  - Are RBAC-protected
  - Are masked from Pod descriptions
  - Support automatic rotation (CSI drivers)

**4. Namespaced**
- Secrets are namespace-scoped, same as ConfigMaps.
- Pods must be in the same namespace to use them.

**5. Can be created from literals or files**
- Examples:
  ```
  kubectl create secret generic db-secret --from-literal=password=Pass123
  ```
  ```
  kubectl create secret generic tls-secret --from-file=tls.crt --from-file=tls.key
  ```

**6. Available secret types**
- Common types:
  - Opaque (generic key-value)
  - kubernetes.io/dockerconfigjson (private registry auth)
  - kubernetes.io/tls (TLS certs)
  - bootstrap tokens
  - ServiceAccount token secrets

**7. Used widely in production**
- Secrets are essential for:
  - Database credentials
  - OAuth tokens
  - TLS termination (Ingress)
  - mTLS service mesh certificates
  - Private repo access
  - CNF/Telecom sensitive configs (e.g., 5G NFs credentials)


## Use Cases
- Storing database credentials
- API keys and access tokens
- TLS/SSL certificates
- SSH keys or private keys
- Private registry authentication (imagePullSecrets)
- Sensitive application configuration
- Certificates for service mesh
- Storing encryption keys
- ServiceAccount Tokens (automatically generated)


## Secret YAML (Opaque) Definition
```
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```


## Using Secret in Pod YAML Definition
```
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
  - name: main
    image: nginx
    env:
    - name: DB_USER
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: username
    - name: DB_PASS
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```
