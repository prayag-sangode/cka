## Day 8 Lab — How Kubernetes Processes & Schedules Requests

### **Step 1: Submit a Request via kubectl**
When you run:
```bash
kubectl apply -f nginx-pod.yaml
```
- The request goes to the **API Server**.  
- The API Server validates the manifest against the schema.  
- If valid, it stores the desired state in **etcd**.

---

### **Step 2: Controller Manager Enforces Desired State**
- The **Controller Manager** notices a new Pod object in etcd.  
- It ensures the desired state (a running pod) is achieved.  
- If replicas are defined, it creates a ReplicaSet to maintain pod count.

---

### **Step 3: Scheduler Assigns Pods to Nodes**
- The **Scheduler** checks available worker nodes.  
- It considers:
  - Resource requests (CPU, memory).  
  - Node labels, taints/tolerations.  
  - Affinity/anti‑affinity rules.  
- It binds the pod to a specific node.

Check scheduling decisions:
```bash
kubectl get pods -o wide
```

---

### **Step 4: kubelet Starts the Pod**
- The **kubelet** on the chosen worker node receives instructions.  
- It pulls the container image (e.g., `nginx:latest`).  
- It starts the container using the **container runtime** (containerd/Docker).  
- kubelet reports pod status back to the API Server.

Inspect pod events:
```bash
kubectl describe pod nginx-pod
```

---

### **Step 5: kube‑proxy Configures Networking**
- kube‑proxy sets up iptables/IPVS rules.  
- Ensures the pod can communicate with other pods and services.  
- DNS resolution is handled by CoreDNS.

Check service endpoints:
```bash
kubectl get endpoints
```

---

### **Step 6: Hands‑On Verification**
Deploy a workload and trace its lifecycle:
```bash
kubectl run demo-pod --image=nginx --restart=Never
kubectl describe pod demo-pod
```
- Observe events: scheduling, image pull, container start.  
- Verify pod is running on a worker node.

---

### **Step 7: Cleanup**
Delete test pod:
```bash
kubectl delete pod demo-pod
```

---

## Lab Verification
- You saw how a request flows from **kubectl → API Server → etcd → Controller Manager → Scheduler → kubelet → kube‑proxy**.  
- You deployed a pod and traced its lifecycle events.  
- You confirmed networking setup via kube‑proxy.  
