# Practice-1

This hands-on lab focused strictly on **Pod Fundamentals**. This module explores the atomic unit of Kubernetes—the Pod—and demonstrates how to create, inspect, debug, and manage them directly.

Create a new folder named `k8s-pod-lab` and follow the steps below.

---

### Lab Files

Create the following file inside your folder.

#### 1. File: `simple-pod.yaml`

*This is a declarative manifest. It tells Kubernetes exactly what we want: one Pod running Nginx.*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
  labels:
    tier: frontend
    env: dev
spec:
  containers:
  - name: nginx-container
    image: nginx:alpine
    ports:
    - containerPort: 80

```

---

### Lab Guide: Pod Fundamentals

**Goal:** Understand the Pod lifecycle, networking, and debugging commands.

#### Step 1: Imperative vs. Declarative Creation

There are two ways to create a Pod. Let's try both.

**A. The "Quick & Dirty" Way - Imperative**
Run this command in your terminal. It tells Kubernetes to run a pod immediately without a file.

```bash
kubectl run temp-pod --image=redis

```

* **Check it:** `kubectl get pods`
* **Why use this?** Good for quick tests, bad for production because it's not version-controlled.

**B. The "Proper" Way - Declarative**
Now, let's use the YAML file you created. This is the industry standard.

```bash
kubectl apply -f simple-pod.yaml

```

#### Step 2: Inspection and "X-Ray" Vision

The most important skill in Kubernetes is knowing *what is happening* inside a Pod.

1. **Get Basic Info:**
```bash
kubectl get pods -o wide

```


* **Note:** Look at the `IP` and `NODE` columns. Each Pod gets its own internal IP address.


2. **The "X-Ray" (Describe):**
If a Pod fails, this is the first command you run. It shows the setup events.
```bash
kubectl describe pod my-first-pod

```


* **Task:** Scroll to the bottom to the **Events** section. You will see the timeline: `Scheduled` -> `Pulling` -> `Pulled` -> `Created` -> `Started`.



#### Step 3: Accessing the Pod (Port Forwarding)

Since we haven't built a "Service" (network load balancer) yet, the Pod is isolated inside the cluster. We can use **Port Forwarding** to tunnel into it directly from your laptop.

1. Run this command (it will block your terminal):
```bash
kubectl port-forward pod/my-first-pod 8080:80

```


2. Open your browser to `http://localhost:8080`.
3. You should see "Welcome to nginx!".
4. Press `Ctrl+C` in your terminal to stop the tunnel.

#### Step 4: The "Black Box" Recorder (Logs)

Applications print output to "stdout" (the screen). Kubernetes captures this.

```bash
kubectl logs my-first-pod

```

* **Task:** You should see the access logs from when you visited the site in Step 3 (e.g., `"GET / HTTP/1.1" 200`).

#### Step 5: Going Inside (Exec)

Sometimes you need to log into the container to check files or connectivity.

```bash
kubectl exec -it my-first-pod -- /bin/sh

```

* You are now **inside** the Pod's container!
* Run `ls` to see file structure.
* Run `cat /usr/share/nginx/html/index.html` to see the web page code.
* Type `exit` to leave.

#### Step 6: Cleanup

Let's remove the resources to keep your cluster clean.

```bash
kubectl delete pod temp-pod
kubectl delete -f simple-pod.yaml

```

---

### Key Concepts Verification
| Concept | What you just did |
| --- | --- |
| **Atomic Unit** | You realized you cannot deploy *just* a container; you must wrap it in a Pod. |
| **Ephemeral Nature** | If you delete these Pods, they are gone forever. Unlike "Deployments" (from the previous lab), standalone Pods do not restart automatically if you delete them. |
| **Isolation** | You saw that the Pod has its own IP and files, separate from your host machine. |

**Would you like me to generate a "Module 3" lab focusing on ConfigMaps? This will show you how to change the "Welcome to nginx" message without rebuilding the image.**
