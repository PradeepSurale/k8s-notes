## What is ConfigMap?
- A ConfigMap is a Kubernetes object used to store non-confidential configuration data in key-value format.
- It allows you to separate configuration from application code, so you don’t need to rebuild containers when config changes.
- Examples of what ConfigMaps store:
  - Environment variables
  - App configuration files
  - Command-line arguments
  - JSON/YAML configs
  - Property files

`ConfigMap helps make applications portable and environment-agnostic.`

## Key Characteristics
**1. Stores non-sensitive configuration**
- ConfigMaps are for non-secret data.
(Secrets are used for sensitive data like passwords.)

**2. Key-value structure**
- ConfigMap data is stored as:
  ```
  data:
    key1: value1
    key2: value2
  ```
- Values can be:
  - Plain text
  - Entire config files
  - JSON blobs
  - Multi-line text

**3. Multiple ways to consume the data**
- ConfigMaps can be consumed by Pods as:
  - Environment variables
    ```
    envFrom:
      - configMapRef:
          name: my-config
    ```

  - Command arguments
  - Mounted as files inside a volume
    ```
    volumeMounts:
      - name: config
        mountPath: /etc/config
    ```
  - Full configuration files
- Very common for apps like NGINX, Spring Boot, Envoy, etc.

**4. Decouples config from container image**
- You can update a ConfigMap without rebuilding the container image.
- This supports:
  - Config reuse across environments
  - CI/CD best practices

**5. Can be updated independently**
- Updating a ConfigMap:
  - Does not automatically restart Pods
  - Pods need a redeploy/restart to pick new values (unless watching files)

**6. Namespace-scoped**
- ConfigMaps exist within a namespace.
- Pods must be in the same namespace to use them.

**7. Can be created from files or literals**
- Examples:
  ```
  kubectl create configmap app-config --from-literal=mode=prod
  kubectl create configmap conf --from-file=config.json
  ```


## Use Cases
- ✔ Store app settings (URLs, modes, versions)
- ✔ Provide configuration files to containers
- ✔ Store feature flags
- ✔ Provide Pod environment variables
- ✔ Configure service endpoints
- ✔ Telecom CNFs config (policies, routing rules, feature switches)


## YAML Definition
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "production"
  LOG_LEVEL: "info"
  config.json: |
    {
      "retry": 3,
      "timeout": 5000
    }
```
