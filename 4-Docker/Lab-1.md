## Docker Lab-1

### Create an Docker instance on AWS portal and install docker on it.

- Step-1: Create Linux (Ubuntu) instance on AWS portal using AWS-Console.
- Install docker on it.
```
#run below command to install docker on Ubuntu machine:

sudo apt-get update
sudo apt install docker.io

```

- check docker version after successfull installation:
```
docker --version
```

- run `docker ps` command to check running containers.
- Incase getting permission error then add 'ubuntu` user into `docker` group.
```
sudo usermod -aG docker $USER

# now run below command
sudo systemctl restart docker

# Now check
docker ps

# Still not resolved then reboot the instance.
sudo reboot
```

### Scenario: Pull mysql latest image from docker-hub and run it as container.
- Pull `mysql` docker image.
```
docker pull mysql:latest
# where mysql:latest is the image name which is on docker-hub.

```
- Now check available images on machine.
```
docker images
```

- Now create container using the above image.
```
docker run mysql:latest

# this will required environment variable to setup so above command will throw an error, to fix this run below command:
docker run -d -e MYSQL_ROOT_PASSWORD=test@123 mysql:latest
```
- Now run `docker ps` this will show you all running containers.
- Go inside the running container
```
docker exec -it <containerID> sh

# -it - this is intractive Terminal
# sh - this is for shell access
```
- Now you are inside the shell run below command to access mysql DB.
```
mysql -u root -p test@123
```
- Now you can run mysql queries on this terminal.
- Type `exit` and press `enter` button to exit from terminal
- Agian type `exit` and press `enter` button to be exited from container.



