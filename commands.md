```
az group create \
  --name aks \
  --location eastus

az aks create \
  --resource-group aks \
  --name mycluster \
  --node-count 2 \
  --node-vm-size Standard_DS2_v2 \
  --enable-managed-identity \
  --generate-ssh-keys

az aks get-credentials \
  --resource-group aks \
  --name mycluster

// create pod
touch pod.yaml        
nano pod.yaml 
kubectl apply -f pod.yaml 
pod/nginx-pod created
kubectl get pods

// create replicaset 
nano replicaset.yaml
kubectl apply -f replicaset.yaml
kubectl get pods
kubectl get pods
kubectl delete pod nginx-rs-2zzrs
kubectl get pods

// create deployment 
nano deployment.yaml 
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
kubectl delete pod nginx-deploy-8ff954c69-2gpz2
kubectl delete pod nginx-deploy-8ff954c69-x9hjc
kubectl get pods
kubectl get deployments
kubectl delete deployment nginx-deploy
kubectl get deployments

// create service - cluster-ip
nano service-cluster-ip.yaml
kubectl apply -f service-cluster-ip.yaml
kubectl get services
nano service-cluster-ip.yaml

// create service - nodeport 
nano service-nodeport.yaml            
kubectl apply -f service-nodeport.yaml 
kubectl get services


apiVersion: v1
kind: Service
metadata:
  name: demo-nodeport-service
spec:
  type: NodePort
  selector:
    app: demo
  ports:
    - port: 80          # Service port
      targetPort: 80    # Container port
      nodePort: 30007   # NodePort (range: 30000–32767)

// create namespace
nano ns.yaml
kubectl apply -f ns.yaml
kubectl get namespace
kubectl get ns

// create configmap 
nano configmap.yaml
kubectl apply -f configmap.yaml
kubectl get configmap
kubectl get cm

// create secrets 
nano secrets.yaml 
kubectl apply -f secrets.yaml
kubectl get secrets

// pod volume & deployment volume 

nano pod-volume.yaml
nano deployment-volume.yaml
kubectl apply -f pod-volume.yaml
kubectl apply -f deployment-volume.yaml
kubectl get pods
kubectl exec -it volume-demo-pod -- /bin/sh
// get inside container 
cd /data
echo "Hello Kubernetes Volume" > test.txt
ls

// ingress 
nano ingress.yaml 
kubectl apply -f ingress.yaml
kubectl get ingress\n
kubectl get ing\n
kubectl get ingress -n default\n
kubectl describe ingress app-ingress
kubectl get ingress app-ingress -o json\n
kubectl get ingress -A -o wide\n

// daemonset 
nano daemonset.yaml
kubectl apply -f daemonset.yaml
kubectl get daemonset
kubectl get ds -n kube-system
kubectl get ds node-monitor
kubectl get ds -o wide
kubectl describe ds node-monitor
kubectl get pods -l app=monitor
kubectl get pods -l app=monitor -o wide
kubectl get nodes
kubectl get pods -o wide | grep node-monitor
kubectl rollout status daemonset node-monitor
kubectl rollout restart daemonset node-monitor
kubectl logs -l app=monitor
kubectl delete ds node-monitor

```
