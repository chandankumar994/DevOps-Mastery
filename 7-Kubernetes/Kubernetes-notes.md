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

