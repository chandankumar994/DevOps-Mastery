# Complete Guide: Create Azure VM with Terraform

## Table of Contents
1. Prerequisites & Setup
2. Project Structure
3. Azure Authentication & Secrets Management
4. Terraform Configuration Files
5. Running Terraform Commands

---

## Step 1: Prerequisites & Setup

### Install Required Tools

```bash
# Install Azure CLI
# Windows (PowerShell)
winget install Microsoft.AzureCLI

# macOS
brew install azure-cli

# Ubuntu/Debian
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Install Terraform
# Windows (PowerShell)
winget install Hashicorp.Terraform

# macOS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Ubuntu/Debian
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Verify installations
az --version
terraform --version
```

---

## Step 2: Azure Authentication Setup

### Login to Azure & Create Service Principal

```bash
# Login to Azure
az login

# List subscriptions
az account list --output table

# Set specific subscription
az account set --subscription "your-subscription-id"

# Create Service Principal for Terraform
az ad sp create-for-rbac \
  --name "terraform-sp" \
  --role="Contributor" \
  --scopes="/subscriptions/YOUR_SUBSCRIPTION_ID"
```

### Output from Service Principal (Save these values!)

```json
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",        # CLIENT_ID
  "displayName": "terraform-sp",
  "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",     # CLIENT_SECRET
  "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"        # TENANT_ID
}
```

---

## Step 3: Secrets Management (Multiple Methods)

### Method 1: Environment Variables (Recommended for CI/CD)

```bash
# Linux/macOS - Add to ~/.bashrc or ~/.zshrc
export ARM_CLIENT_ID="your-app-id"
export ARM_CLIENT_SECRET="your-password"
export ARM_SUBSCRIPTION_ID="your-subscription-id"
export ARM_TENANT_ID="your-tenant-id"

# Windows PowerShell
$env:ARM_CLIENT_ID="your-app-id"
$env:ARM_CLIENT_SECRET="your-password"
$env:ARM_SUBSCRIPTION_ID="your-subscription-id"
$env:ARM_TENANT_ID="your-tenant-id"

# Verify
echo $ARM_CLIENT_ID
```

### Method 2: terraform.tfvars (Local Development)

```bash
# Create terraform.tfvars file
cat > terraform.tfvars << EOF
client_id       = "your-app-id"
client_secret   = "your-password"
subscription_id = "your-subscription-id"
tenant_id       = "your-tenant-id"
admin_password  = "YourSecureP@ssword123!"
EOF

# IMPORTANT: Add to .gitignore
echo "terraform.tfvars" >> .gitignore
echo "*.tfstate" >> .gitignore
echo "*.tfstate.backup" >> .gitignore
echo ".terraform/" >> .gitignore
```

### Method 3: Azure Key Vault (Production Recommended)

```bash
# Create Key Vault
az keyvault create \
  --name "myterraformkv" \
  --resource-group "myResourceGroup" \
  --location "eastus"

# Store secrets in Key Vault
az keyvault secret set --vault-name "myterraformkv" --name "vm-admin-password" --value "YourSecureP@ssword123!"
az keyvault secret set --vault-name "myterraformkv" --name "client-secret" --value "your-client-secret"

# Retrieve secret (to verify)
az keyvault secret show --vault-name "myterraformkv" --name "vm-admin-password"
```

---

## Step 4: Project Structure

```
azure-vm-terraform/
├── main.tf                 # Main resources
├── variables.tf            # Variable declarations
├── outputs.tf              # Output values
├── providers.tf            # Provider configuration
├── terraform.tfvars        # Variable values (gitignored)
├── .gitignore              # Git ignore file
└── modules/
    └── vm/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

```bash
# Create project directory
mkdir azure-vm-terraform && cd azure-vm-terraform
```

---

## Step 5: Terraform Configuration Files

### File 1: providers.tf

```hcl
# providers.tf
terraform {
  required_version = ">= 1.0.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.0"
    }
  }

  # Optional: Remote State Backend (Recommended for teams)
  # backend "azurerm" {
  #   resource_group_name  = "tfstate-rg"
  #   storage_account_name = "tfstatestorageacct"
  #   container_name       = "tfstate"
  #   key                  = "terraform.tfstate"
  # }
}

