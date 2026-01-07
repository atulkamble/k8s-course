# Project 2: Multi-Tier Application (Frontend + Backend + Database)

## Objective
Deploy a complete 3-tier application with frontend, backend API, and database.

## Architecture

```
Internet → Ingress → Frontend Service → Frontend Pods
                          ↓
                   Backend Service → Backend Pods
                          ↓
                   Database Service → Database Pod (StatefulSet)
```

## Project Structure
```
project2/
├── namespace.yaml
├── database/
│   ├── secret.yaml
│   ├── statefulset.yaml
│   └── service.yaml
├── backend/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
├── frontend/
│   ├── deployment.yaml
│   └── service.yaml
└── ingress.yaml
```

## Step 1: Create Namespace

`namespace.yaml`:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: multi-tier-app
```

```bash
kubectl apply -f namespace.yaml
```

## Step 2: Deploy Database Layer

### Create Database Secret
`database/secret.yaml`:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
  namespace: multi-tier-app
type: Opaque
stringData:
  postgres-password: "mysecretpassword"
  postgres-user: "appuser"
  postgres-database: "appdb"
```

### Create StatefulSet for PostgreSQL
`database/statefulset.yaml`:
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: multi-tier-app
spec:
  serviceName: postgres
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:14
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-database
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-user
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 5Gi
```

### Create Database Service
`database/service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: multi-tier-app
spec:
  clusterIP: None
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
```

```bash
kubectl apply -f database/
```

## Step 3: Deploy Backend Layer

### Create Backend ConfigMap
`backend/configmap.yaml`:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: backend-config
  namespace: multi-tier-app
data:
  DATABASE_HOST: "postgres.multi-tier-app.svc.cluster.local"
  DATABASE_PORT: "5432"
  API_PORT: "8080"
```

### Create Backend Deployment
`backend/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: multi-tier-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
      tier: backend
  template:
    metadata:
      labels:
        app: backend
        tier: backend
    spec:
      containers:
      - name: backend
        image: your-backend-image:v1  # Replace with your backend image
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: backend-config
              key: DATABASE_HOST
        - name: DATABASE_PORT
          valueFrom:
            configMapKeyRef:
              name: backend-config
              key: DATABASE_PORT
        - name: DATABASE_NAME
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-database
        - name: DATABASE_USER
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-user
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
        resources:
          requests:
            memory: "128Mi"
            cpu: "250m"
          limits:
            memory: "256Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
```

### Create Backend Service
`backend/service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: multi-tier-app
spec:
  selector:
    app: backend
    tier: backend
  ports:
  - port: 8080
    targetPort: 8080
  type: ClusterIP
```

```bash
kubectl apply -f backend/
```

## Step 4: Deploy Frontend Layer

### Create Frontend Deployment
`frontend/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: multi-tier-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
      tier: frontend
  template:
    metadata:
      labels:
        app: frontend
        tier: frontend
    spec:
      containers:
      - name: frontend
        image: nginx:1.21  # Replace with your frontend image
        ports:
        - containerPort: 80
        env:
        - name: BACKEND_URL
          value: "http://backend.multi-tier-app.svc.cluster.local:8080"
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
```

### Create Frontend Service
`frontend/service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: multi-tier-app
spec:
  selector:
    app: frontend
    tier: frontend
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

```bash
kubectl apply -f frontend/
```

## Step 5: Create Ingress

`ingress.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: multi-tier-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.local
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend
            port:
              number: 8080
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
```

```bash
kubectl apply -f ingress.yaml
```

## Step 6: Verify Deployment

```bash
# Check all resources in namespace
kubectl get all -n multi-tier-app

# Check pods
kubectl get pods -n multi-tier-app

# Check services
kubectl get svc -n multi-tier-app

# Check ingress
kubectl get ingress -n multi-tier-app

# View logs
kubectl logs -n multi-tier-app -l tier=backend
kubectl logs -n multi-tier-app -l tier=frontend
```

## Step 7: Test Database Connection

```bash
# Connect to database pod
kubectl exec -it -n multi-tier-app postgres-0 -- psql -U appuser -d appdb

# Inside psql
\dt  # List tables
\q   # Quit
```

## Step 8: Scale Application

```bash
# Scale backend
kubectl scale deployment backend -n multi-tier-app --replicas=4

# Scale frontend
kubectl scale deployment frontend -n multi-tier-app --replicas=5

# Verify
kubectl get pods -n multi-tier-app
```

## Step 9: Setup Horizontal Pod Autoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
  namespace: multi-tier-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

```bash
kubectl apply -f hpa.yaml
```

## Step 10: Access Application

```bash
# Add to /etc/hosts (for local testing)
echo "$(minikube ip) myapp.local" | sudo tee -a /etc/hosts

# Access application
curl http://myapp.local
curl http://myapp.local/api/health
```

## Monitoring Commands

```bash
# Watch pods
kubectl get pods -n multi-tier-app -w

# View events
kubectl get events -n multi-tier-app --sort-by='.lastTimestamp'

# View resource usage
kubectl top pods -n multi-tier-app
kubectl top nodes

# View HPA status
kubectl get hpa -n multi-tier-app
```

## Cleanup

```bash
# Delete all resources
kubectl delete namespace multi-tier-app
```

## Learning Outcomes
- Multi-tier application deployment
- StatefulSet for stateful applications
- Service discovery and DNS
- ConfigMaps and Secrets management
- Ingress for routing
- Horizontal Pod Autoscaling
- Resource management
- Health checks and probes
