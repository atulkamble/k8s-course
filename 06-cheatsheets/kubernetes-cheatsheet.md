# Kubernetes Cheat Sheet

## Quick Reference

### Essential Commands

```bash
kubectl get pods                    # List all pods
kubectl get svc                     # List all services
kubectl get deploy                  # List all deployments
kubectl get all                     # List all resources
kubectl describe pod <name>         # Detailed pod info
kubectl logs <pod-name>             # View pod logs
kubectl exec -it <pod> -- /bin/bash # SSH into pod
kubectl delete pod <name>           # Delete a pod
kubectl apply -f <file.yaml>        # Create/update resource
```

### Resource Types (Short Names)

| Resource | Short Name |
|----------|------------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| statefulsets | sts |
| daemonsets | ds |
| namespaces | ns |
| nodes | no |
| persistentvolumes | pv |
| persistentvolumeclaims | pvc |
| configmaps | cm |
| secrets | secret |
| ingresses | ing |
| storageclasses | sc |
| endpoints | ep |

## Common Operations

### Cluster & Context

```bash
kubectl config view                           # View config
kubectl config get-contexts                   # List contexts
kubectl config current-context                # Current context
kubectl config use-context <context>          # Switch context
kubectl cluster-info                          # Cluster info
```

### Create Resources

```bash
kubectl create deployment <name> --image=<image>
kubectl create service clusterip <name> --tcp=80:80
kubectl create configmap <name> --from-literal=key=value
kubectl create secret generic <name> --from-literal=key=value
kubectl create namespace <name>
kubectl run <name> --image=<image>           # Quick pod
kubectl expose deployment <name> --port=80   # Create service
```

### View & Describe

```bash
kubectl get <resource>                        # List resources
kubectl get <resource> -n <namespace>         # List in namespace
kubectl get <resource> -A                     # List in all namespaces
kubectl get <resource> -o wide                # More details
kubectl get <resource> -o yaml                # YAML output
kubectl get <resource> -o json                # JSON output
kubectl get <resource> --show-labels          # Show labels
kubectl describe <resource> <name>            # Detailed info
```

### Edit & Update

```bash
kubectl edit <resource> <name>                # Edit resource
kubectl scale deployment <name> --replicas=3  # Scale
kubectl set image deployment/<name> container=image:tag
kubectl rollout restart deployment/<name>     # Restart
kubectl rollout undo deployment/<name>        # Rollback
kubectl rollout status deployment/<name>      # Rollout status
kubectl rollout history deployment/<name>     # Rollout history
kubectl apply -f <file>                       # Apply changes
kubectl patch <resource> <name> -p '<json>'   # Patch resource
```

### Delete Resources

```bash
kubectl delete <resource> <name>              # Delete by name
kubectl delete -f <file>                      # Delete from file
kubectl delete pods --all                     # Delete all pods
kubectl delete <resource> -l key=value        # Delete by label
```

### Logs & Debugging

```bash
kubectl logs <pod>                            # Pod logs
kubectl logs <pod> -c <container>             # Container logs
kubectl logs -f <pod>                         # Follow logs
kubectl logs --previous <pod>                 # Previous instance logs
kubectl exec -it <pod> -- <command>           # Execute command
kubectl exec -it <pod> -- /bin/bash           # Interactive shell
kubectl top nodes                             # Node metrics
kubectl top pods                              # Pod metrics
kubectl get events                            # Cluster events
kubectl describe pod <name>                   # Debug pod issues
```

### Port Forwarding & Proxy

```bash
kubectl port-forward <pod> 8080:80            # Forward pod port
kubectl port-forward svc/<service> 8080:80    # Forward service port
kubectl proxy                                 # Start proxy to API server
```

## YAML Structure Template

### Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
  - name: my-container
    image: nginx:1.21
    ports:
    - containerPort: 80
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:1.21
        ports:
        - containerPort: 80
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

### ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key1: value1
  key2: value2
```

### Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=    # base64 encoded
  password: cGFzc3dvcmQ=
```

### Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```

## Labels & Selectors

