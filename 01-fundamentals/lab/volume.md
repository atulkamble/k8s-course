## 🔹 Option 1: Basic Pod with `emptyDir` Volume (Simplest)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-demo-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: app-volume
      mountPath: /data
  volumes:
  - name: app-volume
    emptyDir: {}
```

### ✅ What happens?

* `/data` directory is created inside the container
* Data exists **only while the Pod is running**
* When Pod is deleted → data is deleted

---

## 🔹 Option 2: Basic Deployment with `emptyDir` Volume (Recommended)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: volume-demo-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: volume-demo
  template:
    metadata:
      labels:
        app: volume-demo
    spec:
      containers:
      - name: app-container
        image: nginx
        volumeMounts:
        - name: app-volume
          mountPath: /data
      volumes:
      - name: app-volume
        emptyDir: {}
```

---

## 🔹 Verify Volume Inside Pod

```bash
kubectl get pods
kubectl exec -it volume-demo-pod -- /bin/sh
```

Inside container:

```bash
cd /data
echo "Hello Kubernetes Volume" > test.txt
ls
```

---

## 🔹 Key Points to Remember (Interview Friendly)

| Feature                   | emptyDir                       |
| ------------------------- | ------------------------------ |
| Storage type              | Temporary                      |
| Shared between containers | ✅ Yes (same Pod)               |
| Survives Pod restart      | ❌ No                           |
| Use case                  | Cache, temp files, shared data |

---

## 🔹 When to Use `emptyDir`

* Temporary storage
* Cache directories
* Sharing data between containers in the same Pod
* CI/CD or build artifacts

---

