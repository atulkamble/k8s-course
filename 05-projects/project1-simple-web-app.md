# Project 1: Deploy a Simple Web Application

## Objective
Deploy a simple NGINX web application with custom content using Kubernetes.

## Prerequisites
- Kubernetes cluster (minikube, kind, or cloud provider)
- kubectl installed and configured

## Project Structure
```
project1/
├── deployment.yaml
├── service.yaml
├── configmap.yaml
└── README.md
```

## Step 1: Create a ConfigMap with Custom HTML

Create `configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-content
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
        <title>My Kubernetes App</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                text-align: center;
                padding: 50px;
                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                color: white;
            }
            h1 { font-size: 3em; }
        </style>
    </head>
    <body>
        <h1>Welcome to My Kubernetes Application!</h1>
        <p>This is deployed using Kubernetes</p>
        <p>Hostname: <span id="hostname"></span></p>
        <script>
            document.getElementById('hostname').textContent = window.location.hostname;
        </script>
    </body>
    </html>
```

Apply: `kubectl apply -f configmap.yaml`

## Step 2: Create Deployment

Create `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        volumeMounts:
        - name: web-content
          mountPath: /usr/share/nginx/html
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 3
      volumes:
      - name: web-content
        configMap:
          name: web-content
```

Apply: `kubectl apply -f deployment.yaml`

## Step 3: Create Service

Create `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
```

Apply: `kubectl apply -f service.yaml`

## Step 4: Verify Deployment

```bash
# Check pods
kubectl get pods

# Check deployment
kubectl get deployment web-app

# Check service
kubectl get service web-service

# Get service URL
kubectl get service web-service -o wide
```

## Step 5: Access Application

```bash
# For minikube
minikube service web-service

# For cloud providers, get external IP
kubectl get service web-service
# Visit http://<EXTERNAL-IP>
```

## Step 6: Scale the Application

```bash
# Scale to 5 replicas
kubectl scale deployment web-app --replicas=5

# Verify
kubectl get pods
```

## Step 7: Update the Application

```bash
# Update nginx image version
kubectl set image deployment/web-app nginx=nginx:1.22

# Watch rollout
kubectl rollout status deployment/web-app

# Check rollout history
kubectl rollout history deployment/web-app
```

## Step 8: Cleanup

```bash
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
kubectl delete -f configmap.yaml
```

## Commands Summary

```bash
# Deploy everything
kubectl apply -f .

# View all resources
kubectl get all

# View logs
kubectl logs -l app=web

# Describe deployment
kubectl describe deployment web-app

# Delete everything
kubectl delete -f .
```

## Learning Outcomes
- Created and used ConfigMaps
- Deployed multi-replica applications
- Exposed applications using Services
- Implemented health checks
- Scaled applications
- Performed rolling updates
