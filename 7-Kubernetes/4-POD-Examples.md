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
- Run below command to create container
  ```
  kubectl apply -f pod1.yml
  ```
- Get pods
  ```
  kubectl get pods
  ```
- Describe pod
  ```
  kubectl describe pod <podname>
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
- Command to check IP (this will return pod IP), Note: IP will be same for all containers(c00,c001 both).
  ```
  kubectl exec testpod3 -c c00 -- hostname -i
  ```
- Command to go inside a container:
  ```
  kubectl exec testpod3 it -c c01 -- /bin/bash
  ```
- Now you can access everything inside the container
  ```
  docker ps
  ps -ef
  ```
---
### POD environment variables (env.yml):
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
- Create pod with using below command:
  ```
  kubectl apply -f env.yml
  kubectl get pods
  ```
- Go inside the pod
  ```
  kubectl exec <pod-name> -it -- /bin/bash
  ```
- Run below command to see environment variable values:
  ```
  env
  # this `env` command will print all environment variables

  echo $MYNAME
  # output- CHANDAN

  exit
  # using `exit` command you will be out form the pod.
  ```

---
### Pod with ports (portdemo.yml):
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
- Create pod with using below command:
  ```
  kubectl apply -f portdemo.yml
  kubectl get pods
  ```
- Get Pod information in details:
  ```
  kubectl get pods -o wide
  #you will get all the details about the POD including pod's IP
  ```
- Test service is running on given port or not
  ```
  curl <pod-ip>:80
  curl 10.90.33.40:80 

  # OutPut - it works
  ```

---
### Delete PODs
- delete single pod
  ```
  kubectl delete pod <pod-name>
  ```
- delete POD using manifest file:
  ```
  kubectl delete pod -f <file-name.yml>
  ```
---
### Labels and Selectors:
- Labels are the machenism you use to organize the kubernetes objects.
- A labels is a key-value pair without any predefined meaning that can be attached to the ojbects.
- Labels are similar to tag in AWS as git when you use a name to quick reference.
- So you are free to choose levels as you need it to refer on environment which is used for dev or testing or production, refer a product group like- Department A, Department B.
- Multiple label can be added to a single object.

#### Example of Labels (delhipod.yml)
```
kind: Pod
apiVersion: v1
metadata:
  name: delhipod
  labels:                                                   
    env: development
    class: pods
    purpose: testing
    team: dev
spec:
    containers:
       - name: cZ1
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Chandan; sleep 5 ; done"]
```
- Run the manifest file:
  ```
  kubectl apply -f delhipod.yml
  kubectl get pods
  ```
- How to select label
  ```
  kubectl get pods --show-labels
  kubectl get nodes --show-labels
  ```
- How to add level on existing pod
  ```
  kubectl level pods <pod-name> <key=value>
  kubectl level pods delhipod myname=chandan
  kubectl get pods --show-labels
  ```
- Now list pods matching a labels
  ```
  kubectl get pods -l env=development
  ```
- Give a list, where 'development' label is not present
  ```
  kubectl get pods -l env!=development
  ```
- Delete pod using label
  ```
  kubectl delete pod -l env!=development
  ```
- **Note:**
  - Unlike name/uid, level do not provide uniqueness, as in general, we can expect many object it carry the same label.
  - One labels are attached to an object, we would need filters to narrow down and these are called as label Selectors.
  - The api currently supports two types of selectors:
    - Equality based (where we use `=`, `!=`)
    - Set based (where `in`,`notin` and `exist`)
      ```
      kubectl get pods -l 'env in (production, dev)'
      kubectl get pods -l 'env notin (team1, team2)'
      ```
  - A label selector can be made of multiple requirement which are comma separated.
    ```
      kubectl get pods -l class=pods,team=dev
    ```
  
---
### Node-Selectors:






