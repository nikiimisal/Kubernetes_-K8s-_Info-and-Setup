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

###  Kubernetes vs Docker


| Docker             | Kubernetes         |
| ------------------ | ------------------ |
| Creates containers | Manages containers |
| Single host        | Multi-node cluster |
| Manual scaling     | Auto scaling       |
| No self-healing    | Self-healing       |

---

















































































































































