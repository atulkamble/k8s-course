<div align="center">
<h1>🚀 Kubernetes Complete Course</h1>
<p><strong>Built with ❤️ by <a href="https://github.com/atulkamble">Atul Kamble</a></strong></p>

<p>
<a href="https://codespaces.new/atulkamble/k8s-course.git">
<img src="https://github.com/codespaces/badge.svg" alt="Open in GitHub Codespaces" />
</a>
<a href="https://vscode.dev/github/atulkamble/k8s-course">
<img src="https://img.shields.io/badge/Open%20with-VS%20Code-007ACC?logo=visualstudiocode&style=for-the-badge" alt="Open with VS Code" />
</a>
<a href="https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/atulkamble/k8s-course">
<img src="https://img.shields.io/badge/Dev%20Containers-Ready-blue?logo=docker&style=for-the-badge" />
</a>
<a href="https://desktop.github.com/">
<img src="https://img.shields.io/badge/GitHub-Desktop-6f42c1?logo=github&style=for-the-badge" />
</a>
</p>

<p>
<a href="https://github.com/atulkamble">
<img src="https://img.shields.io/badge/GitHub-atulkamble-181717?logo=github&style=flat-square" />
</a>
<a href="https://www.linkedin.com/in/atuljkamble/">
<img src="https://img.shields.io/badge/LinkedIn-atuljkamble-0A66C2?logo=linkedin&style=flat-square" />
</a>
<a href="https://x.com/atul_kamble">
<img src="https://img.shields.io/badge/X-@atul_kamble-000000?logo=x&style=flat-square" />
</a>
</p>

<strong>Version 1.0.0</strong> | <strong>Last Updated:</strong> January 2026
</div>

---

# Kubernetes Complete Course

A comprehensive guide to Kubernetes with commands, code examples, concepts, definitions, cheatsheets, projects, and architecture diagrams.

## 📚 Course Overview

This course is designed to take you from Kubernetes basics to advanced deployments with hands-on projects and real-world examples.

## 🗂️ Course Structure

### 1. [Fundamentals](01-fundamentals/)
Learn the core concepts and definitions of Kubernetes.

- **[Core Concepts](01-fundamentals/concepts.md)** - Comprehensive guide to Kubernetes objects and components
- **[Kubernetes Benefits](01-fundamentals/kubernetes-benefits.md)** - Why use Kubernetes and common use cases

**Topics Covered:**
- What is Kubernetes
- Pods, Deployments, Services
- Namespaces, ConfigMaps, Secrets
- Labels, Selectors, Annotations
- Resource Management
- Probes (Liveness, Readiness, Startup)
- Scaling concepts (HPA, VPA, Cluster Autoscaler)

### 2. [Architecture](02-architecture/)
Understand the internal architecture and components of Kubernetes.

- **[Kubernetes Architecture](02-architecture/kubernetes-architecture.md)** - Detailed architecture diagrams and explanations

**Topics Covered:**
- Control Plane Components (API Server, etcd, Scheduler, Controller Manager)
- Worker Node Components (kubelet, kube-proxy, Container Runtime)
- Pod Architecture
- Networking Architecture
- Storage Architecture
- Request Flow Diagrams
- Security Architecture

### 3. [Commands](03-commands/)
Master kubectl and essential Kubernetes commands.

- **[kubectl Commands](03-commands/kubectl-commands.md)** - Complete command reference with examples

**Sections:**
- Cluster Information
- Context and Configuration
- Namespace Operations
- Pod Operations
- Deployment Operations
- Service Operations
- ConfigMap & Secret Operations
- Volume and Storage Operations
- RBAC Operations
- Debugging and Troubleshooting
- Advanced Operations

### 4. [YAML Examples](04-yaml-examples/)
Ready-to-use YAML configurations for various Kubernetes resources.

- **[Pods](04-yaml-examples/01-pods.yaml)** - Basic to advanced pod configurations
- **[Deployments](04-yaml-examples/02-deployments.yaml)** - Deployment strategies and patterns
- **[Services](04-yaml-examples/03-services.yaml)** - All service types with examples
- **[ConfigMaps & Secrets](04-yaml-examples/04-configmaps-secrets.yaml)** - Configuration management
- **[Storage](04-yaml-examples/05-storage.yaml)** - PV, PVC, StorageClass examples
- **[Advanced Resources](04-yaml-examples/06-advanced.yaml)** - Ingress, NetworkPolicy, HPA, StatefulSet, DaemonSet, Jobs

### 5. [Projects](05-projects/)
Hands-on projects to practice Kubernetes deployments.

- **[Project 1: Simple Web Application](05-projects/project1-simple-web-app.md)**
  - Deploy NGINX with custom content
  - ConfigMaps and Services
  - Scaling and rolling updates
  - **Difficulty:** Beginner

