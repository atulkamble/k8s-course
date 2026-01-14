# AKS Commands Cheat Sheet

## Cluster Management
- **Create AKS Cluster:**
  ```sh
  az aks create --resource-group <ResourceGroupName> --name <AKSClusterName> --node-count 3 --enable-addons monitoring --generate-ssh-keys
  ```
- **Get Credentials:**
  ```sh
  az aks get-credentials --resource-group <ResourceGroupName> --name <AKSClusterName>
  ```
- **List Clusters:**
  ```sh
  az aks list -o table
  ```
- **Delete Cluster:**
  ```sh
  az aks delete --resource-group <ResourceGroupName> --name <AKSClusterName>
  ```

## Node Pool Operations
- **Scale Node Pool:**
  ```sh
  az aks scale --resource-group <ResourceGroupName> --name <AKSClusterName> --node-count 5
  ```
- **Add Node Pool:**
  ```sh
  az aks nodepool add --resource-group <ResourceGroupName> --cluster-name <AKSClusterName> --name <NodePoolName> --node-count 2
  ```
- **List Node Pools:**
  ```sh
  az aks nodepool list --resource-group <ResourceGroupName> --cluster-name <AKSClusterName> -o table
  ```

## Upgrades & Maintenance
- **Upgrade Cluster:**
  ```sh
  az aks upgrade --resource-group <ResourceGroupName> --name <AKSClusterName> --kubernetes-version <version>
  ```
- **Upgrade Node Pool:**
  ```sh
  az aks nodepool upgrade --resource-group <ResourceGroupName> --cluster-name <AKSClusterName> --name <NodePoolName> --kubernetes-version <version>
  ```

## Monitoring & Diagnostics
- **Enable Monitoring:**
  ```sh
  az aks enable-addons --resource-group <ResourceGroupName> --name <AKSClusterName> --addons monitoring
  ```
- **View Cluster Nodes:**
  ```sh
  kubectl get nodes
  ```
- **View Cluster Info:**
  ```sh
  kubectl cluster-info
  ```

## Useful Links
- [AKS CLI Reference](https://learn.microsoft.com/cli/azure/aks)
- [kubectl Reference](https://kubernetes.io/docs/reference/kubectl/)
