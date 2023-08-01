# Part-1
# What is DevOps
In simple word Devops means everything which is improving your release cycle including Development , testing , deployment with high stability , high speed infrastructure and with less complexities .

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/bcc07327-d770-49c0-9026-45f5156a4344)


## Why DevOps ?
The software industry is growing at the speed of light, but software delivery in IT firm is suffering from a lack of department integration. Problems are lobbed over the wall across the team and process is suffering. As a developer, you need to wait for work to get aligned with production and operation team. Moreover, most recurring challenges for developers to balance pending and new codes along with errors solving when new code is pushed into the production environment. Here DevOps helps you get the job done.

This happens when the production environment is not identical with the development department. Take the operation team in the scenario, as a system administrator, they are responsible for production environment uptime with maintaining code deployment schedule.

That is why DevOps came in the picture. It is based on the simple fact that Development and operation work better together to have better collaboration, communication with reduced friction in the process. DevOps is derived from Development and Operation. DevOps framework inherits from the agile system administration movements .It works to improve collaboration between teams for better product delivery, profitability and satisfied customers.

It is achieved by automating most of the process like software delivery, workflow, testing and infrastructure designs. To avoid complexity, it is done in small intervals. It also builds an identical environment for Operation and development team.

It is a super-framework. It connects the dots between other frameworks, tools, vocabulary, practices, and principles. Most importantly, the goal is to inspire systems thinking across entire value stream, in order to deploy frequently and faster. For example, DevOps shift-left approach helps testing, development, security, and operational professionals engage their practices and processes earlier as an integral facet of the entire system instead of as some downstream activity.

With implementing DevOps culture you can make your product more stable , secure by providing quality releases in very short time .

## Best practices for DevOps culture :
* Easy integrations of multiple technologies which are improving your release and deployment cycle including Development. 
* Communication and collaboration with teams .
* Stability
* Security
* Backup
* Monitoring
* Cleanup

## Who can be a good DevOps Engineer ?
The one who has quality and understanding about above best practices and complete understanding about how code is deploying into production can become a good DevOps Engineer.

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/735cd57a-6cf5-451d-9b45-6f9dcc1c2602)


## What things you need to learn if you want to move your career into DevOps ?
If you want to start with DevOps you can start learning about below things, I will explain below topic in comming notes.
* Basic Linux commands .
* Unix internals basics .
* Build , Release and Deployment process .
* Git version control system .
* Learn about CI/CD and its Tools (Like: Azure DevOps, Jenkins, CircleCI)
* Build process of various languages based project (Like: .Net, Java, Python, Go, Angular, React)
* Unit testing frameworks.
* Build tools (Like: Maven, Ant)
* Any public cloud (AWS/Azure/GCP)
* Code Quality Ananysis tools (Like: Sonarqube)
* Artifactory
* Docker
* Kebernetes
* Terraform (IaaC- Infrastructure provisioning tool developed by Hasicorp)
* Ansible (Configuration management tool)
* Grafana and Prometheus

# Learn about automation tools and Integrations .
* Static code analysis [Sonarqube]
* Artifactory to store binaries , docker images and artifact packages such as JFROG , Nexus .
* Centralized logging with ELK
* docker vulnerability scanning [ Anchore Engine ]
* Security scanning tools depenedency track , Nexus lifecycle .
* Helmchart .
* Groovy script pipeline coding .
* grafana and slack monitoring .
* Configuration management tools .
* Secrets management with Hashicorp [ Vault ]
* Restore , rollback , backup , migration strategies [ velero ]
* Network and infrastructural issues debugging techniques and resolution skills.
* Networking basics - port , ping , netstat, Load Balancing , public IP , private IP , VM , cloud configs .
* Keep HandsOn about application setup .

# Benefits of DevOps :

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/2faf2be2-194a-4e75-b0aa-4d2d6d18507e)

* Improve operational support and faster releases .
* Good process across IT and Teams , including automation .
* Increasing team flexibility and agility .
* Make your team more engaged .
* Cross skilling and continuous improvements .
* Collaborative working .
* Faster resolution of problems.
* More stable environments .
* Less complexity to manage infrastructure .