```bash
# Add label
kubectl label pod <name> env=prod

# Remove label
kubectl label pod <name> env-

# Show labels
kubectl get pods --show-labels

# Filter by label
kubectl get pods -l env=prod
kubectl get pods -l 'env in (prod,dev)'
kubectl get pods -l env!=prod

# Label multiple resources
kubectl label pods --all env=prod
```

## Namespaces

```bash
# Create namespace
kubectl create namespace dev

# Set default namespace
kubectl config set-context --current --namespace=dev

# Get resources in namespace
kubectl get pods -n dev

# Get resources in all namespaces
kubectl get pods -A
kubectl get pods --all-namespaces
```

## Resource Management

```bash
# Set resource requests/limits
kubectl set resources deployment nginx -c=nginx \
  --requests=cpu=100m,memory=128Mi \
  --limits=cpu=200m,memory=256Mi

# Autoscale
kubectl autoscale deployment nginx --min=2 --max=10 --cpu-percent=80
```

## Common Selectors & JSONPath

```bash
# Get pod names
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# Get pod IPs
kubectl get pods -o jsonpath='{.items[*].status.podIP}'

# Get container images
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

# Custom columns
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase
```

## Useful Flags

```bash
--all-namespaces, -A        # All namespaces
--namespace, -n             # Specific namespace
--output, -o                # Output format (json, yaml, wide, name)
--selector, -l              # Label selector
--watch, -w                 # Watch for changes
--dry-run=client            # Preview without creating
--dry-run=server            # Server-side dry run
--force                     # Force operation
--grace-period=0            # Immediate deletion
--help, -h                  # Help
--recursive, -R             # Process directory recursively
```

## Pro Tips

```bash
# Aliases
alias k='kubectl'
alias kg='kubectl get'
alias kd='kubectl describe'
alias ka='kubectl apply -f'
alias kdel='kubectl delete'

# Auto-completion (bash)
source <(kubectl completion bash)
complete -F __start_kubectl k

# Auto-completion (zsh)
source <(kubectl completion zsh)

# Multiple resources
kubectl get pods,services,deployments

# Apply entire directory
kubectl apply -f ./manifests/

# Diff before apply
kubectl diff -f deployment.yaml

# Wait for condition
kubectl wait --for=condition=ready pod -l app=nginx

# Sort by age
kubectl get pods --sort-by=.metadata.creationTimestamp

# Show recent events
kubectl get events --sort-by='.lastTimestamp'
```

## Common Troubleshooting

```bash
# Pod not starting
kubectl describe pod <name>
kubectl logs <name>
kubectl get events

# Check node status
kubectl describe node <name>
kubectl top nodes

# Network issues
kubectl get svc
kubectl get endpoints
kubectl describe service <name>

# Permission issues
kubectl auth can-i <verb> <resource>
kubectl get roles
kubectl get rolebindings

# Resource exhaustion
kubectl top nodes
kubectl top pods
kubectl describe node <name>
```

## Quick Deployment Workflow

```bash
# 1. Create deployment
kubectl create deployment nginx --image=nginx:1.21

# 2. Expose as service
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# 3. Scale up
kubectl scale deployment nginx --replicas=3

# 4. Update image
kubectl set image deployment/nginx nginx=nginx:1.22

# 5. Check rollout
kubectl rollout status deployment/nginx

# 6. Rollback if needed
kubectl rollout undo deployment/nginx
```

## Resource Limits

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**CPU**: 1 CPU = 1000m (millicores)
**Memory**: Ki, Mi, Gi, Ti (binary) or K, M, G, T (decimal)

## Service Types

- **ClusterIP**: Internal cluster IP (default)
- **NodePort**: Exposes on each node's IP at static port
- **LoadBalancer**: Creates external load balancer
- **ExternalName**: Maps to DNS name

## Restart Policy

- **Always**: Always restart (default for Deployment)
- **OnFailure**: Restart only on failure (for Jobs)
- **Never**: Never restart

## Common Annotations

```yaml
annotations:
  kubernetes.io/change-cause: "Image updated to v2"
  description: "Application frontend"
```
