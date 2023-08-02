# Interview Question and Answers (Which I faced in interview).
## Role - DevOps Engineer 

### 🚀Section-0 (Very Important, because this question will decide the interview side)🚀

#### Question-1: Tell me about yourself and explain your day to day activities ?
#### Question-2: Tell me about yourself and which project you are working and what are your role and responsibilities ?
Ans: **Note** Please be very carefull while answering this question, because this question's answer will decide the interview's future..


### 🚀Section-1 (Linux)🚀

#### Question-1: Explain file system architecture of Linux OS ?
**Ans:** The file system architecture of a Linux operating system is a hierarchical structure that organizes and manages files, directories, and other data on storage devices such as hard drives and SSDs. The architecture is based on the Filesystem Hierarchy Standard (FHS), which defines the layout and naming conventions for different directories in the system. Below is an explanation of the key components of the file system architecture in Linux:

* **Root (/):** The root directory is the top-level directory in the file system hierarchy, denoted by a forward slash (/). All other directories and files are located beneath the root directory. The root directory is essential for the system's functioning, and it contains directories like /bin, /etc, /home, /usr, and others.

* **/bin:** This directory contains essential binary executables that are required for basic system operations. These binaries are essential for system maintenance and booting, and they are available to all users.

* **/boot:** The /boot directory contains files related to the boot process, including the Linux kernel, initial ramdisk (initrd), boot configuration files, and bootloader (e.g., GRUB).

* **/dev:** The /dev directory contains device files that represent physical and virtual devices on the system. These files allow users and applications to interact with hardware devices.

* **/etc:** This directory contains system-wide configuration files. Important configuration files for various applications, services, and system settings are stored here.

* **/home:** Each user on the system typically has their own subdirectory under /home, where they can store their personal files and settings. For example, the user "john" might have their home directory at /home/john.

* **/lib and /lib64:** These directories contain shared libraries (also known as dynamic link libraries) that are required by various programs on the system.

* **/media and /mnt:** These directories are used to temporarily mount removable media devices (e.g., USB drives) and other file systems manually.

* **/opt:** The /opt directory is used for installing optional software packages. It is typically used by software that is not part of the core system distribution.

* **/proc:** The /proc directory is a virtual file system that provides information about processes and system status. It contains dynamic kernel data that can be accessed as regular files.

* **/root:** This is the home directory for the root user, the administrative user with full privileges on the system.

* **/sbin:** Similar to /bin, the /sbin directory contains essential system binaries, but these are meant for system administrators and require elevated privileges.

* **/tmp:** The /tmp directory is used to store temporary files that may be required by various processes. The contents of this directory are usually cleared on system boot.

* **/usr:** The /usr directory contains user-related files and programs that are not essential for the system's basic operation. It includes user binaries, libraries, documentation, and more.

* **/var:** This directory contains variable data that can change frequently during system operation. It includes log files, spool files (e.g., for printing and mail), temporary files, and more.

#### Question-2: Explain file and folder permission architecture in Linux?
Ans: ![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/2a44cad8-2954-46c6-9d1e-7c4aa3fc2fe1)


#### Question-3: Where you will check event log in Linux OS?
Ans: Use `journalctl` command to see the current event log and We can also check the logs under `var/log/` dir.
#### Question-4: Disk is full of logs and you deleted the logs around 10 Gib from log folder, and machine is still showing not enough space, how you will release the memory of disk without rebooting the machine?
Ans: use `sudo sync` command, for example:
```
sudo sync; echo 1 | sudo tee /proc/sys/vm/drop_caches
```
#### Question-5: How to replace word "2022" with "2023" into all file (including sub folders) using shell scripting ?
Ans: using sed command this can be acieved.
```
sed -i "s/2022/2023/g" *.*
# Note: 2022 is old value and 2023 is new value (which we want to replace with)
```
```
sed -i "s/Sunday/Monday/g" *.*
Note: Sunday is old value and Monday is new value (which we want to replace with)
```
#### Question-6: How to remove files and its directory ?
Ans:
```
rm -rf <dir-path>
```
#### Question-7: What is `grep` command, and how to use it ?
Ans: `grep` stands for Global Regular Expression Print, basically we use `grep` command to perform search operation in Linux.
Example: 
```
# Below is the command to search those lines which contains ERROR.
grep 'ERROR' file1.txt
```
#### Question-8: What is `find` command, and how to use it ?
Ans: `find` command also used to perform search operation in linux.
Example:
```
find <expression> <path>
find *.log /var/log
find *.txt .
```

