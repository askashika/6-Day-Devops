# Devops Training - Day 5 :  Kubernetes Cluster Creation, Configuration, and Namespace Management

---

##  Creating a Kubernetes Cluster on AWS EC2

### Step 1: Launch EC2 Instance
- Go to AWS EC2 console
- Launch a new instance:
  - **Instance type**: `t2.medium`
  - **OS**: Ubuntu
  - **Memory**: up to 25 GB
- Configure and launch the instance
- Connect using **PuTTY**

---

##  Initial Setup in PuTTY Console

```bash
sudo su
apt update -y
```

---

##  Install `kubectl`

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
kubectl version --client
```

---

##  Install `eksctl`

```bash
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo mv /tmp/eksctl /usr/local/bin
```

---

##  Install `awscli`

```bash
sudo apt-get install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

---

##  Configure AWS CLI

```bash
aws configure
```

- Enter:
  - **Access Key ID**
  - **Secret Access Key**
  - **Region** (e.g., `sa-east-1`)

---

##  Create EKS Cluster using `eksctl`

```bash
eksctl create cluster --name <cluster-name> --region <region-name> --zones <zone>,sa-east-1b --nodegroup-name <name> --node-type t2.medium --nodes 2
```

---

##  Verifying Cluster and Namespace Setup

### Become Root User

```bash
sudo su
```

### Check Nodes

```bash
kubectl get nodes
```

### Create Namespace

```bash
kubectl create namespace ash
```

Or create using a YAML file:

```bash
touch ash-namespace.yaml
vi ash-namespace.yaml
```

#### `ash-namespace.yaml` content:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ash-2
```

```bash
kubectl apply -f ash-namespace.yaml
kubectl get ns
```

---

##  Creating a Pod

```bash
vi ash-pod.yaml
```

#### `ash-pod.yaml` content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ash-2
  namespace: ash-namespace
spec:
  containers:
    - name: c-1
      image: daviddocker526/ipl
      ports:
        - containerPort: 80
```

```bash
kubectl apply -f ash-pod.yaml
kubectl get pods -n ash-2
kubectl logs ash-pod1 -n ash-2
kubectl describe pod ash-pod1 -n ash-2
kubectl delete pod ash-pod1 -n ash-2
```

---

##  Create a ReplicaSet

```bash
vi ash-replica.yaml
```

#### `ash-replica.yaml` content:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: ash-replica
  namespace: ash-2
spec:
  replicas: 5
  selector:
    matchLabels:
      app: ipl
  template:
    metadata:
      labels:
        app: ipl
    spec:
      containers:
        - name: c-2
          image: daviddocker526/ipl
          ports:
            - containerPort: 80
```

```bash
kubectl apply -f ash-replica.yaml
kubectl get rs -n ash-2
```

---

 