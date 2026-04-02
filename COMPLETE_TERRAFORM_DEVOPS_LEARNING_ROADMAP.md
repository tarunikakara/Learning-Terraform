# Complete Terraform + DevOps Learning Roadmap

## Overview

This roadmap consolidates all 4 Terraform learning topics into a structured learning path with clear progression from basics to advanced CI/CD automation.

---

## Learning Progression Path

```
LEVEL 1 (Basics)
↓
Terraform on Azure
↓
Terraform on AWS
↓
LEVEL 2 (Platform & State Management)
↓
Azure DevOps Pipeline + Terraform
↓
LEVEL 3 (Advanced CI/CD)
↓
Jenkins Pipeline + Terraform + Azure
```

---

## Comprehensive Learning Roadmap Table

| # | Topic | What You Learn | Key Tools | Key Concepts | Important Commands | Cloud Platform |
|---|-------|----------------|-----------|--------------|-------------------|-----------------|
| 1 | **Terraform on Azure** | Clone Terraform from GitHub, configure Azure authentication via Service Principal, run Terraform locally, store state in Azure Storage, create VNet/subnets, assign IAM permissions | VS Code, GitHub, Azure CLI, Service Principal, Azure Storage | Provider configuration, variables, remote backend, IAM, Resource Group, VNet, Subnets | `terraform init`, `terraform plan`, `terraform apply`, `terraform destroy` | Azure |
| 2 | **Terraform on AWS** | Write Terraform for AWS, create IAM user with access keys, attach policies, create VPC/EC2/S3, manage credentials, destroy resources | AWS Console, IAM, EC2, S3, Access Keys | IAM authentication, VPC networking, Security Groups, Key Pairs, EC2 instances, credential management best practices | `terraform init`, `terraform validate`, `terraform plan`, `terraform apply`, `terraform destroy` | AWS |
| 3 | **Azure DevOps Pipeline + Terraform** | Automate Terraform execution through CI/CD, integrate GitHub with Azure DevOps, use Azure-hosted agents, configure service connections, run Terraform stages in pipeline, demonstrate idempotency | Azure DevOps, GitHub, Pipeline Agent, Terraform Extension, Azure Storage | Pipelines, agents, service connections, backend configuration, idempotency, automation, remote state | Push to GitHub → Pipeline auto-runs → stages execute sequentially | Azure |
| 4 | **Jenkins Pipeline + Terraform + Azure** | Install Jenkins on Azure VM, integrate GitHub and Azure credentials, create declarative pipelines, run Terraform stages through Jenkins, automate infrastructure creation/destruction | Jenkins, Azure VM, Linux, GitHub, Service Principal, Terraform Plugin, Azure Credentials Plugin | Jenkins server setup, credentials management, plugins, pipeline stages, Linux server administration, credential security | Pipeline checkout → init → validate → plan → apply → manual destroy | Azure |

---

## Detailed Topic Comparison

### Topic 1: Terraform on Azure

**Duration to Learn:** 1-2 hours
**Difficulty Level:** Beginner
**Prerequisites:** Azure account, GitHub account

**Learning Outcomes:**
- Install and configure Terraform
- Authenticate with Azure using Service Principal
- Write basic Terraform code for networking
- Understand remote state concept
- Manage Python variables and outputs

**Step-by-Step Flow:**
```
1. Clone Terraform repo from GitHub
2. Configure Azure CLI credentials
3. Create Service Principal in Azure
4. Assign Contributor role at subscription level
5. Edit provider.tf with credentials
6. Run terraform init (initializes backend)
7. Run terraform plan (preview)
8. Run terraform apply (creates resources)
9. Verify in Azure portal
10. Store state in Azure Storage container
```

**Files Used:**
- `main.tf` - Infrastructure definitions
- `variables.tf` - Input variables
- `terraform.tfvars` - Variable values
- `providers.tf` - Provider configuration
- `terraform.tfstate` - State file (in Azure Storage)

