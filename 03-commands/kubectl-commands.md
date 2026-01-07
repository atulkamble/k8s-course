# Kubernetes Commands Reference

## Cluster Information

```bash
# Display cluster info
kubectl cluster-info

# Display cluster version
kubectl version

# View cluster nodes
kubectl get nodes

# Get detailed node information
kubectl describe node <node-name>

# Display node resource usage
kubectl top nodes
```

## Context and Configuration

```bash
# View current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>

# View kubeconfig
kubectl config view

# Set namespace for current context
kubectl config set-context --current --namespace=<namespace>
```

## Namespace Operations

```bash
# List all namespaces
kubectl get namespaces
kubectl get ns

# Create namespace
kubectl create namespace <namespace-name>

# Delete namespace
kubectl delete namespace <namespace-name>

# Set default namespace
kubectl config set-context --current --namespace=<namespace-name>
```

## Pod Operations

```bash
# List all pods
kubectl get pods
kubectl get po

# List pods in all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# List pods with more details
kubectl get pods -o wide

# Describe pod
kubectl describe pod <pod-name>

# Create pod from YAML
kubectl apply -f pod.yaml

# Delete pod
kubectl delete pod <pod-name>

# Delete pod immediately
kubectl delete pod <pod-name> --grace-period=0 --force

# Get pod logs
kubectl logs <pod-name>

# Get logs from previous container instance
kubectl logs <pod-name> --previous

# Follow logs
kubectl logs -f <pod-name>

# Get logs from specific container in pod
kubectl logs <pod-name> -c <container-name>

# Execute command in pod
kubectl exec <pod-name> -- <command>

# Get interactive shell
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it <pod-name> -- sh

# Copy files to/from pod
kubectl cp <pod-name>:/path/to/file /local/path
kubectl cp /local/path <pod-name>:/path/to/file

# Port forwarding
kubectl port-forward <pod-name> <local-port>:<pod-port>

# Watch pods in real-time
kubectl get pods -w
```

## Deployment Operations

```bash
# List deployments
kubectl get deployments
kubectl get deploy

# Create deployment
kubectl create deployment <name> --image=<image>

# Create deployment from YAML
kubectl apply -f deployment.yaml

# Describe deployment
kubectl describe deployment <deployment-name>

# Edit deployment
kubectl edit deployment <deployment-name>

# Delete deployment
kubectl delete deployment <deployment-name>

# Scale deployment
kubectl scale deployment <deployment-name> --replicas=<count>

# Autoscale deployment
kubectl autoscale deployment <deployment-name> --min=<min> --max=<max> --cpu-percent=<percent>

# Update image
kubectl set image deployment/<deployment-name> <container-name>=<new-image>

# Rollout status
kubectl rollout status deployment/<deployment-name>

# Rollout history
kubectl rollout history deployment/<deployment-name>

# Rollback to previous version
kubectl rollout undo deployment/<deployment-name>

# Rollback to specific revision
kubectl rollout undo deployment/<deployment-name> --to-revision=<revision-number>

# Pause rollout
kubectl rollout pause deployment/<deployment-name>

# Resume rollout
kubectl rollout resume deployment/<deployment-name>

# Restart deployment
kubectl rollout restart deployment/<deployment-name>
```

## Service Operations

```bash
# List services
kubectl get services
kubectl get svc

# Create service
kubectl expose deployment <deployment-name> --port=<port> --target-port=<target-port>

# Create service from YAML
kubectl apply -f service.yaml

# Describe service
kubectl describe service <service-name>

# Delete service
kubectl delete service <service-name>

# Get service endpoints
kubectl get endpoints <service-name>
```

## ConfigMap Operations

```bash
# List ConfigMaps
kubectl get configmaps
kubectl get cm

# Create ConfigMap from literal
kubectl create configmap <name> --from-literal=<key>=<value>

# Create ConfigMap from file
kubectl create configmap <name> --from-file=<path>

# Create ConfigMap from YAML
kubectl apply -f configmap.yaml

# Describe ConfigMap
kubectl describe configmap <configmap-name>

# Edit ConfigMap
kubectl edit configmap <configmap-name>

# Delete ConfigMap
kubectl delete configmap <configmap-name>
```

## Secret Operations

```bash
# List secrets
kubectl get secrets

# Create secret from literal
kubectl create secret generic <name> --from-literal=<key>=<value>

# Create secret from file
kubectl create secret generic <name> --from-file=<path>

# Create TLS secret
kubectl create secret tls <name> --cert=<cert-file> --key=<key-file>

# Create Docker registry secret
kubectl create secret docker-registry <name> \
  --docker-server=<server> \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email>

# Describe secret
kubectl describe secret <secret-name>

# View secret data (base64 encoded)
kubectl get secret <secret-name> -o yaml

# Decode secret
kubectl get secret <secret-name> -o jsonpath='{.data.<key>}' | base64 --decode

# Delete secret
kubectl delete secret <secret-name>
```

## Volume and Storage Operations

```bash
# List Persistent Volumes
kubectl get pv

# List Persistent Volume Claims
kubectl get pvc

# Describe PV
kubectl describe pv <pv-name>

# Describe PVC
kubectl describe pvc <pvc-name>

# List Storage Classes
kubectl get storageclass
kubectl get sc

# Delete PVC
kubectl delete pvc <pvc-name>
```

## Ingress Operations

```bash
# List ingresses
kubectl get ingress
kubectl get ing

# Create ingress from YAML
kubectl apply -f ingress.yaml

# Describe ingress
kubectl describe ingress <ingress-name>

# Delete ingress
kubectl delete ingress <ingress-name>
```