# Conclusion :
DevOps is all about removing bottlenecks, better delivery practices, automation, and its an Agile at Organisation level. It’s not a tool, it cannot be built in a day or month. It is a roadmap or set of best practices that needs to be followed .


# Part-2

#### What are the basic DevOps tools?
There are a number of different DevOps tools available that can be used to support the various stages of the DevOps process. Here are some of the most commonly used DevOps tools:

- **Source code management (SCM) tools:** These are tools used to manage and version control source code, such as Git, Subversion (SVN), and Perforce.
- **Continuous Integration (CI) tools:** These are tools used to automatically build, test, and deploy code changes as they are committed to the source code repository. Examples include Jenkins, Travis CI, and CircleCI.
- **Configuration Management (CM) tools:** These are tools used to automate the provisioning and configuration of infrastructure and application components. Examples include Ansible, Chef, and Puppet.
- **Containerization tools:** These are tools used to create, deploy, and manage containerized applications. Examples include Docker, Kubernetes, and OpenShift.
- **Monitoring and Logging tools:** These are tools used to monitor the performance and availability of applications and infrastructure, and to log and analyze system events and data. Examples include Prometheus, Grafana, and Splunk.
- **Collaboration and Communication tools:** These are tools used to facilitate collaboration and communication among team members, such as chat tools (Slack), project management tools (Jira), and collaboration tools (Microsoft Teams).

These are just a few examples of the many DevOps tools available. The specific tools used will depend on the needs and requirements of the organization and the specific DevOps processes being implemented.

#### What is azure DevOps?
Azure DevOps is a cloud-based set of services that supports software development processes including Continuous Integration (CI), Continuous Delivery (CD), and Agile project management. It provides a comprehensive suite of tools for software development teams to plan, develop, test, and deploy applications.

Azure DevOps provides a range of integrated tools and services, including:

- **Azure Boards:** A work tracking system that provides Agile project management tools for planning, tracking, and reporting on tasks, bugs, and other work items.
- **Azure Repos:** A Git-based source control system that allows teams to manage and version control their code repositories.
- **Azure Artifacts:** A package management system that provides a central repository for storing and sharing software artifacts, including packages, binaries, and container images.
- **Azure Pipelines:** A continuous integration and delivery (CI/CD) system that automates build, test, and deployment workflows.
- **Azure Test Plans:** A testing solution that provides manual and exploratory testing tools, as well as load testing and performance testing capabilities.

Azure DevOps also provides integration with a wide range of third-party tools and services, such as Jenkins, GitHub, and Slack, to enable teams to use the tools that best fit their needs.

Overall, Azure DevOps provides a comprehensive set of tools and services that support the entire software development lifecycle, enabling teams to develop and deploy high-quality software more efficiently and effectively.

#### What is Jenkins?
Jenkins is an open source automation server that is used to automate various stages of software development, including building, testing, and deploying code. It allows developers to integrate their code changes into a shared repository and automatically trigger builds and tests to ensure that the code is working as expected. Jenkins supports a wide range of plugins and integrations with other tools, making it highly customizable and extensible.

Jenkins is built on Java and provides a web-based interface for managing jobs and configuring build pipelines. Jobs can be configured to execute various build and test scripts, and can be triggered automatically by code changes or scheduled to run at specific times.

Jenkins also supports distributed builds, allowing build tasks to be distributed across multiple machines to speed up the build process. It provides a dashboard for monitoring build status and provides alerts when builds fail or encounter issues.

Overall, Jenkins is a powerful tool for automating the software development process, allowing developers to focus on writing code and ensuring that it is properly tested and deployed.

#### What is JIRA?
JIRA is a popular project management tool used by software development teams to plan, track, and manage software development projects. It provides a wide range of features for issue tracking, project management, and agile development methodologies.

Some of the key features of JIRA include:
- **Issue tracking:** JIRA allows teams to track and manage issues, bugs, and tasks related to a project. It provides a flexible issue management system that can be customized to meet the needs of different teams.