**Key Authentication Component:**
```
Service Principal:
├── Client ID
├── Client Secret
├── Tenant ID
└── Subscription ID
```

---

### Topic 2: Terraform on AWS

**Duration to Learn:** 1-2 hours
**Difficulty Level:** Beginner
**Prerequisites:** AWS account, GitHub account

**Learning Outcomes:**
- Understand AWS IAM and access keys
- Create Terraform code for AWS resources
- Manage credentials securely (important!)
- Create VPC, EC2, S3 infrastructure
- Practice resource creation and destruction

**Step-by-Step Flow:**
```
1. Create IAM user in AWS
2. Generate access key and secret key
3. Assign policies:
   - AdministratorAccess (or specific policies)
   - AmazonEC2FullAccess
   - AmazonS3FullAccess
4. Configure provider with credentials
5. Run terraform init
6. Run terraform validate (new!)
7. Run terraform plan
8. Run terraform apply
9. Verify EC2/VPC/S3 in AWS Console
10. Run terraform destroy
```

**Files Used:**
- `main.tf` - VPC, EC2, Security Group, S3
- `provider.tf` - AWS provider configuration
- `variables.tf` - Input variables

**Key Authentication Component:**
```
IAM User:
├── Access Key ID
├── Secret Access Key
└── Policies (permissions)
```

**Important Learning:**
- Do NOT hardcode credentials in production
- Use environment variables or credential files
- Practice secure credential handling

---

### Topic 3: Azure DevOps Pipeline + Terraform

**Duration to Learn:** 2-3 hours
**Difficulty Level:** Intermediate
**Prerequisites:** Complete Topics 1 & 2

**Learning Outcomes:**
- Understand CI/CD pipeline concept
- Integrate GitHub with Azure DevOps
- Automate Terraform execution
- Configure service connections
- Learn pipeline stages and tasks
- Understand Terraform idempotency
- Practice infrastructure lifecycle management

**Step-by-Step Flow:**
```
1. Create backend in Azure (manually):
   - Resource Group
   - Storage Account
   - Container (tfstate)
   
2. In Azure DevOps Organization:
   - Install Terraform extension from Marketplace
   - Install Sonar extension (optional)
   
3. In Azure DevOps Project:
   - Create service connections:
     * GitHub (PAT authentication)
     * Azure subscription (service principal)
   
4. Create new Pipeline:
   - Select Azure-hosted agent (Windows)
   - Point to GitHub repository
   - Select branch (master/main)
   
5. Add pipeline tasks:
   - Terraform Tool Installer
   - Terraform Init (with backend config)
   - Terraform Validate
   - Terraform Plan
   - Terraform Apply
   
6. Configure backend parameters:
   - Resource Group: terraform
   - Storage Account: xxx
   - Container: tfstate
   - State key: terraform.tfstate
   
7. Run pipeline:
   - Monitor execution
   - Check logs
   - Verify resources in Azure
   
8. Enable Destroy task:
   - Run pipeline with destroy
   - Verify cleanup
```

**Key Pipeline Stages:**
```
Trigger (GitHub checkout) →
Tool Installer →
Terraform Init →
Terraform Plan →
Terraform Validate →
Terraform Apply →
Infrastructure Created
```

**Important Concepts:**
- **Idempotency:** If infrastructure already exists and matches code, Terraform makes no changes
- **Service Connection:** Secure link from Azure DevOps to GitHub and Azure
- **Azure-hosted Agent:** Microsoft manages the build machine
- **Remote Backend:** State file stored centrally in Azure Storage

---

### Topic 4: Jenkins Pipeline + Terraform + Azure

**Duration to Learn:** 3-4 hours
**Difficulty Level:** Advanced
**Prerequisites:** Complete Topics 1, 2, & 3

**Learning Outcomes:**
- Install and configure Jenkins on Azure VM
- Integrate Jenkins with GitHub
- Store and use Azure credentials in Jenkins
- Install Jenkins plugins for Terraform
- Create declarative pipelines
- Understand Jenkins credential management
- Practice Linux server administration
- Learn production-ready automation

