## Day 10 Lab — Labels, Selectors & Annotations

### **Step 1: Understand Labels**
- Labels are **key‑value pairs** attached to objects.  
- They’re used for grouping, selecting, and organizing workloads.  
- Example: `app=frontend`, `env=dev`.

---

### **Step 2: Add Labels to a Pod**
Create a pod manifest `nginx-labeled.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-labeled
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

Apply it:
```bash
kubectl apply -f nginx-labeled.yaml
kubectl get pods --show-labels
```

---

### **Step 3: Use Label Selectors**
Query pods by label:
```bash
kubectl get pods -l app=nginx
kubectl get pods -l tier=frontend
```
Combine selectors:
```bash
kubectl get pods -l app=nginx,tier=frontend
```

---

### **Step 4: Add Labels to Existing Objects**
```bash
kubectl label pod nginx-labeled env=dev
kubectl get pods --show-labels
```
- Adds a new label `env=dev` to the pod.  

---

### **Step 5: Understand Annotations**
- Annotations store **non‑identifying metadata** (notes, descriptions, links).  
- Unlike labels, they’re not used for selection.  
- Example: `description="Owned by Team A"`.

Add an annotation:
```bash
kubectl annotate pod nginx-labeled owner="Team A" purpose="demo"
kubectl describe pod nginx-labeled
```

---

### **Step 6: Hands‑On Verification**
Deploy multiple pods with different labels:
```bash
kubectl run nginx-backend --image=nginx --labels="app=nginx,tier=backend"
kubectl run nginx-cache --image=nginx --labels="app=nginx,tier=cache"
```
Query by tier:
```bash
kubectl get pods -l tier=backend
kubectl get pods -l tier=cache
```

---

### **Step 7: Cleanup**
```bash
kubectl delete pod nginx-labeled nginx-backend nginx-cache
```

---

## Lab Verification
- You created pods with labels.  
- You queried workloads using selectors.  
- You added annotations for descriptive metadata.  
- You confirmed labels enable grouping and isolation.  

