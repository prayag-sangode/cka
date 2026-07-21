## Day 6 Lab — Control Plane Components

### **Step 1: Explore Control Plane Pods**
Run:
```bash
kubectl get pods -n kube-system -o wide
```
You’ll see pods like:
- `kube-apiserver-<node>`  
- `etcd-<node>`  
- `kube-scheduler-<node>`  
- `kube-controller-manager-<node>`  

These are the control plane components running inside your cluster.

---

### **Step 2: Understand API Server**
- Acts as the **front door** to the cluster.  
- All `kubectl` commands go through the API server.  
- Try:
```bash
kubectl get --raw /api
```
This shows raw API endpoints exposed by the server.

---

### **Step 3: Understand etcd**
- A distributed **key‑value store** holding cluster state.  
- Stores objects like pods, deployments, services.  
- Backup/restore of etcd is critical for disaster recovery.  
Check etcd pod logs:
```bash
kubectl logs -n kube-system etcd-<node>
```

---

### **Step 4: Understand Scheduler**
- Decides **which node** a pod runs on.  
- Factors: resource requests, taints/tolerations, affinity rules.  
Test scheduling:
```bash
kubectl create deployment busybox --image=busybox --replicas=2 -- sleep 3600
kubectl get pods -o wide
```
Observe how pods are distributed across worker nodes.

---

### **Step 5: Understand Controller Manager**
- Runs controllers that enforce desired state.  
- Examples: ReplicaSet controller, Node controller, Job controller.  
Test by scaling a deployment:
```bash
kubectl scale deployment busybox --replicas=5
kubectl get pods
```
The controller manager ensures the pod count matches the desired state.

---

### **Step 6: Visualize Control Plane Architecture**
Here’s a simplified diagram of how components interact:





---

### **Step 7: Cleanup**
Delete test deployments:
```bash
kubectl delete deployment nginx busybox
```

---

## Lab Verification
- You listed control plane pods in `kube-system`.  
- You queried API server endpoints.  
- You inspected etcd logs.  
- You observed scheduler distributing pods.  
- You saw controller manager enforce desired state.  