**Step-by-Step Flow:**
```
1. Create Azure VM (Linux):
   - Choose Ubuntu or CentOS
   - Open ports 22 (SSH), 8080 (Jenkins)
   
2. Connect to VM and install:
   - Java/JDK (requirement for Jenkins)
   - Jenkins
   - Terraform
   - Azure CLI
   - Git
   
3. Create Azure backend (manually):
   - Resource Group: terraform
   - Storage Account
   - Container: tfstate
   
4. Configure Azure Service Principal:
   - Create in Azure
   - Grant Contributor access
   - Note: Client ID, Secret, Tenant, Subscription
   
5. Start Jenkins:
   - Access via http://vm-ip:8080
   - Unlock with initial admin password
   
6. Install Jenkins Plugins:
   - Plugin Manager → Available
   - Install:
     * Terraform plugin
     * Azure Credentials plugin
     * GitHub plugin
     * Pipeline plugin
   
7. Configure Jenkins Credentials:
   - Manage Jenkins → Credentials
   - Add:
     * GitHub personal access token
     * Azure Service Principal (JSON format)
   
8. Create Jenkins Pipeline Project:
   - New Item → Pipeline
   
9. Configure GitHub Integration:
   - Repository URL (GitHub SSH or HTTPS)
   - Branch: master/main
   - Credentials: GitHub token
   
10. Define Pipeline Stages (Declarative):
    - Checkout (from GitHub)
    - Terraform Init (with backend)
    - Terraform Validate
    - Terraform Plan
    - Terraform Apply
    - Terraform Destroy
    
11. Run Pipeline:
    - Build Now
    - Monitor in real-time
    - Check logs
    
12. Verify Infrastructure:
    - Check Azure portal
    - Confirm VNet/subnets created
    
13. Run Destroy:
    - Trigger destroy stage
    - Verify cleanup
```

**Jenkins Pipeline Structure:**
```
pipeline {
  agent any
  
  environment {
    // Define credentials
  }
  
  stages {
    stage('Checkout') { }
    stage('Terraform Init') { }
    stage('Terraform Validate') { }
    stage('Terraform Plan') { }
    stage('Terraform Apply') { }
    stage('Terraform Destroy') { }
  }
  
  post {
    // Cleanup actions
  }
}
```

**Key Components:**
```
Jenkins Server (Azure VM)
├── Jenkins application
├── Terraform
├── Azure CLI
├── Git
├── Plugins (Terraform, Azure, GitHub)
└── Credentials (GitHub, Azure Service Principal)
```

**Production Considerations:**
- Secure credential storage
- Audit logging
- Pipeline approval steps
- Parameter-driven execution
- Error handling and notifications

---

## Comparison Matrix: All 4 Topics

| Aspect | Topic 1: Azure | Topic 2: AWS | Topic 3: Azure DevOps | Topic 4: Jenkins |
|--------|---|---|---|---|
| **Execution** | Manual (local) | Manual (local) | Automated (DevOps) | Automated (Jenkins) |
| **Server Setup** | Local machine | Local machine | Azure DevOps Service | Azure VM (self-managed) |
| **Code Source** | GitHub | GitHub | GitHub | GitHub |
| **State Storage** | Azure Storage | Local or S3 | Azure Storage | Azure Storage |
| **Authentication** | Service Principal | IAM access keys | Service connections | Jenkins credentials + Service Principal |
| **Programming** | Terraform code | Terraform code | Pipeline YAML | Declarative Pipeline |
| **Learning Curve** | Beginner | Beginner | Intermediate | Advanced |
| **Real-world Use** | Dev/Test environments | Dev/Test environments | Team workflows | Enterprise automation |
| **Scalability** | Limited | Limited | Good (managed service) | Excellent (enterprise) |
| **Cost** | Low | Low | Medium | Medium-High |