#### Question-9: What is use of `pipe` command, and how to use it ?
Ans: The pipe command, often represented by the vertical bar symbol |, is a powerful feature in command-line interfaces and shell scripting. It allows you to take the output of one command and use it as the input to another command. This feature enables you to chain commands together, creating powerful and flexible data processing pipelines.

The basic syntax of using the pipe command is:
```
command1 | command2
cat file1.txt | sort | head -4
```
```
cat file1.txt | grep "Apple"
```

#### Question-10: What is the difference between `inode` and `ProcessID` ?
**Ans:** In Linux, an `inode` is a data structure that stores metadata about a file, such as ownership, permissions, and disk block locations. A process ID (PID) is a unique identifier assigned to each running process. 
Inodes represent files on the filesystem, while PIDs identify active processes in the operating system.

#### Question-11: How to check process-id in Linux ?
**Ans:** using `top` and `ps -ef` command.
```
top

ps -ef | grep nginx    # it will only show the nginx process
```
#### Question-12: What is swap space in linux ?
**Ans:** Swap space in Linux is a designated area on the hard drive used as virtual memory extension when physical RAM is insufficient. It allows the OS to move inactive data from RAM, freeing up memory for active processes.

#### Question-13: What is the command to check which service is running on which port ?
**Ans:** 
```
# To check all running applications
netstat -ano

# To check which service is running on 8080 port
netstat -ano | grep 8080
```
#### Question-13: What is `lsof` command ?
**Ans:** This `lsof` command will return the list of open files on the system.





### 🚀Section-2 (GIT - SCM tool)🚀

#### Question-1: What is git ?
**Ans:** GIT is a distributed version control system.

#### Question-2: What is difference between GIT and GitHub ?
**Ans:** Git is a version control system that allows developers to track changes in their code. GitHub is a web-based hosting service for git repositories. In simple terms, you can use git without Github, but you cannot use GitHub without Git.

#### Question-3: What is difference between `git fetch` and `git pull` ?
**Ans:** Git Fetch is the command that tells the local repository that there are changes available in the remote repository without bringing the changes into the local repository. Git Pull on the other hand brings the copy of the remote directory changes into the local repository

#### Question-4: What is `git stash` command ?
**Ans:** The git stash command takes your uncommitted changes (both staged and unstaged), saves them away for later use, and then reverts them from your working copy.

#### Question-5: What is `git rebase` command and when we use it ?
**Ans:** Rebasing is the process of moving or combining a sequence of commits to a new base commit. Rebasing is most useful and easily visualized in the context of a feature branching workflow.

#### Question-6: What is difference `git fork` and `git clone` ? 
**Ans:** A fork creates a completely independent copy of Git repository. In contrast to a fork, a Git clone creates a linked copy that will continue to synchronize with the target repository.

#### Question-7: What is `git revert` command ? 
**Ans:** The git revert command is a forward-moving undo operation that offers a safe method of undoing changes. Instead of deleting or orphaning commits in the commit history, a revert will create a new commit that inverses the changes specified.






### 🚀Section-3 (CI/CD - Build and releases)🚀
#### Question-1: Explain the build pipeline stages which you have worked or working on ?
**Ans:** 
#### Question-2: What is the "POM.xml" file in java based appliction, and what are the important information a devops engineer need from POM.xml to build this java project ?
**Ans:** The "POM.xml" is a Project Object Model file used in Java-based applications with Maven. DevOps engineers require it for project dependencies, build configurations, and version management during the build process.

#### Question-3: How to build dot net based application using pipeline ?
**Ans:** Use a CI/CD pipeline tool like Azure DevOps or Jenkins. Define pipeline stages for build, test, and deploy. Configure triggers and agent settings to automate the process. **Note:** We use `MS build` tool to build dot net based projects.

