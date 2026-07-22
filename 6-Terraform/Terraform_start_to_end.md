# 🚀 Terraform Guide for DevOps Engineers

> A practical, easy-to-understand guide with real-life examples, code samples, and interview preparation

---

## 📋 Table of Contents

1. [What is Terraform?](#what-is-terraform)
2. [Basic Concepts](#basic-concepts)
3. [How to Get Started](#how-to-get-started)
4. [Sample Code with Explanations](#sample-code-with-explanations)
5. [Terraform Workflow](#terraform-workflow)
6. [Real-Life Examples](#real-life-examples)
7. [Interview Questions & Answers](#interview-questions--answers)

---

## 🤔 What is Terraform?

Imagine you are building a house. Instead of manually placing each brick, you give an **architect's blueprint** to a machine that builds the entire house for you automatically.

**Terraform is that machine for cloud infrastructure.**

- You write **code** (the blueprint) describing what infrastructure you want
- Terraform **reads** that code and creates the infrastructure on cloud providers like AWS, Azure, or GCP
- If you want to **change** something, update the code and Terraform handles the rest
- If you want to **destroy** everything, one command does it all

> 🏠 **Real-Life Analogy:** Think of Terraform like ordering food on Zomato/Swiggy. You select what you want, click order, and it gets delivered. You don't care HOW the restaurant cooks it — you just defined what you want!

---

## 📚 Basic Concepts

### 1. 🏗️ Infrastructure as Code (IaC)

Writing infrastructure setup in code files instead of clicking around in the AWS console manually.

| Traditional Way | IaC Way (Terraform) |
|----------------|---------------------|
| Click on AWS Console | Write `.tf` files |
| Manual, error-prone | Automated, consistent |
| Hard to reproduce | Easy to replicate |
| No version history | Git version controlled |

---

### 2. 🔧 Providers

Providers are **plugins** that allow Terraform to talk to different cloud platforms or services.

```
Terraform → Provider → Cloud Platform
```

**Examples of Providers:**

| Provider | What it manages |
|----------|----------------|
| `aws` | Amazon Web Services resources |
| `azurerm` | Microsoft Azure resources |
| `google` | Google Cloud Platform resources |
| `kubernetes` | Kubernetes clusters |
| `github` | GitHub repositories |

> 🍕 **Real-Life Analogy:** Provider is like a **translator**. If you speak English and the restaurant menu is in Italian, the provider translates your order so the kitchen (cloud) understands it.

---

### 3. 📦 Resources

Resources are the **actual things** you create — like EC2 instances, S3 buckets, VPCs, databases, etc.

```hcl
resource "aws_instance" "my_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

- `aws_instance` → Type of resource (EC2 instance)
- `my_server` → Your custom name for this resource
- Inside `{}` → Configuration/settings

---

### 4. 📊 State File (`terraform.tfstate`)

Terraform keeps a **record** of everything it created in a file called `terraform.tfstate`.

> 📒 **Real-Life Analogy:** It's like your **purchase receipt**. When you buy groceries, the receipt records exactly what you bought. Terraform's state file records exactly what infrastructure it created.

**Why is it important?**
- Terraform compares your code with the state file to figure out **what changed**
- It knows what to **add**, **update**, or **delete**

---

### 5. 🗂️ Modules

Modules are **reusable packages** of Terraform code — like functions in programming.

> 🏭 **Real-Life Analogy:** Think of modules like **pre-built apartment units**. Instead of constructing each room from scratch every time, you use a pre-designed unit and just customize it.

---

### 6. 📝 Variables

Variables make your code **flexible and reusable** — instead of hardcoding values.

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

---

### 7. 📤 Outputs

Outputs **display information** after Terraform runs — like the IP address of the server it just created.

```hcl
output "server_ip" {
  value = aws_instance.my_server.public_ip
}
```

---

### 8. 🔒 Remote Backend

Instead of storing the state file locally, you store it in a **shared location** (like S3) so the whole team can access it.

> 🏦 **Real-Life Analogy:** Like storing important documents in a **bank locker** instead of your house — safer and accessible to authorized people.

---

## 🚦 How to Get Started

### Step 1: Install Terraform

```bash
# macOS (using Homebrew)
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Ubuntu/Debian
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | \
  sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Verify installation
terraform --version
```

---

### Step 2: Configure AWS CLI (for AWS Provider)

```bash
# Install AWS CLI
pip install awscli

# Configure your credentials
aws configure
# It will ask for:
# AWS Access Key ID
# AWS Secret Access Key
# Default region (e.g., us-east-1)
# Output format (json)
```

---

### Step 3: Create Your First Project Folder

```bash
mkdir my-first-terraform
cd my-first-terraform
touch main.tf variables.tf outputs.tf
```

---

### Step 4: Understand File Structure

```
my-first-terraform/
│
├── main.tf           # Main configuration (resources)
├── variables.tf      # Input variables
├── outputs.tf        # Output values
├── terraform.tfvars  # Variable values (don't commit secrets!)
└── providers.tf      # Provider configuration
```

---

## 💻 Sample Code with Explanations

### Example 1: Create an EC2 Instance on AWS

#### `providers.tf`
```hcl
# This tells Terraform we're using AWS as our cloud provider
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"  # Where to download the plugin from
      version = "~> 5.0"         # Use version 5.x
    }
  }
}

# Configure the AWS provider with the region
provider "aws" {
  region = "us-east-1"  # We want to create resources in US East
}
```

---

#### `variables.tf`
```hcl
# Define a variable for the instance type
variable "instance_type" {
  description = "Type of EC2 instance"  # What this variable is for
  type        = string                   # Must be a string value
  default     = "t2.micro"              # Default value if not provided
}

# Define a variable for the server name
variable "server_name" {
  description = "Name tag for the server"
  type        = string
  default     = "MyWebServer"
}

# Define a variable for the environment
variable "environment" {
  description = "Deployment environment"
  type        = string
  default     = "dev"
  
  # Only allow specific values
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

---

#### `main.tf`
```hcl
# Data source: Fetch the latest Amazon Linux 2 AMI automatically
# Instead of hardcoding AMI ID (which changes per region), we fetch it dynamically
data "aws_ami" "amazon_linux" {
  most_recent = true  # Get the latest version
  owners      = ["amazon"]  # Only from official Amazon

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]  # Pattern for Amazon Linux 2
  }
}

# Create a Security Group (like a firewall for your server)
resource "aws_security_group" "web_sg" {
  name        = "${var.server_name}-sg"  # Dynamic name using variable
  description = "Security group for web server"

  # Allow incoming HTTP traffic (port 80) from anywhere
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # 0.0.0.0/0 means "from any IP"
  }

  # Allow incoming SSH traffic (port 22) from anywhere
  # In production, restrict this to your IP only!
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Allow all outgoing traffic
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"  # -1 means all protocols
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name        = "${var.server_name}-sg"
    Environment = var.environment
  }
}

# Create the EC2 Instance (Your virtual server)
resource "aws_instance" "web_server" {
  ami           = data.aws_ami.amazon_linux.id  # Use the AMI we fetched above
  instance_type = var.instance_type              # Use the variable

  # Attach the security group we created above
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  # This script runs automatically when the server starts
  # It installs and starts a web server (Apache)
  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Hello from Terraform! Server: ${var.server_name}</h1>" > /var/www/html/index.html
  EOF

  # Tags are labels — helps you identify resources in AWS console
  tags = {
    Name        = var.server_name
    Environment = var.environment
    ManagedBy   = "Terraform"  # Good practice: label Terraform-managed resources
  }
}
```

---

#### `outputs.tf`
```hcl
# After Terraform creates the server, show us these values

output "server_public_ip" {
  description = "Public IP address of the web server"
  value       = aws_instance.web_server.public_ip
}

output "server_public_dns" {
  description = "Public DNS of the web server"
  value       = aws_instance.web_server.public_dns
}

output "instance_id" {
  description = "EC2 Instance ID"
  value       = aws_instance.web_server.id
}

output "website_url" {
  description = "URL to access the website"
  value       = "http://${aws_instance.web_server.public_ip}"
}
```

---

#### `terraform.tfvars`
```hcl
# This file provides actual values for variables
# ⚠️ Add this file to .gitignore if it contains secrets!

instance_type = "t2.micro"
server_name   = "MyProductionWebServer"
environment   = "prod"
```

---

### Example 2: Remote Backend with S3 (Team Setup)

```hcl
# Store state file in S3 bucket so the whole team can share it
# This goes in your providers.tf or a separate backend.tf file

terraform {
  backend "s3" {
    bucket         = "my-company-terraform-state"  # S3 bucket name
    key            = "prod/web-server/terraform.tfstate"  # Path inside bucket
    region         = "us-east-1"
    
    # Enable state locking using DynamoDB
    # Prevents two people from running terraform at the same time
    dynamodb_table = "terraform-state-lock"
    encrypt        = true  # Encrypt the state file
  }
}
```

---

### Example 3: Using Modules

#### Create a reusable module (`modules/ec2-instance/main.tf`)

```hcl
# modules/ec2-instance/main.tf

variable "instance_type" {}
variable "instance_name" {}
variable "environment" {}

resource "aws_instance" "this" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = var.instance_type

  tags = {
    Name        = var.instance_name
    Environment = var.environment
  }
}

output "instance_id" {
  value = aws_instance.this.id
}

output "public_ip" {
  value = aws_instance.this.public_ip
}
```

#### Use the module in your main code

```hcl
# main.tf — Using the module we created

# Create a Dev server using the module
module "dev_server" {
  source = "./modules/ec2-instance"  # Path to module

  instance_type = "t2.micro"
  instance_name = "dev-web-server"
  environment   = "dev"
}

# Create a Prod server using the SAME module — just different values!
module "prod_server" {
  source = "./modules/ec2-instance"

  instance_type = "t2.large"      # Bigger for production
  instance_name = "prod-web-server"
  environment   = "prod"
}

# Output from modules
output "dev_server_ip" {
  value = module.dev_server.public_ip
}

output "prod_server_ip" {
  value = module.prod_server.public_ip
}
```

---

### Example 4: Create S3 Bucket with Versioning

```hcl
# Create an S3 bucket for storing application files
resource "aws_s3_bucket" "app_storage" {
  bucket = "my-app-storage-${var.environment}-2024"  # Must be globally unique!

  tags = {
    Name        = "App Storage"
    Environment = var.environment
  }
}

# Enable versioning — keeps old versions of files (like Git for files)
resource "aws_s3_bucket_versioning" "app_storage_versioning" {
  bucket = aws_s3_bucket.app_storage.id  # Link to the bucket above

  versioning_configuration {
    status = "Enabled"
  }
}

# Block all public access to the bucket (security best practice)
resource "aws_s3_bucket_public_access_block" "app_storage_access" {
  bucket = aws_s3_bucket.app_storage.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

---

## 🔄 Terraform Workflow

```
Write Code → Initialize → Plan → Apply → Destroy
   .tf files    terraform   terraform  terraform  terraform
                  init       plan      apply      destroy
```

### Commands Explained

```bash
# 1. INIT — Download providers and set up backend
# Like "npm install" — downloads necessary plugins
terraform init

# 2. VALIDATE — Check if your code is syntactically correct
terraform validate

# 3. FORMAT — Auto-format your code (like Prettier for Terraform)
terraform fmt

# 4. PLAN — Preview what Terraform WILL do (dry run)
# Shows: what will be created (+), changed (~), or deleted (-)
terraform plan

# 5. APPLY — Actually create/update the infrastructure
terraform apply

# Apply without asking for confirmation (use in CI/CD pipelines)
terraform apply -auto-approve

# 6. SHOW — Show current state
terraform show

# 7. OUTPUT — Show output values
terraform output

# 8. DESTROY — Delete ALL resources (be careful!)
terraform destroy

# Destroy a specific resource only
terraform destroy -target=aws_instance.web_server

# 9. IMPORT — Import existing AWS resource into Terraform management
terraform import aws_instance.web_server i-1234567890abcdef0

# 10. STATE — Manage state file
terraform state list          # List all resources in state
terraform state show aws_instance.web_server  # Show details of a resource
terraform state rm aws_instance.web_server    # Remove from state (doesn't delete actual resource)
```

---

## 🌍 Real-Life Examples

### Scenario 1: Startup Setting Up AWS Infrastructure

> A startup needs to quickly set up a web application with a database.

```hcl
# Complete startup infrastructure setup

# VPC — Your private network in the cloud
resource "aws_vpc" "startup_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "startup-vpc"
  }
}

# Public Subnet — Where your web servers live
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.startup_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true  # Automatically assign public IP

  tags = {
    Name = "public-subnet"
  }
}

# Private Subnet — Where your database lives (no direct internet access)
resource "aws_subnet" "private_subnet" {
  vpc_id            = aws_vpc.startup_vpc.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"

  tags = {
    Name = "private-subnet"
  }
}

# Internet Gateway — Allows internet traffic in/out of VPC
resource "aws_internet_gateway" "startup_igw" {
  vpc_id = aws_vpc.startup_vpc.id

  tags = {
    Name = "startup-igw"
  }
}

# RDS Database — Managed MySQL database
resource "aws_db_instance" "startup_db" {
  allocated_storage    = 20          # 20 GB storage
  storage_type         = "gp2"
  engine               = "mysql"
  engine_version       = "8.0"
  instance_class       = "db.t3.micro"
  db_name              = "startupdb"
  username             = "admin"
  password             = var.db_password  # Never hardcode passwords!
  skip_final_snapshot  = true

  tags = {
    Name = "startup-database"
  }
}
```

---

### Scenario 2: DevOps Team with Multiple Environments

```
project/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   └── terraform.tfvars
│   ├── staging/
│   │   ├── main.tf
│   │   └── terraform.tfvars
│   └── prod/
│       ├── main.tf
│       └── terraform.tfvars
└── modules/
    ├── ec2-instance/
    ├── rds-database/
    └── vpc-network/
```

```hcl
# environments/dev/terraform.tfvars
environment    = "dev"
instance_type  = "t2.micro"
instance_count = 1
db_instance    = "db.t3.micro"

# environments/prod/terraform.tfvars
environment    = "prod"
instance_type  = "t3.large"
instance_count = 3
db_instance    = "db.r5.large"
```

---

### Scenario 3: Using `count` and `for_each` — Create Multiple Resources

```hcl
# Create 3 EC2 instances using count
resource "aws_instance" "web_servers" {
  count         = 3  # Creates 3 identical instances
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t2.micro"

  tags = {
    # count.index gives: 0, 1, 2
    Name = "web-server-${count.index + 1}"  # web-server-1, web-server-2, web-server-3
  }
}

# Create S3 buckets for different teams using for_each
variable "team_buckets" {
  default = {
    "frontend" = "us-east-1"
    "backend"  = "us-west-2"
    "data"     = "eu-west-1"
  }
}

resource "aws_s3_bucket" "team_buckets" {
  for_each = var.team_buckets  # Creates one bucket per team

  bucket = "company-${each.key}-bucket-2024"  # company-frontend-bucket-2024, etc.
  
  tags = {
    Team   = each.key    # frontend, backend, data
    Region = each.value  # The region value
  }
}
```

---

### Scenario 4: Conditional Resources

```hcl
variable "create_elastic_ip" {
  description = "Whether to create an Elastic IP"
  type        = bool
  default     = false
}

# Only create Elastic IP if variable is true
resource "aws_eip" "web_eip" {
  count    = var.create_elastic_ip ? 1 : 0  # Ternary condition
  instance = aws_instance.web_server.id
}
```

---

## 🎯 Interview Questions & Answers

### 🟢 Basic Level

---

**Q1: What is Terraform and why do we use it?**

> **Answer:**
> Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp. It allows you to define, provision, and manage infrastructure using declarative configuration files.
>
> **We use it because:**
> - **Consistency:** Same code = same infrastructure every time
> - **Automation:** No manual clicking in cloud consoles
> - **Version Control:** Store infrastructure code in Git
> - **Multi-Cloud:** Works with AWS, Azure, GCP and 300+ providers
> - **Team Collaboration:** Everyone uses the same codebase
>
> **Real Example:** Instead of manually creating 50 EC2 instances clicking through AWS console (and possibly making mistakes), you write code once and Terraform creates all 50 instances consistently.

---

**Q2: What is the difference between `terraform plan` and `terraform apply`?**

> **Answer:**
>
> | `terraform plan` | `terraform apply` |
> |-----------------|------------------|
> | Dry run — shows WHAT will happen | Actually executes the changes |
> | Does NOT make any real changes | Creates/modifies/deletes resources |
> | Safe to run anytime | Irreversible (be careful!) |
> | Like checking your cart before buying | Like clicking "Place Order" |
>
> **Always run `plan` before `apply`!** It shows you a preview with `+` (add), `~` (modify), `-` (destroy) symbols.

---

**Q3: What is a Terraform state file? Why is it important?**

> **Answer:**
> The state file (`terraform.tfstate`) is a JSON file that **records the current state** of your infrastructure as managed by Terraform.
>
> **It's important because:**
> - Terraform compares your code with the state to determine what **changed**
> - It maps your Terraform resources to **real cloud resources**
> - Without it, Terraform doesn't know what already exists
>
> **Analogy:** It's like an **inventory ledger** for a shop. Without it, the shop owner doesn't know what's in stock.
>
> **Best Practices:**
> - Store remotely (S3, Terraform Cloud) — not locally
> - Enable state locking (DynamoDB) to prevent conflicts
> - Enable encryption
> - Never edit manually!

---

**Q4: What are Terraform providers?**

> **Answer:**
> Providers are **plugins** that allow Terraform to interact with APIs of cloud platforms and services. Each provider knows how to create, read, update, and delete resources on that platform.
>
> ```hcl
> provider "aws" {
>   region = "us-east-1"
> }
> ```
>
> Popular providers: `aws`, `azurerm`, `google`, `kubernetes`, `helm`, `github`, `datadog`
>
> **Analogy:** Like a **USB driver** — your computer needs a specific driver to talk to a specific device. Terraform needs a specific provider to talk to a specific cloud.

---

**Q5: What is the difference between `variable` and `local` in Terraform?**

> **Answer:**
>
> | `variable` | `local` |
> |-----------|---------|
> | Input from outside (user provides values) | Computed internally within the code |
> | Can be overridden via CLI, tfvars file | Cannot be overridden from outside |
> | Like function **parameters** | Like function **local variables** |
>
> ```hcl
> # Variable — user can change this
> variable "environment" {
>   default = "dev"
> }
>
> # Local — computed internally
> locals {
>   common_tags = {
>     Environment = var.environment
>     ManagedBy   = "Terraform"
>     CreatedAt   = timestamp()
>   }
> }
>
> # Use locals
> resource "aws_instance" "web" {
>   # ...
>   tags = local.common_tags
> }
> ```

---

### 🟡 Intermediate Level

---

**Q6: What is Terraform Remote Backend? Why use it?**

> **Answer:**
> Remote Backend stores the Terraform state file in a remote, shared location instead of locally on your machine.
>
> **Why use it?**
> - **Team collaboration:** Everyone accesses the same state
> - **Safety:** Not lost if your laptop crashes
> - **Locking:** Prevents simultaneous runs causing conflicts
> - **Security:** Can encrypt sensitive state data
>
> ```hcl
> terraform {
>   backend "s3" {
>     bucket         = "company-terraform-state"
>     key            = "prod/main/terraform.tfstate"
>     region         = "us-east-1"
>     dynamodb_table = "terraform-locks"  # For state locking
>     encrypt        = true
>   }
> }
> ```
>
> **Popular backends:** S3 (AWS), GCS (GCP), Azure Blob Storage, Terraform Cloud

---

**Q7: What is the difference between `count` and `for_each`?**

> **Answer:**
>
> | `count` | `for_each` |
> |---------|-----------|
> | Creates N identical resources | Creates resources from a map/set |
> | Indexed by number (0, 1, 2...) | Indexed by key |
> | If you remove middle item, index shifts | Stable — removing one doesn't affect others |
> | Good for identical resources | Good for similar but slightly different resources |
>
> ```hcl
> # count — 3 identical servers
> resource "aws_instance" "servers" {
>   count = 3
>   # Accessed as: aws_instance.servers[0], [1], [2]
> }
>
> # for_each — different S3 buckets per team
> resource "aws_s3_bucket" "team_buckets" {
>   for_each = toset(["frontend", "backend", "data"])
>   bucket   = "${each.key}-bucket"
>   # Accessed as: aws_s3_bucket.team_buckets["frontend"]
> }
> ```
>
> **Rule of thumb:** Use `for_each` when possible — it's more stable than `count` for most use cases.

---

**Q8: What is Terraform taint and when would you use it?**

> **Answer:**
> `terraform taint` marks a resource as **"needs to be replaced"** on the next `apply`, even if nothing in the code changed.
>
> **When to use:**
> - A resource is in a **bad state** (e.g., EC2 instance is corrupted)
> - You want to **force re-creation** of a specific resource
> - A resource wasn't set up correctly (e.g., user_data script failed)
>
> ```bash
> # Old way (Terraform < 0.15.2)
> terraform taint aws_instance.web_server
>
> # New way (Terraform >= 0.15.2)
> terraform apply -replace="aws_instance.web_server"
> ```
>
> **Note:** `taint` command is deprecated in newer versions. Use `-replace` flag instead.

---

**Q9: Explain the difference between `terraform destroy` and removing a resource from code.**

> **Answer:**
>
> | `terraform destroy` | Removing from code + apply |
> |--------------------|--------------------------|
> | Destroys ALL resources in state | Destroys only the removed resource |
> | Dangerous in production | More controlled approach |
> | Good for complete teardown (dev/test) | Good for specific resource removal |
>
> **Example — Removing from code:**
> ```hcl
> # Before: two instances
> resource "aws_instance" "web1" { ... }
> resource "aws_instance" "web2" { ... }
>
> # After: remove web2 from code
> resource "aws_instance" "web1" { ... }
> # web2 is deleted — Terraform detects it's gone from code
> # and will destroy it on next apply
> ```
>
> ```bash
> terraform plan    # Shows: - aws_instance.web2 will be destroyed
> terraform apply   # Destroys only web2
> ```

---

**Q10: What are Terraform workspaces?**

> **Answer:**
> Workspaces allow you to **manage multiple state files** with the same configuration — useful for managing multiple environments with the same codebase.
>
> ```bash
> # Create workspaces for different environments
> terraform workspace new dev
> terraform workspace new staging
> terraform workspace new prod
>
> # Switch between workspaces
> terraform workspace select prod
>
> # List workspaces
> terraform workspace list
>
> # Show current workspace
> terraform workspace show
> ```
>
> ```hcl
> # Use workspace name in your code
> resource "aws_instance" "web" {
>   instance_type = terraform.workspace == "prod" ? "t3.large" : "t2.micro"
>
>   tags = {
>     Environment = terraform.workspace  # "dev", "staging", or "prod"
>   }
> }
> ```
>
> **Note:** While workspaces work, many teams prefer **separate directories per environment** for better isolation and clarity.

---

### 🔴 Advanced Level

---

**Q11: How do you handle sensitive data in Terraform?**

> **Answer:**
> Never hardcode passwords or secrets! Here are the approaches:
>
> ```hcl
> # 1. Use sensitive variables
> variable "db_password" {
>   type      = string
>   sensitive = true  # Won't show in plan/apply output
> }
>
> # 2. Use AWS Secrets Manager
> data "aws_secretsmanager_secret_version" "db_pass" {
>   secret_id = "prod/db/password"
> }
>
> resource "aws_db_instance" "db" {
>   password = data.aws_secretsmanager_secret_version.db_pass.secret_string
> }
>
> # 3. Use environment variables (no trace in code)
> # export TF_VAR_db_password="mypassword"
> # Terraform automatically picks up TF_VAR_* variables
>
> # 4. Mark outputs as sensitive
> output "db_password" {
>   value     = var.db_password
>   sensitive = true  # Won't display in output
> }
> ```

---

**Q12: What is `terraform import` and when would you use it?**

> **Answer:**
> `terraform import` brings **existing infrastructure** (created manually or by other tools) **under Terraform management** without recreating it.
>
> **When to use:**
> - Team was creating resources manually, now switching to Terraform
> - Resources created before Terraform was adopted
> - You accidentally deleted from state but the resource still exists
>
> ```bash
> # Import an existing EC2 instance into Terraform
> terraform import aws_instance.web_server i-1234567890abcdef0
>
> # Import an S3 bucket
> terraform import aws_s3_bucket.my_bucket my-existing-bucket-name
>
> # Import RDS instance
> terraform import aws_db_instance.mydb mydbidentifier
> ```
>
> **Important:** Import only updates the **state file**. You still need to **write the matching Terraform code** manually or use `terraform show` to see the imported resource configuration.

---

**Q13: What is the difference between `depends_on` implicit and explicit dependency?**

> **Answer:**
>
> **Implicit Dependency** — Terraform automatically detects dependencies through references:
> ```hcl
> resource "aws_security_group" "sg" {
>   name = "my-sg"
> }
>
> resource "aws_instance" "web" {
>   # By referencing sg.id, Terraform knows: create sg FIRST, then web
>   vpc_security_group_ids = [aws_security_group.sg.id]
> }
> ```
>
> **Explicit Dependency** — Use `depends_on` when there's no direct reference but order matters:
> ```hcl
> resource "aws_s3_bucket" "data_bucket" {
>   bucket = "my-data-bucket"
> }
>
> resource "aws_instance" "processor" {
>   ami           = "ami-123456"
>   instance_type = "t2.micro"
>
>   # EC2 doesn't reference the bucket, but must wait for it
>   depends_on = [aws_s3_bucket.data_bucket]
> }
> ```
>
> **Best Practice:** Prefer implicit dependencies — use `depends_on` only when necessary, as it reduces Terraform's ability to parallelize resource creation.

---

**Q14: How do you prevent accidental deletion of critical resources?**

> **Answer:**
>
> ```hcl
> # 1. lifecycle prevent_destroy
> resource "aws_db_instance" "production_db" {
>   # ... configuration
>
>   lifecycle {
>     prevent_destroy = true  # Terraform will ERROR if you try to destroy this
>   }
> }
>
> # 2. ignore_changes — Don't update if someone manually changed these
> resource "aws_instance" "web" {
>   ami           = var.ami_id
>   instance_type = var.instance_type
>
>   lifecycle {
>     ignore_changes = [
>       ami,           # Don't update if AMI changes
>       user_data,     # Don't update if user_data changes (avoids server restart)
>     ]
>   }
> }
>
> # 3. create_before_destroy — Create new resource before deleting old one
> # Ensures zero downtime during replacements
> resource "aws_instance" "web" {
>   # ... configuration
>
>   lifecycle {
>     create_before_destroy = true
>   }
> }
> ```

---

**Q15: How do you manage Terraform in a CI/CD pipeline?**

> **Answer:**
> Here's a real GitLab CI/CD example:
>
> ```yaml
> # .gitlab-ci.yml
> stages:
>   - validate
>   - plan
>   - apply
>
> variables:
>   TF_VERSION: "1.6.0"
>
> before_script:
>   - apt-get update && apt-get install -y wget unzip
>   - wget https://releases.hashicorp.com/terraform/${TF_VERSION}/terraform_${TF_VERSION}_linux_amd64.zip
>   - unzip terraform_${TF_VERSION}_linux_amd64.zip
>   - mv terraform /usr/local/bin/
>   - terraform init
>
> validate:
>   stage: validate
>   script:
>     - terraform validate
>     - terraform fmt -check  # Fail if code is not formatted
>
> plan:
>   stage: plan
>   script:
>     - terraform plan -out=tfplan  # Save plan to file
>   artifacts:
>     paths:
>       - tfplan  # Pass plan file to next stage
>
> apply:
>   stage: apply
>   script:
>     - terraform apply -auto-approve tfplan  # Apply the saved plan
>   when: manual  # Require manual approval for apply
>   only:
>     - main  # Only apply from main branch
> ```
>
> **Best Practices in CI/CD:**
> - Run `plan` on every PR/MR — let developers see changes
> - Require **manual approval** for `apply` in production
> - Store secrets in CI/CD secret variables, not in code
> - Use **remote backend** so CI/CD agents share state
> - Use **separate pipelines** for different environments

---

## 🎁 Bonus Tips

### 📌 Terraform Best Practices Cheat Sheet

```
✅ DO's                              ❌ DON'Ts
─────────────────────────────────    ──────────────────────────────────
Use remote backend for state         Don't store state in Git
Use variables for flexibility        Don't hardcode values
Use modules for reusability          Don't repeat yourself (DRY)
Always run plan before apply         Don't run apply in production blindly
Use for_each over count              Don't use count for dissimilar resources
Tag all your resources               Don't skip tagging (cost tracking!)
Store secrets in Secrets Manager     Don't hardcode passwords in .tf files
Use lifecycle rules for safety       Don't manually modify state files
Format code with terraform fmt       Don't ignore formatting standards
Lock provider versions               Don't use unpinned provider versions
```

---

### 🔑 Quick Reference Card

```bash
terraform init          # Initialize project
terraform validate      # Check syntax
terraform fmt           # Format code
terraform plan          # Preview changes
terraform apply         # Create/update infrastructure
terraform destroy       # Delete everything
terraform output        # Show outputs
terraform state list    # List resources in state
terraform import        # Import existing resource
terraform workspace     # Manage workspaces
```

---

## 📚 Learning Resources

| Resource | Link |
|----------|------|
| Official Docs | [developer.hashicorp.com/terraform](https://developer.hashicorp.com/terraform) |
| Terraform Registry | [registry.terraform.io](https://registry.terraform.io) |
| AWS Provider Docs | [registry.terraform.io/providers/hashicorp/aws](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) |
| Terraform Best Practices | [terraform-best-practices.com](https://www.terraform-best-practices.com) |
| Practice Labs | [learn.hashicorp.com](https://developer.hashicorp.com/terraform/tutorials) |

---

> 💡 **Pro Tip:** The best way to learn Terraform is to **build something real**. Start with creating a simple EC2 instance, then gradually add VPC, RDS, Load Balancer, and Auto Scaling. Break things, fix them, and you'll master Terraform faster than reading any guide!

---

*Happy Terraforming! 🚀*
