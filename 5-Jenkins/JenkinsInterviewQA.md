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

### 14. What is the process for creating a backup and copy files in Jenkins?
If we want to create a backup for our file, then we need to regularly backup our Jenkins_Home directory. This will include all the build jobs configuration, all the slave node configuration, and the build history. If we want to create a Jenkins backup, we can copy a job directory to a clone or can rename the directory.

### 15. State some of the advantages of using Jenkins.
Here are some of the most important advantages of Jenkins:
- We will get an automated build report every time a change is made to the source code.
- We will be able to achieve continuous integration with agile methodology principles.
- We can automate the maven release project with a few simple steps.
- Bugs can be easily tracked at an early development stage.

### 16. What are the requirements for using Jenkins?
Some of the requirements for using Jenkins are as follows:
- A source code repository like a Git repository
- A build script like a Maven script that is checked into the repository

### 17. What are some of the useful plugins in Jenkins?
Some of the important plugins that are used with Jenkins are:
- Git repository
- Amazon EC2
- HTML Publisher
- JDK Parameter Plugin
- Configuration Slicing Plugin

### 18. How will you create Pipeline in Jenkins?
The Jenkins Pipeline code is written into a text file called Jenkinsfile, which in turn is checked into a project’s source control repository.
- Click on the New Item option on the Jenkins dashboard
- Create a name for the new item
- Choose the ‘Pipeline’ project, and click on OK
- In the Pipeline tab, put the Jenkinsfile code
- Click on Apply and Save
- Select Build Now, which will start building the Pipeline
After the Pipeline is set up, any new branches or pull requests that are created in the repository will be automatically detected by Jenkins, and it will start running Pipelines for them.

### 19. How to configure Docker in Jenkins?
To configure Docker in Jenkins, we have to follow the below steps:
- First, we need to click on Manage Jenkins on the Jenkins dashboard.
- We will then select Manage Plugins on the Configuration page.
- Next, we need to click on Available once in the tabbed interface, and this will show all the Jenkins plugins available for installation.
- Then, we will search for a Docker plugin in the search box. There are multiple Docker plugins to choose from.
- Finally, we will select the Docker plugin.

### 20. When can you use the GitHub plugin in Jenkins?
Whenever one wants to integrate Jenkins with GitHub projects, the GitHub plugin can be used. It is used to enable the scheduling of a build, pulling data and code files from the GitHub repository to the Jenkins machine, and triggering every build automatically on the Jenkins server after each commit on the Git repository. This saves time and allows one to incorporate the specific project into the CI process.

### 21. How to integrate Git with Jenkins?
Before we begin, we need to first have the GitHub Jenkins plugin installed. 
- Go to Manage Jenkins on the Jenkins dashboard, and then select Configure System
- Choose Add GitHub Server in the GitHub section
- Add the GitHub token as credentials, and Save
- Open the Jenkins project
- Tick off the GitHub project checkbox, and give the GitHub Repository path for the Project URL
- Under Source Code Management, check the Git box, and in Repositories, give the GitHub Repository URL 
- Under Build Triggers, check the ‘Build when a change is pushed to GitHub’ box
- Set a webhook to Jenkins after the plugin installation is done
- Go to Settings and then to Integrations & Services from the GitHub repository
- Select Add Service, and then add Jenkins (GitHub plugin)
- The URL of the Jenkins machine should be given as Jenkins Hook URL. Add /github-webhook/

### 22. Name some of the SCM tools that are supported by Jenkins.
Some of the important SCM tools that are supported by Jenkins include:
- Git
- Subversion
- CVS
- Mercurial

# Advanced Interview Questions
### 23. Name two ways a Jenkins node agent can be configured to communicate back with the Jenkins master.
These are the mechanisms for starting a Jenkins node agent:
- From the browser window, launch a Jenkins node agent
- From the command line, launch a Jenkins node agent
When we launch a Jenkins node agent, it will download a JNLP file. A new process is launched on the client machine by JNLP when it runs.

### 24. How to turn off Jenkins Security if the administrative users have locked out of the admin console?
There is a folder that contains a file named config.xml. We need to change the settings to false for the security to be disabled when Jenkins is started the next time.

### 25. What is the process for securing Jenkins?
First, we need to ensure global security. Then, we have to make sure that Jenkins is integrated with the user directory through an appropriate plugin. The project matrix is enabled for fine tuning the access using the custom version-controlled script for automating the process of rights and privileges in Jenkins. The access to Jenkins data or folder is limited. We will run security audits on it.

### 26. What is the relation between Hudson and Jenkins?
Initially, Jenkins was called Hudson. However, due to some reasons, the name was changed from Hudson to Jenkins.

### 27. If there is a broken build in your Jenkins project, then what will you do?
First, we need to open the console output where the broken build is created and then see if there are any file changes that were missed. If we do not find any issues in this manner, then we can update our local workspace and replicate the problem and then try to solve it.

### 28. From one server to another, how do you copy or move your Jenkins jobs?
First, we need to copy our jobs directory from the old to the new server. There are multiple ways to do it. We can either move the job from the installation by simply copying the corresponding job directory or we can make a clone of the job directory by making an existing job’s copy. For this, we need to have a different name, which we can rename later.

29. How to schedule builds in Jenkins?
Some steps for scheduling builds in Jenkins are as follows:
- First, we should have a source code management commit.
- We have to complete the other builds.
- Then, we have to schedule it to run at a specified time.
- We need to then give a manual build request.

### 30. How to configure Jenkins with Maven?
To configure Jenkins with Maven:
- On the Jenkins dashboard, go to Manage Jenkins, and select Configure System 
- Scroll down until the Maven section is seen, and then go to Add Maven
- Uncheck the ‘Install automatically’ box
- Add any name for the setting and the location of the MAVEN_HOME
- After saving, one can create a job with the ‘Maven project’ option

### 31. How to create a slave node in Jenkins?
To create a slave node in Jenkins:
- Go to Manage Jenkins, and scroll down to Manage Nodes
- Click on New Node
- Set the node name, choose the Dumb slave option, and then click on OK
- Enter the node slave machine details, and click on Save

### 32. How to deploy from Jenkins?
- On the Jenkins dashboard, go to Manage Jenkins and then to Manage Plugins. In the Available section, tick off the ‘Deploy to Container Plugin’ box and install it. Then, restart the Jenkins server
- The next step is to go to the Build project and click on Configure. Choose the ‘Deploy war/ear to a container’ option
- In the Deploy war/ear to a container section, enter the server details on which the files need to be deployed, and press Save
After a successful build, the necessary files will get deployed to the required container.
