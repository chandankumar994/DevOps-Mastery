# Scaling and Replication:

Kubernetes was designed to orchestrate multiple containers and replication.
Need for multiple containers/replication helps us with these..

- **Relability:** By having multiple version of an application, you prevent problems one or more fails.

- **Load Balancing:** Having multiple versions of containers enables you to easy send traffic to different instance to revent overloading of a single instance or pod.

- **Scaling:** When load does become too much for the number of existing instance, Kubernetes enables you to easy scale up your application adding additional instances needed.

- **Rolling Update:** Update to a service bt replacing pods one by one.


## Replication Controller:
- A replication controller is an object that enables you to easlyy create multiple pods, then make sure that nymber of pods always exist.
- If a pod created using replication controller (RC) will be automatically replaced if they does crashed, failed or terminated.
- RC is recommended if you just want to make sure 1 pos is always running, even after system reboots.
- You can run RC with 1 replica and the RC will make sure the pod is always running.

### Example of Replication Controller (RC) ChandanRC.yml:
```
kind: ReplicationController               
apiVersion: v1
metadata:
  name: myreplica
spec:
  replicas: 2            
  selector:        
    myname: ChandanRC                            
  template:                
    metadata:
      name: testpodRC
      labels:            
        myname: ChandanRC
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-ChandanRC; sleep 5 ; done"]
```
- Command Run the manifest:
  ```
  kubectl apply -f ChandanRC.yml
  ```
- Command to check replication Controller:
  ```
  kubectl get rc
  ```
- Describe:
  ```
  kybectl describe rc <replicaname>
  kybectl describe rc myreplica
  ```
- Note: if you delete any pod RC will automatically create new pod.
  ```
  kubectl get pods
  kubectl delete <podname>
  # new pod will be created automatically after its deletion.
  ```
- Scale up replicas using command:
  ```
  kubectl scale --replicas=4 rc -l myname=ChandanRC
  
  #to check:
  kubectl get rc
  
  kubectl get pods
  #O/P - 4 pod will be in output.
  ```
- Scale down replicas using command:
  ```
  kubectl scale --replicas=1 rc -l myname=ChandanRC
  #to check:
  kubectl get rc

  kubectl get pods --show-labels
  ```
- Delele pods:
  ```
  kubectl delete -f ChandanRC.yml
  ```


## Replica Set: (advance version of Replication Controller)
- Replicaset is a next generation replication controller.
- The replication controller only supports `equality-based` selector whereas the replica set supports `set based` selector. ie filtering according to set of values.
- Replicaset rather than the replication controller is used by other objects like deployment.

### Example of Replicaset (RC) myrs.yml:
```
kind: ReplicaSet                                    
apiVersion: apps/v1                            
metadata:
  name: myrs
spec:
  replicas: 2  
  selector:                  
    matchExpressions:                             # these must match the labels
      - {key: myname, operator: In, values: [Chandan, Anand, Harshita]}
      - {key: env, operator: NotIn, values: [production]}
  template:      
    metadata:
      name: testpod7
      labels:              
        myname: Chandan
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Chandan Kumar; sleep 5 ; done"]
```
- Command Run the manifest:
  ```
  kubectl apply -f myrs.yml
  ```

---
## Deployment (deployment.yml)
Example: kind must be "Deployment".
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
- to execute:
  ```
  kubectl apply -f deployment.yml
  ```
- To check deployment:
  ```
  kubectl get deploy
  ```
  ![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/0fd94afc-bc3b-4509-bccf-9ecaf3e2d5ad)

### Reasons of deployment failure:
- Insufficient quota.
- Readiness probe failure
- Image Pull error
- Insufficient permission
- Limit renges
- Application runtime mis-configuration.