## StatefulSet Operations

```bash
# List StatefulSets
kubectl get statefulsets
kubectl get sts

# Create StatefulSet
kubectl apply -f statefulset.yaml

# Scale StatefulSet
kubectl scale statefulset <name> --replicas=<count>

# Delete StatefulSet
kubectl delete statefulset <name>

# Delete StatefulSet keeping pods
kubectl delete statefulset <name> --cascade=orphan
```

## DaemonSet Operations

```bash
# List DaemonSets
kubectl get daemonsets
kubectl get ds

# Create DaemonSet
kubectl apply -f daemonset.yaml

# Describe DaemonSet
kubectl describe daemonset <name>

# Delete DaemonSet
kubectl delete daemonset <name>
```

## Job and CronJob Operations

```bash
# List Jobs
kubectl get jobs

# Create Job
kubectl apply -f job.yaml

# Delete Job
kubectl delete job <job-name>

# List CronJobs
kubectl get cronjobs
kubectl get cj

# Create CronJob
kubectl apply -f cronjob.yaml

# Suspend CronJob
kubectl patch cronjob <name> -p '{"spec":{"suspend":true}}'

# Resume CronJob
kubectl patch cronjob <name> -p '{"spec":{"suspend":false}}'

# Delete CronJob
kubectl delete cronjob <name>
```

## Resource Management

```bash
# Get all resources
kubectl get all

# Get all resources in namespace
kubectl get all -n <namespace>

# Delete all resources
kubectl delete all --all

# Get resource usage
kubectl top pods
kubectl top nodes

# Set resource requests/limits
kubectl set resources deployment <name> -c=<container> --requests=cpu=200m,memory=512Mi
```

## Labels and Annotations

```bash
# Show labels
kubectl get pods --show-labels

# Add label
kubectl label pod <pod-name> <key>=<value>

# Remove label
kubectl label pod <pod-name> <key>-

# Update label
kubectl label pod <pod-name> <key>=<new-value> --overwrite

# Select by label
kubectl get pods -l <key>=<value>
kubectl get pods --selector=<key>=<value>

# Multiple label selectors
kubectl get pods -l <key1>=<value1>,<key2>=<value2>

# Add annotation
kubectl annotate pod <pod-name> <key>=<value>

# Remove annotation
kubectl annotate pod <pod-name> <key>-
```

## Debugging and Troubleshooting

```bash
# Get events
kubectl get events
kubectl get events --sort-by='.lastTimestamp'

# Describe resource for debugging
kubectl describe <resource-type> <resource-name>

# Check logs
kubectl logs <pod-name>
kubectl logs <pod-name> --previous

# Debug with ephemeral container
kubectl debug <pod-name> -it --image=busybox

# Run temporary pod for debugging
kubectl run tmp-shell --rm -i --tty --image=busybox -- /bin/sh

# Check API resources
kubectl api-resources

# Explain resource
kubectl explain <resource>
kubectl explain pod.spec.containers
```

## RBAC Operations

```bash
# List roles
kubectl get roles

# List cluster roles
kubectl get clusterroles

# List role bindings
kubectl get rolebindings

# List cluster role bindings
kubectl get clusterrolebindings

# Create role
kubectl create role <name> --verb=<verb> --resource=<resource>

# Create role binding
kubectl create rolebinding <name> --role=<role> --user=<user>

# Check permissions
kubectl auth can-i <verb> <resource>
kubectl auth can-i create pods

# Check permissions for user
kubectl auth can-i create pods --as=<user>
```

## Network Policies

```bash
# List network policies
kubectl get networkpolicies
kubectl get netpol

# Create network policy
kubectl apply -f networkpolicy.yaml

# Describe network policy
kubectl describe networkpolicy <name>

# Delete network policy
kubectl delete networkpolicy <name>
```

## Advanced Operations

```bash
# Apply multiple files
kubectl apply -f ./directory/

# Apply from URL
kubectl apply -f https://example.com/file.yaml

# Dry run (test without applying)
kubectl apply -f file.yaml --dry-run=client
kubectl apply -f file.yaml --dry-run=server

# Output in different formats
kubectl get pods -o json
kubectl get pods -o yaml
kubectl get pods -o wide
kubectl get pods -o name

# Use JSONPath
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# Patch resource
kubectl patch deployment <name> -p '{"spec":{"replicas":3}}'

# Replace resource
kubectl replace -f file.yaml

# Create from stdin
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  containers:
  - name: test
    image: nginx
EOF

# Wait for condition
kubectl wait --for=condition=Ready pod/<pod-name>
kubectl wait --for=delete pod/<pod-name> --timeout=60s

# Diff before applying
kubectl diff -f file.yaml
```

## Cleanup Operations

```bash
# Delete by file
kubectl delete -f file.yaml

# Delete all pods in namespace
kubectl delete pods --all -n <namespace>

# Delete resources by label
kubectl delete pods -l <key>=<value>

# Force delete
kubectl delete pod <pod-name> --grace-period=0 --force

# Delete with cascade
kubectl delete deployment <name> --cascade=foreground
```

## Tips and Best Practices

```bash
# Use aliases
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias kl='kubectl logs'
alias kex='kubectl exec -it'

# Use short names
kubectl get po  # pods
kubectl get svc  # services
kubectl get deploy  # deployments
kubectl get ns  # namespaces
kubectl get cm  # configmaps
kubectl get pv  # persistent volumes
kubectl get pvc  # persistent volume claims

# Use --help for any command
kubectl <command> --help

# Tab completion (add to .bashrc or .zshrc)
source <(kubectl completion bash)
source <(kubectl completion zsh)
```
