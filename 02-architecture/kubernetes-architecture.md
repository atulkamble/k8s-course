# Kubernetes Architecture

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Kubernetes Cluster                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │                    Control Plane (Master)                     │  │
│  ├──────────────────────────────────────────────────────────────┤  │
│  │                                                                │  │
│  │  ┌───────────────┐  ┌──────────────┐  ┌──────────────────┐  │  │
│  │  │   API Server  │  │   Scheduler  │  │ Controller Mgr   │  │  │
│  │  │               │  │              │  │                  │  │  │
│  │  │  (kube-api)   │  │(kube-scheduler│  │(kube-controller) │  │  │
│  │  └───────┬───────┘  └──────┬───────┘  └────────┬─────────┘  │  │
│  │          │                  │                    │            │  │
│  │  ┌───────┴──────────────────┴────────────────────┴─────────┐ │  │
│  │  │                       etcd                                │ │  │
│  │  │            (Distributed Key-Value Store)                  │ │  │
│  │  └───────────────────────────────────────────────────────────┘ │  │
│  │                                                                │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                              │                                       │
│                              │ kubectl                               │
│                              │                                       │
│  ┌──────────────────────────┴────────────────────────────────────┐ │
│  │                     Worker Nodes                               │ │
│  ├────────────────────────────────────────────────────────────────┤ │
│  │                                                                 │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │ │
│  │  │   Node 1     │  │   Node 2     │  │   Node 3     │        │ │
│  │  ├──────────────┤  ├──────────────┤  ├──────────────┤        │ │
│  │  │              │  │              │  │              │        │ │
│  │  │  ┌────────┐  │  │  ┌────────┐  │  │  ┌────────┐  │        │ │
│  │  │  │ kubelet│  │  │  │ kubelet│  │  │  │ kubelet│  │        │ │
│  │  │  └────────┘  │  │  └────────┘  │  │  └────────┘  │        │ │
│  │  │  ┌────────┐  │  │  ┌────────┐  │  │  ┌────────┐  │        │ │
│  │  │  │kube-   │  │  │  │kube-   │  │  │  │kube-   │  │        │ │
│  │  │  │proxy   │  │  │  │proxy   │  │  │  │proxy   │  │        │ │
│  │  │  └────────┘  │  │  └────────┘  │  │  └────────┘  │        │ │
│  │  │              │  │              │  │              │        │ │
│  │  │ ┌──────────┐ │  │ ┌──────────┐ │  │ ┌──────────┐ │        │ │
│  │  │ │Container │ │  │ │Container │ │  │ │Container │ │        │ │
│  │  │ │ Runtime  │ │  │ │ Runtime  │ │  │ │ Runtime  │ │        │ │
│  │  │ │(Docker/  │ │  │ │(containerd│ │  │ │(CRI-O)   │ │        │ │
│  │  │ │containerd│ │  │ │)         │ │  │ │          │ │        │ │
│  │  │ └──────────┘ │  │ └──────────┘ │  │ └──────────┘ │        │ │
│  │  │              │  │              │  │              │        │ │
│  │  │ ┌──────────┐ │  │ ┌──────────┐ │  │ ┌──────────┐ │        │ │
│  │  │ │  Pods    │ │  │ │  Pods    │ │  │ │  Pods    │ │        │ │
│  │  │ └──────────┘ │  │ └──────────┘ │  │ └──────────┘ │        │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘        │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                       │
└───────────────────────────────────────────────────────────────────────┘
```

## Control Plane Components

### 1. API Server (kube-apiserver)
- **Front-end** for the Kubernetes control plane
- Exposes **REST API** for all operations
- All components communicate through API server
- Validates and processes API requests
- Updates etcd with cluster state

**Key Functions:**
- Authentication and authorization
- Admission control
- API validation
- Interface for kubectl

### 2. etcd
- **Distributed key-value store**
- Stores all cluster data and configuration
- Source of truth for cluster state
- Highly available and consistent
- Uses Raft consensus algorithm

**Stores:**
- Cluster configuration
- Current state of all objects
- Metadata

### 3. Scheduler (kube-scheduler)
- Assigns pods to nodes
- Considers resource requirements
- Respects constraints and affinity rules
- Optimizes resource utilization

**Scheduling Factors:**
- Resource availability (CPU, memory)
- Node selector and affinity
- Taints and tolerations
- Pod priority and preemption

### 4. Controller Manager (kube-controller-manager)
- Runs controller processes
- Watches cluster state via API server
- Takes corrective actions

**Built-in Controllers:**
- **Node Controller**: Monitors node health
- **Replication Controller**: Maintains correct number of pods
- **Endpoints Controller**: Populates endpoint objects
- **Service Account Controller**: Creates default service accounts
- **Namespace Controller**: Manages namespace lifecycle

### 5. Cloud Controller Manager (optional)
- Interacts with cloud provider APIs
- Manages cloud-specific resources

**Controllers:**
- Node controller
- Route controller
- Service controller
- Volume controller

## Worker Node Components

### 1. kubelet
- **Primary node agent**
- Runs on every worker node
- Communicates with API server
- Manages pod lifecycle

**Responsibilities:**
- Pod creation and deletion
- Health monitoring (probes)
- Resource management
- Volume mounting
- Reports node and pod status

### 2. kube-proxy
- **Network proxy** on each node
- Maintains network rules
- Enables service abstraction
- Implements Service concept

**Modes:**
- **iptables**: Default, uses iptables rules
- **IPVS**: High performance, uses IPVS
- **userspace**: Legacy, proxies connections

### 3. Container Runtime
- Runs containers
- Pulls images from registry
- Manages container lifecycle

**Supported Runtimes:**
- containerd
- CRI-O
- Docker Engine (deprecated)

## Pod Architecture

```
┌────────────────────────────────────────┐
│              Pod                        │
├────────────────────────────────────────┤
│                                         │
│  ┌──────────────────────────────────┐  │
│  │     Pause Container               │  │
│  │  (Infrastructure Container)       │  │
│  │  - Holds network namespace        │  │
│  │  - Shares IP with all containers  │  │
│  └──────────────────────────────────┘  │
│                                         │
│  ┌──────────────┐  ┌──────────────┐   │
│  │ Container 1  │  │ Container 2  │   │
│  │              │  │              │   │
│  │  App Code    │  │  Sidecar     │   │
│  └──────────────┘  └──────────────┘   │
│                                         │
│  ┌──────────────────────────────────┐  │
│  │     Shared Storage Volumes       │  │
│  └──────────────────────────────────┘  │
│                                         │
│  Network: Pod IP (shared)              │
│  IPC: Shared                           │
│  UTS: Shared hostname                  │
└────────────────────────────────────────┘
```

## Networking Architecture

```
┌───────────────────────────────────────────────────────────────┐
│                     Cluster Network                            │
├───────────────────────────────────────────────────────────────┤
│                                                                 │
│  Pod Network (10.244.0.0/16)                                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
│  │ Pod A       │  │ Pod B       │  │ Pod C       │           │
│  │ 10.244.1.2  │  │ 10.244.1.3  │  │ 10.244.2.2  │           │
│  └─────────────┘  └─────────────┘  └─────────────┘           │
│         │                │                │                    │
│         └────────────────┴────────────────┘                    │
│                          │                                     │
│  Service Network (10.96.0.0/12)                               │
│  ┌──────────────────────┴────────────────────────┐            │
│  │           Service (ClusterIP)                  │            │
│  │           10.96.0.10:80                        │            │
│  │           ┌────────────────┐                   │            │
│  │           │  Load Balancer │                   │            │
│  │           └────────────────┘                   │            │
│  └────────────────────────────────────────────────┘            │
│                          │                                     │
│  ┌──────────────────────┴────────────────────────┐            │
│  │              Ingress Controller                │            │
│  │              (nginx/traefik)                   │            │
│  └────────────────────────────────────────────────┘            │
│                          │                                     │
└──────────────────────────┼─────────────────────────────────────┘
                           │
                    External Traffic
```

## Storage Architecture

```
┌──────────────────────────────────────────────────────────┐
│                   Storage System                          │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  ┌──────────────────────────────────────────────────┐    │
│  │         Storage Class                             │    │
│  │  (Defines storage types and provisioners)         │    │
│  └────────────────┬─────────────────────────────────┘    │
│                   │                                        │
│  ┌────────────────▼─────────────────────────────────┐    │
│  │     Persistent Volume (PV)                        │    │
│  │  - Actual storage resource                        │    │
│  │  - Provisioned by admin or dynamically            │    │
│  └────────────────┬─────────────────────────────────┘    │
│                   │                                        │
│  ┌────────────────▼─────────────────────────────────┐    │
│  │  Persistent Volume Claim (PVC)                    │    │
│  │  - Request for storage by user                    │    │
│  │  - Binds to available PV                          │    │
│  └────────────────┬─────────────────────────────────┘    │
│                   │                                        │
│  ┌────────────────▼─────────────────────────────────┐    │
│  │            Pod Volume Mount                       │    │
│  │  - Mounted into container filesystem              │    │
│  └──────────────────────────────────────────────────┘    │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

## Request Flow

### Pod Creation Flow

```
1. User → kubectl apply -f pod.yaml

2. kubectl → API Server
   ↓
3. API Server
   - Authenticates & Authorizes
   - Validates request
   - Writes to etcd
   ↓
4. Scheduler (watching API Server)
   - Detects unscheduled pod
   - Selects best node
   - Updates pod spec with node name
   ↓
5. API Server → Updates etcd
   ↓
6. kubelet (on selected node, watching API Server)
   - Detects pod assigned to its node
   - Pulls container image
   - Creates containers via container runtime
   - Reports status back to API Server
   ↓
7. API Server → Updates pod status in etcd
   ↓
8. kubectl get pods → Shows pod running
```

### Service Request Flow

```
External Request → Ingress → Service → Pods

1. Client makes HTTP request
   ↓
2. DNS Resolution (if using hostname)
   - kube-dns/CoreDNS resolves service name
   ↓
3. Request reaches Service IP
   ↓
4. kube-proxy (iptables/IPVS rules)
   - Intercepts request
   - Selects backend pod
   - NATs to pod IP
   ↓
5. Request forwarded to Pod
   ↓
6. Container processes request
   ↓
7. Response returns same path (reversed)
```

## Deployment Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    Deployment                                │
│  - Manages ReplicaSets                                       │
│  - Handles rolling updates                                   │
└─────────────────┬───────────────────────────────────────────┘
                  │
    ┌─────────────┴─────────────┐
    │                           │
    ▼                           ▼
┌────────────────┐      ┌────────────────┐
│ ReplicaSet v1  │      │ ReplicaSet v2  │
│ (Old version)  │      │ (New version)  │
│ Replicas: 0    │      │ Replicas: 3    │
└────────────────┘      └───────┬────────┘
                                │
                    ┌───────────┼───────────┐
                    │           │           │
                    ▼           ▼           ▼
                ┌──────┐    ┌──────┐    ┌──────┐
                │Pod 1 │    │Pod 2 │    │Pod 3 │
                └──────┘    └──────┘    └──────┘
```

## Namespace Isolation

```
┌──────────────────────────────────────────────────────────┐
│                   Kubernetes Cluster                      │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  ┌─────────────────┐  ┌─────────────────┐               │
│  │  Namespace: dev │  │ Namespace: prod │               │
│  ├─────────────────┤  ├─────────────────┤               │
│  │ - Pods          │  │ - Pods          │               │
│  │ - Services      │  │ - Services      │               │
│  │ - Deployments   │  │ - Deployments   │               │
│  │ - ConfigMaps    │  │ - ConfigMaps    │               │
│  │ - Secrets       │  │ - Secrets       │               │
│  │                 │  │                 │               │
│  │ Resource Quotas │  │ Resource Quotas │               │
│  │ Network Policies│  │ Network Policies│               │
│  └─────────────────┘  └─────────────────┘               │
│                                                            │
│  ┌──────────────────────────────────────┐                │
│  │    Cluster-wide Resources            │                │
│  │  - Nodes                              │                │
│  │  - PersistentVolumes                  │                │
│  │  - Namespaces                         │                │
│  │  - ClusterRoles                       │                │
│  └──────────────────────────────────────┘                │
└──────────────────────────────────────────────────────────┘
```

## Security Architecture

```
┌──────────────────────────────────────────────────────────┐
│               Authentication & Authorization              │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  1. Authentication                                         │
│     ┌──────────────────────────────────────┐             │
│     │ - X.509 Certificates                  │             │
│     │ - Bearer Tokens                       │             │
│     │ - Service Accounts                    │             │
│     │ - OpenID Connect                      │             │
│     └──────────────────────────────────────┘             │
│                      │                                     │
│  2. Authorization    ▼                                     │
│     ┌──────────────────────────────────────┐             │
│     │ RBAC (Role-Based Access Control)     │             │
│     │  ├─ Roles / ClusterRoles             │             │
│     │  └─ RoleBindings / ClusterRoleBindings│            │
│     └──────────────────────────────────────┘             │
│                      │                                     │
│  3. Admission Control▼                                     │
│     ┌──────────────────────────────────────┐             │
│     │ - PodSecurityPolicy                   │             │
│     │ - ResourceQuota                       │             │
│     │ - LimitRanger                         │             │
│     │ - Custom Admission Webhooks           │             │
│     └──────────────────────────────────────┘             │
│                      │                                     │
│                      ▼                                     │
│              API Request Processed                         │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

## Key Architectural Principles

### 1. Declarative Configuration
- Define desired state, not steps
- Kubernetes ensures actual state matches desired state

### 2. API-Driven
- Everything accessible through REST API
- Consistent interface for all operations

### 3. Self-Healing
- Automatic restart of failed containers
- Rescheduling of pods on node failure
- Replacement of containers that don't pass health checks

### 4. Scalability
- Horizontal pod autoscaling
- Vertical pod autoscaling
- Cluster autoscaling

### 5. Extensibility
- Custom Resource Definitions (CRDs)
- Operators for complex applications
- Admission webhooks for custom policies

### 6. Portability
- Cloud-agnostic design
- Runs on any infrastructure
- Consistent API across environments
