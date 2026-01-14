# AKS Commands List 

```bash
az login
az account set --subscription <SUBSCRIPTION_ID>
az extension add --name aks-preview
az provider register --namespace Microsoft.ContainerService
```

---

## 🔹 1. Resource Group (AKS Dependency)

```bash
az group create --name aks-rg --location eastus
az group list
az group show --name aks-rg
az group delete --name aks-rg --yes --no-wait
```

---

## 🔹 2. AKS Cluster – Create & List

```bash
az aks create \
  --resource-group aks-rg \
  --name aks-cluster \
  --node-count 2 \
  --node-vm-size Standard_DS2_v2 \
  --enable-managed-identity \
  --enable-addons monitoring \
  --generate-ssh-keys
```

```bash
az aks list
az aks show --resource-group aks-rg --name aks-cluster
az aks get-credentials --resource-group aks-rg --name aks-cluster
az aks get-credentials --resource-group aks-rg --name aks-cluster --overwrite-existing
```

---

## 🔹 3. Node Pools (Agent Pools)

```bash
az aks nodepool list --resource-group aks-rg --cluster-name aks-cluster
```

```bash
az aks nodepool add \
  --resource-group aks-rg \
  --cluster-name aks-cluster \
  --name userpool \
  --node-count 2 \
  --node-vm-size Standard_DS2_v2 \
  --mode User
```

```bash
az aks nodepool scale \
  --resource-group aks-rg \
  --cluster-name aks-cluster \
  --name userpool \
  --node-count 3
```

```bash
az aks nodepool update \
  --resource-group aks-rg \
  --cluster-name aks-cluster \
  --name userpool \
  --enable-cluster-autoscaler \
  --min-count 2 \
  --max-count 5
```

```bash
az aks nodepool delete \
  --resource-group aks-rg \
  --cluster-name aks-cluster \
  --name userpool
```

---

## 🔹 4. Scale AKS Cluster

```bash
az aks scale \
  --resource-group aks-rg \
  --name aks-cluster \
  --node-count 3
```

---

## 🔹 5. Kubernetes Version Management

```bash
az aks get-versions --location eastus
```

```bash
az aks upgrade \
  --resource-group aks-rg \
  --name aks-cluster \
  --kubernetes-version 1.29.2 \
  --yes
```

```bash
az aks nodepool upgrade \
  --resource-group aks-rg \
  --cluster-name aks-cluster \
  --name nodepool1 \
  --kubernetes-version 1.29.2
```

---

## 🔹 6. Start / Stop AKS (Cost Saving)

```bash
az aks stop --resource-group aks-rg --name aks-cluster
az aks start --resource-group aks-rg --name aks-cluster
```

---

## 🔹 7. Identity & Access (RBAC / AAD)

```bash
az aks update \
  --resource-group aks-rg \
  --name aks-cluster \
  --enable-aad
```

```bash
az aks update \
  --resource-group aks-rg \
  --name aks-cluster \
  --disable-local-accounts
```

```bash
az aks show --resource-group aks-rg --name aks-cluster --query identity
```

---

## 🔹 8. Networking

```bash
az aks show \
  --resource-group aks-rg \
  --name aks-cluster \
  --query networkProfile
```

```bash
az aks update \
  --resource-group aks-rg \
  --name aks-cluster \
  --load-balancer-sku standard
```

---

## 🔹 9. Monitoring & Logs

```bash
az aks enable-addons \
  --resource-group aks-rg \
  --name aks-cluster \
  --addons monitoring
```

```bash
az aks disable-addons \
  --resource-group aks-rg \
  --name aks-cluster \
  --addons monitoring
```

```bash
az aks show \
  --resource-group aks-rg \
  --name aks-cluster \
  --query addonProfiles
```

---

## 🔹 10. Attach / Detach Azure Container Registry (ACR)

```bash
az aks update \
  --resource-group aks-rg \
  --name aks-cluster \
  --attach-acr myacr
```

```bash
az aks update \
  --resource-group aks-rg \
  --name aks-cluster \
  --detach-acr myacr
```

---

## 🔹 11. Maintenance Windows

```bash
az aks maintenanceconfiguration list \
  --resource-group aks-rg \
  --cluster-name aks-cluster
```

---

## 🔹 12. Backup & Restore (Preview)

```bash
az aks enable-backup \
  --resource-group aks-rg \
  --name aks-cluster
```

---

## 🔹 13. Diagnostics

```bash
az aks show --resource-group aks-rg --name aks-cluster --output table
az aks check-acr --resource-group aks-rg --name aks-cluster --acr myacr
```

---

## 🔹 14. Delete AKS Cluster

```bash
az aks delete \
  --resource-group aks-rg \
  --name aks-cluster \
  --yes --no-wait
```

---

## 🔹 15. Useful kubectl (Post-AKS)

```bash
kubectl get nodes
kubectl get pods -A
kubectl get svc -A
kubectl describe node <node-name>
kubectl cluster-info
```

---

## 🔹 16. All AKS Command Discovery

```bash
az aks --help
az aks nodepool --help
az aks maintenanceconfiguration --help
```

---

## 📌 Pro Tip (Interview + Production)

* **System node pool** → critical workloads
* **User node pool** → application workloads
* Always **upgrade control plane first**
* Use **autoscaler + spot pools** for cost optimization
* Disable **local accounts** for security

---
