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


5. cloud-controller-manager (Optional)

- Integrates with cloud providers
- Manages:
  - Load balancers
  - Volumes
  - Nodes (AWS / Azure / GCP)


---

### 2️⃣ Worker Node


👉 Where applications actually run

🔹 Components of Worker Node

1. kubelet

- Agent running on each node
- Talks to API Server
- Ensures Pods are running

📌 kubelet = Pod manager of the node



2. Container Runtime

- Runs containers
- Examples:
  - Docker
  - containerd
  - CRI-O

3. kube-proxy

- Manages networking
- Enables service-to-pod communication
- Handles load balancing



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
- Used to manage Node instances
- Automatically adds/removes EC2 instances
- Ensures high availability of Nodes


---













---

###  Kubernetes vs Docker


| Docker             | Kubernetes         |
| ------------------ | ------------------ |
| Creates containers | Manages containers |
| Single host        | Multi-node cluster |
| Manual scaling     | Auto scaling       |
| No self-healing    | Self-healing       |

---

















































































































































