# Pod Examples:

### Pre-requisites:
- An Ubuntu Machine with minimum 2 virtual CPUs.
- Login with root user using `sudo su`
- Now install docker `sudo apt update && apt -y install docker.io`
- install Kubectl
- install Minikube

---
### Basic pod demo (manifest file) pod1.yml.
```
kind: Pod                              
apiVersion: v1                     
metadata:                           
  name: testpod                  
spec:                                    
  containers:                      
    - name: c00                     
      image: ubuntu              
      command: ["/bin/bash", "-c", "while true; do echo Hello-Chandan; sleep 5 ; done"]
  restartPolicy: Never         # Defaults to Always
```
run below command to create container
```
kubectl apply -f pod1.yml
```
---
### Multi container Pod (pod2.xml)
```
kind: Pod
apiVersion: v1
metadata:
  name: testpod3
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello from Container-1; sleep 5 ; done"]
    - name: c01
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello from Container-2; sleep 5 ; done"]
```
Command to check IP:
```
kubectl exec testpod3 -c c00 -- hostname -i
```
---
### POD environment variables:
```
kind: Pod
apiVersion: v1
metadata:
  name: environments
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-CHANDAN; sleep 5 ; done"]
      env:                        # List of environment variables to be used inside the pod
      - name: MYNAME
        value: CHANDAN
```
---
### Pod with ports:
```
kind: Pod
apiVersion: v1
metadata:
  name: testpod4
spec:
  containers:
    - name: c00
      image: httpd
      ports:
       - containerPort: 80 
```
