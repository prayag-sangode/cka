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

### **Step 2: Create Pod with RestartPolicy = Never**
```bash
cat <<EOF > busybox-pod.yaml
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
EOF

kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl get pod busybox-pod -o jsonpath='{.status.phase}'
```

Pod runs once, exits with error, **Phase = Failed**, **RestartCount = 0**.

---

### **Step 3: Change to RestartPolicy = OnFailure**
```bash
sed -i 's/restartPolicy: Never/restartPolicy: OnFailure/' busybox-pod.yaml
kubectl delete pod busybox-pod
kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl describe pod busybox-pod
kubectl get pod busybox-pod -o jsonpath='{.status.phase}'
```

Pod restarts after non‑zero exit, **RestartCount increases**, Phase shows `Running`, events show `BackOff restarting failed container`.

---

### **Step 4: Change to RestartPolicy = Always**
```bash
sed -i 's/restartPolicy: OnFailure/restartPolicy: Always/' busybox-pod.yaml
kubectl delete pod busybox-pod
kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl describe pod busybox-pod
```

Pod restarts continuously, **RestartCount keeps climbing**, Phase = `Running`.

---

### **Step 5: Inspect Pod Events & Status**
```bash
kubectl get pod busybox-pod -o wide
kubectl describe pod busybox-pod
kubectl logs busybox-pod
```
- Shows restart count, last state, and failure reason.  
- Confirms kubelet is applying restart policy.

---

### **Step 6: Cleanup**
```bash
kubectl delete pod busybox-pod
```

---

## Lab Verification
- You identified pod lifecycle phases.  
- You tested `Never`, `OnFailure`, and `Always` restart policies using `cat <<EOF` and `sed -i`.  
- You observed differences in **Pod Phase** and **Restart Count**.  
- You inspected pod events, logs, and restart behavior.  
```

---

This version now matches your workflow exactly:  
- Start with `cat <<EOF > busybox-pod.yaml`.  
- Use `sed -i` to flip between `Never`, `OnFailure`, and `Always`.  
- Delete and re‑apply the pod each time to observe behavior.  

