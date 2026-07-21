## Day 9 Lab — Namespaces for Resource Isolation

### **Step 1: Understand Namespaces**
- Namespaces provide **logical isolation** within a cluster.  
- Useful for separating environments (dev, test, prod) or teams.  
- Default namespaces: `default`, `kube-system`, `kube-public`, `kube-node-lease`.

---

### **Step 2: Create a Namespace**
```bash
kubectl create namespace dev-team
```
Verify:
```bash
kubectl get namespaces
```

---

### **Step 3: Deploy Resources into a Namespace**
Create a pod in the new namespace:
```bash
kubectl run nginx-dev --image=nginx --namespace=dev-team
kubectl get pods -n dev-team
```

---

### **Step 4: Set a Default Namespace for Context**
Instead of typing `-n dev-team` every time:
```bash
kubectl config set-context --current --namespace=dev-team
```
Now all commands default to `dev-team`.

Check:
```bash
kubectl get pods
```

---

### **Step 5: Resource Isolation Demo**
Deploy the same pod in two namespaces:
```bash
kubectl run nginx-test --image=nginx --namespace=test-team
kubectl run nginx-dev --image=nginx --namespace=dev-team
```
Verify:
```bash
kubectl get pods -n test-team
kubectl get pods -n dev-team
```
- Same pod name can exist in different namespaces without conflict.  

---

### **Step 6: Inspect Namespace Resources**
List all resources in a namespace:
```bash
kubectl get all -n dev-team
```

---

### **Step 7: Cleanup**
Delete namespaces and their resources:
```bash
kubectl delete namespace dev-team
kubectl delete namespace test-team
```

---

## Lab Verification
- You created custom namespaces.  
- You deployed workloads into different namespaces.  
- You set a default namespace for your context.  
- You confirmed resource isolation across namespaces.  

