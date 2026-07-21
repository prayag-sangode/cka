## 🖥️ Day 7 Lab — Worker Node Components

### **Step 1: Explore Worker Node Pods**
List system pods running on worker nodes:
```bash
kubectl get pods -n kube-system -o wide
```
You’ll see:
- `kube-proxy-<node>` → networking proxy on each worker node.  
- Other system pods scheduled across workers.

---

### **Step 2: Understand kubelet**
- kubelet is the **node agent** that ensures pods are running as defined.  
- It communicates with the API server and reports node/pod status.  
Check kubelet logs (inside the worker container):
```bash
docker exec -it <worker-container-id> journalctl -u kubelet
```

---

### **Step 3: Understand kube‑proxy**
- kube‑proxy maintains network rules for Services.  
- It ensures pods can talk to each other and external clients.  
Inspect kube‑proxy logs:
```bash
kubectl logs -n kube-system kube-proxy-<node>
```

---

### **Step 4: Understand Container Runtime**
- The container runtime (Docker, containerd, CRI‑O) runs containers inside pods.  
- Kind uses **containerd** by default.  
Check runtime version:
```bash
docker info | grep "Runtime"
```

---

### **Step 5: Hands‑On Verification**
Deploy a workload and observe worker node behavior:
```bash
kubectl create deployment nginx --image=nginx --replicas=3
kubectl get pods -o wide
```
- Pods should be distributed across worker nodes.  
- kubelet ensures they stay running.  
- kube‑proxy sets up networking rules.  
- Container runtime executes the actual containers.

---

### **Step 6: Troubleshooting Exercise**
Stop kubelet on a worker node:
```bash
docker exec -it <worker-container-id> systemctl stop kubelet
```
- Node will be marked **NotReady**.  
- Control plane may reschedule pods to other nodes.  

---

### **Step 7: Cleanup**
Delete test deployments:
```bash
kubectl delete deployment nginx
```

---

## Lab Verification
- You identified worker node components (kubelet, kube‑proxy, container runtime).  
- You inspected logs for kubelet and kube‑proxy.  
- You deployed workloads and observed scheduling.  
- You tested node failure and saw how Kubernetes reacts.  

