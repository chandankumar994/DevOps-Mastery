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
- Command to check os version inside pod:
  ```
  kubectl exec <pod-name>  -- cat /etc/os-release
  kubectl exec nginx-deployment-7fb96c846b-n9zwm  -- cat /etc/os-release
  kubectl exec testpod -- cat /etc/os-release
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
