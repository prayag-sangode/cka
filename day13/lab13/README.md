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
EOF

kubectl apply -f multi-pod.yaml
kubectl get pods
```

The **writer** container writes a file into `/data/message.txt`.  
The **reader** container reads the same file from the shared volume.

---

### **Step 3: Inspect Pod Details**
```bash
kubectl describe pod multi-pod
```
- Check both containers are listed.  
- Verify the `emptyDir` volume is mounted in both.

---

### **Step 4: Verify Shared Volume Behavior**
```bash
kubectl exec -it multi-pod -c reader -- cat /data/message.txt
```
Output should be: `Hello from Writer`.

---

### **Step 5: Experiment with Restart Policies**
Change restart policy using `sed -i`:
```bash
sed -i 's/restartPolicy: Never/restartPolicy: OnFailure/' multi-pod.yaml
kubectl delete pod multi-pod
kubectl apply -f multi-pod.yaml
```

Observe how containers behave differently when one fails.

---

### **Step 6: Add a Third Sidecar Container**
```bash
cat <<EOF >> multi-pod.yaml
    - name: logger
      image: busybox
      command: ["sh", "-c", "tail -f /data/message.txt"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
EOF

kubectl delete pod multi-pod
kubectl apply -f multi-pod.yaml
```

Now you have **writer**, **reader**, and **logger** all sharing the same volume.

---

### **Step 7: Cleanup**
```bash
kubectl delete pod multi-pod
```

---

## Lab Verification
- You created a multi‑container pod with a shared `emptyDir` volume.  
- You verified containers can read/write to the same file.  
- You experimented with restart policies using `sed -i`.  
- You extended the pod with a sidecar container.  

