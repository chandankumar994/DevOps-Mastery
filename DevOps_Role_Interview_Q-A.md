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
Ans: 
#### Question-3: Where you will check event log in Linux OS?
Ans: Use `journalctl` command to see the current event log and We can also check the logs under `var/log/` dir.
#### Question-4: Disk is full of logs and you deleted the logs from log folder, then why machine is still showing not enough space, how you will release the memory of disk without rebooting the machine?
Ans: 
#### Question-5: How to replace word "2022" with "2023" into all file (including sub folders) using shell scripting ?
Ans: using sed command this can be acieved.
```
sed -i "s/2022/2023/g" *.*
# Note: 2022 is old value and 2023 is new value (which we wanr=t to replace with)
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
Ans: `grep` stands for Global Regular Expression Print, basically we use 'grep' command to perform operation in Linux.
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
finf *.txt .
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
**Ans:** using `top` command.
```
top
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




### 🚀Section-3 (CI/CD - Build and releases)🚀
#### Question-1: Explain the build pipeline stages which you have worked or working on ?
Ans: 
#### Question-2: What is the "POM.xml" file in java based appliction, and what are the important information a devops engineer need from POM.xml to build this java project ?
Ans: 
#### Question-3: How to build dot net based application using pipeline ?
Ans: 
#### Question-4: Wht we use "Maven clean build" in the build pipeline ?
Ans: 
#### Question-5: What is the difference between `maven clean build` and `maven clean install` ?
Ans: 
#### Question-6: What is multi-branching pipeline ?
Ans: 
#### Question-7: Explain about branching technique ?
Ans: 



### 🚀Section-4 (Cloud AWS/Azure)🚀
#### Question-1: How you will retrive password if lost password for virtual machine ?
Ans: 
#### Question-2: How you will create 3 tier architecture application, what all components you will use (Draw the architecture) ?
Ans: 



### 🚀Section-5 (Azure DevOps)🚀
#### Question-1: How to add multiple user in Azure DevOps?
Ans:
#### Question-2: How to migrate items from jira to Azure DevOps?
Ans:
#### Question-3: How to configure subscription in Azure DevOps?
Ans:
#### Question-4: Adding users in Azure DevOps, does it cost ?
Ans:
#### Question-5: What is the difference between self-hosted and microsoft-hosted agents in Azure DevOps?
Ans:
#### Question-6: How you will manage 100 projects in Azure DevOps?
Ans: 
#### Question-7: What are the best practices to use Azure DevOps?
Ans:
#### Question-8: How to set permission in Azure DevOps?
Ans:
#### Question-9: What is the difference between the roles like Basic, Basic + Test Plan, Stackeholder and Visual Studio ?
Ans:
#### Question-10: How you manage thousand (1000) pipelines in Azure DevOps ?
Ans:
#### Question-11: Have you ever build .Net, Java, and Python based project?
Ans:
#### Question-12: How to migrate tasks and pipelines from Jenkins to Azure DevOps?
Ans:
#### Question-13: What are the tool stack you are using in your project?
Ans:
#### Question-14: What tool you use to manage the database ?
Ans:
#### Question-15: Where you store your artifacts?
Ans:
#### Question-16: In which language you fron-end application is prepared  and how you are building it in the pipeline?
Ans:
#### Question-1: 
Ans:
#### Question-1: 
Ans:
#### Question-1: 
Ans:



### 🚀Section-6 (Jenkins)🚀

### 🚀Section-7 (Ansible)🚀

### 🚀Section-8 (terraform)🚀
#### Question-1: How to provision infrastructure for any subscription within azure using terraform?
Ans: 

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