- **[Project 2: Multi-Tier Application](05-projects/project2-multi-tier-app.md)**
  - Frontend, Backend, and Database
  - StatefulSet for PostgreSQL
  - Service discovery
  - Ingress configuration
  - **Difficulty:** Intermediate

- **[Project 3: Microservices Architecture](05-projects/project3-microservices.md)**
  - Multiple microservices
  - API Gateway pattern
  - Service mesh basics
  - Network policies
  - **Difficulty:** Advanced

### 6. [Cheat Sheets](06-cheatsheets/)
Quick reference guides for common operations.

- **[Kubernetes Cheat Sheet](06-cheatsheets/kubernetes-cheatsheet.md)** - Quick reference for all essential commands

**Includes:**
- Essential commands
- Resource short names
- Common operations
- YAML templates
- JSONPath queries
- Troubleshooting tips
- Pro tips and aliases

## 🚀 Getting Started

### Prerequisites

```bash
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client

# Install minikube (for local testing)
brew install minikube

# Start local cluster
minikube start
```

### Quick Start

1. **Learn the basics**: Start with [Fundamentals](01-fundamentals/)
2. **Understand architecture**: Read [Architecture](02-architecture/)
3. **Practice commands**: Use [Commands](03-commands/) reference
4. **Deploy examples**: Try [YAML Examples](04-yaml-examples/)
5. **Build projects**: Complete [Projects](05-projects/) step-by-step

## 📖 Learning Path

### Beginner Path
1. Read Fundamentals concepts
2. Setup local Kubernetes cluster (minikube)
3. Practice basic kubectl commands
4. Deploy simple pods and deployments
5. Complete Project 1

### Intermediate Path
1. Understand Kubernetes architecture
2. Learn about Services and Ingress
3. Practice with ConfigMaps and Secrets
4. Implement storage solutions
5. Complete Project 2

### Advanced Path
1. Master RBAC and security
2. Implement Network Policies
3. Setup monitoring and logging
4. Learn about StatefulSets and DaemonSets
5. Complete Project 3

## 🛠️ Common Commands

```bash
# Cluster info
kubectl cluster-info
kubectl get nodes

# Deploy application
kubectl apply -f deployment.yaml

# Check status
kubectl get pods
kubectl get services

# View logs
kubectl logs <pod-name>

# Execute command in pod
kubectl exec -it <pod-name> -- /bin/bash

# Scale deployment
kubectl scale deployment <name> --replicas=3

# Delete resources
kubectl delete -f deployment.yaml
```

## 📊 Architecture Overview

```
┌─────────────────────────────────────────┐
│        Kubernetes Cluster                │
├─────────────────────────────────────────┤
│                                          │
│  ┌────────────────────────────────┐    │
│  │     Control Plane (Master)      │    │
│  │  - API Server                   │    │
│  │  - etcd                          │    │
│  │  - Scheduler                     │    │
│  │  - Controller Manager            │    │
│  └────────────────────────────────┘    │
│              │                           │
│              │                           │
│  ┌───────────┴──────────────────────┐  │
│  │       Worker Nodes                │  │
│  │  - kubelet                        │  │
│  │  - kube-proxy                     │  │
│  │  - Container Runtime              │  │
│  │  - Pods                           │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

## 🔑 Key Concepts

| Concept | Description |
|---------|-------------|
| **Pod** | Smallest deployable unit, contains one or more containers |
| **Deployment** | Manages replica sets and rolling updates |
| **Service** | Exposes pods and provides stable networking |
| **ConfigMap** | Stores non-sensitive configuration data |
| **Secret** | Stores sensitive information (passwords, keys) |
| **Volume** | Persistent storage for pods |
| **Namespace** | Virtual clusters for resource isolation |
| **Ingress** | Manages external access to services |

## 📚 Additional Resources

- **Official Kubernetes Documentation**: https://kubernetes.io/docs/
- **kubectl Reference**: https://kubernetes.io/docs/reference/kubectl/
- **Kubernetes GitHub**: https://github.com/kubernetes/kubernetes
- **CNCF Landscape**: https://landscape.cncf.io/

## 🎯 Course Goals

By the end of this course, you will be able to:

✅ Understand Kubernetes architecture and components  
✅ Deploy and manage containerized applications  
✅ Configure networking and storage  
✅ Implement security best practices  
✅ Scale applications automatically  
✅ Troubleshoot common issues  
✅ Deploy production-ready applications  

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 📄 License

This course is open source and available under the MIT License.

## 🌟 Support

If you find this course helpful, please give it a star ⭐

---

**Happy Learning! 🚀**
