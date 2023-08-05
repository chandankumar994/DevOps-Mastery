## Lab-2

### Create a directory and Dockerfile in this dir and prepare a container using the Dockerfile to run java-app into it.
Steps to perform this lab:
- create a directory name `java-app` using `mkdir java-app`.
- Go to this directory using `cd java-app`.
- Create a `Hello.java` file in this direcrory.

```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Guys");
    }
}
```
- Now create Dockerfile in this directory using any editor eg: `sudo nano Dockerfile`, put below code into it and save it.

```
# Getting Base image
FROM openjdk:11

# Work directory were code will be kept
WORKDIR app/

# copy app to current dir of container
COPY Hello.java .

# Compile java code
RUN javac Hello.java

# Run java code
CMD ["java","Hello"]
```

- Now create an image of above Dockerfile using below command.
```
docker build . -t java-app
```
- Now check images using `docker images` command
- Now run this image t create a container.
```
docker run java-app
```
- you will get an output on the screen written in java app.
