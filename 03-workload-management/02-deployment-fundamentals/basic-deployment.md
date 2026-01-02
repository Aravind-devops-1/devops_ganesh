# 1️. basic-deployment-failed-probes.yaml

**Liveness & Readiness probes FAIL**

This simulates a **broken application** scenario where:

* The container is running
* But the health endpoint does **not exist**
* Kubernetes detects failure and restarts/removes the Pod

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-failed-probes
  labels:
    app: nginx-failed
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-failed
  template:
    metadata:
      labels:
        app: nginx-failed
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80

        # Liveness Probe (FAILS)
        livenessProbe:
          httpGet:
            path: /healthz   # ❌ This endpoint does NOT exist
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3

        # Readiness Probe (FAILS)
        readinessProbe:
          httpGet:
            path: /ready     # ❌ This endpoint does NOT exist
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```

###  What You Will Observe

* Pods start → probes fail
* Readiness probe fails → Pod NOT added to Service
* Liveness probe fails → Container restarted
* Pod enters **CrashLoopBackOff**

---

# 2️. basic-deployment-passed-probes.yaml

**Liveness & Readiness probes PASS**

This deployment uses valid endpoints that **exist by default** in Nginx.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-passed-probes
  labels:
    app: nginx-passed
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-passed
  template:
    metadata:
      labels:
        app: nginx-passed
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80

        # Liveness Probe (PASSES)
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3

        # Readiness Probe (PASSES)
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```

### What You Will Observe

* Pods start normally
* Readiness probe passes → Pod receives traffic
* Liveness probe passes → No restarts
* Deployment remains **stable**

---

# Verification Commands (Hands-on)

```bash
# Apply deployment
kubectl apply -f basic-deployment-failed-probes.yaml
kubectl apply -f basic-deployment-passed-probes.yaml

# Watch pod behavior
kubectl get pods -w

# Describe pod to see probe failures
kubectl describe pod <pod-name>

# Check restart count
kubectl get pods
```

---

# Key Learning (Very Important)

| Probe Type | Failure Result           |
| ---------- | ------------------------ |
| Readiness  | Pod removed from Service |
| Liveness   | Container restarted      |
| Both Fail  | CrashLoopBackOff         |

> **Running container ≠ healthy application**

---

# CKA / CKAD Exam Tip

* Probes are executed by **kubelet**
* Liveness failure → restart
* Readiness failure → no traffic
* Default HTTP success range = **200–399**
