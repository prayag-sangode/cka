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
kubectl get pod busybox-pod -o jsonpath='{.status.phase}'
```

---

### **Step 2: Explore Restart Policies**
Restart policies are defined in the pod spec:
- **Always** → Restart container if it exits (default for Deployments).  
- **OnFailure** → Restart only if container exits with non‑zero status.  
- **Never** → Do not restart container.  

---

### **Step 3: Test RestartPolicy = Never**
Create manifest:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
spec:
  restartPolicy: Never
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "exit 1"]
```

Apply:
```bash
kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl get pod busybox-pod -o jsonpath='{.status.phase}'
```

Pod runs once, exits with error, **Phase = Failed**, **RestartCount = 0**.

---

### **Step 4: Test RestartPolicy = OnFailure**
Update manifest:
```bash
sed -i 's/restartPolicy: Never/restartPolicy: OnFailure/' busybox-pod.yaml
kubectl delete pod busybox-pod
kubectl apply -f busybox-pod.yaml
```

Check:
```bash
kubectl get pods
kubectl describe pod busybox-pod
kubectl get pod busybox-pod -o jsonpath='{.status.phase}'
```

Pod restarts after non‑zero exit, **RestartCount increases**, Phase shows `Running`, events show `BackOff restarting failed container`.

---

### **Step 5: Test RestartPolicy = Always**
Update manifest:
```bash
sed -i 's/restartPolicy: OnFailure/restartPolicy: Always/' busybox-pod.yaml
kubectl delete pod busybox-pod
kubectl apply -f busybox-pod.yaml
```

Check:
```bash
kubectl get pods
kubectl describe pod busybox-pod
```

Pod restarts continuously, **RestartCount keeps climbing**, Phase = `Running`.

---

### **Step 6: Inspect Pod Events & Status**
```bash
kubectl get pod busybox-pod -o wide
kubectl describe pod busybox-pod
kubectl logs busybox-pod
```
- Shows restart count, last state, and failure reason.  
- Confirms kubelet is applying restart policy.

---

### **Step 7: Cleanup**
```bash
kubectl delete pod busybox-pod
```

---

## Lab Verification
- You identified pod lifecycle phases.  
- You tested `Never`, `OnFailure`, and `Always` restart policies.  
- You observed differences in **Pod Phase** and **Restart Count**.  
- You inspected pod events, logs, and restart behavior.  
```
