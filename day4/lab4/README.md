## Day 4 Lab — Managing Clusters with kubectl & kubeconfig

### **Step 1: Understand kubeconfig**
- kubeconfig is the configuration file that stores cluster connection details.  
- Default location: `~/.kube/config`.  
- It contains:
  - **clusters** → API server endpoints & certificates  
  - **users** → authentication info  
  - **contexts** → mapping of cluster + user + namespace  

---

### **Step 2: View Current Context**
Check which cluster you’re connected to:
```bash
kubectl config current-context
kubectl config get-contexts
```
- Shows all available contexts.  
- Highlights the active one with `*`.

---

### **Step 3: Switch Between Contexts**
If you have multiple clusters (e.g., `kind-my-cluster`, `kind-multi-cluster`):
```bash
kubectl config use-context kind-my-cluster
kubectl config use-context kind-multi-cluster
```
- Switches your active cluster.  

---

### **Step 4: Create a New Context**
You can manually add a context:
```bash
kubectl config set-context my-lab-context \
  --cluster=kind-my-cluster \
  --user=kind-my-cluster \
  --namespace=default
```
- Creates a custom context named `my-lab-context`.  

---

### **Step 5: Use Namespaces with Contexts**
Set a default namespace for a context:
```bash
kubectl config set-context dev-context \
  --cluster=kind-multi-cluster \
  --user=kind-multi-cluster \
  --namespace=development
```
Switch to it:
```bash
kubectl config use-context dev-context
```
Now all kubectl commands default to the `development` namespace.

---

### **Step 6: Inspect kubeconfig File**
Open the file:
```bash
cat ~/.kube/config
```
You’ll see YAML with clusters, users, and contexts defined.

---

### **Step 7: Hands‑On Verification**
Deploy a pod in different contexts:
```bash
kubectl run testpod --image=nginx
kubectl get pods
```
Switch context and deploy again:
```bash
kubectl config use-context kind-my-cluster
kubectl run testpod2 --image=nginx
kubectl get pods
```
- Confirms workloads are isolated per cluster/context.

---

## Lab Verification
- You can list and switch contexts.  
- You can set default namespaces per context.  
- You understand how kubeconfig stores cluster credentials.  
- You deployed pods across different clusters using contexts.  

