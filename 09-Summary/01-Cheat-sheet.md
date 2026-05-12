# Kubernetes Advanced – Cheat Sheet

## 1. Kubernetes Basics

* Container orchestration platform for automating deployment, scaling, and management

```bash
kubectl version                   # Show kubectl version
kubectl cluster-info              # Show cluster information
kubectl config view               # View kubeconfig
kubectl config get-contexts       # List contexts
kubectl config use-context dev    # Switch context
kubectl get nodes                 # List cluster nodes
kubectl api-resources             # List API resources
kubectl api-versions              # List API versions
```

---

## 2. Namespace Management

```bash
kubectl get ns                    # List namespaces
kubectl create ns dev             # Create namespace
kubectl delete ns dev             # Delete namespace
kubectl config set-context --current --namespace=dev  # Set default namespace
```

---

## 3. Pod Management

```bash
kubectl get pods                  # List pods
kubectl get pods -A               # List all namespace pods
kubectl get pod pod1 -o yaml      # Pod YAML output
kubectl describe pod pod1         # Pod details
kubectl run nginx --image=nginx   # Create pod
kubectl delete pod pod1           # Delete pod
kubectl logs pod1                 # View pod logs
kubectl logs -f pod1              # Follow logs live
kubectl exec -it pod1 -- bash     # Access pod shell
kubectl cp file.txt pod1:/tmp     # Copy file to pod
kubectl top pod                   # Pod resource usage
kubectl port-forward pod1 8080:80 # Forward local port
```

---

## 4. Deployment Management

```bash
kubectl get deployments           # List deployments
kubectl create deployment web --image=nginx  # Create deployment
kubectl scale deployment web --replicas=3    # Scale deployment
kubectl rollout status deployment/web        # Rollout status
kubectl rollout history deployment/web       # Rollout history
kubectl rollout undo deployment/web          # Rollback deployment
kubectl set image deployment/web nginx=nginx:1.25  # Update image
kubectl delete deployment web     # Delete deployment
```

---

## 5. Service Management

```bash
kubectl get svc                   # List services
kubectl expose deployment web --port=80 --type=ClusterIP  # Expose deployment
kubectl describe svc web          # Service details
kubectl delete svc web            # Delete service
```

### Service Types

```text
ClusterIP     → Internal communication
NodePort      → Expose on node port
LoadBalancer  → Cloud load balancer
ExternalName  → Map external DNS
```

---

## 6. ConfigMap & Secret Management

```bash
kubectl create configmap app-config --from-literal=env=prod  # Create ConfigMap
kubectl get configmap             # List ConfigMaps
kubectl describe configmap app-config  # ConfigMap details
kubectl create secret generic db-secret --from-literal=password=admin  # Create Secret
kubectl get secrets               # List Secrets
kubectl describe secret db-secret # Secret details
```

---

## 7. StatefulSets & DaemonSets

```bash
kubectl get statefulsets          # List StatefulSets
kubectl get daemonsets            # List DaemonSets
kubectl describe statefulset app  # StatefulSet details
kubectl describe daemonset ds1    # DaemonSet details
```

---

## 8. Job & CronJob Management

```bash
kubectl get jobs                  # List jobs
kubectl get cronjobs              # List cronjobs
kubectl create job test --image=busybox  # Create job
kubectl create cronjob backup --image=busybox --schedule="*/5 * * * *"  # Create cronjob
kubectl delete job test           # Delete job
kubectl delete cronjob backup     # Delete cronjob
```

---

## 9. Storage Management

```bash
kubectl get pv                    # List Persistent Volumes
kubectl get pvc                   # List Persistent Volume Claims
kubectl describe pv pv1           # PV details
kubectl describe pvc pvc1         # PVC details
kubectl get storageclass          # List StorageClasses
```

---

## 10. Node Management

```bash
kubectl get nodes -o wide         # Detailed node info
kubectl describe node node1       # Node details
kubectl cordon node1              # Mark node unschedulable
kubectl uncordon node1            # Mark node schedulable
kubectl drain node1 --ignore-daemonsets  # Drain node
kubectl taint nodes node1 key=value:NoSchedule  # Apply taint
```

---

## 11. Resource Monitoring

```bash
kubectl top nodes                 # Node resource usage
kubectl top pods                  # Pod resource usage
kubectl describe pod pod1         # Pod events and status
kubectl get events                # Cluster events
```

---

## 12. YAML Operations

```bash
kubectl apply -f app.yaml         # Apply YAML
kubectl create -f app.yaml        # Create resources
kubectl delete -f app.yaml        # Delete resources
kubectl replace -f app.yaml       # Replace resource
kubectl diff -f app.yaml          # Compare changes
kubectl kustomize ./              # Render kustomization
```

### Basic Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

---

## 13. Troubleshooting Commands

```bash
kubectl describe pod pod1         # Pod troubleshooting
kubectl logs pod1                 # Application logs
kubectl logs pod1 -c container1   # Specific container logs
kubectl get events --sort-by=.metadata.creationTimestamp  # Recent events
kubectl exec -it pod1 -- sh       # Debug shell
kubectl auth can-i create pods    # RBAC validation
```

---

## 14. RBAC (Role Based Access Control)

```bash
kubectl get roles                 # List roles
kubectl get rolebindings          # List rolebindings
kubectl get clusterroles          # List cluster roles
kubectl get clusterrolebindings   # List cluster rolebindings
kubectl describe role admin       # Role details
```

---

## 15. Helm Integration

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami  # Add repo
helm repo update                  # Update repositories
helm search repo nginx           # Search chart
helm install web bitnami/nginx    # Install chart
helm list                         # List releases
helm upgrade web bitnami/nginx    # Upgrade release
helm rollback web 1               # Rollback release
helm uninstall web                # Remove release
```

---

## 16. Daily Must-Have Commands

```bash
kubectl get pods -A               # All pods
kubectl get svc                   # Services
kubectl get deployments           # Deployments
kubectl logs -f pod1              # Live logs
kubectl exec -it pod1 -- bash     # Access pod
kubectl describe pod pod1         # Pod details
kubectl apply -f app.yaml         # Apply YAML
kubectl delete -f app.yaml        # Delete YAML resources
```

---

## 17. Kubernetes Architecture

```text
User → kubectl CLI
kubectl → API Server
API Server → etcd
API Server → Scheduler
API Server → Controller Manager
Scheduler → Worker Nodes
Controller Manager → Worker Nodes
Worker Node → kubelet
kubelet → Container Runtime
Container Runtime → Pods
kube-proxy → Service Networking
```

---

### Tip

Mastering Kubernetes helps automate large-scale containerized infrastructure efficiently.