#### Question-4: Why we use "Maven clean build" in the build pipeline ?
**Ans:** Ensures a fresh build by cleaning previous artifacts.

#### Question-5: What is the difference between `maven clean build` and `maven clean install` ?
**Ans:** 
- **Maven clean build:** Compiles and packages the project.
- **Maven clean install:** Compiles, packages, and installs the project's artifact into the local Maven repository.

#### Question-6: What is multi-branching pipeline ?
**Ans:** A CI/CD pipeline that automates building, testing, and deploying code from multiple branches simultaneously.

#### Question-7: Explain about branching technique ?
**Ans:** Branching is a version control technique where developers create isolated copies of the codebase to work on new features or fixes independently. It helps manage changes and reduce conflicts.
I have used Sprint wise, DEV, QA, MAIN Branches in my previous projects.


### 🚀Section-4 (Cloud AWS/Azure)🚀
#### Question-1: What are the services you have worked in AWS ?
**Ans:** IAM, EC2, VPC, S3, ECS, Cloud Watch, Lambda, AWS RDS, AWS SNS ect.

#### Question-2: How you will retrive password if password is lost for any virtual machine ?
**Ans:** If the password is lost for a virtual machine, you can typically reset it using the cloud provider's management console or API by providing appropriate credentials or using the key pair method.

#### Question-3: How you will create 3 tier architecture application, what all components you will use (Draw the architecture) ?
**Ans:** Creating a 3-tier architecture application involves dividing the application into three layers: presentation, application, and data.

- **Presentation Layer:** User interacts with the front-end (web or mobile app).
**Components:** Web or Mobile Application, User Interface (UI).

- **Application Layer:** Business logic and processing occur here.
**Components:** Load Balancer, Application Servers (e.g., Node.js, Java, .NET), API Gateway.

- **Data Layer:** Handles data storage and retrieval.
**Components:** Database (e.g., RDS, DynamoDB), Cache (e.g., Redis), File Storage (e.g., S3).

The Load Balancer distributes incoming traffic among Application Servers, which interact with the Database and Cache for data management. The UI layer communicates with the Application Layer via APIs provided by the API Gateway.

Note: The actual architecture may vary based on specific requirements and technologies used.


#### Question-4: How many types of EC2 instances we are having based on costs ?
**Ans:** EC2 instances have three cost-based types: 
- **On-Demand** (pay-as-you-go), 
- **Reserved** (upfront payment with discounted hourly rates),
- **Spot** (bid-based pricing, fluctuates with demand and supply).

#### Question-5: What is the difference between 'on-demand' and 'reserved' instance ?
**Ans:** On-demand instances are pay-as-you-go, billed hourly with no upfront commitment. Reserved instances require an upfront payment for a significant discount on the hourly rate over a 1 or 3-year term.

#### Question-6: What is load balancer and why we use it ?
**Ans:** A load balancer distributes incoming network traffic across multiple servers to ensure high availability, fault tolerance, and optimal resource utilization. It enhances application performance, mitigates downtime, and scales efficiently as traffic fluctuates, ensuring a seamless user experience.

#### Question-7: When we use application and when we use network load balancer ?
**Ans:** Use Application Load Balancer (ALB) for HTTP/HTTPS applications and advanced routing. 
Use Network Load Balancer (NLB) for low-latency, TCP/UDP-based services

#### Question-8: What is horizental and vertical scaling in cloud computing ?
**Ans:** The primary difference between horizontal scaling and vertical scaling is that horizontal scaling involves adding more machines or nodes to a system, while vertical scaling involves adding more power (CPU, RAM, storage, etc.) to an existing machine

#### Question-9: Which loadbalancer is suitable for 'Live-BroadCasting' ?
**Ans:** Network load balancer.

#### Question-10: What is the difference between TCP and UDP protocol ?
**Ans:** 
- **TCP (Transmission Control Protocol)** is a connection-oriented protocol that ensures reliable data delivery through acknowledgment and retransmission, suitable for applications requiring data integrity. 

