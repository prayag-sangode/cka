## Day 12 Lab — Pod Lifecycle & Restart Policies

### **Step 1: Understand Pod Lifecycle Phases**
Pods go through these phases:
- **Pending** → Pod accepted but not yet scheduled.  
- **Running** → Pod scheduled and containers running.  
- **Succeeded** → All containers terminated successfully.  
- **Failed** → All containers terminated with errors.  
- **Unknown** → Node communication issue.  

Check pod phase:
```bash
kubectl get pod demo-pod -o jsonpath='{.status.phase}'
```

---

### **Step 2: Explore Restart Policies**
Restart policies are defined in the pod spec:
- **Always** → Restart container if it exits (default for Deployments).  
- **OnFailure** → Restart only if container exits with non‑zero status.  
- **Never** → Do not restart container.  

Example manifest `busybox-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
spec:
  restartPolicy: OnFailure
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "exit 1"]
```

---

### **Step 3: Deploy and Observe Behavior**
Apply:
```bash
kubectl apply -f busybox-pod.yaml
kubectl get pods
```
Describe events:
```bash
kubectl describe pod busybox-pod
```
- With `OnFailure`, the pod restarts after a non‑zero exit.  
- With `Never`, the pod stays in **Failed** state.  

---

### **Step 4: Test Restart Policy = Always**
Modify manifest:
```yaml
restartPolicy: Always
```
Apply and observe:
```bash
kubectl delete pod busybox-pod
kubectl apply -f busybox-pod.yaml
kubectl get pods
```
- Pod keeps restarting after failure.  

---

### **Step 5: Inspect Pod Events & Status**
```bash
kubectl get pod busybox-pod -o wide
kubectl describe pod busybox-pod
kubectl logs busybox-pod
```
- Shows restart count, last state, and failure reason.  

---

### **Step 6: Cleanup**
```bash
kubectl delete pod busybox-pod
```

---

## Lab Verification
- You identified pod lifecycle phases.  
- You deployed pods with different restart policies.  
- You observed how Kubernetes handles container exits.  
- You inspected pod events, logs, and restart counts.  
