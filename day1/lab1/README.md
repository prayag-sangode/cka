## Day 1 Lab — Installing Docker, Kind, kubectl & Helm

### **Step 1: Install Docker**
Docker is required because **Kind runs Kubernetes clusters inside Docker containers**.  
```bash
sudo apt update && sudo apt -y install docker.io
```
- Updates package index and installs Docker.  

```bash
sudo usermod -aG docker $USER
newgrp docker
docker ps
```
- Adds your user to the `docker` group so you can run Docker without `sudo`.  
- `docker ps` verifies Docker is working.


### **Step 2: Install Kind**
Kind = *Kubernetes IN Docker*.  
```bash
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.32.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```
- Downloads the latest Kind binary for Linux x86_64.  
- Makes it executable and moves it into your PATH.  


### **Step 3: Install kubectl**
kubectl is the CLI tool to interact with Kubernetes clusters.  
```bash
curl -LO https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
```
- Downloads the latest stable kubectl binary.  
- Makes it executable and moves it into your PATH.  


### **Step 4: Install Helm**
Helm is the package manager for Kubernetes.  
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh
```
- Downloads Helm install script.  
- Runs the script to install Helm v4.  


### **Step 5: Verify All Tools**
Check versions to confirm installation:  
```bash
docker --version ; echo
kind version ; echo
kubectl version --client ; echo
helm version ; echo
```


### **Step 6: Create Your First Kind Cluster**
Now let’s test the setup by creating a cluster.  
```bash
kind create cluster --name my-cluster
kubectl cluster-info
kubectl get nodes
```
- Creates a cluster named `my-cluster`.  
- Shows cluster info and verifies nodes are running.  

Check context explicitly:  
```bash
kubectl cluster-info --context kind-my-cluster
kubectl cluster-info
kubectl get nodes
```
### **Cleanup**
```
kind delete cluster --name my-cluster
docker ps -a
kubectl config get-contexts
kubectl config delete-context kind-my-cluster
kubectl config delete-cluster kind-my-cluster
kubectl config unset users.kind-my-cluster
```

## Lab Verification
- Docker is installed and running (`docker ps` works).  
- Kind successfully created a cluster (`kind create cluster`).  
- kubectl connects to the cluster and lists nodes.  
- Helm is installed and ready for package management.  
