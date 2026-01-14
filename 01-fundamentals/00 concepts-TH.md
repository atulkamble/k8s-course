# Kubernetes Core Concepts

## What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

## Key Definitions

### 1. **Cluster**
A set of machines (nodes) that run containerized applications managed by Kubernetes.

### 2. **Node**
A worker machine in Kubernetes (can be VM or physical machine). Each node contains:
- **kubelet**: Agent that ensures containers are running
- **Container runtime**: Software for running containers (Docker, containerd, CRI-O)
- **kube-proxy**: Network proxy maintaining network rules

### 3. **Pod**
- Smallest deployable unit in Kubernetes
- Encapsulates one or more containers
- Shares storage, network, and specification for running containers
- Ephemeral in nature (not durable)

### 4. **ReplicaSet**
Ensures a specified number of pod replicas are running at all times.

### 5. **Deployment**
- Provides declarative updates for Pods and ReplicaSets
- Manages rolling updates and rollbacks
- Most common way to deploy applications

### 6. **Service**
- Abstract way to expose applications running on Pods
- Provides stable IP address and DNS name
- Types: ClusterIP, NodePort, LoadBalancer, ExternalName

### 7. **Namespace**
Virtual clusters within a physical cluster for resource isolation and organization.

### 8. **ConfigMap**
Stores non-confidential configuration data in key-value pairs.

### 9. **Secret**
Stores sensitive information (passwords, tokens, keys) in base64 encoded format.

### 10. **Volume**
Directory accessible to containers in a pod, persists data beyond pod lifecycle.

### 11. **Ingress**
Manages external access to services, typically HTTP/HTTPS routing.

### 12. **DaemonSet**
Ensures all (or some) nodes run a copy of a pod (useful for monitoring, logging).

### 13. **StatefulSet**
Manages stateful applications with stable network identities and persistent storage.

### 14. **Job**
Creates one or more pods and ensures they successfully terminate.

### 15. **CronJob**
Creates Jobs on a time-based schedule.

## Kubernetes Objects

Every Kubernetes object has two main sections:

### Spec
Desired state of the object (what you want)

### Status
Current state of the object (what exists)

Kubernetes constantly works to match Status with Spec.

## Labels and Selectors

### Labels
Key-value pairs attached to objects for identification and organization.

```yaml
labels:
  app: nginx
  environment: production
  tier: frontend
```

### Selectors
Query labels to identify groups of objects.

```yaml
selector:
  matchLabels:
    app: nginx
    environment: production
```

## Container Lifecycle

1. **Pending**: Pod accepted but container(s) not yet created
2. **Running**: Pod bound to node, all containers created
3. **Succeeded**: All containers terminated successfully
4. **Failed**: All containers terminated, at least one failed
5. **Unknown**: Pod state cannot be determined

## Resource Management

### Resource Requests
Minimum resources guaranteed to a container.

### Resource Limits
Maximum resources a container can use.

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

## Probes

### Liveness Probe
Determines if container is running. Restarts if fails.

### Readiness Probe
Determines if container is ready to serve traffic.

### Startup Probe
Determines if application has started (for slow-starting containers).

## Networking Concepts

### Pod Network
Each pod gets its own IP address.

### Service Network
Services get virtual IP addresses from service CIDR.

### Cluster DNS
Automatic DNS resolution for services within cluster.

## Storage Concepts

### Persistent Volume (PV)
Cluster resource representing physical storage.

### Persistent Volume Claim (PVC)
Request for storage by a user.

### Storage Class
Provides way to describe storage "classes" with different properties.

## Security Concepts

### RBAC (Role-Based Access Control)
Controls access to Kubernetes API based on roles.

### Service Account
Provides identity for processes running in pods.

### Network Policies
Control traffic flow between pods and network endpoints.

### Pod Security Standards
- Privileged: Unrestricted
- Baseline: Minimally restrictive
- Restricted: Heavily restricted

## Scaling Concepts

### Horizontal Pod Autoscaler (HPA)
Automatically scales number of pods based on metrics.

### Vertical Pod Autoscaler (VPA)
Automatically adjusts CPU and memory requests/limits.

### Cluster Autoscaler
Automatically adjusts cluster size based on resource needs.
