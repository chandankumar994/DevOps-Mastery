# Docker Volume

### What is docker volume?
**Docker volume:** Isolated storage for containers, preserving data between container restarts, and enabling data sharing across containers.

### Architecture?

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/e49fdfd6-673d-4c2c-bb06-3c3b9f35b9b4)

### Why we use docker volume?
Because data will be available until container is running, thats why we attach docker volume for backup of data.

### How to create docker volume and attach with a docker container?
- **Create volume:**
  ```
  docker volume create --name <volume-name> --opt type=none --opt device=</path/on/host> --opt o=bind
  
  #Example:
  docker volume create --name django-app-volume --opt type=none --opt device=var/lib/django-app-volume --opt o=bind
  ```
- **List volumes:**
  ```
  docker volume ls
  ```
- Attach volume with container:
  ```
  docker run -d --mount source=django-app-volume,target=/data -p 8000:8000 django-app:latest

  #Note: /data - this is working directory of container.
  #Using below command you can go inside container and create any file that will be saved in source volume too.

  docker exec -it <containerID> bash
  ```


## For Lab: clone below repository:
```
git clone https://github.com/chandankumar994/django-todo-cicd.git
```
