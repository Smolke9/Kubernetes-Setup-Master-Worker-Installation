
# 🐳 Kubernetes Cluster Setup (v1.30)
This guide helps you set up a Kubernetes cluster with **one master** and **one worker node** using **Ubuntu** servers.

---

## ⚙️ Instance Configuration
- Launch 2 Ubuntu EC2 Instances (or any Cloud VM)
- Instance Type: Basic (min. 4 GB RAM)
- Security Group: `KubernetesSG` with following ports:
  - `22`   → SSH
  - `80`   → HTTP
  - `8080` → Alternate HTTP
  - `10250` → Kubelet API
  - `6443` → Kubernetes API Server

---

## 🧩 Steps to Run on **Both Master and Worker Nodes**

### 🔹 Step 1: Set Hostname
> Run different commands depending on the node
```bash
# On Master Node
sudo hostnamectl set-hostname master-k8s

# On Worker Node
sudo hostnamectl set-hostname worker-k8s
```

---

### 🔹 Step 2: Disable Swap (Required by Kubernetes)
```bash
sudo swapoff -a
```

---

### 🔹 Step 3: Install Docker (Container Runtime)
```bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```

---

### 🔹 Step 4: Configure Kubernetes APT Repository (v1.30)
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | \
sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
```

---

### 🔹 Step 5: Install Kubernetes Components
```bash
sudo apt-get install -y kubelet kubeadm kubectl
```

- `kubelet`: runs containers on each node
- `kubeadm`: tool to bootstrap the cluster
- `kubectl`: command-line tool to interact with cluster

---

### 🔹 Step 6: Prevent Automatic Updates (Recommended)
```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## ✅ Summary of Commands (for quick copy-paste)

```bash
sudo hostnamectl set-hostname worker-k8s         # or master-k8s
sudo swapoff -a
sleep 2
sudo apt-get update
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## 📌 Next Steps

- After completing setup on both nodes:
  - Run `kubeadm init` on **Master Node**
  - Copy the `kubeadm join` command and execute it on **Worker Node**
  - Setup `kubectl` config on Master for cluster control
  - You can now deploy pods and services on your cluster!

---

## 🧹 Optional: Reset Cluster (for retry)
```bash
sudo kubeadm reset -f
sudo rm -rf /etc/cni/net.d
sudo rm -rf ~/.kube
```

---

## 🔚 Clean-Up
Once your project is done, you can safely terminate both EC2 instances.
