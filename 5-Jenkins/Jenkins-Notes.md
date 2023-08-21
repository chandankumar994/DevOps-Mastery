# Jenkins
## About Jenkins.
Jenkins is an open source continuous integration/continuous delivery and deployment (CI/CD) automation software DevOps tool written in the Java programming language. It is used to implement CI/CD workflows, called pipelines.

Pipelines automate testing and reporting on isolated changes in a larger code base in real time and facilitates the integration of disparate branches of the code into a main branch. They also rapidly detect defects in a code base, build the software, automate testing of their builds, prepare the code base for deployment (delivery), and ultimately deploy code to containers and virtual machines, as well as bare metal and cloud servers. There are several commercial versions of Jenkins. This definition only describes the upstream open source project.

### History
Jenkins is a fork of a project called Hudson, which was trademarked by Oracle. Hudson was eventually donated to the Eclipse Foundation and is no longer under development. Jenkins development is now managed as an open source project under the governance of the CD Foundation, an organization within the Linux Foundation.

Continuous integration has evolved since its conception. Originally, a daily build was the standard. Now, the usual rule is for each team member to submit work, called a commit, on a daily (or more frequent) basis and for a build to be conducted with each significant change. When used properly, continuous integration provides various benefits, such as constant feedback on the status of the software. Because CI detects deficiencies early on in development, defects are typically smaller, less complex and easier to resolve.

### Jenkins and CI/CD
Over time, continuous delivery and deployment features have been added to Jenkins. Continuous delivery is the process of automating the building and packaging of code for eventual deployment to test, production staging, and production environments. Continuous deployment automates the final step of deploying the code to its final destination.

In both cases, automation reduces the number of errors that occur because the correct steps and best practices are encoded into Jenkins. Jenkins describes a desired state and the automation server ensures that that state is achieved. In addition, the velocity of releases can be increased since deployments are no longer bounded by personnel limitations, such as operator availability. Finally, Jenkins reduces stress on the development and operations team, by removing the need for middle of the night and weekend rollouts.

### Jenkins and microservices
The need for Jenkins becomes especially acute when deploying to a microservices architecture. Since one of the goals of microservices is to frequently update applications and services, the ability to do so cannot be bounded by release bandwidth. More and smaller services with faster update intervals can only be achieved by the type of automation Jenkins provides.

### Jenkins X
The Jenkins X project was formerly launched in 2018 with the goal of creating a modern, cloud native Jenkins. It is also a project under the guidance of the CD Foundation. Its architecture, technology and pipeline language are completely different from Jenkins. Jenkins X is designed for Kubernetes and uses it in its own implementation. Other cloud native technology that Jenkins X uses are Helm and Tekton.

### How Jenkins works
Jenkins runs as a server on a variety of platforms including Windows, MacOS, Unix variants and especially, Linux. It requires a Java 8 VM and above and can be run on the Oracle JRE or OpenJDK. Usually, Jenkins runs as a Java servlet within a Jetty application server. It can be run on other Java application servers such as Apache Tomcat. More recently, Jenkins has been adapted to run in a Docker container. There are read-only Jenkins images available in the Docker Hub online repository.

To operate Jenkins, pipelines are created. A pipeline is a series of steps the Jenkins server will take to perform the required tasks of the CI/CD process. These are stored in a plain text Jenkinsfile. The Jenkinsfile uses a curly bracket syntax that looks similar to JSON. Steps in the pipeline are declared as commands with parameters and encapsulated in curly brackets. The Jenkins server then reads the Jenkinsfile and executes its commands, pushing the code down the pipeline from committed source code to production runtime. A Jenkinsfile can be created through a GUI or by writing code directly.

### Plugins
A plugin is an enhancement to the Jenkins system. They help extend Jenkins capabilities and integrated Jenkins with other software. Plugins can be downloaded from the online Jenkins Plugin repository and loaded using the Jenkins Web UI or CLI. Currently, the Jenkins community claims over 1500 plugins available for a wide range of uses.

Plugins help to integrate other developer tools into the Jenkins environment, add new user interface elements to the Jenkins Web UI, help with administration of Jenkins, and enhance Jenkins for build and source code management. One of the more common uses of plugins is to provide integration points for CI/CD sources and destinations. These include software version control systems (SVCs) such as Git and Atlassian BitBucket, container runtime systems -- especially Docker, virtual machine hypervisors such as VMware vSphere, public cloud instances including Google Cloud Platform and Amazon AWS, and private cloud systems such as OpenStack. There are also plugins that assist in communicating with operating systems over FTP, CIFS, and SSH.

A plugin is written in Java. Plugins use their own set of Java Annotations and design patterns that define how the plugin is instantiated, extension points, the function of the plugin and the UI representation in the Jenkins Web UI. Plugin development also makes use of Maven deployment to Jenkins.

### Security
Jenkins security revolves around securing the server and the user. Server security is achieved in the same way any other server is secured. Access to where it resides, such as a VM or bare metal server, is configured to allow for the fewest number of processes to communicate with the server. This is accomplished through typical server OS and networking security features.

In addition, access to the server via the Jenkins UI is similarly limited to the fewest number of users using standard techniques such as multifactor authentication. This can be accomplished by using the user security features of the HTTP server in use for the UI.

Jenkins also supports security for its internal user database. These features are accessed via the Jenkins Web UI. Jenkins supports two security realms: the Security Realm and the Authorization Realm. The Security Realm allows an operator to decide who has access to Jenkins and the Authorization Realm determines what they can do with that access.

### Advantages and disadvantages
- As is the case with most software, there are pros and cons to Jenkins.  One of the advantages of Jenkins is that it can be extended using plugins. This makes Jenkins adaptable to changes in IT environments. Plugins also contribute to the flexibility of Jenkins, as does the rich scripting and declarative languages that allow for highly custom pipelines. Since Jenkins is highly unopinionated, it fits well into most environments, including complex hybrid and multi-cloud systems.

- Jenkins has been around much longer than other solutions in this space. This, plus its flexibility, has led to it being widely deployed. For this reason, Jenkins is well understood, with a broad knowledge base, extensive documentation, and abundant community resources. These resources make it easier to install, manage and troubleshoot Jenkins installation.

- Finally, Jenkins and its plugins are built on Java. Java is a proven enterprise development language with a broad ecosystem. This places Jenkins on a solid base that can be extended using common design patterns and frameworks.

- Jenkins is, of course, not perfect. While it is easy to install (with simple to follow directions), production Jenkins can be difficult to implement.  Developing production pipelines using Jenkinsfiles requires coding in either its declarative or scripting language. Complex pipelines, especially, can be difficult to code, debug and maintain.

- The open source system is also a single server architecture. This makes it easy to install but caps resources to those of a single computer, virtual machine or container. Jenkins does not allow for federation across servers resulting in performance issues. Lack of federation can also lead to a proliferation of independent Jenkins servers that are difficult to manage across a large enterprise.

- Jenkins relies on older Java architectures and technology, specifically servlets and Maven. Even the Jenkins Docker installation still requires that the Jenkins code and servlet middleware be packaged into a container together, maintaining its monolithic architecture. In addition, it is not designed to be implemented using newer Java technology such as Spring Boot or GraalVM.
