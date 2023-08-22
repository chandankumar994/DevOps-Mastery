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

#### 4- Controller Manager:
- Manke sure actual state of cluster match the desigred state.
- Two possible choices for controll manager:
  - If K8s on cloud, then it will be `cloud-controller-manager`.
  - if K8s on none-cloud, then it will be `kube-conrtoller-manager`.
  - ##### Conponents on Master that runs controller:
    - Node Controller: Responsible for checking the cloud providers to determine if a node has beem dected on the cloud after it steps responding.
    - Rout Controller: Responsible for setting up networks, routs on your cloud.
    - Service Controller: Responsible for load balancer on your cloud against service type load-balancer.
    - Volume Controller: Respondible for creating attaching and mounting volume and intracting with the cloud providers to orchestrate volume.

### Node:
Node is going to run 3 important piece of software/processes.

#### 1- Kublet: 
- Agents running on the node.
- Listen the kubernetes master (eg- POD creation request)
- Use port 10255
- Send success/fail response to master.

#### 2- Container Engine:
- Work with kublet
- Pulling image
- Start/Stop containers
- Exposing containers on ports specified in manifest file.

#### 3- Kube-Proxy:
- Assign IP to each POD.
- It requested to assign an IP address to POds (dynamically).
- Kube-proxy runs on each node and this make sure that each POD will get its own unique IP address.

### POD: (About POD)
- POD is a smallest unit of kubernetes.
- POD is a group of one or more containers, that are deployed together on the same host.
- A cluster is a goup of nodes.
- A cluster has alleast one worker node and one master node.
- In Kubernetes the control-unit is the POD not containers.
- Consist of one or more tightely coupled containers.
- PODs runs on the nodes which is controlled by master.
- Kubernetes only know about the PODs (does not know about individual container)
- Cann't start container without a POD
- One POD usually contains one container.

#### Multi container PODs:
- Share access to the memory space.
- Connect to eachother using localhost <container-port>.
- Sahre access to same volume.
- Contains within the POD are deployed in an all or nothing manner.
- Entire POD is hosted on the same node (shcedular will decide about which node).

#### POD Limitations:
- No auto-healing ot auto-scalling (by default).
- POD Crashes.

#### Kubernetes Objects:
- **Replica-set:** Scalling and healing.
- **Deployment:** Versioning and rollback.
- **Service:** Static IP (No temperary).
- **Volume:** No temperary storage.

#### Important:
**kubectl:** Single cloud
**kubeadm:** on premise
**kubefed:** hu=ybrid cloud
 
