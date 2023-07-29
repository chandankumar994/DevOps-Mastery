## AWS Notes

#### What is cloud?
Cloud refers to a network of remote servers hosted on the internet that store, manage, and process data, providing on-demand access to computing resources and services, enabling flexible, scalable, and cost-effective solutions for businesses and individuals.

#### Types of Cloud computing ?
There are three main types of cloud computing: 
- **Public Cloud** - shared resources over the internet
- **Private Cloud** - dedicated resources for one organization
- **Hybrid Cloud** - a mix of public and private clouds

#### What is AWS ?
AWS (Amazon Web Services) is a cloud computing platform that provides a wide range of on-demand services, including computing power, storage, and databases, to help individuals and organizations build and scale applications and services over the internet.

### AWS Services

### 1- IAM:
**Note:** IAM stands for Identity Access Management.
- IAM used to solve Authentication and Authorisation problem in AWS Accounts.

#### Components of IAM:
- **Users:** IAM users represent individual people or entities (such as applications or services) that interact with your AWS resources. Each user has a unique name and security credentials (password or access keys) used for authentication and access control.
- **Groups:** IAM groups are collections of users with similar access requirements. Instead of managing permissions for each user individually, you can assign permissions to groups, making it easier to manage access control. Users can be added or removed from groups as needed.
- **Roles:** IAM roles are used to grant temporary access to AWS resources. Roles are typically used by applications or services that need to access AWS resources on behalf of users or other services. Roles have associated policies that define the permissions and actions allowed for the role.
- **Policies:** IAM policies are JSON documents that define permissions. Policies specify the actions that can be performed on AWS resources and the resources to which the actions apply. Policies can be attached to users, groups, or roles to control access. IAM provides both AWS managed policies (predefined policies maintained by AWS) and customer managed policies (policies created and managed by you).


#### Summary IAM:
- AWS IAM (Identity and Access Management) is a service provided by Amazon Web Services (AWS) that helps you manage access to your AWS resources. It's like a security system for your AWS account.
- IAM allows you to create and manage users, groups, and roles. Users represent individual people or entities who need access to your AWS resources. Groups are collections of users with similar access requirements, making it easier to manage permissions. Roles are used to grant temporary access to external entities or services.
- With IAM, you can control and define permissions through policies. Policies are written in JSON format and specify what actions are allowed or denied on specific AWS resources. These policies can be attached to IAM entities (users, groups, or roles) to grant or restrict access to AWS services and resources.
- IAM follows the principle of least privilege, meaning users and entities are given only the necessary permissions required for their tasks, minimizing potential security risks. IAM also provides features like multi-factor authentication (MFA) for added security and an audit trail to track user activity and changes to permissions.
- By using AWS IAM, you can effectively manage and secure access to your AWS resources, ensuring that only authorized individuals have appropriate permissions and actions are logged for accountability and compliance purposes.
- Overall, IAM is an essential component of AWS security, providing granular control over access to your AWS account and resources, reducing the risk of unauthorized access and helping maintain a secure environment.


    
### 2- EC2:


    












