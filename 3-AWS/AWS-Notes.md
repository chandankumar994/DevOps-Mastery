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

#### 1- IAM:
**Note:** IAM stands for Identity Access Management.
- IAM used to solve Authentication and Authorisation problem in AWS Accounts.
- What is User, Policies, Groups and Roles?
  - **Users:** Usually we create Users for Authentication purpose.
  - **Policies:** We create some pilicies and provide authorisation to the users according to their role.
  - **Groups:** We generally create group to make efficiency, and allocate users and pilicies to the groups.
    Example group: DEV, QA, DBA, Others.
  - **Roles:** Roles are basically similar to users (Contains temperory userid & Password), which we generally create to access some service.

     Example1: Dev team is looking to connect with DB Service then we can provide them a role to access it.
     Example2: We can also use Roles to make communication between two or more services.
    


    