provider "azurerm" {
  features {
    virtual_machine {
      delete_os_disk_on_deletion     = true
      graceful_shutdown              = false
      skip_shutdown_and_force_delete = false
    }
    key_vault {
      purge_soft_delete_on_destroy    = true
      recover_soft_deleted_key_vaults = true
    }
  }

  # Credentials from environment variables or tfvars
  client_id       = var.client_id
  client_secret   = var.client_secret
  subscription_id = var.subscription_id
  tenant_id       = var.tenant_id
}
```

### File 2: variables.tf

```hcl
# variables.tf

# ==========================================
# Authentication Variables
# ==========================================
variable "client_id" {
  description = "Azure Service Principal Client ID"
  type        = string
  sensitive   = true
}

variable "client_secret" {
  description = "Azure Service Principal Client Secret"
  type        = string
  sensitive   = true
}

variable "subscription_id" {
  description = "Azure Subscription ID"
  type        = string
  sensitive   = true
}

variable "tenant_id" {
  description = "Azure Tenant ID"
  type        = string
  sensitive   = true
}

# ==========================================
# Resource Variables
# ==========================================
variable "resource_group_name" {
  description = "Name of the Resource Group"
  type        = string
  default     = "myTerraformRG"
}

variable "location" {
  description = "Azure Region for resources"
  type        = string
  default     = "East US"

  validation {
    condition = contains([
      "East US", "East US 2", "West US", "West US 2",
      "Central US", "North Europe", "West Europe", "Southeast Asia"
    ], var.location)
    error_message = "Please provide a valid Azure region."
  }
}

