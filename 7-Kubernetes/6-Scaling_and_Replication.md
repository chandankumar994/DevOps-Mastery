# Scaling and Replication:

Kubernetes was designed to orchestrate multiple containers and replication.
Need for multiple containers/replication helps us with these..

- **Relability:** By having multiple version of an application, you prevent problems one or more fails.

- **Load Balancing:** Having multiple versions of containers enables you to easy send traffic to different instance to revent overloading of a single instance or pod.

- **Scaling:** When load does become too much for the number of existing instance, Kubernetes enables you to easy scale up your application adding additional instances needed.

- **Rolling Update:** Update to a service bt replacing pods one by one.

### EXAMPLE OF REPLICATION CONTROLLER:
```
kind: ReplicationController               
apiVersion: v1
metadata:
  name: myreplica
spec:
  replicas: 2            
  selector:        
    myname: Chandan Kumar                             
  template:                
    metadata:
      name: testpod6
      labels:            
        myname: Chandan
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Chandan; sleep 5 ; done"]
```

