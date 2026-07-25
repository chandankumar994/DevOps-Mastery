# Kubernetes Notes

### Kubernetes:
Kubernetes is a container management tool that supports multiple containerization tools.

### Provides:
- Scalability
- High availability
- Load Balancing
- Container deployment

### Notes:
- It schedule, runs and manage isolated containers which are running on Virtual/Physical/Cloud Machine.
- All top cloud providers supports Kubernetes.

### History:
- Google developed on internal system called `borg` (later name as `omega`) to deploy and manage thousand google application and services on their cluster (Cluster means - Group of containers).
- In 2014, Google introduced Kubernetes on an open-source platform written in `golang` and later donated to `CNCF` (CNCF - Cloud Native Computing Foundation).

### Some online platforms for K8s:
- Kubernetes playground
- play with k8s
- Play with kubernetes classroom

### Some offline tools for practice:
- Minikube
- Kubeadm

### Without Kubernetes:
- Containers can't communicate with each other

| Without Kubernetes | With Kubernetes |
| --- | --- |
| Containers can't communicate with each other | Orchestration (clustering on any number of containers running on different H/W |
| auto ccalling and load balancing was not possible | Auto scalling (Vertical and Horizontal) |
| Containers have to be managed carefully| Auto healing |
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
- We create a manifest file (.yaml/.yml).
- Apply this to the cluster (to the master) to bring it into the desired state.
- POD runs on nodes, which is controlled by master.

### Role of master node:
- Kubernetes cluster contains containersrunning on VM-Instance/Bare-Metal/Cloud-Instance/All-Mixedup.
- Kubernetes designates one or more of these as Master and all other as Workers.
- The Master is now going to run set of K8s process, therse process will ensure smooth functioning of the cluster. These processes are called `Control Plane` .
- It can be multi-master for high availability.
- Master runs Control Plane to run cluster smoothly.

### Components of Control-Plane (Master):
- Kube-Api-Server
- etcd
- Kube-schedular
- Controller-manager

#### 1- Kube-Api-server (for all communication):
- The API server interacts directly with the user (ie- we apply .yml or .json manifest to kube-api-server).
- This kube-api-server meant to scale automatically as per load.
- Kube-api-server is the front end of the control plane.

#### 2- ETCD:
- Stores metadata and state of the cluster.
- ETCD is a consistent and highly available store (key-value store)
- Source of truth for cluster state (info about the state of the cluster).
- **Features:**
  - **Fully Replicated:** The entire state id available on every node in the cluster.
  - **Secure:** Implements TLS with optional client certificate authentication.
  - **Fast:** Benchmarks at 10,000 writes per second.

#### 3- Kube-scheduler: (action)
- When a user requests the creation and management of Pods, Kube-scheduler is going to take action on the request.
- Handles POD creation and management.
- Kube-schedular match/assign any node to create and run PODs.
- A scheduler watches for newly created pods that have no node assigned for every pod, that the scheduler discovers, the scheduler becomes responsible for finding best node for that POD to run on.
- The scheduler gets the information for hardware configuration from the configuration files and schedules the PODs on nodes accordingly.

#### 4- Controller Manager:
- Make sure the actual state of the cluster matches the desired state.
- Two possible choices for controller manager:
  - If K8s on cloud, then it will be `cloud-controller-manager`.
  - if K8s on non-cloud, then it will be `kube-controller-manager`.
  - ##### Components on Master that runs controller:
    - **Node Controller:** Responsible for checking the cloud providers to determine if a node has beem dected on the cloud after it starts responding.
    - **Route Controller:** Responsible for setting up networks, routs on your cloud.
    - **Service Controller:** Responsible for load balancer on your cloud against service type load-balancer.
    - **Volume Controller:** Responsible for creating, attaching and mounting volume and intracting with the cloud providers to orchestrate volumes.

### Node:
Node is going to run 3 important piece of software/processes.

#### 1- Kubelet: 
- Agents running on the node.
- Listen the Kubernetes master (eg- POD creation request)
- Use port 10255
- Send success/fail response to master.

#### 2- Container Engine:
- Work with Kubelet
- Pulling image
- Start/Stop containers
- Exposing containers on ports specified in manifest file.

#### 3- Kube-Proxy:
- Assign IP to each POD.
- It is requested to assign an IP address to POds (dynamically).
- Kube-Proxy runs on each node and this make sure that each POD will get its own unique IP address.

### POD: (About POD)
- POD is the smallest unit of Kubernetes.
- A POD is a group of one or more containers that are deployed together on the same host.
- A cluster is a group of nodes.
- A cluster has at least one worker node and one master node.
- In Kubernetes, the control unit is the POD, not containers.
- Consists of one or more tightly coupled containers.
- PODs runs on the nodes, which is controlled by master.
- Kubernetes only know about the PODs (does not know about individual container)
- Cann't start container without a POD
- One POD usually contains one container.

#### Multi-container PODs:
- Share access to the memory space.
- Connect to eachother using localhost <container-port>.
- Share access to the same volume.
- Containers within the POD are deployed in an all or nothing manner.
- Entire POD is hosted on the same node (scheduler will decide about which node).

#### POD Limitations:
- No auto-healing or auto-scaling (by default).
- POD Crashes.

#### Kubernetes Objects:
- **Replica-set:** Scalling and healing.
- **Deployment:** Versioning and rollback.
- **Service:** Static IP (no temporary).
- **Volume:** No temperary storage.

#### Important:
- **kubectl:** Single cloud
- **kubeadm:** on premise
- **kubefed:** hybrid cloud
 