---

## Command Progression Across Topics

### Topic 1 & 2 (Local Execution)
```bash
terraform init          # Initialize working directory
terraform validate      # Check syntax (AWS)
terraform plan          # Preview changes
terraform apply         # Create resources
terraform destroy       # Delete resources
```

### Topic 3 (Azure DevOps Pipeline)
```yaml
# Pipeline tasks instead of manual commands
- TerraformToolInstaller@0
- TerraformTaskV2@2
  inputs:
    command: init
    backendServiceArm: azure-subscription
```

### Topic 4 (Jenkins Pipeline)
```groovy
// Jenkins Declarative Pipeline
stage('Terraform Init') {
  steps {
    sh 'terraform init ...'
  }
}
```

---

## Authentication/Credential Progression

```
Topic 1: Azure
└── Service Principal: Client ID + Secret + Tenant + Subscription

Topic 2: AWS
└── IAM User: Access Key + Secret Key

Topic 3: Azure DevOps
├── GitHub: Personal Access Token
└── Azure: Service connections

Topic 4: Jenkins
├── GitHub: Personal Access Token (stored in Jenkins)
└── Azure: Service Principal (stored in Jenkins as credentials)
```

---

## Real-World Application Flow

```
COMPLETE END-TO-END WORKFLOW:

Developer writes Terraform code
         ↓
Commits to GitHub
         ↓
Jenkins Pipeline Triggered (Topic 4)
         ↓
Checkout from GitHub
         ↓
Run Terraform Init (with backend in Azure Storage)
         ↓
Run Terraform Plan (show proposed changes)
         ↓
Run Terraform Apply (create infrastructure)
         ↓
Jenkins logs execution
         ↓
Infrastructure created in Azure
         ↓
Teams verify resources
         ↓
When done: Run Terraform Destroy
         ↓
Infrastructure cleaned up
         ↓
State file updated in Azure Storage
```

---

## Recommended Study Schedule

### Week 1: Topics 1 & 2 (Foundation)
- **Days 1-3:** Terraform on Azure (manual execution)
- **Days 4-5:** Terraform on AWS (manual execution)
- **Days 6-7:** Practice and compare

### Week 2: Topic 3 (Pipeline Automation)
- **Days 1-3:** Azure DevOps Pipeline + Terraform (understanding Azure DevOps)
- **Days 4-5:** Service connections and backend setup
- **Days 6-7:** Practice end-to-end pipeline

### Week 3: Topic 4 (Advanced Automation)
- **Days 1-3:** Jenkins setup on Azure VM
- **Days 4-5:** Plugins installation and credential management
- **Days 6-7:** Pipeline creation and execution

### Week 4: Integration & Practice
- **Days 1-3:** Combine all topics in practice lab
- **Days 4-5:** Interview questions and deep concepts
- **Days 6-7:** Real-world scenario simulations

---

## Key Differences Summary

### Manual vs Automated
- **Topics 1 & 2:** You run commands manually
- **Topics 3 & 4:** Pipelines run automatically when code changes

### Single Cloud vs Multi-Cloud Skills
- **Topic 1:** Azure only
- **Topic 2:** AWS only (demonstrates multi-cloud capability)
- **Topics 3 & 4:** Azure focus, but concepts apply to any cloud

### Scalability
- **Topic 1 & 2:** Good for learning, but doesn't scale
- **Topic 3:** Azure DevOps managed service (scales well)
- **Topic 4:** Jenkins self-managed (maximum control and customization)

---

## Interview Preparation by Topic

### Topic 1 Interview Q&A
Q: What is Service Principal?
A: An identity in Azure that allows applications like Terraform to authenticate and create resources.

Q: Why store state in Azure Storage?
A: To keep state centralized, safe, and accessible to teams.

### Topic 2 Interview Q&A
Q: Why should we not hardcode AWS credentials?
A: It's a security risk. Use environment variables or IAM roles instead.

