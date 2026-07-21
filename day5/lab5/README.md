## Day 5 Lab — Writing & Validating YAML Manifests

### **Step 1: Understand YAML Basics**
- YAML = “YAML Ain’t Markup Language” → human‑friendly data format.  
- Key rules:
  - Indentation matters (use spaces, not tabs).  
  - Key‑value pairs are written as `key: value`.  
  - Lists use `- item`.  
  - Strings don’t need quotes unless special characters are used.  

---

### **Step 2: Write a Simple Pod Manifest**
Create a file `nginx-pod.yaml`:

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

This defines a **Pod** running an NGINX container.

---

### **Step 3: Validate YAML Syntax**
Use `kubectl` to check the manifest before applying:
```bash
kubectl apply --dry-run=client -f nginx-pod.yaml
```
- `--dry-run=client` → validates syntax locally without creating resources.  

You can also use:
```bash
kubectl explain pod
```
- Shows schema and fields for Pods.

---

### **Step 4: Apply the Manifest**
Create the pod:
```bash
kubectl apply -f nginx-pod.yaml
```
Verify:
```bash
kubectl get pods
kubectl describe pod nginx-pod
```

---

### **Step 5: Debug Common YAML Issues**
- **Indentation errors** → always use 2 spaces per level.  
- **Missing apiVersion/kind** → required fields.  
- **Wrong field names** → check with `kubectl explain`.  
- **Image pull errors** → check with `kubectl describe pod`.

---

### **Step 6: Clean Up**
Delete the pod when finished:
```bash
kubectl delete -f nginx-pod.yaml
```

---

## Lab Verification
- You wrote a valid YAML manifest.  
- You validated it with `--dry-run`.  
- You deployed and verified the pod.  
- You learned how to debug YAML issues.  
