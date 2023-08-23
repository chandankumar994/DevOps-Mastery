# Labels and Selectors

## Labels
- Labels are the machenism you use to organize the kubernetes objects.
- A labels is a key-value pair without any predefined meaning that can be attached to the ojbects.
- Labels are similar to tag in AWS as git when you use a name to quick reference.
- So you are free to choose labels as you need it to refer on environment which is used for dev or testing or production, refer a product group like- Department A, Department B.
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

## Node-Selectors:
- One usecase for selecting labels is to constrain the set of nodes onto whic a pod can schedule. ie- you can tell a pod to only be able to run on particular nodes.
- Generally such constraints are unnecessary as the schedular will automatically do a reasonable placement, but on certain circumstances we might need it.
- We can use lebels to tag nodes.
- You the nodes are tagged, you can use the label selectors to specify the pods run only of specific nodes.
- First we give label to the node.
- Then use node selector to the pod configuration.

#### Example: (node-selector.yml)
```
kind: Pod
apiVersion: v1
metadata:
  name: nodelabels
  labels:
    env: development
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Chandan; sleep 5 ; done"]
    nodeSelector:                                         
       hardware: t2-medium
```
- Create pod using above manifest:
  ```
  kubectl apply -f node-selector.yml

  kubectl get pods
  #o/p - pod will not be created because `t2-medium` label does not exist on any node, now you need to apply node label on node.
  ```
- Apply label on node
  ```
  kubectl get nodes
  #o/p - all node will be displayed

  kubectl label nodes <node-name> hardware=t2-medium

  #to check:
  kubectl describe pod nodelabels
  ```