variable "environment" {
  description = "Environment name (dev/staging/prod)"
  type        = string
  default     = "dev"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

# ==========================================
# Networking Variables
# ==========================================
variable "vnet_address_space" {
  description = "Virtual Network Address Space"
  type        = list(string)
  default     = ["10.0.0.0/16"]
}

variable "subnet_address_prefix" {
  description = "Subnet Address Prefix"
  type        = list(string)
  default     = ["10.0.1.0/24"]
}

# ==========================================
# Virtual Machine Variables
# ==========================================
variable "vm_name" {
  description = "Name of the Virtual Machine"
  type        = string
  default     = "myTerraformVM"
}

variable "vm_size" {
  description = "Size of the Virtual Machine"
  type        = string
  default     = "Standard_B2s"
}

variable "admin_username" {
  description = "Administrator username for the VM"
  type        = string
  default     = "adminuser"
}

variable "admin_password" {
  description = "Administrator password for the VM"
  type        = string
  sensitive   = true

  validation {
    condition     = length(var.admin_password) >= 12
    error_message = "Admin password must be at least 12 characters long."
  }
}

variable "os_disk_size_gb" {
  description = "OS Disk Size in GB"
  type        = number
  default     = 30
}

variable "os_disk_type" {
  description = "OS Disk Storage Account Type"
  type        = string
  default     = "Standard_LRS"
}

variable "vm_image" {
  description = "VM Image configuration"
  type = object({
    publisher = string
    offer     = string
    sku       = string
    version   = string
  })
  default = {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}

# ==========================================
# Tags
# ==========================================
variable "tags" {
  description = "Tags to apply to all resources"
  type        = map(string)
  default = {
    ManagedBy   = "Terraform"
    Environment = "dev"
    Project     = "AzureVM"
  }
}
```

### File 3: main.tf

```hcl
# main.tf

# ==========================================
# Random String for unique naming
# ==========================================
resource "random_string" "suffix" {
  length  = 6
  special = false
  upper   = false
}

# ==========================================
# Resource Group
# ==========================================
resource "azurerm_resource_group" "main" {
  name     = "${var.resource_group_name}-${var.environment}"
  location = var.location
  tags     = var.tags
}

# ==========================================
# Networking Resources
# ==========================================

# Virtual Network
resource "azurerm_virtual_network" "main" {
  name                = "vnet-${var.environment}-${random_string.suffix.result}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  address_space       = var.vnet_address_space
  tags                = var.tags
}

# Subnet
resource "azurerm_subnet" "main" {
  name                 = "subnet-${var.environment}"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = var.subnet_address_prefix
}

# Public IP Address
resource "azurerm_public_ip" "main" {
  name                = "pip-${var.vm_name}-${var.environment}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  allocation_method   = "Static"
  sku                 = "Standard"
  tags                = var.tags
}

# Network Security Group
resource "azurerm_network_security_group" "main" {
  name                = "nsg-${var.vm_name}-${var.environment}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  tags                = var.tags

  # Allow SSH
  security_rule {
    name                       = "Allow-SSH"
    priority                   = 1001
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "*"    # Restrict to your IP in production
    destination_address_prefix = "*"
  }

  # Allow HTTP
  security_rule {
    name                       = "Allow-HTTP"
    priority                   = 1002
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "80"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  # Allow HTTPS
  security_rule {
    name                       = "Allow-HTTPS"
    priority                   = 1003
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "443"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  # Deny all inbound (default - lowest priority)
  security_rule {
    name                       = "Deny-All-Inbound"
    priority                   = 4096
    direction                  = "Inbound"
    access                     = "Deny"
    protocol                   = "*"
    source_port_range          = "*"
    destination_port_range     = "*"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
}

# Network Interface
resource "azurerm_network_interface" "main" {
  name                = "nic-${var.vm_name}-${var.environment}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  tags                = var.tags

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.main.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.main.id
  }
}

# Associate NSG with Network Interface
resource "azurerm_network_interface_security_group_association" "main" {
  network_interface_id      = azurerm_network_interface.main.id
  network_security_group_id = azurerm_network_security_group.main.id
}

# ==========================================
# Key Vault for Secrets Management
# ==========================================

# Get current client config
data "azurerm_client_config" "current" {}

resource "azurerm_key_vault" "main" {
  name                        = "kv-${var.environment}-${random_string.suffix.result}"
  location                    = azurerm_resource_group.main.location
  resource_group_name         = azurerm_resource_group.main.name
  enabled_for_disk_encryption = true
  tenant_id                   = data.azurerm_client_config.current.tenant_id
  soft_delete_retention_days  = 7
  purge_protection_enabled    = false
  sku_name                    = "standard"
  tags                        = var.tags

  access_policy {
    tenant_id = data.azurerm_client_config.current.tenant_id
    object_id = data.azurerm_client_config.current.object_id

    key_permissions = [
      "Get", "List", "Create", "Delete", "Update"
    ]

    secret_permissions = [
      "Get", "List", "Set", "Delete", "Recover", "Backup", "Restore"
    ]

    storage_permissions = [
      "Get", "List"
    ]
  }
}

# Store VM Password in Key Vault
resource "azurerm_key_vault_secret" "vm_password" {
  name         = "vm-admin-password"
  value        = var.admin_password
  key_vault_id = azurerm_key_vault.main.id
  tags         = var.tags

  content_type = "password"

  # Optional: Set expiration
  # expiration_date = "2025-12-31T00:00:00Z"
}

# ==========================================
# Virtual Machine
# ==========================================
resource "azurerm_linux_virtual_machine" "main" {
  name                            = "${var.vm_name}-${var.environment}"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = var.vm_size
  admin_username                  = var.admin_username
  admin_password                  = var.admin_password
  disable_password_authentication = false

  network_interface_ids = [
    azurerm_network_interface.main.id
  ]

  os_disk {
    name                 = "osdisk-${var.vm_name}-${var.environment}"
    caching              = "ReadWrite"
    storage_account_type = var.os_disk_type
    disk_size_gb         = var.os_disk_size_gb
  }

  source_image_reference {
    publisher = var.vm_image.publisher
    offer     = var.vm_image.offer
    sku       = var.vm_image.sku
    version   = var.vm_image.version
  }

  tags = var.tags

  # Custom data (cloud-init script)
  custom_data = base64encode(<<-EOF
    #!/bin/bash
    sudo apt-get update -y
    sudo apt-get install -y nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx
    echo "<h1>Azure VM Created with Terraform!</h1>" | sudo tee /var/www/html/index.html
  EOF
  )
}

# ==========================================
# Managed Disk (Additional Data Disk)
# ==========================================
resource "azurerm_managed_disk" "main" {
  name                 = "datadisk-${var.vm_name}-${var.environment}"
  location             = azurerm_resource_group.main.location
  resource_group_name  = azurerm_resource_group.main.name
  storage_account_type = "Standard_LRS"
  create_option        = "Empty"
  disk_size_gb         = 10
  tags                 = var.tags
}

# Attach Data Disk to VM
resource "azurerm_virtual_machine_data_disk_attachment" "main" {
  managed_disk_id    = azurerm_managed_disk.main.id
  virtual_machine_id = azurerm_linux_virtual_machine.main.id
  lun                = "10"
  caching            = "ReadWrite"
}
```

### File 4: outputs.tf

```hcl
# outputs.tf

output "resource_group_name" {
  description = "Name of the Resource Group"
  value       = azurerm_resource_group.main.name
}

output "vm_name" {
  description = "Name of the Virtual Machine"
  value       = azurerm_linux_virtual_machine.main.name
}

output "vm_id" {
  description = "ID of the Virtual Machine"
  value       = azurerm_linux_virtual_machine.main.id
}

output "public_ip_address" {
  description = "Public IP Address of the VM"
  value       = azurerm_public_ip.main.ip_address
}

output "private_ip_address" {
  description = "Private IP Address of the VM"
  value       = azurerm_network_interface.main.private_ip_address
}

output "admin_username" {
  description = "Admin Username"
  value       = azurerm_linux_virtual_machine.main.admin_username
}

output "ssh_connection_command" {
  description = "SSH command to connect to the VM"
  value       = "ssh ${azurerm_linux_virtual_machine.main.admin_username}@${azurerm_public_ip.main.ip_address}"
}

output "key_vault_name" {
  description = "Name of the Key Vault"
  value       = azurerm_key_vault.main.name
}

output "key_vault_uri" {
  description = "URI of the Key Vault"
  value       = azurerm_key_vault.main.vault_uri
}

output "vm_password_secret_id" {
  description = "Key Vault Secret ID for VM Password"
  value       = azurerm_key_vault_secret.vm_password.id
  sensitive   = true
}
```

### File 5: terraform.tfvars

```hcl
# terraform.tfvars - DO NOT COMMIT TO GIT!

# Authentication
client_id       = "your-app-id-here"
client_secret   = "your-client-secret-here"
subscription_id = "your-subscription-id-here"
tenant_id       = "your-tenant-id-here"

# Resource Configuration
resource_group_name = "myTerraformRG"
location            = "East US"
environment         = "dev"

# VM Configuration
vm_name        = "myTerraformVM"
vm_size        = "Standard_B2s"
admin_username = "adminuser"
admin_password = "YourSecureP@ssword123!"

# Networking
vnet_address_space    = ["10.0.0.0/16"]
subnet_address_prefix = ["10.0.1.0/24"]

# OS Disk
os_disk_size_gb = 30
os_disk_type    = "Standard_LRS"

# VM Image (Ubuntu 22.04 LTS)
vm_image = {
  publisher = "Canonical"
  offer     = "0001-com-ubuntu-server-jammy"
  sku       = "22_04-lts"
  version   = "latest"
}

# Tags
tags = {
  ManagedBy   = "Terraform"
  Environment = "dev"
  Project     = "AzureVM"
  Owner       = "YourName"
}
```

### File 6: .gitignore

```gitignore
# .gitignore

# Terraform State Files
*.tfstate
*.tfstate.*
*.tfstate.backup

# Terraform Variables (Contains Secrets)
terraform.tfvars
*.tfvars
!example.tfvars

# Terraform Directories
.terraform/
.terraform.lock.hcl

# Crash logs
crash.log
crash.*.log

# Override files
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Sensitive files
*.pem
*.key
*.p12
secrets/
```

---

## Step 6: Running Terraform Commands

### Initialize Terraform

```bash
# Initialize - Downloads providers and modules
terraform init

# Expected output:
# Initializing the backend...
# Initializing provider plugins...
# - Installing hashicorp/azurerm v3.x.x
# Terraform has been successfully initialized!
```

### Validate Configuration

```bash
# Validate syntax and configuration
terraform validate

# Expected output:
# Success! The configuration is valid.

# If errors exist, fix them and re-validate
```

### Format Code

```bash
# Format all .tf files
terraform fmt

# Check formatting without changing
terraform fmt -check

# Format recursively
terraform fmt -recursive
```

### Terraform Plan

```bash
# See what will be created/changed/destroyed
terraform plan

# Save plan to file (recommended)
terraform plan -out=tfplan

# Plan with specific var file
terraform plan -var-file="terraform.tfvars"

# Plan with inline variable override
terraform plan -var="environment=prod" -var="vm_size=Standard_B4ms"

# Expected output:
# Plan: 12 to add, 0 to change, 0 to destroy.
```

### Terraform Apply

```bash
# Apply with saved plan (safest)
terraform apply tfplan

# Apply with auto-approval (for CI/CD)
terraform apply -auto-approve

# Apply with specific variables
terraform apply -var-file="prod.tfvars"

# Apply specific resource only
terraform apply -target=azurerm_linux_virtual_machine.main

# Expected output:
# Apply complete! Resources: 12 added, 0 changed, 0 destroyed.
# 
# Outputs:
# public_ip_address = "20.x.x.x"
# ssh_connection_command = "ssh adminuser@20.x.x.x"
```

### Verify Deployment

```bash
# Show current state
terraform show

# List all resources in state
terraform state list

# Show specific resource
terraform state show azurerm_linux_virtual_machine.main

# Show outputs
terraform output

# Show specific output
terraform output public_ip_address

# SSH into VM (after apply)
ssh adminuser@$(terraform output -raw public_ip_address)
```

### Terraform Destroy

```bash
# Destroy all resources
terraform destroy

# Destroy with auto-approval
terraform destroy -auto-approve

# Destroy specific resource
terraform destroy -target=azurerm_linux_virtual_machine.main

# Plan destroy first (to review)
terraform plan -destroy

# Expected output:
# Destroy complete! Resources: 12 destroyed.
```

---

## Step 7: Useful Additional Commands

```bash
# Refresh state (sync with actual Azure)
terraform refresh

# Import existing resource to state
terraform import azurerm_resource_group.main /subscriptions/xxx/resourceGroups/myRG

# Remove resource from state (without destroying)
terraform state rm azurerm_linux_virtual_machine.main

# Move resource in state
terraform state mv azurerm_linux_virtual_machine.main azurerm_linux_virtual_machine.new_name

# Get provider versions
terraform version

# Unlock state (if locked)
terraform force-unlock LOCK_ID

# Graph dependencies
terraform graph | dot -Tsvg > graph.svg
```

---

## Step 8: Complete Workflow Summary

```bash
# ============================
# COMPLETE WORKFLOW
# ============================

# 1. Login to Azure
az login

# 2. Set subscription
az account set --subscription "your-subscription-id"

# 3. Create Service Principal
az ad sp create-for-rbac --name "terraform-sp" --role="Contributor" \
  --scopes="/subscriptions/YOUR_SUBSCRIPTION_ID"

# 4. Update terraform.tfvars with credentials

# 5. Initialize Terraform
terraform init

# 6. Validate configuration
terraform validate

# 7. Format code
terraform fmt

# 8. Plan deployment
terraform plan -out=tfplan

# 9. Apply deployment
terraform apply tfplan

# 10. Verify outputs
terraform output

# 11. SSH into VM
ssh adminuser@$(terraform output -raw public_ip_address)

# 12. Destroy when done
terraform destroy -auto-approve
```

---

## Troubleshooting Common Issues

```bash
# Issue 1: Provider authentication error
# Fix: Check environment variables or tfvars
echo $ARM_CLIENT_ID

# Issue 2: State lock error
# Fix: Force unlock
terraform force-unlock <LOCK_ID>

# Issue 3: Resource already exists
# Fix: Import existing resource
terraform import azurerm_resource_group.main <RESOURCE_ID>

# Issue 4: Quota exceeded
# Check Azure quotas
az vm list-usage --location "East US" --output table

# Issue 5: Invalid provider version
# Fix: Remove lock file and reinitialize
rm .terraform.lock.hcl
terraform init -upgrade
```

This guide covers everything from setup to teardown! 🚀
