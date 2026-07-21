## Day 2 Lab — Multi‑Node Kind Cluster

### **Step 1: Create a Kind Cluster Config File**
Kind lets you define cluster topology in YAML. Create a file named `kind-multi-node.yaml`:

```yaml
# kind-multi-node.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

This defines **1 control‑plane node** and **2 worker nodes**.

---

### **Step 2: Create the Cluster**
Run:
```bash
kind create cluster --name my-multi-cluster --config kind-multi-node.yaml
```

- `--name` → names the cluster.  
- `--config` → uses the YAML file to define node roles.

---

### **Step 3: Verify Cluster Nodes**
Check that all nodes are running:
```bash
kubectl get nodes -o wide
```

You should see:
- 1 control‑plane node  
- 2 worker nodes  

---

### **Step 4: Inspect Cluster Info**
Run:
```bash
kubectl cluster-info
kubectl get pods -n kube-system
```

- Confirms control plane components (API server, etcd, scheduler, controller manager).  
- Lists system pods like CoreDNS and kube‑proxy.

---

### **Step 5: Test Scheduling Across Nodes**
Deploy a simple workload to verify pods are distributed:

```bash
kubectl create deployment nginx --image=nginx --replicas=3
kubectl get pods -o wide
```

- Pods should be scheduled across worker nodes.  

---

### **Step 6: Delete the Cluster (Optional)**
When finished, clean up:
```bash
kind delete cluster --name my-multi-cluster
```

---

## Lab Verification
- Cluster has **1 control‑plane + 2 worker nodes**.  
- Pods are scheduled across worker nodes.  
- System components (CoreDNS, kube‑proxy) are running correctly.  
