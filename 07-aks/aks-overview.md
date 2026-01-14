# Azure Kubernetes Service (AKS)

## Introduction
Azure Kubernetes Service (AKS) is a managed Kubernetes service provided by Microsoft Azure. It simplifies deploying, managing, and scaling containerized applications using Kubernetes on Azure.

---

## Key Features
- **Managed Control Plane:** Azure manages the Kubernetes master nodes, upgrades, and patching.
- **Integrated Azure Services:** Seamless integration with Azure Active Directory, Azure Monitor, Azure Policy, and more.
- **Scaling:** Supports manual and auto-scaling of node pools.
- **Security:** Built-in RBAC, network policies, and integration with Azure security services.
- **Multi-AZ Support:** High availability with support for multiple availability zones.

---

## AKS Architecture
- **Control Plane:** Managed by Azure, not billed separately.
- **Node Pools:** User-managed, can run Linux and Windows containers.
- **Virtual Networking:** Integrates with Azure VNet for secure communication.

---

## Common AKS Operations
### 1. Create an AKS Cluster
```sh
az aks create \
  --resource-group <ResourceGroupName> \
  --name <AKSClusterName> \
  --node-count 3 \
  --enable-addons monitoring \
  --generate-ssh-keys
```

### 2. Get AKS Credentials
```sh
az aks get-credentials --resource-group <ResourceGroupName> --name <AKSClusterName>
```

### 3. Scale Node Pool
```sh
az aks scale --resource-group <ResourceGroupName> --name <AKSClusterName> --node-count 5
```

### 4. Upgrade AKS Cluster
```sh
az aks upgrade --resource-group <ResourceGroupName> --name <AKSClusterName> --kubernetes-version <version>
```

---

## Best Practices
- Use Azure AD for authentication and RBAC for authorization.
- Enable monitoring and logging with Azure Monitor.
- Use node pools for workload isolation (e.g., Linux/Windows, GPU/CPU).
- Regularly upgrade Kubernetes versions for security and features.
- Secure cluster networking with network policies and private clusters.

---

## References
- [Azure AKS Documentation](https://learn.microsoft.com/azure/aks/)
- [AKS Best Practices](https://learn.microsoft.com/azure/aks/best-practices)
- [Azure CLI AKS Reference](https://learn.microsoft.com/cli/azure/aks)
