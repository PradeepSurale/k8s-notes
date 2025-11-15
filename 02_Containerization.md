## What is Containerization?
Containerization is the process of bundling:
  - The application code
  - Runtime (JDK, Python, Node, etc.)
  - Dependencies (libraries, packages)
  - System tools
  - Configuration

``…into a single self-contained unit called a container image.``
These containers run on any host machine with a container runtime (Docker, containerd, CRI-O) without needing a full operating system per application.

### Analogy:
Think of a container as a sealed tiffin box:
  - Your food (application) + spices & ingredients (dependencies) remain the same anywhere you take it.


## Why Do We Need Containerization?

**1. Solve “It works on my machine” problem**

Containers package everything the app needs. So the app runs the same on:
  1. Developer laptop
  2. QA environment
  3. Production server
  4. Cloud or on-prem

➡ No dependency mismatch.
➡ No OS conflicts.

**2. Lightweight and Efficient**

Containers share the host OS kernel, so they use far fewer resources compared to VMs:

| Virtual Machine | Container         |
| --------------- | ----------------- |
| Full OS per VM  | Shares OS kernel  |
| Heavy (GBs)     | Light (MBs)       |
| Slow boot       | Starts in seconds |

**3. Faster Deployment & Scalability**

Containers start and stop very fast, enabling:
- Auto-scaling
- Rolling updates
- Blue-green deployments
- High availability

In Kubernetes, this is extremely powerful.

**4. Portability Across Environments**
A container image runs anywhere:
 - On-prem servers
 - Cloud (AWS, GCP, Azure)
 - Hybrid setups
 - Edge devices

Because the runtime is standardized (OCI).

**5. Isolation and Security**
Each container runs in its own isolated user space:
  - One container crash won’t affect others
  - Limited access to host resources
  - Runtime security policies (AppArmor, Seccomp)


## Summary

Containerization = package everything + isolate the runtime + run anywhere.

### Benefits
  1. Consistency
  2. Portability
  3. Isolation
  4. Efficiency
  5. Scalability
  6. Fast deployment
  7. Microservices-friendly
