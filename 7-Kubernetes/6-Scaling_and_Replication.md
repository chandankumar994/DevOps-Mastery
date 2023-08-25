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
