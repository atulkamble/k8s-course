# Project 3: Microservices with Service Mesh

## Objective
Deploy a microservices application demonstrating service-to-service communication, load balancing, and observability.

## Architecture

```
User → Ingress → API Gateway
                      ↓
         ┌────────────┼────────────┐
         ↓            ↓            ↓
    User Service  Order Service  Product Service
         ↓                         ↓
    PostgreSQL                  Redis Cache
```

## Prerequisites
- Kubernetes cluster
- kubectl installed
- Helm (optional for advanced features)

## Step 1: Create Namespace and Resources

`namespace.yaml`:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: microservices
  labels:
    name: microservices
```

## Step 2: Deploy Redis Cache

`redis/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: microservices
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7-alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
---
apiVersion: v1
kind: Service
metadata:
  name: redis
  namespace: microservices
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
```

## Step 3: Deploy Product Service

`product-service/configmap.yaml`:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: product-config
  namespace: microservices
data:
  REDIS_HOST: "redis.microservices.svc.cluster.local"
  REDIS_PORT: "6379"
  SERVICE_PORT: "8080"
```

`product-service/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: microservices
spec:
  replicas: 3
  selector:
    matchLabels:
      app: product-service
      version: v1
  template:
    metadata:
      labels:
        app: product-service
        version: v1
    spec:
      containers:
      - name: product
        image: your-registry/product-service:v1
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: product-config
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
---
apiVersion: v1
kind: Service
metadata:
  name: product-service
  namespace: microservices
  labels:
    app: product-service
spec:
  selector:
    app: product-service
  ports:
  - name: http
    port: 8080
    targetPort: 8080
```

## Step 4: Deploy User Service

`user-service/secret.yaml`:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: user-db-secret
  namespace: microservices
type: Opaque
stringData:
  database-url: "postgresql://user:pass@postgres:5432/userdb"
```

`user-service/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  namespace: microservices
spec:
  replicas: 2
  selector:
    matchLabels:
      app: user-service
      version: v1
  template:
    metadata:
      labels:
        app: user-service
        version: v1
    spec:
      containers:
      - name: user
        image: your-registry/user-service:v1
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: user-db-secret
              key: database-url
        - name: SERVICE_PORT
          value: "8080"
        resources:
          requests:
            memory: "128Mi"
            cpu: "250m"
          limits:
            memory: "256Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: user-service
  namespace: microservices
spec:
  selector:
    app: user-service
  ports:
  - name: http
    port: 8080
    targetPort: 8080
```

## Step 5: Deploy Order Service

`order-service/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  namespace: microservices
spec:
  replicas: 2
  selector:
    matchLabels:
      app: order-service
      version: v1
  template:
    metadata:
      labels:
        app: order-service
        version: v1
    spec:
      containers:
      - name: order
        image: your-registry/order-service:v1
        ports:
        - containerPort: 8080
        env:
        - name: USER_SERVICE_URL
          value: "http://user-service.microservices.svc.cluster.local:8080"
        - name: PRODUCT_SERVICE_URL
          value: "http://product-service.microservices.svc.cluster.local:8080"
        - name: SERVICE_PORT
          value: "8080"
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
---
apiVersion: v1
kind: Service
metadata:
  name: order-service
  namespace: microservices
spec:
  selector:
    app: order-service
  ports:
  - name: http
    port: 8080
    targetPort: 8080
```

## Step 6: Deploy API Gateway

`api-gateway/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-gateway
  namespace: microservices
spec:
  replicas: 2
  selector:
    matchLabels:
      app: api-gateway
  template:
    metadata:
      labels:
        app: api-gateway
    spec:
      containers:
      - name: gateway
        image: nginx:1.21
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nginx-config
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
      volumes:
      - name: nginx-config
        configMap:
          name: gateway-config
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: gateway-config
  namespace: microservices
data:
  nginx.conf: |
    events {
        worker_connections 1024;
    }
    http {
        upstream user_service {
            server user-service:8080;
        }
        upstream product_service {
            server product-service:8080;
        }
        upstream order_service {
            server order-service:8080;
        }
        server {
            listen 80;
            location /api/users {
                proxy_pass http://user_service;
            }
            location /api/products {
                proxy_pass http://product_service;
            }
            location /api/orders {
                proxy_pass http://order_service;
            }
            location /health {
                return 200 "OK";
            }
        }
    }
---
apiVersion: v1
kind: Service
metadata:
  name: api-gateway
  namespace: microservices
spec:
  type: LoadBalancer
  selector:
    app: api-gateway
  ports:
  - port: 80
    targetPort: 80
```

## Step 7: Network Policies

`network-policy.yaml`:
```yaml
# Allow API Gateway to communicate with services
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-gateway
  namespace: microservices
spec:
  podSelector:
    matchLabels:
      app: user-service
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api-gateway
    ports:
    - protocol: TCP
      port: 8080
---
# Allow Order Service to communicate with User and Product services
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: order-service-policy
  namespace: microservices
spec:
  podSelector:
    matchLabels:
      app: order-service
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: user-service
    ports:
    - protocol: TCP
      port: 8080
  - to:
    - podSelector:
        matchLabels:
          app: product-service
    ports:
    - protocol: TCP
      port: 8080
  - to:
    - namespaceSelector:
        matchLabels:
          name: kube-system
    ports:
    - protocol: UDP
      port: 53
```

## Step 8: Setup Monitoring

`servicemonitor.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: product-metrics
  namespace: microservices
  labels:
    app: product-service
spec:
  selector:
    app: product-service
  ports:
  - name: metrics
    port: 9090
    targetPort: 9090
```

## Step 9: Deploy Everything

```bash
# Create namespace
kubectl apply -f namespace.yaml

# Deploy Redis
kubectl apply -f redis/

# Deploy services
kubectl apply -f product-service/
kubectl apply -f user-service/
kubectl apply -f order-service/

# Deploy API Gateway
kubectl apply -f api-gateway/

# Apply network policies
kubectl apply -f network-policy.yaml
```

## Step 10: Test Microservices

```bash
# Get API Gateway URL
kubectl get service api-gateway -n microservices

# Test endpoints
GATEWAY_IP=$(kubectl get service api-gateway -n microservices -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

curl http://$GATEWAY_IP/api/products
curl http://$GATEWAY_IP/api/users
curl http://$GATEWAY_IP/api/orders
```

## Step 11: Load Testing

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: load-test
  namespace: microservices
spec:
  template:
    spec:
      containers:
      - name: load-test
        image: williamyeh/hey
        args:
        - "-n"
        - "1000"
        - "-c"
        - "10"
        - "http://api-gateway/api/products"
      restartPolicy: Never
```

## Monitoring Commands

```bash
# View all services
kubectl get all -n microservices

# Check service endpoints
kubectl get endpoints -n microservices

# View logs
kubectl logs -n microservices -l app=order-service
kubectl logs -n microservices -l app=product-service

# Watch pods
kubectl get pods -n microservices -w

# View resource usage
kubectl top pods -n microservices
```

## Cleanup

```bash
kubectl delete namespace microservices
```

## Learning Outcomes
- Microservices architecture
- Service-to-service communication
- API Gateway pattern
- Network policies for security
- Service discovery
- Load balancing
- Configuration management
- Monitoring and observability
