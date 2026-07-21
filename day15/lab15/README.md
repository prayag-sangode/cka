## Day 15 Lab — Implementing Sidecar Containers for Logging and Monitoring

### **Step 1: Understand Sidecar Pattern**
- A **sidecar container** runs alongside the main application container in the same pod.  
- Common use cases:
  - Logging (collecting app logs).  
  - Monitoring (metrics exporters).  
  - Proxying (service mesh sidecars).  
- Sidecars share volumes and network with the main container.

---

### **Step 2: Create Pod with App, Log Collector, and Monitor**
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

    - name: monitor
      image: busybox
      command: ["sh", "-c", "while true; do echo Monitoring metrics >> /var/log/metrics.log; sleep 10; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log
EOF

kubectl apply -f sidecar-pod.yaml
kubectl get pods
```

The **app** container writes logs into `/var/log/app.log`.  
The **log-collector** sidecar tails the same file continuously.  
The **monitor** sidecar writes metrics into `/var/log/metrics.log`.

---

### **Step 3: Inspect Pod Details**
```bash
kubectl describe pod sidecar-pod
```
- Shows all three containers (`app`, `log-collector`, `monitor`).  
- Confirms shared `emptyDir` volume mounted at `/var/log`.

---

### **Step 4: Check Logs for Each Container**
```bash
kubectl logs sidecar-pod -c app
kubectl logs sidecar-pod -c log-collector
kubectl logs sidecar-pod -c monitor
```

---

### **Step 5: Exec into Each Container and check file contents**
```bash
# App container: exec and check its log file
kubectl exec -it sidecar-pod -c app -- sh -c "cat /var/log/app.log"

# Log-collector container: exec and check tailed log file
kubectl exec -it sidecar-pod -c log-collector -- sh -c "cat /var/log/app.log"

# Monitor container: exec and check metrics file
kubectl exec -it sidecar-pod -c monitor -- sh -c "cat /var/log/metrics.log"

```

---

### **Step 6: Inspect Pod Status JSON**
```bash
kubectl get pod sidecar-pod -o json | jq '.status.containerStatuses'
```
- Shows readiness, restart counts, and state for each container.

---

### **Step 7: Cleanup**
```bash
kubectl delete pod sidecar-pod
```

---

## Lab Verification
- You implemented a pod with **app**, **log-collector**, and **monitor** containers.  
- You verified logs and metrics are shared via `emptyDir` volume.  
- You checked logs for each container with `-c`.  
- You exec’d into each container to confirm shared volume access.  
- You inspected containerStatuses via JSON output.  
