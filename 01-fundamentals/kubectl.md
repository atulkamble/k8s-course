# 🧰 kubectl – All Essential Commands (Cheat Sheet)

---

## 🔹 1. Cluster & Context Commands

```bash
kubectl version
kubectl version --short
kubectl cluster-info
kubectl get nodes
kubectl describe node <node-name>

kubectl config get-contexts
kubectl config current-context
kubectl config use-context <context-name>
kubectl config view
```

---

## 🔹 2. Namespace Commands

```bash
kubectl get namespaces
kubectl create namespace dev
kubectl delete namespace dev
kubectl config set-context --current --namespace=dev
kubectl get all -n dev
```

---

## 🔹 3. Get / List Resources

```bash
kubectl get all
kubectl get pods
kubectl get svc
kubectl get deploy
kubectl get rs
kubectl get ds
kubectl get sts
kubectl get job
kubectl get cronjob
kubectl get cm
kubectl get secret
kubectl get ingress
kubectl get hpa
kubectl get pv
kubectl get pvc
kubectl get events
```

With options:

```bash
kubectl get pods -o wide
kubectl get pods -o yaml
kubectl get pods -o json
kubectl get pods --show-labels
```

---

## 🔹 4. Describe Resources (Debugging)

```bash
kubectl describe pod <pod-name>
kubectl describe deploy <deploy-name>
kubectl describe svc <service-name>
kubectl describe node <node-name>
kubectl describe hpa <hpa-name>
kubectl describe ingress <ingress-name>
```

---

## 🔹 5. Create / Apply / Delete

```bash
kubectl apply -f file.yaml
kubectl create -f file.yaml
kubectl delete -f file.yaml

kubectl delete pod <pod-name>
kubectl delete deploy <deploy-name>
kubectl delete svc <service-name>
```

---

## 🔹 6. Pod & Container Debugging

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs -f <pod-name>

kubectl exec -it <pod-name> -- /bin/sh
kubectl exec -it <pod-name> -- /bin/bash

kubectl port-forward pod/<pod-name> 8080:80
```

---

## 🔹 7. Scaling & Rollouts

```bash
kubectl scale deploy <deploy-name> --replicas=3

kubectl rollout status deploy <deploy-name>
kubectl rollout history deploy <deploy-name>
kubectl rollout undo deploy <deploy-name>
kubectl rollout restart deploy <deploy-name>
```

---

## 🔹 8. Deployment Shortcuts

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
kubectl set image deploy/nginx nginx=nginx:latest
```

---

## 🔹 9. Services & Networking

```bash
kubectl get svc
kubectl describe svc <service-name>
kubectl expose pod <pod-name> --type=ClusterIP --port=80
kubectl expose deploy <deploy-name> --type=LoadBalancer --port=80
```

---

## 🔹 10. ConfigMap & Secret

```bash
kubectl create configmap app-config --from-literal=env=prod
kubectl get configmap
kubectl describe configmap app-config

kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=12345

kubectl get secrets
kubectl describe secret db-secret
```

---

## 🔹 11. Jobs & CronJobs

```bash
kubectl get jobs
kubectl describe job <job-name>

kubectl get cronjob
kubectl describe cronjob <cronjob-name>

kubectl create job test-job --from=cronjob/daily-job
```

---

## 🔹 12. HPA (Auto Scaling)

```bash
kubectl get hpa
kubectl describe hpa <hpa-name>
kubectl autoscale deployment web \
  --cpu-percent=70 \
  --min=2 \
  --max=5
```

---

## 🔹 13. Volumes & Storage

```bash
kubectl get pv
kubectl get pvc
kubectl describe pvc <pvc-name>
```

---

## 🔹 14. Labels & Selectors

```bash
kubectl get pods --selector app=nginx
kubectl label pod <pod-name> env=prod
kubectl label pod <pod-name> env-
```

---

## 🔹 15. Taints & Tolerations

```bash
kubectl taint nodes <node-name> key=value:NoSchedule
kubectl taint nodes <node-name> key=value:NoSchedule-
```

---

## 🔹 16. API Discovery & Help

```bash
kubectl api-resources
kubectl api-versions
kubectl explain pod
kubectl explain deployment.spec
kubectl explain pod.spec.containers
```

---

## 🔹 17. Events & Troubleshooting

```bash
kubectl get events
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

---

## 🔹 18. Cleanup & Maintenance

```bash
kubectl delete pod --all
kubectl delete deploy --all
kubectl delete svc --all
kubectl delete all --all
```

---

## 🔹 19. Dry Run & Validation

```bash
kubectl apply -f file.yaml --dry-run=client
kubectl create deploy test --image=nginx --dry-run=client -o yaml
```

---

## 🔹 20. Common Aliases (Highly Recommended)

```bash
alias k=kubectl
alias kgp='kubectl get pods'
alias kgs='kubectl get svc'
alias kga='kubectl get all'
alias kdp='kubectl describe pod'
```

---

## 📌 Interview Tip

> If you **master `get`, `describe`, `logs`, `exec`, `apply`, `delete`, `rollout`, `scale`**, you already cover **80% of real-world Kubernetes work**.

---


