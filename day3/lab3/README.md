## Day 3 Lab — Kubernetes Cluster Architecture

## Make sure kind exists

### **Step 1: Understand the Control Plane**
The control plane manages the cluster state. Key components:
- **API Server** → entry point for all kubectl and client requests.  
- **etcd** → distributed key‑value store for cluster data.  
- **Controller Manager** → ensures desired state (e.g., ReplicaSets maintain pod count).  
- **Scheduler** → decides which node a pod runs on.

---

### **Step 2: Understand Worker Nodes**
Worker nodes run workloads. Key components:
- **kubelet** → agent that ensures pods are running as expected.  
- **kube‑proxy** → handles networking and service routing.  
- **Container Runtime (Docker/CRI‑O)** → runs containers inside pods.

---

### **Step 3: Explore Cluster Components in Your Lab**
Run:
```bash
kubectl get nodes -o wide
kubectl get pods -n kube-system
```
- Lists control plane and worker nodes.  
- Shows system pods like CoreDNS, kube‑proxy, etc.

---

### **Step 4: Visualize the Architecture**
Here’s a simplified diagram of Kubernetes architecture:





- Control plane (API server, etcd, scheduler, controller manager) manages cluster state.  
- Worker nodes (kubelet, kube‑proxy, container runtime) run workloads.  
- Communication flows via the API server.

---

### **Step 5: Hands‑On Verification**
Deploy a test workload and observe scheduling:
```bash
kubectl create deployment nginx --image=nginx --replicas=2
kubectl get pods -o wide
```
- Pods should be scheduled across worker nodes.  
- Control plane ensures desired state is maintained.

---

### **Step 6: Troubleshooting Exercise**
Try stopping kubelet on a worker node (inside Docker container):
```bash
docker exec -it <worker-container-id> systemctl stop kubelet
```
- Observe how the control plane marks the node as **NotReady**.  
- Pods may reschedule to other nodes.

---

## Lab Verification
- You can identify control plane vs worker node components.  
- You’ve seen system pods running in `kube-system`.  
- You’ve deployed workloads and observed scheduling.  
- You understand how the control plane enforces desired state.

