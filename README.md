
<h1 align="center">Kubernetes (K8s) Info and Setup</h1>

---

- [Introduction to Kubernetes(K8s)](#example-0)
- [Kubernetes Architecture Overview](#example-1)
   - [Kubernetes Architecture Details](#example-2)
- [k8s Related some concepts](#example-3)
- [Kubernetes vs Docker](#example-4)
- [How Kubernetes Came to the Market](#example-5)

- [Setup](#example-6)



<br>


---

<a id="example-0"></a>

# 🚢 Introduction to Kubernetes (K8s)

###  What is Kubernetes?

Kubernetes (often written as K8s) is an open-source container orchestration platform used to deploy, manage, scale, and maintain containerized applications automatically.



In simple words:<br>
👉 Docker runs containers<br>
👉 Kubernetes manages those containers in production


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/images.jpg?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>




---

###  Why is it called K8s?

The word Kubernetes has 8 letters between K and s, so it’s shortened as K + 8 + s = K8s.

---

### Why do we need Kubernetes?

When applications grow, managing containers manually becomes difficult.

Kubernetes helps with:

- Running multiple containers
- Handling crashes automatically
- Scaling apps up/down
- Load balancing
- Zero-downtime deployments

---

### What problems does Kubernetes solve?

Without Kubernetes:

- Containers may crash ❌
- Manual scaling ❌
- Hard to manage networking ❌
- No auto-healing ❌

With Kubernetes:

- ✅ Auto restart (self-healing)
- ✅ Auto scaling
- ✅ Load balancing
- ✅ Easy deployments & rollbacks


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-18%20104338.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/kubernetes_ecosystem.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---

### Key Components of Kubernetes


- Cluster – A group of machines
- Node – A single machine (VM/Server)
- Pod – Smallest unit (runs containers)
- Deployment – Manages pod replicas
- Service – Exposes application
- ConfigMap / Secret – App configuration
- Namespace – Logical separation

---

<a id="example-1"></a>

##  🏗️ Kubernetes Architecture Overview 

<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/img%204.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


###  1️⃣ Kubernetes Cluster


👉 Whole setup is called a Cluster

A Kubernetes Cluster is a group of machines (nodes) that work together to run containerized applications.

Cluster = Master Node + Worker Nodes

---

###  2️⃣ Master Node (Control Plane)

  👉 Brain of the cluster

- Created using: `kubeadm init`
- Controls the entire cluster
- Makes decisions (scheduling, scaling, healing)

Main components inside Master:

- API Server
- Scheduler
- Controller Manager
- etcd (cluster data)

📌 Master does not run application pods (best practice)

---

###  3️⃣ Worker Node (Slave Node)


👉 Where application actually runs

- Joined using: `kubeadm join`
- Can be 1 or many worker nodes
- Each worker node runs pods

Components inside Worker Node:

- kubelet
- kube-proxy
- Container runtime (Docker / containerd)

---


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-18%20104355.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



---

### 4️⃣ Node


👉 A Node is a single machine (EC2 / VM / Server)

- Master is also a node
- Worker is also a node
- Node can be:
   - Master Node
   - Worker (Slave) Node

📌 Node = EC2 / VM

---

### 5️⃣ Pod

👉 Smallest deployable unit in Kubernetes or it is rapper around your container

- Pod runs one or more containers
- Pods are created on Worker Nodes
- Kubernetes never deploys containers directly → it deploys Pods

📌 Pod = Wrapper around container


---

### 🔁 Architecture Flow 

1. Create cluster using `kubeadm init` → Master ready
2. Worker nodes join using `kubeadm join`
3. User applies deployment (`kubectl apply`)
4. Master schedules Pods
5. Pods run on Worker Nodes
6. Containers run inside Pods








---

<a id="example-2"></a>

### Kubernetes Architecture


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/kubernetes-architecture.jpg?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


```pgsql

                User
                 |
              kubectl
                 |
         ------------------
         |  API Server   |
         ------------------
           |      |     |
        etcd  Scheduler Controller
                 |
        ---------------------
        |     Worker Node   |
        |  kubelet          |
        |  kube-proxy       |
        |  Containers       |
        ---------------------





```


```pgsql


                               👤 USER
                                 |
                    --------------------------------
                    |                              |
                 kubectl                        Browser
                    |                              |
        (Kubernetes API Request)            HTTP / HTTPS Traffic
                    |                          ( kubectl )
                    |                              |
                    |                      ┌──────────────────┐
                    |                      │  CLOUD LOAD      │
                    |                      │  BALANCER (ELB)  │
                    |                      └─────────┬────────┘
                    |                                |
                    |                    Kubernetes Service
                    |                 (LoadBalancer / NodePort)
                    |                                |
        ==================================================================
        |                    KUBERNETES CLUSTER                           |
        |                                                                 |
        |   ┌─────────────────────────────────────────────────────────┐   |
        |   │             MASTER NODE (Control Plane)                 │   |
        |   │                     (EC2 / VM)                          │   |
        |   │                                                         │   |
        |   │   kubeadm init                                          │   |
        |   │                                                         │   |
        |   │   ┌──────────────┐                                      │   |
        |   │   │  API SERVER  │ ◄──── kubectl / Service Requests     │   |
        |   │   └──────┬───────┘                                      │   |
        |   │          │                                              │   |
        |   │   ┌──────▼──────┐                                       │   |
        |   │   │    etcd     │  (Cluster State DB)                   │   |
        |   │   └─────────────┘                                       │   |
        |   │                                                         │   |
        |   │   ┌──────────────┐   ┌────────────────────────────┐     │   |
        |   │   │  Scheduler   │   │ kube-controller-manager     │    │   |
        |   │   │ (Pod → Node) │   │ (Desired = Actual State)    │    │   |
        |   │   └──────┬───────┘   └───────────┬────────────────┘     │   |
        |   └──────────┼───────────────────────┼──────────────────────┘   |
        |              |                       |                          |
        |==============|=======================|==========================|
                       |                       |
                 Pod Scheduling           Health / Replica Mgmt
                       |                       |
        ------------------------------------------------------------------
        |        AUTO SCALING GROUP – WORKER NODES (Slave Nodes)          |
        ------------------------------------------------------------------
        |                                                                 |
        |   ┌──────────────────┐     ┌──────────────────┐                 |
        |   │  WORKER NODE 1   │     │  WORKER NODE 2   │   (+ More)      |
        |   │     (EC2 / VM)   │     │     (EC2 / VM)   │                 |
        |   │   kubeadm join   │     │   kubeadm join   │                 |
        |   │                  │     │                  │                 |
        |   │ ┌────────────┐   │     │ ┌────────────┐   │                 |
        |   │ │  kubelet   │◄──┼────►│ │  kubelet   │   │                 |
        |   │ └────────────┘   │     │ └────────────┘   │                 |
        |   │        ▲         │     │        ▲         │                 |
        |   │        │ API     │     │        │ API     │                 |
        |   │        │ Server  │     │        │ Server  │                 |
        |   │        ▼         │     │        ▼         │                 |
        |   │ ┌────────────┐   │     │ ┌────────────┐   │                 |
        |   │ │ kube-proxy │───┼────►│ │ kube-proxy │   │                 |
        |   │ └────────────┘   │     │ └────────────┘   │                 |
        |   │        |         │     │        |         │                 |   
        |   └────────┬─────────┘     └────────┬─────────┘                 |  
        |   │   Services / Load Balancing (ClusterIP)   |                 |
        |   │                 |      |                  |                 |
        |   │ ┌──────── POD ─────────┐                  |                 |
        |   │ │  Container (App)     │                  |                 |   
        |   │ └──────────────────────┘                  |                 |   
        |   │ ┌──────── POD ─────────┐                  |                 |
        |   │ │  Container (App)     │                  |                 |
        |   │ └──────────────────────┘                  |                 |
        |   └──────────────────┘     └──────────────────┘                 |
        |                                                                 |
        ------------------------------------------------------------------

```

---


Kubernetes architecture is divided into two main parts:<br>
1️⃣ Control Plane (Master Node)<br>
2️⃣ Worker Nodes

Together, they form a Kubernetes Cluster.


---

##  1️⃣ Control Plane (Master Node)

👉 Brain of Kubernetes<br>
👉 Makes decisions & manages the cluster

---

🔹 Components of Control Plane

---

### 1. kube-apiserver

<br>

- Entry point of Kubernetes
- All requests go through API Server
- Communicates with kubectl, UI, and components
- Validates & processes requests

📌 Example:

```
kubectl get pods
→ API Server
```

---

### 2. etcd


<br>

 - The master node requires a database to store all cluster-related information.

- This database tracks changes in the cluster, such as:
  - Resources increasing or decreasing
  - Current status of the cluster

- It maintains details like:
  - How many Pods are running
  - How many Nodes are available
  - The overall state of the cluster

- All this information is stored in **etcd**, which runs on the master node.

- etcd stores everything, including:
  - Pod details and Pod count
  - Node details and Node count
  - Cluster configurations
  - The current state of the entire Kubernetes cluster

Example: 

```json

{
  "nodes": 2,
  "pods": 3,
  "podsList": [
    {"name": "nginx-pod", "status": "Running"},
    {"name": "redis-pod", "status": "Running"},
    {"name": "app-pod", "status": "Pending"}
  ]
}
```

<br>

key points :

- Key-value database
- Stores cluster state
- Stores:
  - Pods
  - Nodes
  - ConfigMaps
  - Secrets

📌 If etcd is lost → cluster data is lost








---

### 3. kube-scheduler

<br>


>The scheduler’s job is to schedule Pods, see where they should run, and act like a manager deciding what runs where.


- The scheduler decides **which Node a Pod should run on**.  
- It watches all **unscheduled Pods** in the cluster.  
- It checks Node **resources, labels, taints, affinities**, etc.  
- Then it assigns the Pod to the **most suitable Node**.  
- After scheduling, the **Pod runs on that Node**.

<br> 

key points :

- Decides where to run Pods
- Selects best worker node based on:
  - CPU
  - Memory
  - Availability

📌 Scheduler does NOT run pods, only assigns nodes.


---


### 4. kube-controller-manager

<br>

   
- The Control Manager’s job is to monitor cluster health and manage resources.
- Before a Pod is created on any Node, the Scheduler asks the Control Manager if the Node is ready.
  
- The Control Manager continuously checks the health of all Nodes:
  - When a Node joins the cluster, it verifies its health.
  - It keeps monitoring the Node over time.

- Only if the Node is healthy, the Scheduler assigns the Pod to that Node.
- In short:<br>
   Scheduler decides “where to run,” but only after the Control Manager confirms the Node is healthy.

>The Control Manager checks the health of Nodes. Only if a Node is healthy, the Scheduler assigns Pods to it.

<br>

key points :


- Runs controllers
- Ensures desired state = actual state
Examples:
  - Node Controller
  - ReplicaSet Controller
  - Deployment Controller

📌 If Pod dies → controller creates new Pod.


---


### 5. cloud-controller-manager (Optional)


<br>

- Integrates with cloud providers
- Manages:
  - Load balancers
  - Volumes
  - Nodes (AWS / Azure / GCP)


---
---


## 2️⃣ Worker Node


👉 Where applications actually run

🔹 Components of Worker Node

---

### 1. kubelet

- Agent running on each node
- Talks to API Server
- Ensures Pods are running

📌 kubelet = Pod manager of the node


---

### 2. Container Runtime

- Runs containers
- Examples:
  - Docker
  - containerd
  - CRI-O
 
 ---

### 3. kube-proxy

- Manages networking
- Enables service-to-pod communication
- Handles load balancing

---

### 4. Pods

- Smallest deployable unit
- Contains one or more containers
- Shares:
  - Network
  - Storage

---

### 🔁 How Kubernetes Architecture Works (Flow)

1. User runs kubectl apply
2. Request goes to API Server
3. Data stored in etcd
4. Scheduler selects node
5. kubelet runs Pod
6. kube-proxy enables networking
7. Controller ensures desired state


---

<a id="example-3"></a>

## Here are some more points


### ■ Kubernetes Service
- Used because Pods have dynamic IPs
- Provides a stable IP/DNS
- Used to expose and access Pods
- Works by routing traffic to matching Pods using labels

### ■ Load Balancer
- Used to distribute traffic evenly
- Prevents overload on a single Pod
- Works by sending requests to multiple Pods via a Service

### ■ Horizontal Pod Autoscaler (HPA)
- Used to handle increasing load
- Automatically increases/decreases Pod count
- Works by monitoring CPU/Memory metrics

### ■ Cluster Autoscaler
- Used when Pods cannot be scheduled due to lack of Nodes
- Automatically adds or removes Nodes
- Works with cloud Auto Scaling Groups (ASG)

### ■ Auto Scaling Group (ASG)

- Auto Scaling handles the management of Nodes.
- And Kubernetes handles the Pods running inside the Nodes.
- Used to manage Node instances
- Automatically adds/removes EC2 instances
- Ensures high availability of Nodes


---





---


<a id="example-4"></a>

### Kubernetes vs Docker

| Docker                          | Kubernetes                         |
| ------------------------------- | ---------------------------------- |
| Creates and runs containers     | Manages and orchestrates containers |
| Works mainly on a single host   | Works across a multi-node cluster  |
| Manual container scaling        | Automatic scaling of applications  |
| No built-in self-healing        | Self-healing (auto restart, replace) |
| Limited networking features    | Advanced networking and services  |
| No load balancing by default   | Built-in load balancing            |
| Best for development & testing | Best for production environments  |
| Focuses on containers          | Focuses on container management   |


---
---
---

<a id="example-5"></a>


## 🌍 How Kubernetes Came to the Market





<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Kubernetes.webp?raw=true" width="500" alt="Initialize Repository Screenshot">
</p> 



Kubernetes came from Google’s internal system called Borg.
Google was running millions of containers for years and needed a powerful system to manage them automatically.

### 📜 The Story


- Google started building an internal system called **Borg** around 2003–2004
  to manage containers and applications at a very large scale.

- Borg was created by Google engineers to run millions of containers reliably
  across thousands of machines.

- Based on the experience from Borg, Google decided to build a new,
  more general platform for everyone.

- In **2014**, Google open-sourced this project and named it **Kubernetes**.

- The name Kubernetes comes from a Greek word meaning **“helmsman”**,
  the person who steers a ship 🚢.

- In **2015**, Kubernetes was donated to the **Cloud Native Computing Foundation (CNCF)**,
  which helped it grow as a community-driven project.

- The main goal of Kubernetes is to **automatically manage containers in production**:
  scaling, healing, networking, and deployments.



### 📅 Kubernetes Timeline

- 2003–2004 → Google starts building **Borg** for internal container management.
- 2014 → Google open-sources the project as **Kubernetes**.
- 2015 → Kubernetes is donated to **CNCF** (Cloud Native Computing Foundation).
- 2016 → Kubernetes gains wide adoption in the industry.
- Present → Kubernetes is the most popular container orchestration platform.

---

###  🚀 Why Kubernetes Became Popular

- Companies were using Docker, but:
   - Docker alone was not enough for large-scale apps
   - No auto-scaling, no self-healing, no cluster management

- Kubernetes solved these problems:
  - Auto-healing
  - Auto-scaling
  - Load balancing
  - Zero-downtime deployments


---

### 🏢 Big Companies Adopted Kubernetes

- Google → GKE
- Microsoft → AKS
- Amazon → EKS
- Red Hat → OpenShift

👉 When Microsoft adopted Kubernetes and launched Azure AKS, it became enterprise-ready and trusted globally.

---

### 💡 Microsoft & Kubernetes


Microsoft saw that:

- Enterprises need cloud-native, scalable, portable apps
- Kubernetes works across Azure, AWS, GCP, on-prem
  
So Microsoft:

- Fully supported Kubernetes
- Launched Azure Kubernetes Service (AKS)
- Contributed code to Kubernetes (open-source)

---

### 📈 Why Kubernetes Won the Market

- Cloud-agnostic (works everywhere)
- Backed by Google + Microsoft + CNCF
- Perfect for microservices
- Industry standard for DevOps & Cloud

---
---
---


<a id="example-6"></a>


<h1>Setup</h1>

- [Screenshots](#example-7)
- [Screpts](#example-8)
- [Click to see linkdin post ..PPT is also there.]()


---

##  Kubernetes Cluster Setup on AWS (For kubeadm)


To set up a Kubernetes cluster using kubeadm, we first need to launch two EC2 instances on AWS.

###  Step 0: Launch EC2 Instances

We launch two EC2 instances with the following roles:

1️⃣ Kubernetes Master Instance

- Instance Name: `k8s-master`
- Instance Type: `t3.small`
- OS: `Ubuntu` (20.04 / 22.04)
- Purpose: Runs Kubernetes Control Plane components

Security Group – Inbound Rules (Master)


| Port  | Protocol | Source    | Purpose               |
| ----- | -------- | --------- | --------------------- |
| 22    | TCP      | 0.0.0.0/0 | SSH access            |
| 80    | TCP      | 0.0.0.0/0 | Application access    |
| 443   | TCP      | 0.0.0.0/0 | HTTPS access          |
| 6443  | TCP      | 0.0.0.0/0 | Kubernetes API Server |
| 10250 | TCP      | 0.0.0.0/0 | Kubelet communication |



2️⃣ Kubernetes Worker Instance

- Instance Name: `k8s-node`
- Instance Type: `t3.micro`
- OS: `Ubuntu`
- Purpose: Runs application workloads (Pods)


Security Group – Inbound Rules (Worker)<br>
The worker node uses the same ports as the Master:

- 22 (SSH)
- 80 (HTTP)
- 443 (HTTPS)
- 6443 (API communication)
- 10250 (Kubelet)




<p align="center">
  <img src="" width="500" alt="Initialize Repository Screenshot">
</p>






---
---
  

##  Kubernetes Cluster Setup Using kubeadm

There are multiple ways (minimum 5) to set up a Kubernetes cluster, such as:

- Minikube
- Kind
- KOPS
- Managed Kubernetes (EKS, AKS, GKE)
- kubeadm ✅

> In this project, we are using kubeadm to set up the Kubernetes cluster.<br>
  So, for this requirement, we need a master–slave architecture, and we will create (design) and implement it.

 ---

##  Master Node Setup


To create a Kubernetes cluster using kubeadm, we first need to configure a Master node.<br>
The following steps are performed on the Master server.

###  Step 1: Connect to the Master Server  & Change the hostname


```bash

ssh -i server1.pem ubuntu@<public-ip>

sudo hostnamectl set-hostname Master
exit
```
This sets the server hostname to Master, which helps identify the role of the node in the cluster.

---

###  Step 2: Common Kubernetes Setup (Master + Worker)

```bash
nano k8s-common-containerd.sh
sudo chmod +x k8s-common-containerd.sh
sudo ./k8s-common-containerd.sh
```

This script:

- Installs containerd
- Installs kubeadm, kubelet, and kubectl
- Disables swap
- Performs common prerequisites required on all nodes

---

###   Step 3: Initialize Kubernetes Master


```bash
nano k8s-master-init.sh
sudo chmod +x k8s-master-init.sh
sudo ./k8s-master-init.sh
```

This script:

- Initializes the Kubernetes control plane
- Configures kubectl access
- Deploys the pod network
- Generates the join token for worker nodes

---

###   Step 4: Verify Master Node


```
kubectl get nodes -o wide
```

This command is used to verify that the Master node is ready and running.

---
---

##  Worker (Node) Setup


After setting up the Master, we configure the Worker node.


###   Step 1: Set Hostname as Node

```
sudo hostnamectl set-hostname Node
exit
```

This sets the hostname to Node, identifying it as a worker node.


---

###   Step 2: Common Kubernetes Setup

```bash
nano k8s-common-containerd.sh
sudo chmod +x k8s-common-containerd.sh
sudo ./k8s-common-containerd.sh
```

The same common setup script is executed on the worker node.

---

###  Step 3: Join Worker Node to Cluster

```bash
nano k8s-worker-join.sh
sudo chmod +x k8s-worker-join.sh
sudo ./k8s-worker-join.sh
```

This script uses the kubeadm join command to connect the worker node to the Master.

---

###  Step 4: Verify from Master

```
kubectl get nodes
```

This confirms that the worker node has successfully joined the cluster.

---

###   Final Cluster Flow

```markdown
User → kubectl → Master Node
                 ↓
            Worker Node
```

###  Key Points

- kubeadm is used for production-like cluster setup
- containerd is the container runtime
- Master manages cluster state via etcd
- Worker nodes run application pods
- kubeadm join securely adds nodes to the cluster

---
---

<a id="example-7"></a>

## Screenshots


- The common part between the two servers.


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-22%20121300.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p> 


---



- Then, based on their dependencies, we created .sh files and gave permission to run them.

<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-22%20121353.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p> 


---


- Run k8s-master-init.sh on the master node.
- A token is generated for worker nodes to join the cluster.
- Copy and use the token when running the script on worker (slave) nodes.
- The token is required for worker nodes to connect to the master.
- After providing the token, worker nodes successfully connect.
- Verify the connection to ensure cluster readiness.



<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-22%20114500.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-22%20121432.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---

-  And here is last part to see history

<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/Screenshot%202026-01-22%20114500.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>




---

---


<a id="example-8"></a>


#  Script

-  `k8s-common-containerd.sh`

```bash

#!/bin/bash
set -e
 
echo "==== COMMON: Starting Kubernetes base setup (containerd + kubeadm) ===="
 
# Disable swap (required for Kubernetes)
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
echo "Swap disabled."
 
# Load kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
 
# Apply sysctl parameters
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
sudo sysctl --system
 
# Install dependencies
sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl gpg lsb-release
 
# Remove any old Docker/containerd versions
sudo apt-get remove -y docker docker-engine docker.io containerd runc || true
 
# Setup Docker repository (for containerd)
sudo rm -f /etc/apt/keyrings/docker.gpg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
 
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 
sudo apt-get update -y
sudo apt-get install -y containerd.io
 
# Configure containerd for CRI
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
echo "Containerd configured successfully."
 
# Setup Kubernetes repository
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list > /dev/null
 
sudo apt-get update -y
sudo apt-get install -y kubelet kubeadm kubectl cri-tools
sudo apt-mark hold kubelet kubeadm kubectl
echo "==== COMMON: Completed successfully ===="
```

---


-  `k8s-master-init.sh`

```bash
#!/bin/bash
set -e
 
echo "==== MASTER: Initializing Kubernetes Control Plane ===="
 
# Initialize Kubernetes with containerd
sudo kubeadm init --cri-socket unix:///run/containerd/containerd.sock --pod-network-cidr=10.244.0.0/16
 
# Setup kubeconfig for the ubuntu user automatically
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
echo "Kubeconfig setup complete."
 
# Deploy Flannel CNI plugin
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
 
echo "==== MASTER: Kubernetes Control Plane is ready ===="
echo "Run this command to get join token for workers:"
echo "  kubeadm token create --print-join-command"
```

---

-  `k8s-worker-join.sh`

```bash
#!/bin/bash
set -e
 
echo "==== WORKER: Joining Kubernetes cluster ===="
 
read -p "Paste the kubeadm join command from master: " joincmd
sudo $joincmd --cri-socket unix:///run/containerd/containerd.sock
 
echo "==== WORKER: Successfully joined cluster! ===="

```











































