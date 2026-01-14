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

//create job 
kubectl apply -f job.yaml
kubectl get jobs
kubectl get jobs -o wide
kubectl get jobs -w
kubectl describe job batch-job
kubectl get pods
kubectl get pods --selector=job-name=batch-job
kubectl get pods -o wide
kubectl logs $(kubectl get pods --selector=job-name=batch-job -o jsonpath='{.items[0].metadata.name}')
kubectl get job batch-job
kubectl get job batch-job -o jsonpath='{.status.succeeded}'
kubectl get job batch-job -o jsonpath='{.status.failed}'
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl describe pod $(kubectl get pods --selector=job-name=batch-job -o jsonpath='{.items[0].metadata.name}')
kubectl apply -f job.yaml --dry-run=client
kubectl get job batch-job -o yaml > exported-job.yaml
kubectl delete job batch-job
kubectl apply -f job.yaml
kubectl delete job batch-job --cascade=foreground
kubectl create job echo-job --image=busybox -- echo "Hello Kubernetes"
kubectl delete jobs --all

// cron job 
kubectl apply -f cron-job.yaml
kubectl explain cronjob.spec.jobTemplate.spec.template.spec
kubectl get cronjob
kubectl describe cronjob daily-job
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get pods
kubectl logs <pod-name>
nano cron-job.yaml
kubectl create job test-job --from=cronjob/daily-job
kubectl get jobs
kubectl describe job test-job
kubectl get jobs --watch
kubectl get pods --watch
kubectl delete cronjob daily-job
kubectl delete job test-job
kubectl delete pod <pod-name>

// create hpa
nano hpa.yaml
kubectl apply -f hpa.yaml 
cat hpa.yam
nano hpa.yaml
cat hpa.yaml
kubectl apply -f hpa.yaml
kubectl delete -f hpa.yaml
kubectl replace -f hpa.yaml
kubectl get hpa
kubectl get hpa web-hpa
kubectl describe hpa web-hpa
kubectl explain hpa
kubectl explain hpa.spec
kubectl explain hpa.spec.metrics
kubectl get deployments
kubectl get deployment web-deployment
kubectl describe deployment web-deployment
kubectl get pods -l app=web
kubectl top nodes
kubectl top pods
kubectl top pods -l app=web
kubectl get --raw /apis/metrics.k8s.io/v1beta1/pods
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes
kubectl get pods -w
kubectl describe hpa web-hpa
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get deployment metrics-server -n kube-system
kubectl logs -n kube-system deployment/metrics-server
kubectl get apiservices | grep metrics
kubectl run load-generator \
  --image=busybox \
  --restart=Never \
  -- /bin/sh -c "while true; do wget -q -O- http://web-service; done"
kubectl delete pod load-generator
kubectl delete hpa web-hpa
kubectl delete deployment web-deployment
```