- **UDP (User Datagram Protocol)** is connectionless, providing faster but unreliable data transmission, ideal for real-time applications where speed is prioritized over data integrity.

#### Question-11: What is VPC ?
**Ans:** VPC (Virtual Private Cloud) is a virtual network within any public cloud that enables users to launch resources securely and isolate them from the public internet.

#### Question-12: What is the difference between Internet-Gateway and Nat-Gateway ?
**Ans:** 
- An **Internet Gateway** allows communication between a VPC and the public internet.
- **NAT Gateway enables** private resources within a VPC to access the internet.

#### Question-13: If any server is hosted on private subnet, then how this server will communicate with external internet ?
**Ans:** Through a NAT Gateway or a VPN connection.

#### Question-14: What is Cloud-Watch service and why we use it ?
**Ans:** CloudWatch is an AWS monitoring service that collects and tracks metrics, logs, and events for resources, helping to monitor and troubleshoot applications and infrastructure.

#### Question-15: What is RDS service ?
**Ans:** RDS (Relational Database Service) is an AWS service providing managed database solutions for various relational database engines.

#### Question-16: How to configure SNS (Simple Notification Service) ?
**Ans:** To configure SNS, create a topic, add subscribers, set access policies, and publish messages to the topic via the AWS Management Console or API, enabling scalable, flexible notification delivery.

#### Question-17: 
**Ans:**

#### Question-18: 
**Ans:**

#### Question-19: 
**Ans:**

#### Question-20: 
**Ans:**

#### Question-21: 
**Ans:**

#### Question-22: 
**Ans:**

#### Question-23: 
**Ans:**

#### Question-24: 
**Ans:**



### 🚀Section-5 (Azure DevOps)🚀
#### Question-1: How to add multiple user in Azure DevOps?
Ans: We can add multiple users in the azure devops from `Organization settings` -> `User` -> `add Users` options.
![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/97c313e3-c52e-44dd-9c82-00874460c04a)

#### Question-2: How to migrate items from jira to Azure DevOps?
Ans: 
#### Question-3: How to configure subscription in Azure DevOps?
Ans: Under `Billing` section of `Organization Settings` in azure DevOps.
![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/b0fe0129-24b8-4e43-a50f-2be194cbc767)


#### Question-4: Adding users in Azure DevOps, does it cost ?
Ans: Yes, till than 5 users it is free, but beond this it will cost.

#### Question-5: What is the difference between self-hosted and microsoft-hosted agents in Azure DevOps?
Ans: Self-hosted agents run on your infrastructure, Microsoft-hosted agents run on Azure infrastructure for Azure DevOps pipeline execution.

#### Question-6: How you will manage 100 projects in Azure DevOps?
Ans: 
- Use appropriate naming conventions and organization schemes for clarity.
- Utilize Azure DevOps security features to control access.
- Create project templates for consistency.
- Leverage Azure DevOps boards and backlogs to prioritize and track progress.
- Group related projects into Azure DevOps Organizations or collections.
- Set up appropriate pipelines and releases to automate workflows.
- Regularly review and optimize projects for efficiency.
- Train teams on best practices and tools.

#### Question-7: What are the best practices to use Azure DevOps?
Ans: Best practices for using Azure DevOps include:
- Organize projects with clear naming conventions.
- Use version control for code.
- Create CI/CD pipelines for automated builds and deployments.
- Set up work tracking to manage tasks.
- Implement security measures.
- Regularly review and optimize processes.
- Train teams effectively.

#### Question-8: How to set permission in Azure DevOps?
Ans: In Azure DevOps, set permissions through "Security" settings at the organization, project, or repository levels to control access.

#### Question-9: What is the difference between the roles like Basic, Basic + Test Plan, Stackeholder and Visual Studio ?
Ans: 
- **Basic:** Allows code, work item, and pipeline access.
- **Basic + Test Plan**: Adds Test Plan features for testing.
- **Stakeholder:** Limited access to work items and feedback.
- **Visual Studio:** Full access to Azure DevOps features for Visual Studio subscribers.
- 
#### Question-10: How you manage thousand (1000) pipelines in Azure DevOps ?
Ans: To manage thousands of pipelines in Azure DevOps, use proper naming conventions, tags, and folder structures for organization and efficiency.

