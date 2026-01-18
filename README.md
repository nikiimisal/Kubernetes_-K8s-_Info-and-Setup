# Kubernetes (K8s) Info and Setup






# 🚢 Introduction to Kubernetes (K8s)

###  What is Kubernetes?

Kubernetes (often written as K8s) is an open-source container orchestration platform used to deploy, manage, scale, and maintain containerized applications automatically.



In simple words:
👉 Docker runs containers
👉 Kubernetes manages those containers in production

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

##  🏗️ Kubernetes Architecture Overview 

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

👉 Smallest deployable unit in Kubernetes

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

### Kubernetes Architecture

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
                              Browser
                                 |
                        ┌──────────────────┐
                        │  CLOUD LOAD      │
                        │  BALANCER (ELB)  │
                        └─────────┬────────┘
                                  |
                          Kubernetes Service
                         (NodePort / LoadBalancer)
                                  |
        ==================================================================
        |                    KUBERNETES CLUSTER                           |
        |                                                                 |
        |   ┌─────────────────────────────────────────────────────────┐ |
        |   │             MASTER NODE (Control Plane)                  │ |
        |   │                     (EC2 / VM)                           │ |
        |   │                                                         │ |
        |   │   kubeadm init                                          │ |
        |   │                                                         │ |
        |   │   ┌──────────────┐                                     │ |
        |   │   │  API SERVER  │ ◄──── kubectl / Service Requests     │ |
        |   │   └──────┬───────┘                                     │ |
        |   │          │                                             │ |
        |   │   ┌──────▼──────┐                                      │ |
        |   │   │    etcd     │  (Cluster State DB)                  │ |
        |   │   └─────────────┘                                      │ |
        |   │                                                         │ |
        |   │   ┌──────────────┐   ┌────────────────────────────┐   │ |
        |   │   │  Scheduler   │   │ kube-controller-manager     │   │ |
        |   │   │ (Pod → Node) │   │ (Desired = Actual State)    │   │ |
        |   │   └──────┬───────┘   └───────────┬────────────────┘   │ |
        |   └──────────┼───────────────────────┼────────────────────┘ |
        |              |                       |                      |
        |==============|=======================|======================|
                       |                       |
                 Pod Scheduling           Health / Scaling
                       |                       |
        ------------------------------------------------------------------
        |        AUTO SCALING GROUP – WORKER NODES (Slave Nodes)          |
        ------------------------------------------------------------------
        |                                                                |
        |   ┌──────────────────┐     ┌──────────────────┐              |
        |   │  WORKER NODE 1   │     │  WORKER NODE 2   │   (+ More)    |
        |   │     (EC2)        │     │     (EC2)        │              |
        |   │ kubeadm join     │     │ kubeadm join     │              |
        |   │                  │     │                  │              |
        |   │ ┌────────────┐  │     │ ┌────────────┐  │              |
        |   │ │  kubelet   │◄─┼─────►│ │  kubelet   │  │              |
        |   │ └────────────┘  │     │ └────────────┘  │              |
        |   │ ┌────────────┐  │     │ ┌────────────┐  │              |
        |   │ │ kube-proxy │──┼─────►│ │ kube-proxy │  │              |
        |   │ └────────────┘  │     │ └────────────┘  │              |
        |   │                  │     │                  │              |
        |   │ ┌──────────────────────── POD ───────────────────────┐  |
        |   │ │  Container (App)                                   │  |
        |   │ └────────────────────────────────────────────────────┘  |
        |   │ ┌──────────────────────── POD ───────────────────────┐  |
        |   │ │  Container (App)                                   │  |
        |   │ └────────────────────────────────────────────────────┘  |
        |   └──────────────────┘     └──────────────────┘              |
        |                                                                |
        ------------------------------------------------------------------



```




Kubernetes architecture is divided into two main parts:
1️⃣ Control Plane (Master Node)
2️⃣ Worker Nodes

Together, they form a Kubernetes Cluster.


---

###  1️⃣ Control Plane (Master Node)

👉 Brain of Kubernetes
👉 Makes decisions & manages the cluster

🔹 Components of Control Plane

1. kube-apiserver

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

2. etcd

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

- Key-value database
- Stores cluster state
- Stores:
  - Pods
  - Nodes
  - ConfigMaps
  - Secrets

📌 If etcd is lost → cluster data is lost








---

 3. kube-scheduler


>The scheduler’s job is to schedule Pods, see where they should run, and act like a manager deciding what runs where.


- The scheduler decides **which Node a Pod should run on**.  
- It watches all **unscheduled Pods** in the cluster.  
- It checks Node **resources, labels, taints, affinities**, etc.  
- Then it assigns the Pod to the **most suitable Node**.  
- After scheduling, the **Pod runs on that Node**.



- Decides where to run Pods
- Selects best worker node based on:
  - CPU
  - Memory
  - Availability

📌 Scheduler does NOT run pods, only assigns nodes.


---


4. kube-controller-manager

   
- The Control Manager’s job is to monitor cluster health and manage resources.
- Before a Pod is created on any Node, the Scheduler asks the Control Manager if the Node is ready.
  
- The Control Manager continuously checks the health of all Nodes:
  - When a Node joins the cluster, it verifies its health.
  - It keeps monitoring the Node over time.

- Only if the Node is healthy, the Scheduler assigns the Pod to that Node.
- In short:<br>
   Scheduler decides “where to run,” but only after the Control Manager confirms the Node is healthy.

>The Control Manager checks the health of Nodes. Only if a Node is healthy, the Scheduler assigns Pods to it.


- Runs controllers
- Ensures desired state = actual state
Examples:
  - Node Controller
  - ReplicaSet Controller
  - Deployment Controller

📌 If Pod dies → controller creates new Pod.


---


5. cloud-controller-manager (Optional)

- Integrates with cloud providers
- Manages:
  - Load balancers
  - Volumes
  - Nodes (AWS / Azure / GCP)


---
---


### 2️⃣ Worker Node


👉 Where applications actually run

🔹 Components of Worker Node

---

1. kubelet

- Agent running on each node
- Talks to API Server
- Ensures Pods are running

📌 kubelet = Pod manager of the node


---

2. Container Runtime

- Runs containers
- Examples:
  - Docker
  - containerd
  - CRI-O
 
 ---

3. kube-proxy

- Manages networking
- Enables service-to-pod communication
- Handles load balancing

---

4. Pods

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

### Here are some more points


■ Kubernetes Service
- Used because Pods have dynamic IPs
- Provides a stable IP/DNS
- Used to expose and access Pods
- Works by routing traffic to matching Pods using labels

■ Load Balancer
- Used to distribute traffic evenly
- Prevents overload on a single Pod
- Works by sending requests to multiple Pods via a Service

■ Horizontal Pod Autoscaler (HPA)
- Used to handle increasing load
- Automatically increases/decreases Pod count
- Works by monitoring CPU/Memory metrics

■ Cluster Autoscaler
- Used when Pods cannot be scheduled due to lack of Nodes
- Automatically adds or removes Nodes
- Works with cloud Auto Scaling Groups (ASG)

■ Auto Scaling Group (ASG)

- Auto Scaling handles the management of Nodes.
- And Kubernetes handles the Pods running inside the Nodes.
- Used to manage Node instances
- Automatically adds/removes EC2 instances
- Ensures high availability of Nodes


---





---

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


## 🌍 How Kubernetes Came to the Market

Kubernetes came from Google’s internal system called Borg.
Google was running millions of containers for years and needed a powerful system to manage them automatically.

### 📜 The Story

📜 The Story of Kubernetes (Simple)

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



📅 Kubernetes Timeline

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

































































































































