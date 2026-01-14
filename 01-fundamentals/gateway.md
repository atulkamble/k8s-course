# 🚪 Kubernetes Gateway API – Installation & Gateway Creation Guide

This document explains how to **install Gateway API CRDs**, create a **Gateway resource**, and verify the setup in a Kubernetes cluster.

---

## 1️⃣ Install Gateway API CRDs

Gateway API resources (Gateway, GatewayClass, HTTPRoute, etc.) are **not available by default** in Kubernetes.
You must install the CRDs first.

### ▶ Install Standard Gateway API CRDs

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
```

✔ This installs CRDs such as:

* gateways.gateway.networking.k8s.io
* gatewayclasses.gateway.networking.k8s.io
* httproutes.gateway.networking.k8s.io

---

## 2️⃣ Verify Gateway CRDs Installation

Confirm that Gateway-related CRDs are available:

```bash
kubectl get crds | grep gateway
```

Expected output (example):

```text
gatewayclasses.gateway.networking.k8s.io
gateways.gateway.networking.k8s.io
```

---

## 3️⃣ Create Namespace for Gateway

The Gateway resource will be created inside a dedicated namespace.

```bash
kubectl create namespace example-namespace
```

Verify:

```bash
kubectl get namespaces | grep example-namespace
```

---

## 4️⃣ Create Gateway Manifest

Save the following YAML as **`gateway.yaml`**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: example-gateway
  namespace: example-namespace
spec:
  gatewayClassName: example-class
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    hostname: "www.example.com"
    allowedRoutes:
      namespaces:
        from: Same
```

### 🔍 Key Fields Explained

| Field              | Description                                         |
| ------------------ | --------------------------------------------------- |
| `gatewayClassName` | References a `GatewayClass` managed by a controller |
| `protocol`         | Listener protocol (HTTP / HTTPS / TCP)              |
| `port`             | Port exposed by the Gateway                         |
| `hostname`         | Hostname this Gateway listens on                    |
| `allowedRoutes`    | Restricts routes to the same namespace              |

⚠ **Important:**
A **GatewayClass** with name `example-class` **must already exist**, otherwise the Gateway will remain unprogrammed.

---

## 5️⃣ Apply the Gateway Resource

```bash
kubectl apply -f gateway.yaml
```

Expected output:

```text
gateway.gateway.networking.k8s.io/example-gateway created
```

---

## 6️⃣ Verify Gateway Status

```bash
kubectl get gateway -n example-namespace
```

For detailed status:

```bash
kubectl describe gateway example-gateway -n example-namespace
```

---

## 7️⃣ Common Troubleshooting

### ❌ Error: *no matches for kind "Gateway"*

➡ Gateway CRDs not installed
✔ Fix: Re-run **standard-install.yaml**

---

### ❌ Error: *namespace not found*

➡ Namespace missing
✔ Fix:

```bash
kubectl create namespace example-namespace
```

---

### ❌ Gateway shows `NotAccepted`

➡ No Gateway controller found for the `GatewayClass`
✔ Fix:

* Install a Gateway controller (Istio, NGINX, Envoy, etc.)
* Ensure `controllerName` matches the GatewayClass

---

## 8️⃣ Summary Flow

```text
Install Gateway API CRDs
        ↓
Create Namespace
        ↓
Create Gateway YAML
        ↓
Apply Gateway
        ↓
Verify Gateway Status
```

---