Q: What are the key AWS permissions for Terraform?
A: EC2FullAccess, S3FullAccess, VPCFullAccess, and related policies.

### Topic 3 Interview Q&A
Q: What is a service connection?
A: A secure link from Azure DevOps to external systems like GitHub or Azure subscriptions.

Q: What is Terraform idempotency?
A: The ability for Terraform to safely run multiple times without creating duplicate resources.

### Topic 4 Interview Q&A
Q: Why Jenkins instead of Azure DevOps?
A: Jenkins offers more customization and control, and runs on your infrastructure.

Q: How are credentials secured in Jenkins?
A: Jenkins encrypts stored credentials and manages them securely in its credential store.

---

## Practical Lab Exercises

### Lab 1: (After Topic 1)
- [ ] Create VNet with 3 subnets in Azure using Terraform
- [ ] Store state in Azure Storage
- [ ] Modify and reapply
- [ ] Destroy resources

### Lab 2: (After Topic 2)
- [ ] Create VPC with 2 subnets in AWS using Terraform
- [ ] Create an EC2 instance
- [ ] Create an S3 bucket
- [ ] Destroy all resources

### Lab 3: (After Topic 3)
- [ ] Set up Azure DevOps pipeline
- [ ] Configure GitHub integration
- [ ] Create Azure VNet through pipeline
- [ ] Verify resources automatic creation
- [ ] Run destroy pipeline

### Lab 4: (After Topic 4)
- [ ] Install Jenkins on Azure VM
- [ ] Configure GitHub and Azure credentials
- [ ] Create declarative pipeline
- [ ] Trigger pipeline from GitHub push
- [ ] Verify infrastructure creation
- [ ] Practice destroy through pipeline

---

## Tools You'll Learn

| Tool | What It Does | Used In Topics |
|------|----------|---|
| **Terraform** | Infrastructure as Code | 1, 2, 3, 4 |
| **GitHub** | Code repository | 1, 2, 3, 4 |
| **Azure CLI** | Command-line access to Azure | 1, 3, 4 |
| **Azure Service Principal** | Azure authentication | 1, 3, 4 |
| **Azure DevOps** | CI/CD platform | 3 |
| **Jenkins** | CI/CD automation server | 4 |
| **Git** | Version control | 1, 2, 3, 4 |
| **VS Code/Visual Studio** | Code editor | 1, 2 |
| **Azure Storage** | State file storage | 1, 3, 4 |
| **AWS IAM** | Permission management | 2 |
| **Linux/Ubuntu** | Server OS for Jenkins | 4 |

---

## Career Progression Path

```
Topic 1 & 2 Achievement
│
└─→ "Can write Infrastructure as Code"
│   (Skill Level: Associate)

Topic 3 Achievement
│
└─→ "Can automate infrastructure with CI/CD"
│   (Skill Level: Professional)

Topic 4 Achievement
│
└─→ "Can design and implement enterprise automation"
    (Skill Level: Expert/Architect)
```

---

## Next Steps After This Learning Path

1. **Learn Terraform modules** - Reusable components
2. **Learn Kubernetes** - Container orchestration
3. **Learn CI/CD best practices** - Approval gates, notifications
4. **Learn Infrastructure security** - Vault, encryption
5. **Learn monitoring & logging** - Prometheus, ELK stack
6. **Learn advanced Jenkins** - Scripted pipelines, shared libraries
7. **Explore Terraform Cloud** - Managed Terraform service
8. **Learn GitOps** - ArgoCD, declarative infrastructure

---

## Conclusion

These 4 topics form a complete learning journey:
- **Foundation:** Manual Terraform on Azure & AWS
- **Intermediate:** Pipeline automation with Azure DevOps
- **Advanced:** Enterprise-grade Jenkins automation

Master these concepts and you'll be ready for:
- DevOps Engineer roles
- Cloud Infrastructure roles
- Automation Engineer positions
- Infrastructure as Code specialist roles

Good luck with your learning journey! 🚀