#### Question-11: Have you ever build .Net, Java, and Python based project?
Ans: Yes, I have used To build a .Net project, .NET CLI or Visual Studio. For Java, used Maven or Gradle. For Python, used pip or setup.py, typically with virtual environments for isolation.

#### Question-12: How to migrate tasks and pipelines from Jenkins to Azure DevOps?
Ans: To migrate tasks and pipelines from Jenkins to Azure DevOps, recreate them in Azure DevOps using YAML pipelines or the visual editor, and adapt Jenkins-specific tasks to equivalent Azure DevOps tasks.

#### Question-13: What are the tool stack you are using in your project?
Ans: Below is the list of tools.
- Version Control: Git, Subversion
- Continuous Integration (CI): Jenkins, Azure DevOps, GitLab CI/CD, CircleCI
- Configuration Management: Ansible, Puppet, Chef
- Containerization: Docker, Kubernetes
- Infrastructure as Code (IaC): Terraform, CloudFormation
- Monitoring and Logging: Prometheus, Grafana, ELK Stack (Elasticsearch, Logstash, Kibana)
- Cloud Platforms: AWS, Azure, Google Cloud Platform
- Collaboration & Communication: Slack, Microsoft Teams
- Issue & Project Tracking: Jira, Trello
- Automated Testing: Selenium, JUnit, NUnit
- Deployment: Helm, Octopus Deploy
- Code Quality: SonarQube, ESLint, Checkstyle
- Artifact Repository: Nexus, JFrog Artifactory

#### Question-14: What tool you use to manage the database ?
Ans: SQL Server Management Studio (SSMS) - for Microsoft SQL Server.

#### Question-15: Where you store your artifacts?
Ans: Nexus and JFrog Artifactory.

#### Question-16: In which language your front-end application is prepared  and how you are building it in the pipeline?
Ans: Our front end application is prepared in dot net core and we are using `.NET Core CLI` and `MS build` to build the project using pipeline.
our middle ware is prepared using java so we are using `Maven` to build it using pipeline.

#### Question-1: 
Ans:
#### Question-1: 
Ans:
#### Question-1: 
**Ans:**



### 🚀Section-6 (Jenkins)🚀
#### Question-1: What is Jenkins?
**Ans:**

#### Question-2: How to backup Jenkins server with existing data and pipeline ?
**Ans:**

#### Question-3: What are agents in jenkins?
**Ans:**

#### Question-4: Why we use agent command in jenkins file ?
**Ans:**

#### Question-5: What is the difference between environment variable and parameter in Jenkins ?
**Ans:**

#### Question-6: What is the difference between pre-defined and user-defined variables in Jenkins ?
**Ans:**

#### Question-7: Why we use `test` and `choice` parameter in jenkins ?
**Ans:**

#### Question-8: What is `section` argument in Jenkins ?
**Ans:**

#### Question-9: 
**Ans:**

### 🚀Section-7 (Ansible)🚀

### 🚀Section-8 (terraform)🚀
#### Question-1: How to provision infrastructure for any subscription within azure using terraform?
**Ans:** 

#### Question-2: What is state file locking in terraform ?
**Ans:** 

#### Question-3: How to create ECS and RDS clusted using terraform ?
**Ans:**

#### Question-4: 
**Ans:**

#### Question-5: 
**Ans:**

#### Question-6: 
**Ans:**

### 🚀Section-9 (Docker)🚀
#### Question-1: How you will optimize docker image size, which is around 10 GB ?
Ans: 
#### Question-2: What is docker multi-stage build any why we use it ?
Ans: 

### 🚀Section-10 (Kubernetes)🚀

### 🚀Section-11 (Grafana and Prometheous)🚀



### 🚀Section-12 (Scenario based Question)🚀
#### Question-1: Is there any critical issue where you play a major role to fix that issue ?
Ans: 