- **Project management:** JIRA provides a range of project management features, including task management, project tracking, and progress reporting. It also supports agile development methodologies such as Scrum and Kanban.

- **Collaboration:** JIRA provides tools for team collaboration, including shared dashboards, real-time updates, and comment threads on issues and tasks.

- **Integration:** JIRA can be integrated with a wide range of other software tools, including source code management systems like Git and build automation tools like Jenkins.

Overall, JIRA is a powerful tool for managing software development projects, providing a flexible and customizable platform for teams to plan, track, and collaborate on projects.

#### What is Confluence?
Confluence is a collaboration and knowledge management tool developed by Atlassian, the same company that developed JIRA. It is designed to help teams collaborate and share information by providing a central platform for creating, organizing, and sharing content.

Confluence allows teams to create and share a wide range of content, including documentation, meeting notes, project plans, and knowledge base articles. It provides a flexible and customizable platform that can be tailored to the needs of different teams.

Some of the key features of Confluence include:
- **Document collaboration:** Confluence allows team members to collaborate on documents in real-time, making it easy to create, edit, and share content.
- **Knowledge base:** Confluence can be used to create a knowledge base for storing and sharing information, making it easy for team members to access important information when they need it.
- **Project management:** Confluence can be used to manage projects, providing tools for task management, project tracking, and progress reporting.
- **Team collaboration:** Confluence provides tools for team collaboration, including shared dashboards, calendars, and comment threads on content.
- **Integration:** Confluence can be integrated with a wide range of other software tools, including JIRA, Slack, and Google Drive.

Overall, Confluence is a powerful tool for collaboration and knowledge management, providing a flexible and customizable platform for teams to share information and work together more effectively.

#### Explain azure DevOps build and release pipelines?
Azure DevOps build and release pipelines are tools used to automate the build, testing, and deployment of software applications. These pipelines are integrated with Azure DevOps, a cloud-based service that provides collaboration tools for software development teams.

The build pipeline is used to compile, build, and test the codebase, and can be triggered manually or automatically on code changes. The pipeline can be configured to use different build agents, depending on the requirements of the build. For example, a build pipeline can be set up to use a Linux agent for building and testing code written in Python, while a Windows agent may be used for code written in C#.

Once the build pipeline is complete and the code has passed all tests, the release pipeline can be triggered to deploy the application to the production environment. The release pipeline can be configured to use different deployment environments, such as staging and production. It can also use various deployment methods, such as blue-green or canary deployments, to minimize downtime and reduce the risk of errors.

The release pipeline can be set up to include various approval gates, such as manual approvals or automated testing, to ensure the code is thoroughly tested and validated before deployment. Once the code is deployed, the release pipeline can also perform post-deployment checks to ensure the application is functioning correctly.

Overall, Azure DevOps build and release pipelines enable development teams to automate the entire software development lifecycle, from code changes to production deployment, increasing efficiency and reducing errors.

#### What are best practices in Azure DevOps?
There are several best practices to follow when using Azure DevOps:
- Use a structured naming convention for your Azure DevOps projects and resources to ensure consistency and ease of management.
- Implement security measures such as Role-Based Access Control (RBAC) to ensure that only authorized personnel have access to your Azure DevOps resources.
- Use source control management (SCM) tools such as Git to manage your code changes and ensure version control.
- Implement a Continuous Integration/Continuous Deployment (CI/CD) pipeline to automate the build, test, and deployment of your code.
- Implement infrastructure as code (IaC) principles using tools such as Azure Resource Manager (ARM) templates, Terraform, or PowerShell to manage and automate your infrastructure.
- Use Azure DevOps boards to manage your work items, track progress, and prioritize tasks.
- Implement automated testing to ensure the quality of your code and reduce the risk of bugs in production.
- Use Azure DevOps analytics to measure and monitor the performance of your projects, identify areas for improvement, and optimize your processes.
- Implement DevOps culture and practices to promote collaboration, communication, and continuous improvement across your development and operations teams.
- Regularly review and refine your Azure DevOps processes to ensure that they are aligned with your business goals and objectives.

