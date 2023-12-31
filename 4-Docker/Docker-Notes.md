# Docker

#### What is docker?
Docker is a platform for developing, shipping, and running applications using containerization for easy deployment and scalability.

#### Virtual machine Vs Docker Architecture?

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/37728611-ea2f-423d-98cd-5225e2096b4e)

#### What is container?
- A container is an isolated, lightweight package that includes everything needed to run an application, making it portable and consistent.
- A container don't have its own Kernal
- Container use the host's kernal (This is why containers are very light weight).

#### What is docker engine?
Docker Engine is the core component of Docker that manages containers, allowing for application deployment, execution, and scaling.

#### What are the components of docker engine?
- **dockerd:** Helps in background process to manage containers, which uses `containerd`. 
  - **containerd:** This comes under dockerd.
- **Docker CLI:** Docker CLI is the command-line interface for interacting with Docker.


#### Docker workflows?

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/08036f53-5f35-4471-a984-a8bf9a63a778)

#### What is docker file?
A Dockerfile is a text file containing instructions to build a Docker image, defining the application environment and dependencies.

#### What is docker image?
A Docker image is a static snapshot of an application and its dependencies, used to create and run containers.

#### What is docker container?
A Docker container is an isolated, executable environment that encapsulates an application and its dependencies for consistent deployment.

#### What is dockerhub?
Docker Hub is a cloud-based registry service that allows users to share, store, and download Docker images for easy access.

#### Some docker commands?
- **Remove images:** To remove Docker images using the prune command, you can use the following command:
  ```
  docker image prune
  ```
  This command will remove all the dangling (untagged) images, which are images that are not associated with any container. It helps to clean up unused images and free up disk space. Docker will prompt you to confirm the removal before actually deleting the images.
  
  If you want to bypass the confirmation prompt and forcefully remove the dangling images, you can use the following command:
  ```
  docker image prune -f
  ```
  Please be cautious while using the prune command, as it will remove unused images, and you won't be able to restore them unless you have a backup.

- Remove all stopped containers:
  ```
  docker container prune

  # to remove forcefully
  docker container prune -f
  ```
- Clean up unused data in your Docker environment:
  ```
  docker system prune
  ```
- Remove all images:
  ```
  docker image prune -a
  
  # forcefully
  docker image prune -a -f
  ```
