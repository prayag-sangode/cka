## Day 14 Lab — Using Init Containers for Pre‑Work Setup

### **Step 1: Understand Init Containers**
- Init containers run **before** the main application containers.  
- They are ideal for:
  - Pre‑work setup (e.g., creating files, downloading configs).  
  - Checking dependencies (e.g., waiting for a service).  
  - Security tasks (e.g., injecting secrets).  
- They always run to completion before normal containers start.

---

### **Step 2: Create Pod with Init Container**
```bash
cat <<EOF > init-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-pod
spec:
  volumes:
    - name: shared-data
      emptyDir: {}
  initContainers:
    - name: init-writer
      image: busybox
      command: ["sh", "-c", "echo Init setup complete > /data/init.txt"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
  containers:
    - name: app
      image: busybox
      command: ["sh", "-c", "cat /data/init.txt; sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
EOF

kubectl apply -f init-pod.yaml
kubectl get pods
```

The **init-writer** container runs first, writes a file, then exits.  
The **app** container starts only after init completes.

---

### **Step 3: Inspect Pod Details**
```bash
kubectl describe pod init-pod
```
- Shows init container status.  
- Confirms init container ran successfully before app container started.

---

### **Step 4: Verify Init Container Output**
```bash
kubectl logs init-pod -c init-writer
kubectl logs init-pod -c app
```
- `init-writer` → shows “Init setup complete” written.  
- `app` → reads and prints the file from shared volume.

---

### **Step 5: Exec into App Container**
```bash
kubectl exec -it init-pod -c app -- sh
cat /data/init.txt
```
Confirms file created by init container is available to the app container.

---

### **Step 6: Experiment with Init Logic**
Change init command using `sed -i`:
```bash
sed -i 's/Init setup complete/Pre-work done by Init/' init-pod.yaml
kubectl delete pod init-pod
kubectl apply -f init-pod.yaml
```

The new message will appear in `/data/init.txt`.

---

### **Step 7: Cleanup**
```bash
kubectl delete pod init-pod
```

---

## Lab Verification
- You created a pod with an init container.  
- You verified init container runs before the app container.  
- You confirmed shared volume usage between init and app.  
- You experimented with `sed -i` to change init behavior.  


