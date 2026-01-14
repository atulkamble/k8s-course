# ☸️ Kubernetes Core Concepts (With Easy-to-Understand Code Snippets)

## What is Kubernetes?

**Kubernetes (K8s)** is an open-source container orchestration platform that automates:

* Application deployment
* Scaling
* Self-healing
* Networking
* Storage management

---

## 1️⃣ Cluster

A **cluster** is the complete Kubernetes system.

**It consists of:**

* **Control Plane** → Manages the cluster
* **Worker Nodes** → Run applications (pods)

```
Kubernetes Cluster
 ├── Control Plane (API Server, Scheduler, etcd)
 └── Worker Nodes (Pods, Containers)
```

---

## 2️⃣ Node

A **node** is a machine (VM or physical) where pods run.

### Node Components

| Component         | Purpose                              |
| ----------------- | ------------------------------------ |
| kubelet           | Talks to control plane, runs pods    |
| container runtime | Runs containers (Docker, containerd) |
| kube-proxy        | Handles networking rules             |

---

## 3️⃣ Pod (Smallest Unit)

A **pod** runs one or more containers.

### Simple Pod Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod        # Pod name
spec:
  containers:
  - name: nginx
    image: nginx:latest  # Container image
    ports:
    - containerPort: 80  # App listens on port 80
```

🧠 **Key Idea**

* Pods are **temporary**
* If a pod dies → Kubernetes creates a new one

---

## 4️⃣ ReplicaSet

Ensures **desired number of pods** are always running.

### ReplicaSet Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3            # Always keep 3 pods running
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

---

## 5️⃣ Deployment (MOST IMPORTANT)

Deployment manages:

* ReplicaSets
* Rolling updates
* Rollbacks

### Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```

✅ **Use Deployment for 95% of apps**

---

## 6️⃣ Service (Expose Pods)

Provides **stable IP + DNS**.

### ClusterIP (Internal)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP          # Internal access only
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

### NodePort (External Access)

```yaml
type: NodePort
ports:
- port: 80
  targetPort: 80
  nodePort: 30007
```

---

## 7️⃣ Namespace

Logical separation inside cluster.

### Create Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Usage:

```bash
kubectl get pods -n dev
```

---

## 8️⃣ ConfigMap (Non-Sensitive Config)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
  APP_PORT: "8080"
```

### Use in Pod

```yaml
env:
- name: APP_ENV
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: APP_ENV
```

---

## 9️⃣ Secret (Sensitive Data)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=     # admin
  password: MTIzNDU=     # 12345
```

🔐 Stored as **base64**, not encryption

---

## 🔟 Volume (Data Persistence)

```yaml
volumes:
- name: app-volume
  emptyDir: {}
```

```yaml
volumeMounts:
- name: app-volume
  mountPath: /data
```

---

## 1️⃣1️⃣ Ingress (HTTP Routing)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

---

## 1️⃣2️⃣ DaemonSet

Runs **one pod per node**.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-monitor
spec:
  selector:
    matchLabels:
      app: monitor
  template:
    metadata:
      labels:
        app: monitor
    spec:
      containers:
      - name: monitor
        image: prom/node-exporter
```

---

## 1️⃣3️⃣ StatefulSet

Used for databases (MySQL, MongoDB).

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 2
```

🧠 **Stable pod names**

```
mysql-0
mysql-1
```

---

## 1️⃣4️⃣ Job (Run Once)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    spec:
      containers:
      - name: job
        image: busybox
        command: ["echo", "Hello Kubernetes"]
      restartPolicy: Never
```

---

## 1️⃣5️⃣ CronJob (Scheduled Job)

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-job
spec:
  schedule: "0 2 * * *"   # Every day at 2 AM
```

---

## 🏷 Labels & Selectors (VERY IMPORTANT)

### Labels

```yaml
labels:
  app: web
  env: prod
```

### Selector

```yaml
selector:
  matchLabels:
    app: web
```

---

## 📦 Resource Requests & Limits

```yaml
resources:
  requests:
    cpu: "250m"      # Guaranteed
    memory: "64Mi"
  limits:
    cpu: "500m"      # Max allowed
    memory: "128Mi"
```

---

## ❤️ Probes (Health Checks)

### Liveness

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
```

### Readiness

```yaml
readinessProbe:
  tcpSocket:
    port: 80
```

---

## 📈 Scaling (HPA Example)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## 🔐 Security (Quick View)

| Feature        | Purpose            |
| -------------- | ------------------ |
| RBAC           | API access control |
| ServiceAccount | Pod identity       |
| NetworkPolicy  | Traffic control    |
| Pod Security   | Pod restrictions   |

---

## ✅ Interview-Friendly Summary

| Concept    | Remember       |
| ---------- | -------------- |
| Pod        | Smallest unit  |
| Deployment | App management |
| Service    | Networking     |
| ConfigMap  | Config         |
| Secret     | Sensitive data |
| Ingress    | HTTP routing   |
| HPA        | Auto-scaling   |

---
