## Day 13 Lab — Designing Multi‑Container Pods with Shared Volumes

### **Step 1: Understand Multi‑Container Pods**
- A Pod can run multiple containers that share:
  - **Network namespace** (same IP, localhost).  
  - **Storage volumes** (shared filesystem).  
- Common use cases:
  - Sidecar containers (logging, monitoring).  
  - Helper containers (init, data loader).  
  - Shared cache or scratch space.

---

### **Step 2: Create a Multi‑Container Pod with Shared Volume**
```bash
cat <<EOF > multi-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  restartPolicy: Never
  volumes:
    - name: shared-data
      emptyDir: {}
  containers:
    - name: writer
      image: busybox
      command: ["sh", "-c", "echo Hello from Writer > /data/message.txt; sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
    - name: reader
      image: busybox
      command: ["sh", "-c", "cat /data/message.txt; sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
    - name: logger
      image: busybox
      command: ["sh", "-c", "tail -f /data/message.txt"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
EOF

kubectl apply -f multi-pod.yaml
kubectl get pods
```

---

### **Step 3: Inspect Pod Details**
```bash
kubectl describe pod multi-pod
```
- Shows all 3 containers (`writer`, `reader`, `logger`).  
- Verify the `emptyDir` volume is mounted in all.

---

### **Step 4: Check Logs for Each Container**
```bash
kubectl logs multi-pod -c writer
kubectl logs multi-pod -c reader
kubectl logs multi-pod -c logger
```
- `writer` → writes `Hello from Writer`.  
- `reader` → prints the file contents.  
- `logger` → tails the file continuously.

---

### **Step 5: Exec into Each Container**
```bash
kubectl exec -it multi-pod -c writer -- sh
kubectl exec -it multi-pod -c reader -- sh
kubectl exec -it multi-pod -c logger -- sh
```
- From inside any container, check shared volume:
```bash
cat /data/message.txt
```

---

### **Step 6: Inspect Pod Status JSON**
```bash
kubectl get pod multi-pod -o json | jq '.status.containerStatuses'
```
- Shows readiness, restart counts, and state for each container.

---

### **Step 7: Cleanup**
```bash
kubectl delete pod multi-pod
```

---

## Lab Verification
- You created a multi‑container pod with a shared `emptyDir` volume.  
- You verified containers can read/write to the same file.  
- You checked logs for each container with `-c`.  
- You exec’d into each container to confirm shared volume access.  
- You inspected containerStatuses via JSON output.  

