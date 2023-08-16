## Jenkins interview questions and Answers?
### 1. What is Jenkins?
Jenkins can be thought of as a free and open-source automation tool that is used for continuous integration. It automates some of the software development processes, such as building, testing, and deployment. It can also be integrated with a large number of testing and deployment technologies.

### 2. What is Jenkins used for?
Jenkins is used in the automation of some of the software development processes. With Jenkins, one can continuously test software projects so that developers can integrate the changes to them. Jenkins facilitates continuous integration and delivery through its built-in plugins.

### 3. Which architecture is recommended for a scalable Jenkins environment?
Distributed builds architecture is prescribed for a scalable Jenkins environment because the agents perform the main work and the master maintains the coordination. In this way, the focus is on both the master and the agent. In addition, the master provides the GUI and API endpoints.

### 4. How to add a user in Jenkins?
- Go to Manage Jenkins
- Click on Create User
- Enter all the details
- Select Create User

### 5. What is Jenkins Pipeline?
Jenkins Pipeline, sometimes simply called Pipeline, is a set of plugins, which supports the integration and use of continuous delivery pipelines in Jenkins. The continuous delivery pipeline is the process where automated builds, tests, and deployments are planned and arranged as one release workflow. Simply put, it is the process the code changes will go through for delivering software from the version control to clients and users.

### 6. What is Jenkinsfile?
A Jenkinsfile is essentially a text file containing the steps for running a Jenkins Pipeline and is registered into the source control repository of a project.

### 7. What is continuous integration in Jenkins?
Continuous integration is the process in which all development work integration is done at the earliest and a code is continuously tested after a commit. This process allows one to discover bugs at an early stage and fix them. The Jenkins build server provides this functionality.

### 8. How to change port for Jenkins?
Jenkins, by default, runs on port number 8080. For changing the existing port from 8080 to the desired port number, we can take the following steps:

Run Jenkins using the command line
```
Execute the command, java -jar -httpPort=desired_port jenkins.war
```
If Jenkins is installed using the Windows package, then:
- Go to Program Files/Jenkins directory 
- Open Jenkins.xml in the editor
- Find –httpPort=8080 and replace the port 8080 with the new port number

### 9. What are the software prerequisites that must be met before Jenkins is installed?
The software prerequisites for installing Jenkins are that first we need to install Java Development Kit. We also need to install the Jakarta Enterprise Edition. Jenkins also comes with an embedded Jetty Runtime that can be used if WebSphere or Tomcat is not available.

### 10. How to configure and use third-party tools in Jenkins?
These are the steps used for working with a third-party tool in Jenkins:
- First, we have to install the third-party software.
- We need to have the plugin that supports the third-party tool.
- Then, we must configure the third-party tool in the admin console.
- We can then use the plug-in from the Jenkins build job.

### 11. How to set up a Jenkins job?
First, we need to create a Jenkins job by going to the Jenkins top page and then selecting a ‘New Job’ and building a freestyle software project. Some of the elements of a freestyle project include the following:

- We need a CVS or a subversion where our source code will reside.
- When Jenkins performs the builds, we will need the optional triggers.
- We need a build script like a Maven or Ant where the script is actually built.

### 12. How to take a backup of your Jenkins build jobs?
Within the XML configuration, each Jenkins build is stored. When this folder is copied, the configuration of all the build jobs that are managed by the Jenkins master is backed up. If we can perform a Jenkins Git integration, then it is good. When we copy the contents of the folder, we will see that the build jobs described in the folder will be restored when the Jenkins server is started the next time.

### 13. What are the steps included in a Jenkins pipeline?
A complete Jenkins pipeline includes building a project from the source code, putting it through a variety of units, integrating, testing for user acceptance and performance, and then finally deploying the packaged application on an application server.
So, the steps in a Jenkins pipeline can be listed as below:
- Build
- Test
- Deploy

# Intermediate Interview Questions:
