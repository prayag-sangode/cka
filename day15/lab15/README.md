## Day 15 Lab — Implementing Sidecar Containers for Logging and Monitoring

### **Step 1: Understand Sidecar Pattern**
- A **sidecar container** runs alongside the main application container in the same pod.  
- Common use cases:
  - Logging (collecting app logs).  
  - Monitoring (metrics exporters).  
  - Proxying (service mesh sidecars).  
- Sidecars share volumes and network with the main container.

---

### **Step 2: Create Pod with Logging Sidecar**
```bash
cat <<EOF > sidecar-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-pod
spec:
  volumes:
    - name: shared-logs
      emptyDir: {}
  containers:
    - name: app
      image: busybox
      command: ["sh", "-c", "while true; do echo App log entry >> /var/log/app.log; sleep 5; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log
    - name: log-collector
      image: busybox
      command: ["sh", "-c", "tail -f /var/log/app.log"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log
EOF

kubectl apply -f sidecar-pod.yaml
kubectl get pods
```

The **app** container writes logs into `/var/log/app.log`.  
The **log-collector** sidecar tails the same file continuously.

---

### **Step 3: Inspect Pod Details**
```bash
kubectl describe pod sidecar-pod
```
- Shows both containers (`app`, `log-collector`).  
- Confirms shared `emptyDir` volume mounted at `/var/log`.

---

### **Step 4: Check Logs for Each Container**
```bash
kubectl logs sidecar-pod -c app
kubectl logs sidecar-pod -c log-collector
```

---

### **Step 5: Exec into Each Container**
```bash
kubectl exec -it sidecar-pod -c app -- sh
kubectl exec -it sidecar-pod -c log-collector -- sh
```
- From inside either container, inspect `/var/log/app.log`.

---

### **Step 6: Add Monitoring Sidecar**
```bash
sed -i '/containers:/a \ \ \ - name: monitor\n      image: busybox\n      command: ["sh", "-c", "while true; do echo Monitoring metrics >> /var/log/metrics.log; sleep 10; done"]\n      volumeMounts:\n        - name: shared-logs\n          mountPath: /var/log' sidecar-pod.yaml

kubectl delete pod sidecar-pod
kubectl apply -f sidecar-pod.yaml
```

Now you have **app**, **log-collector**, and **monitor** containers all sharing the same volume.

---

### **Step 7: Inspect All Containers**
```bash
kubectl logs sidecar-pod -c app
kubectl logs sidecar-pod -c log-collector
kubectl logs sidecar-pod -c monitor

kubectl exec -it sidecar-pod -c monitor -- sh
cat /var/log/metrics.log
```

---

### **Step 8: Cleanup**
```bash
kubectl delete pod sidecar-pod
```

---

## Lab Verification
- You implemented a sidecar container for logging.  
- You verified logs are shared via `emptyDir` volume.  
- You added a monitoring sidecar using `sed -i`.  
- You inspected logs and exec’d into each container with `-c`.  
- You confirmed sidecar containers enhance observability without modifying the main app.  
