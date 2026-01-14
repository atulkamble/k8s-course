# AKS Architectures

## 1. Basic AKS Architecture
- **Control Plane:** Managed by Azure (no user access, not billed separately)
- **Node Pools:** User-managed VMs (Linux/Windows)
- **Virtual Network:** Integrates with Azure VNet
- **Load Balancer:** Exposes services externally
- **Azure Container Registry (ACR):** For storing container images

```
+-------------------+         +-------------------+
|   Azure Managed   |         |   User Managed    |
|   Control Plane   | <-----> |   Node Pools      |
+-------------------+         +-------------------+
         |                             |
         v                             v
   Azure VNet <----------------> Azure Services (ACR, LB, etc.)
```

## 2. Advanced AKS Architecture
- **Multiple Node Pools:** For workload isolation (e.g., Linux, Windows, GPU)
- **Availability Zones:** For high availability
- **Private Cluster:** No public endpoint for API server
- **Network Policies:** For secure pod communication
- **Azure AD Integration:** For authentication

```
+-------------------+         +-------------------+
|   Azure Managed   |         |   Multiple Node   |
|   Control Plane   | <-----> |   Pools (Linux,   |
+-------------------+         |   Windows, GPU)   |
         |                             |
         v                             v
   Azure VNet <----------------> Azure Services (ACR, LB, Key Vault, etc.)
         |
         v
   Azure AD, Monitoring, Security
```

## References
- [AKS Architecture Docs](https://learn.microsoft.com/azure/aks/concepts-clusters-workloads)
- [AKS Best Practices](https://learn.microsoft.com/azure/aks/best-practices)
