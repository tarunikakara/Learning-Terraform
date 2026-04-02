# Terraform on Azure: Complete Step-by-Step Guide

## 📚 Table of Contents
1. [Introduction](#introduction)
2. [Why Terraform?](#why-terraform)
3. [Key Concepts](#key-concepts)
4. [Installation](#installation)
5. [Directory Structure](#directory-structure)
6. [Real-World Examples](#real-world-examples)
7. [Common Commands](#common-commands)
8. [State Management](#state-management)
9. [Modules & Reusability](#modules--reusability)
10. [Troubleshooting](#troubleshooting)
11. [Best Practices](#best-practices)

---

## Introduction

Welcome to **Terraform on Azure**! This guide walks you through Infrastructure as Code (IaC) automation using Terraform.

Previously, infrastructure was created through:
- ❌ Manual Azure Portal clicks
- ❌ ARM templates (verbose, complex)
- ❌ PowerShell automation (limited reusability)

Now: ✅ **Terraform** - Write once, deploy anywhere!

### What is Terraform?
Terraform is an **Infrastructure as Code tool** that lets you:
- Define infrastructure in simple configuration files
- Deploy to multiple cloud providers
- Version control your infrastructure
- Replicate environments easily
- Destroy resources with one command

---

## Why Terraform?

### Traditional Approach
```
Developer → Packages App → Ops Team → Manual Deployment → Production
                         (WAR, EAR files)
```

### Infrastructure as Code (IaC) Approach
```
Developer → Writes Code → Version Control → CI/CD Pipeline → Auto Deploy
(Infrastructure)           (Git)          (Jenkins)
```

### Terraform Advantages

| Advantage | Benefit |
|-----------|---------|
| **Multi-Cloud** | Single syntax for AWS, Azure, GCP, Oracle |
| **Declarative** | Describe desired state, not steps |
| **Version Control** | Track infrastructure changes like code |
| **Reusable** | Modules for DRY principle |
| **Safe** | Plan before apply (`terraform plan`) |
| **Easy Rollback** | Previous state always available |
| **Team Friendly** | Collaborative infrastructure management |

### Cloud Providers Supported
Terraform uses **providers** (plugins) for cloud integration:

```
azurerm  → Microsoft Azure
aws      → Amazon Web Services
google   → Google Cloud Platform
oci      → Oracle Cloud Infrastructure
alicloud → Alibaba Cloud
```

---

## Key Concepts

### 1. **Resources**
Actual cloud infrastructure components you want to create.

```hcl
# Example: Azure Resource Group
resource "azurerm_resource_group" "example" {
  name     = "my-resource-group"
  location = "East US"
}
```

### 2. **Data Blocks**
Reference resources created **outside** Terraform (via CLI, portal, etc.).

```hcl
# Example: Reference existing storage account
data "azurerm_storage_account" "existing" {
  name                = "mystorageaccount"
  resource_group_name = "existing-rg"
}
```

### 3. **Variables (`variables.tf`)**
Input values for reusability across environments.

```hcl
variable "environment" {
  type        = string
  description = "Environment name"
  default     = "dev"
}

variable "location" {
  type = string
  default = "East US"
}
```

### 4. **Outputs (`outputs.tf`)**
Export values after deployment (useful for scripts, other modules).

```hcl
output "storage_account_id" {
  value       = azurerm_storage_account.main.id
  description = "Storage account resource ID"
  sensitive   = false
}
```

### 5. **State File (`terraform.tfstate`)**
Tracks current infrastructure state vs. desired state.

```json
{
  "version": 4,
  "terraform_version": "1.5.0",
  "resources": [
    {
      "type": "azurerm_resource_group",
      "name": "example",
      "instances": [
        {
          "attributes": {
            "id": "/subscriptions/xxx/resourceGroups/my-rg",
            "location": "eastus"
          }
        }
      ]
    }
  ]
}
```

**⚠️ Important**: Never commit `terraform.tfstate` to Git (contains secrets).

### 6. **Backends**
Where to store state files (local, Azure Blob, S3, etc.).

```hcl
# Example: Store state in Azure Blob
terraform {
  backend "azurerm" {
    resource_group_name  = "tf-state-rg"
    storage_account_name = "tfstate"
    container_name       = "tfstate"
    key                  = "prod.tfstate"
  }
}
```

### 7. **Provisioners**
Configure resources after creation (like Chef, Puppet).

```hcl
# Example: Run script after VM creation
resource "azurerm_virtual_machine" "example" {
  # ... resource config ...

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
  }
}
```

### 8. **Modules**
Reusable code blocks for consistent deployments.

```
my-terraform-project/
├── main.tf
├── modules/
│   ├── networking/
│   │   ├── main.tf
│   │   └── variables.tf
│   ├── storage/
│   │   ├── main.tf
│   │   └── variables.tf
│   └── compute/
│       └── main.tf
```

### 9. **Service Principal**
Azure identity used by Terraform to authenticate.

```bash
az ad sp create-for-rbac --name terraform-sp
# Returns:
# {
#   "appId": "xxx",
#   "displayName": "terraform-sp",
#   "password": "xxx",
#   "tenant": "xxx"
# }
```

---

## Installation

### Step 1: Install Terraform

**Windows:**
1. Visit: https://www.terraform.io/downloads.html
2. Download **Windows amd64** version
3. Extract the `.zip` file
4. Add executable path to System Environment Variables:
   ```
   C:\terraform\
   ```
5. Verify installation:
   ```powershell
   terraform --version
   # Output: Terraform v1.5.0
   ```

**macOS:**
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
terraform --version
```

**Linux:**
```bash
wget https://releases.hashicorp.com/terraform/1.5.0/terraform_1.5.0_linux_amd64.zip
unzip terraform_1.5.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform --version
```

### Step 2: Install Azure CLI

**Windows:**
1. Download: https://aka.ms/azurecli
2. Run installer
3. Restart terminal

**Verify:**
```bash
az --version
az login
```

### Step 3: Install Visual Studio Code

1. Download: https://code.visualstudio.com/
2. Install recommended plugins:
   - Terraform (HashiCorp)
   - Azure Tools
   - Git Graph

---

## Directory Structure

```
my-terraform-project/
│
├── main.tf              # Main resource definitions
├── variables.tf         # Input variables
├── outputs.tf           # Output values
├── provider.tf          # Provider configuration
├── terraform.tfvars     # Variable values (local)
├── .gitignore           # Ignore state files
│
├── modules/
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── storage/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── compute/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
│
├── environments/
│   ├── dev.tfvars
│   ├── staging.tfvars
│   └── prod.tfvars
│
└── docs/
    └── README.md
```

---

## Real-World Examples

### Example 1: Create Resource Group + Storage Account

**`provider.tf`**
```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
  subscription_id = var.subscription_id
}
```

**`variables.tf`**
```hcl
variable "subscription_id" {
  type        = string
  description = "Azure subscription ID"
}

variable "environment" {
  type    = string
  default = "dev"
}

variable "location" {
  type    = string
  default = "East US"
}

variable "storage_account_name" {
  type        = string
  description = "Name of storage account (must be globally unique)"
}
```

**`main.tf`**
```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.environment}-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = var.storage_account_name
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}
```

**`outputs.tf`**
```hcl
output "resource_group_id" {
  value = azurerm_resource_group.main.id
}

output "storage_account_id" {
  value = azurerm_storage_account.main.id
}

output "storage_account_url" {
  value = azurerm_storage_account.main.primary_blob_endpoint
}
```

**`terraform.tfvars`**
```hcl
subscription_id        = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
environment            = "dev"
location               = "East US"
storage_account_name   = "ystoragedev001"
```

**Execution:**
```bash
# Install provider plugins
terraform init

# Show what will be created (DRY RUN)
terraform plan

# Create resources
terraform apply

# Destroy resources
terraform destroy
```

---

### Example 2: Create Virtual Machine with Network

**`main.tf`**
```hcl
# Resource Group
resource "azurerm_resource_group" "main" {
  name     = "vm-rg"
  location = "East US"
}

# Virtual Network
resource "azurerm_virtual_network" "main" {
  name                = "vnet-main"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
}

# Subnet
resource "azurerm_subnet" "main" {
  name                 = "subnet-main"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Network Interface
resource "azurerm_network_interface" "main" {
  name                = "nic-main"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name

  ip_configuration {
    name                          = "testconfiguration1"
    subnet_id                     = azurerm_subnet.main.id
    private_ip_address_allocation = "Dynamic"
  }
}

# Virtual Machine
resource "azurerm_virtual_machine" "main" {
  name                  = "vm-main"
  location              = azurerm_resource_group.main.location
  resource_group_name   = azurerm_resource_group.main.name
  vm_size               = "Standard_B1s"
  network_interface_ids = [azurerm_network_interface.main.id]

  os_profile {
    computer_name  = "hostname"
    admin_username = "azureuser"
    admin_password = "P@ssw0rd1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  storage_os_disk {
    name              = "osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Premium_LRS"
  }

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "Latest"
  }
}
```

---

### Example 3: Using Modules for Reusability

**`modules/storage/main.tf`**
```hcl
resource "azurerm_storage_account" "main" {
  name                     = var.storage_account_name
  resource_group_name      = var.resource_group_name
  location                 = var.location
  account_tier             = var.account_tier
  account_replication_type = var.replication_type
}

resource "azurerm_storage_container" "main" {
  name                  = var.container_name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "private"
}
```

**`modules/storage/variables.tf`**
```hcl
variable "storage_account_name" {
  type = string
}

variable "resource_group_name" {
  type = string
}

variable "location" {
  type = string
}

variable "account_tier" {
  type    = string
  default = "Standard"
}

variable "replication_type" {
  type    = string
  default = "LRS"
}

variable "container_name" {
  type = string
}
```

**`main.tf` (using module)**
```hcl
module "dev_storage" {
  source = "./modules/storage"
  
  storage_account_name = "devstorageacct"
  resource_group_name  = azurerm_resource_group.main.name
  location             = "East US"
  container_name       = "dev-container"
}

module "prod_storage" {
  source = "./modules/storage"
  
  storage_account_name = "prodstorageacct"
  resource_group_name  = azurerm_resource_group.main.name
  location             = "East US"
  container_name       = "prod-container"
  account_tier         = "Premium"
}
```

---

## Common Commands

### Initialization
```bash
# Initialize Terraform (download providers)
terraform init

# Upgrade provider versions
terraform init -upgrade
```

### Planning & Validation
```bash
# Show what will change (dry run)
terraform plan

# Save plan to file
terraform plan -out=tfplan

# Validate syntax
terraform validate

# Format code
terraform fmt -recursive
```

### Applying Changes
```bash
# Apply planned changes
terraform apply

# Apply with auto-approval (use carefully!)
terraform apply -auto-approve

# Apply specific plan
terraform apply tfplan
```

### State Management
```bash
# Show current state
terraform show

# List all resources
terraform state list

# Show specific resource
terraform state show azurerm_resource_group.main

# Refresh state (sync with actual Azure)
terraform refresh
```

### Destroying
```bash
# Destroy all resources
terraform destroy

# Destroy specific resource
terraform destroy -target azurerm_resource_group.main

# Auto-approve destruction (risky!)
terraform destroy -auto-approve
```

---

## State Management

### Why State Matters

State file tracks:
- What resources exist
- Their current configuration
- Dependencies between resources

```
Current State (terraform.tfstate)
↓
Compare with Desired State (your .tf files)
↓
Terraform calculates changes
↓
Shows what will add/modify/delete
```

### Protecting State File

**`.gitignore`**
```
# Ignore state files
terraform.tfstate
terraform.tfstate.*
crash.log

# Ignore override files
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Ignore CLI configuration files
.terraformrc
terraform.rc

# Ignore variable files
*.auto.tfvars
```

### Remote State (Team Collaboration)

**Using Azure Blob for state:**

1. Create storage account:
```bash
az storage account create \
  --name tfstate \
  --resource-group tf-state-rg \
  --location eastus
```

2. Create container:
```bash
az storage container create \
  --name tfstate \
  --account-name tfstate
```

3. Configure backend in `main.tf`:
```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "tf-state-rg"
    storage_account_name = "tfstate"
    container_name       = "tfstate"
    key                  = "prod.tfstate"
  }
}
```

4. Initialize:
```bash
terraform init
```

---

## Modules & Reusability

### Module Structure
```
modules/
├── web_tier/
│   ├── main.tf              # Resources
│   ├── variables.tf         # Inputs
│   ├── outputs.tf           # Outputs
│   └── README.md            # Documentation
├── database_tier/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
└── networking/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

### Using Modules Across Environments

**`environments/dev.tf`**
```hcl
module "dev_infrastructure" {
  source = "../modules/web_tier"
  
  environment = "dev"
  location    = "East US"
  vm_count    = 1
  vm_size     = "Standard_B1s"
}
```

**`environments/prod.tf`**
```hcl
module "prod_infrastructure" {
  source = "../modules/web_tier"
  
  environment = "prod"
  location    = "East US"
  vm_count    = 3
  vm_size     = "Standard_D2s_v3"  # More powerful
}
```

---

## Troubleshooting

### Common Issues

#### 1. **Authentication Error**
```
Error: Error: Authenticating to Azure... 
```

**Solution:**
```bash
az login
az account show  # Verify subscription
```

#### 2. **State Lock Error**
```
Error: Error acquiring the state lock: ConflictError...
```

**Solution:**
```bash
# Unlock state
terraform force-unlock <lock-id>

# Check locks
terraform state list
```

#### 3. **Provider Not Found**
```
Error: Could not retrieve the list of available versions for provider...
```

**Solution:**
```bash
# Reinitialize
rm -rf .terraform
terraform init
```

#### 4. **Resource Already Exists**
```
Error: Code="ResourceExistsError"
```

**Solution:**
```bash
# Import existing resource
terraform import azurerm_resource_group.main /subscriptions/xxx/resourceGroups/my-rg
```

#### 5. **Syntax Error**
```
Error: Unsupported block type...
```

**Solution:**
```bash
terraform fmt -recursive  # Format code
terraform validate       # Check syntax
```

---

## Best Practices

### 1. **Use Remote State**
```hcl
terraform {
  backend "azurerm" {
    # Enables team collaboration
  }
}
```

### 2. **Separate Variables by Environment**
```
environments/
├── dev.tfvars
├── staging.tfvars
└── prod.tfvars
```

### 3. **Use Data Blocks for Existing Resources**
```hcl
# Don't redefine, reference existing
data "azurerm_resource_group" "existing" {
  name = "my-existing-rg"
}
```

### 4. **Implement Consistent Naming**
```hcl
variable "environment" {
  type    = string
  default = "dev"
}

resource "azurerm_resource_group" "main" {
  name = "${var.environment}-${var.project}-rg"
  # Example output: dev-myapp-rg
}
```

### 5. **Use Workspaces for Multiple Environments**
```bash
# Create workspace
terraform workspace new prod

# Switch workspace
terraform workspace select prod

# List workspaces
terraform workspace list
```

### 6. **Implement Cost Controls**
```hcl
# Use smaller sizes in dev
variable "vm_size" {
  type    = string
  default = var.environment == "prod" ? "Standard_D2s_v3" : "Standard_B1s"
}
```

### 7. **Version Lock Providers**
```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.50"  # Pin major.minor version
    }
  }
}
```

### 8. **Use Count or For_Each for Multiple Resources**

**Using `count`:**
```hcl
resource "azurerm_virtual_machine" "main" {
  count = var.vm_count
  
  name = "${var.environment}-vm-${count.index + 1}"
  # Creates: dev-vm-1, dev-vm-2, dev-vm-3
}
```

**Using `for_each`:**
```hcl
variable "subnets" {
  type = map(object({
    prefix = string
  }))
  default = {
    web = { prefix = "10.0.1.0/24" }
    db  = { prefix = "10.0.2.0/24" }
    app = { prefix = "10.0.3.0/24" }
  }
}

resource "azurerm_subnet" "main" {
  for_each = var.subnets
  
  name = "${each.key}-subnet"
  # Creates: web-subnet, db-subnet, app-subnet
}
```

### 9. **Add Descriptions to Variables**
```hcl
variable "environment" {
  type        = string
  description = "Environment name (dev, staging, prod)"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

### 10. **Document with Comments and README**
```hcl
# Create resource group for all shared resources
# This is the foundational resource for the environment
resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location
}
```

---

## Step-by-Step Demo Walkthrough

### Hands-On: Deploy Storage Account

1. **Create folder structure:**
```bash
mkdir my-terraform-demo
cd my-terraform-demo
```

2. **Create `provider.tf`:**
```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

3. **Create `main.tf`:**
```hcl
resource "azurerm_resource_group" "main" {
  name     = "demo-rg"
  location = "East US"
}

resource "azurerm_storage_account" "main" {
  name                     = "demostg${random_string.suffix.result}"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "random_string" "suffix" {
  length  = 8
  special = false
}
```

4. **Create `outputs.tf`:**
```hcl
output "storage_account_name" {
  value = azurerm_storage_account.main.name
}

output "resource_group_id" {
  value = azurerm_resource_group.main.id
}
```

5. **Execute:**
```bash
# Login to Azure
az login

# Initialize
terraform init

# Plan
terraform plan

# Apply
terraform apply

# View outputs
terraform output

# Show state
terraform state show azurerm_storage_account.main

# Cleanup
terraform destroy
```

6. **Verify in Azure Portal:**
- Go to https://portal.azure.com
- Search "Resource Groups"
- Find "demo-rg"
- Verify storage account created inside

---

## Additional Resources

### Official Documentation
- **Terraform Docs**: https://www.terraform.io/docs
- **Azure Provider**: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs
- **Terraform Registry**: https://registry.terraform.io/

### Learning Resources
- **HashiCorp Learn**: https://learn.hashicorp.com/
- **Terraform Examples**: https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples
- **Azure Terraform Modules**: https://github.com/Azure/terraform-azurerm-examples

### Tools & Extensions
- **Terraform VS Code Plugin**: HashiCorp Terraform
- **Azure CLI**: https://docs.microsoft.com/cli/azure/
- **Azure Portal**: https://portal.azure.com

---

## Summary

| Concept | Purpose | Example |
|---------|---------|---------|
| **Provider** | Cloud connection | `azurerm` for Azure |
| **Resources** | Infrastructure components | `azurerm_virtual_machine` |
| **Variables** | Input parameters | Environment, location |
| **Outputs** | Export values | Resource IDs, URLs |
| **State** | Track current infrastructure | `terraform.tfstate` |
| **Modules** | Reusable code blocks | `modules/networking/` |
| **Backend** | State file storage | Azure Blob Storage |

### Key Takeaways

✅ **Infrastructure as Code** - Define infrastructure like application code  
✅ **Multi-Cloud** - Same syntax for AWS, Azure, GCP  
✅ **Collaborative** - Version controlled, team-friendly  
✅ **Safe** - Plan before applying changes  
✅ **Scalable** - Modules enable consistency  
✅ **Efficient** - Destroy/recreate environments instantly  

---

## Getting Started Checklist

- [ ] Install Terraform
- [ ] Install Azure CLI
- [ ] Login to Azure (`az login`)
- [ ] Clone example repository
- [ ] Run `terraform init` → `terraform plan` → `terraform apply`
- [ ] Verify resources in Azure Portal
- [ ] Run `terraform destroy` to cleanup
- [ ] Study module structure
- [ ] Practice with own infrastructure

---

**Happy Infrastructure as Code learning! 🚀**

For questions, refer to:
- Official Terraform documentation
- Azure Provider documentation
- GitHub issues and discussions
