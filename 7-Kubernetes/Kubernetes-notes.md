# Kubernetes Notes

### Kubernetes:
Kubernetes is a container management tool, supports multiple containerization tools.

### Provides:
- Scalibility
- High availability
- Load Balancing
- Container deployment

### Notes:
- It schedule, runs and manage isolated containers which are running on Virtual/Physical/Cloud Machine.
- All top cloud providers supports Kubernetes.

### History:
- Google developed on internal system called `borg` (later name as `omega`) to deploy and manage thousand google application and services on their cluster (Cluster means - Group of containers).
- In 2014 Google introduces Kubernetes on opensource platform written in `golang` and later donated to `CNCF` (CNCF - Cloud Native Computing Foundation).

### Some online platform for K8s:
- Kubernetes playground
- play with k8s
- Play with kubernetes classroom

### Some offline tools for practice:
- Minikube
- Kubeadm

### Without Kubernetes:
- Container cann't communicate with each other

| Without Kubernetes | With Kubernetes |
| --- | --- |
| Container cann't communicate with each other | Orcharestration (clustering on any numbers of containers running on different H/W |
| auto ccalling and load balancing was not possible | Auto scalling (Vertical and Horizontal) |
| Containers has be managed carefully| Auto healing |
| | Load Balancing |
| | Platform Independent (cloud/virtual/physical |
| | Supports hybrid cloud |
| | Fault tolerance (node/pod failure) |
| | Rollback (Going back to previous version) |
| | Health monitoring of containers |
| | Batch execution (Onetime/sequential/Parallel) |

### Architecture type:
- 1 Master 1 node
- 1 Master Multi node
- Multi Master and Multi node

**Note**: Generally we create 1 container in one POD, but we can create multiple containers too.

### Flow:
| Cluster ->| Nodes ->| Container -> | Application/Microservice|
| --- | --- | --- | ---|

### Architecture:

![Kubernetes-Architecture](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/66f53144-3d35-41ee-8709-db15184741a0)


### Working with Kubernetes:
- We create manifast file (.yaml/.yml).
- apply this to cluster (to master) to bring bring into desigred state.
- POD runs on nodes, which is controlled by smaster.

### Role of master node:
- Kubernetes cluster contains containersrunning on VM-Instance/Bare-Metal/Cloud-Instance/All-Mixedup.
- Kubernetes designated one or more of these as Master and all other as Workers.
- The Master is now going to run set of K8s process, therse process will ensure smooth functioning of cluster, these processes are called `Control Plane` .
- It can be multi-master for high availability.
- Master runs Controll-Plane to run cluster smoothly.

### Components of Control-Plane (Master):
- Kubw-Api-Server
- etcd
- Kube-schedular
- Controller-manager

#### 1- Kube-Api-server (for all communication):
- The Api-server intract directly with user (ie- we apply .yml or .json manifast to kube-api-server).
- This kube-api-server meant to scale automatically as per load.
- Kube-api-server is front end of the control-plane.

#### 2- ETCD:
- Store metadata and states of cluster.
- ETCD is consistent and high-available store (key value store)
- Source of touch for cluster state (info about state of cluster).
- **Features:**
  - **Fully Replicated:** The entire state id available on every node in the cluster.
  - **Secure:** Implement automates TLS with optional client certificate authentication.
  - **Fast:** Benchmark at 10,000 writes per second.

#### 3- Kube-schedular: (action)
- When user make request for creation and management of Pods, Kube-schedular is going to take action on the request.
- Handle POD creation and management.
- Kube-schedular match/assign any node to create and run PODs.
- A schedular watches for newly created pods that have no node assigned for every pod, that the schedular discovers, the schedular become responsible for finding best node for that POD to run on.
- Schedular gets the information for hardware configuration from the fonfiguration files and schedules the PODs on nodes accordingly.

#### 4- Control Manager:
- 





