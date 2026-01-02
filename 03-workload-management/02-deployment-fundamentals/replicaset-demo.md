# 1. replicaset-failed-probes

Below are **two complete Kubernetes ReplicaSet YAML files** designed **exactly like your Deployment demo**, but using **ReplicaSet** instead.

This is perfect for teaching:

* Why **ReplicaSet exists**
* How **self-healing works without rollouts**
* Probe behavior at the **ReplicaSet level**

**ReplicaSet with FAILING Liveness & Readiness Probes**

This simulates:

* Pods are created by ReplicaSet
* Probes continuously fail
* Pods keep restarting, but ReplicaSet keeps the replica count

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs-failed-probes
  labels:
    app: nginx-rs-failed
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-rs-failed
  template:
    metadata:
      labels:
        app: nginx-rs-failed
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80

        #  Liveness Probe (FAILS)
        livenessProbe:
          httpGet:
            path: /healthz   # endpoint does NOT exist
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3

        #  Readiness Probe (FAILS)
        readinessProbe:
          httpGet:
            path: /ready     # endpoint does NOT exist
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```

###  What You Will Observe

* ReplicaSet always shows **2 desired / 2 current**
* Pods enter **CrashLoopBackOff**
* ReplicaSet does **not rollback** or manage versions
* Only **keeps pod count**, nothing else

---

# 2. replicaset-passed-probes.yaml

**ReplicaSet with PASSING Liveness & Readiness Probes**

This version uses valid Nginx endpoints.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs-passed-probes
  labels:
    app: nginx-rs-passed
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-rs-passed
  template:
    metadata:
      labels:
        app: nginx-rs-passed
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80

        #  Liveness Probe (PASSES)
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3

        #  Readiness Probe (PASSES)
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```

###  What You Will Observe

* ReplicaSet remains stable
* Pods stay **Running**
* No restarts
* Traffic-ready Pods

---

#  Hands-on Verification Commands

```bash
# Apply ReplicaSets
kubectl apply -f replicaset-failed-probes.yaml
kubectl apply -f replicaset-passed-probes.yaml

# Watch Pods
kubectl get pods -w

# Inspect probe failures
kubectl describe pod <pod-name>

# Check ReplicaSet status
kubectl get rs
```

---

#  Deployment vs ReplicaSet (Teaching Gold)

| Feature         | ReplicaSet  | Deployment  |
| --------------- | ----------- | ----------- |
| Pod count       | Maintains   |   Maintains |
| Self-healing    | Yes         |   Yes       |
| Rolling updates | No          |   Yes       |
| Rollback        | No          |   Yes       |
| Versioning      | No          |   Yes       |

> **ReplicaSet = Self-healing only**
> **Deployment = Self-healing + Safe updates**

---

#  CKA / CKAD Exam Tip

* ReplicaSet **does not manage updates**
* If image changes → old Pods deleted + new Pods created
* **Deployments own ReplicaSets**, not vice versa
