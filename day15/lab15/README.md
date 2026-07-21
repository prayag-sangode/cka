## Fresh Sidecar Lab Workflow

### **Step 1: Create Pod with App + Log Collector**
```bash
cd ~/cka/day15/lab15

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

---

### **Step 2: Inspect Pod**
```bash
kubectl describe pod sidecar-pod
```
- Confirms both containers are running.  
- Shows shared `emptyDir` volume mounted at `/var/log`.

---

### **Step 3: Check Logs per Container**
```bash
kubectl logs sidecar-pod -c app
kubectl logs sidecar-pod -c log-collector
```

---

### **Step 4: Add Monitoring Sidecar**
Now add a third container using `sed -i`. Make sure you’re inside the correct directory (`~/cka/day15/lab15`):

```bash
cd ~/cka/day15/lab15

sed -i '/containers:/a \ \ \ - name: monitor\n      image: busybox\n      command: ["sh", "-c", "while true; do echo Monitoring metrics >> /var/log/metrics.log; sleep 10; done"]\n      volumeMounts:\n        - name: shared-logs\n          mountPath: /var/log' sidecar-pod.yaml

kubectl delete pod sidecar-pod
kubectl apply -f sidecar-pod.yaml
kubectl get pods
```

👉 Now you have **app**, **log-collector**, and **monitor** containers all sharing the same volume.

---

### **Step 5: Inspect All Containers**
```bash
kubectl logs sidecar-pod -c app
kubectl logs sidecar-pod -c log-collector
kubectl logs sidecar-pod -c monitor

kubectl exec -it sidecar-pod -c monitor -- sh
cat /var/log/metrics.log
```

---

### **Step 6: Cleanup**
```bash
kubectl delete pod sidecar-pod
```

---

## Lab Verification
- You created a pod with **app + log collector**.  
- You added a **monitoring sidecar** using `sed -i`.  
- You verified logs and metrics across all containers with `-c`.  
- You confirmed shared volume usage.  

