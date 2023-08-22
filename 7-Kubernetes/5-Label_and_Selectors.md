# Labels and Selectors

## Labels
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

## Node-Selectors:
