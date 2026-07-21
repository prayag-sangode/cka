## Day 11 Lab — Deploying & Inspecting Pods

### **Step 1: Create a Pod Manifest**
Write a simple pod definition `nginx-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

---

### **Step 2: Deploy the Pod**
Apply the manifest:
```bash
kubectl apply -f nginx-pod.yaml
```
Check pod status:
```bash
kubectl get pods
```

---

### **Step 3: Inspect Pod Details**
Use `describe` to see events and configuration:
```bash
kubectl describe pod nginx-pod
```
This shows:
- Node assignment  
- Container image details  
- Events (scheduling, image pull, start)  

---

### **Step 4: View Pod Logs**
Check container logs:
```bash
kubectl logs nginx-pod
```
Useful for debugging application output.

---

### **Step 5: Execute Commands Inside Pod**
Open a shell inside the container:
```bash
kubectl exec -it nginx-pod -- /bin/bash
```
Test connectivity:
```bash
curl localhost:80
```

---

### **Step 6: Troubleshooting Pod Issues**
- Pod stuck in `ImagePullBackOff` → check image name.  
- Pod stuck in `CrashLoopBackOff` → inspect logs.  
- Pod pending → check node resources with:
```bash
kubectl describe pod nginx-pod
kubectl get nodes
```

---

### **Step 7: Cleanup**
Delete the pod:
```bash
kubectl delete pod nginx-pod
```

---

## Lab Verification
- You created and deployed a pod manifest.  
- You inspected pod details and events.  
- You viewed logs and executed commands inside the pod.  
- You practiced troubleshooting common pod states.  

