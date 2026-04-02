# Agentic AI / Azure DevOps Pipeline + Terraform on Azure Study Notes

## 1. Main Goal of the Session

The speaker explains how to:

* use **Azure DevOps Pipeline**
* pull Terraform code from **GitHub**
* run Terraform through the pipeline
* create Azure infrastructure automatically
* also destroy the infrastructure through the same pipeline

This is basically **Infrastructure as Code through CI/CD**.

---

## 2. Main Infrastructure Created

According to the transcript, the Terraform code creates:

* **Azure Virtual Network (VNet)**
* **Public Subnet**
* **Private Subnet**

The names are driven by a variable called `env`.

Example from the transcript:

* `bhat-vnet`
* `bhat` public/private subnet naming pattern

---

## 3. Prerequisites Before Running the Pipeline

Before the pipeline can work, the speaker manually creates backend storage for Terraform state.

### Required prerequisites:

* **Resource Group**
* **Storage Account**
* **Container**

These are needed so Terraform can store the **state file remotely**.

### Container name mentioned:

* `tfstate`

### Why this is needed:

Terraform needs a remote backend to store and track infrastructure state safely.

---

## 4. Terraform Files Mentioned

### `main.tf`

Contains the Azure resources that will be created, such as:

* VNet
* Subnets

### `providers.tf`

Contains provider and variable-related configuration.

The transcript shows that variable values are referenced from here.

### Variable `env`

A variable called `env` is used for naming.

The speaker checks the assigned value and explains that Azure resources will be created using that value.

Example:

* if `env = bhat`
* then names become like `bhat-vnet`, `bhat-subnet`

---

## 5. Azure DevOps Organization-Level Requirement

The speaker explains that in **Organization Settings** → **Extensions**, certain extensions must be added from the Marketplace.

### Extensions mentioned:

* **Sonar / SonarQube**
* **Terraform**

### Steps explained:

1. Go to **Organization Settings**
2. Open **Extensions**
3. Click **Browse Marketplace**
4. Search for:

   * Sonar / SonarQube
   * Terraform
5. Add them

### Why this matters:

The pipeline tasks for Terraform depend on these extensions.

---

## 6. Project-Level Requirement

The speaker explains that at the **Project Settings** level, service connections are important.

### Service connections needed:

* GitHub connectivity
* Azure connectivity
* connection to Azure resources / subscription

This allows:

* Azure DevOps to fetch code from GitHub
* Azure DevOps to authenticate to Azure
* Terraform to run against the Azure subscription

---

## 7. Pipeline Setup Overview

The speaker already has a pipeline created and shows its configuration.

### Pipeline details mentioned:

* pipeline name: **demo CI**
* uses **Azure-hosted agent**
* not using self-hosted agent
* agent specification: **Windows**
* source code pulled from **GitHub**
* repository example: `Abar335 Terraform Azure`
* branch used: **master**

---

## 8. Azure Hosted Agent vs Self-Hosted Agent

### Azure-hosted agent

Microsoft provides the build machine automatically.

### Self-hosted agent

You create and manage your own VM and configure it manually.

In this transcript, the speaker uses:

* **Azure-hosted agent**
* **Windows agent**

---

## 9. Terraform Tasks Added in Pipeline

The speaker explains that Terraform tasks are added through the pipeline editor.

Tasks mentioned include:

* **Terraform Tool Installer**
* **Terraform Init**
* **Terraform Plan**
* **Terraform Validate**
* **Terraform Apply**
* **Terraform Destroy**

These are added using pipeline task options after installing the Terraform extension.

---

## 10. Pipeline Inputs for Terraform Init

For the Terraform initialization task, the speaker mentions providing:

* Azure subscription
* Resource Group
* Storage Account
* Container
* default Terraform state key
* working directory

### Example values mentioned:

* Resource Group: `terraform`
* Storage Account: something like `terraform7649`
* Container: `tfstate`
* State file key: default Terraform state file name

These are used for backend state configuration.

---

## 11. What Happens When Pipeline Runs

The flow shown in the transcript is:

1. pipeline checks out code from GitHub
2. pipeline installs Terraform
3. pipeline runs `init`
4. pipeline runs `plan`
5. pipeline runs `validate`
6. pipeline runs `apply`
7. Azure infrastructure gets created

The speaker checks Azure after the run and confirms that the resources are created.

---

## 12. Terraform Version Mentioned

The transcript mentions Terraform version:

* **1.11.3**

This is the version installed by the pipeline during execution.

---

## 13. Infrastructure Verification After Apply

Before the pipeline run, the speaker says there is:

* no VNet
* no subnet

After running the pipeline, the speaker refreshes Azure and verifies:

* VNet is created
* subnets are created
* naming matches the variable value (`bhat`)

This confirms that the pipeline successfully created infrastructure from GitHub-hosted Terraform code.

---

## 14. Naming Logic with Variable

The variable `env` controls resource names.

Example from transcript:

* `bhat-vnet`
* `bhat subnet`

The speaker also explains that if you change the variable value, you can create resources with different naming conventions according to organizational standards.

For example:

* change `bhat` to `rahul`
* or another environment / location-based name

Then Terraform will create resources using the updated naming pattern.

---

## 15. Idempotent Behavior Shown

A very important Terraform concept shown in this session is:

### If infrastructure already exists and matches code:

Terraform does **not** recreate it.

The pipeline output says:

* **No changes**

### Meaning:

Terraform compares actual infrastructure with configuration.
If everything already matches, it does nothing.

This is one of Terraform's most important real-world behaviors.

---

## 16. Destroying Infrastructure Through Pipeline

The speaker later enables the **destroy task** in the pipeline.

Then the pipeline is run again.

### Result:

* Terraform destroys the infrastructure defined in the code

The speaker verifies in Azure that the VNet created earlier is gone.

So the same Azure DevOps pipeline can be used for:

* provisioning infrastructure
* destroying infrastructure

---

## 17. Minor Pipeline Error Mentioned

Near the end, a message appears about:

* agent unable to get disk exemption
* value cannot be null

But even with that message, the speaker verifies that the infrastructure was deleted successfully.

### Practical understanding:

Sometimes pipeline logs may show non-critical issues, but the real check is whether the expected infrastructure state is achieved.

---

## 18. Full Step-by-Step Flow

### Step 1

Prepare Terraform code in GitHub.

### Step 2

Create Azure backend prerequisites manually:

* Resource Group
* Storage Account
* Container

### Step 3

Go to **Azure DevOps Organization Settings**.

### Step 4

Install marketplace extensions:

* Terraform
* Sonar / SonarQube

### Step 5

Go to **Project Settings**.

### Step 6

Configure service connections for:

* GitHub
* Azure subscription / portal

### Step 7

Create or edit pipeline.

### Step 8

Use:

* Azure-hosted agent
* Windows image

### Step 9

Import source from GitHub repository and master branch.

### Step 10

Add Terraform tasks:

* tool installer
* init
* plan
* validate
* apply
* destroy

### Step 11

Configure backend values:

* subscription
* resource group
* storage account
* container
* state key

### Step 12

Run pipeline to create infrastructure.

### Step 13

Verify VNet and subnets in Azure portal.

### Step 14

Enable destroy task when needed.

### Step 15

Run pipeline again to delete infrastructure.

---

## 19. Key Concepts to Learn

### Azure DevOps Pipeline

A CI/CD workflow used to automate infrastructure deployment.

### GitHub Integration

Pipeline fetches Terraform code directly from GitHub.

### Terraform Tool Installer

Task used to install Terraform in the pipeline agent.

### Azure-hosted Agent

Managed build machine provided by Azure DevOps.

### Self-hosted Agent

Your own VM or machine configured to run pipeline jobs.

### Remote Backend

Storage location for Terraform state file.

### Storage Account + Container

Used in Azure to store the Terraform state.

### Terraform Init

Initializes backend and provider plugins.

### Terraform Plan

Shows what Terraform is going to do.

### Terraform Validate

Checks configuration validity.

### Terraform Apply

Creates the infrastructure.

### Terraform Destroy

Deletes infrastructure.

### Idempotency

If infrastructure already matches the code, Terraform makes no changes.

### Service Connection

Secure connection from Azure DevOps to GitHub / Azure.

---

## 20. Real-Time Practical Understanding

This transcript is very useful because it shows a real DevOps workflow:

* code stored in GitHub
* CI/CD pipeline in Azure DevOps
* Terraform state stored remotely in Azure Storage
* Terraform executed automatically by pipeline
* infrastructure verified in Azure portal
* same pipeline used for destroy also

This is very close to practical cloud DevOps project work.

---

## 21. Interview Questions and Answers

### Why do we need Azure DevOps Pipeline here?

To automate Terraform execution instead of running commands manually.

### Why do we store Terraform state remotely?

To manage infrastructure safely and centrally.

### Why are service connections needed?

They allow Azure DevOps to securely connect to GitHub and Azure.

### What is the use of Terraform Tool Installer task?

It installs Terraform on the pipeline agent.

### Why use Azure-hosted agent?

Azure DevOps provides and manages the build machine.

### What happens if infrastructure already exists?

Terraform detects no changes and does not recreate resources.

### What infrastructure was created in this example?

* VNet
* public/private subnets

### Can the same pipeline destroy resources?

Yes, by enabling the destroy task.

---

## 22. Short Revision Version

**Topic:** Azure DevOps Pipeline + Terraform + Azure

**Simple Flow:**

1. Keep Terraform code in GitHub
2. Create backend prerequisites in Azure:

   * Resource Group
   * Storage Account
   * Container
3. Add Terraform extension in Azure DevOps
4. Configure service connections
5. Create pipeline with Azure-hosted Windows agent
6. Add tasks:

   * Terraform Tool Installer
   * Init
   * Plan
   * Validate
   * Apply
   * Destroy
7. Run pipeline
8. Verify VNet and subnets in Azure
9. Re-run with destroy task enabled to remove them

---

## 23. One-Line Summary

This session teaches how to use Azure DevOps Pipeline with GitHub-hosted Terraform code to create and destroy Azure infrastructure automatically while storing Terraform state in Azure Storage.

---

## 24. Quick Reference: Pipeline Task Sequence

```
1. GitHub Checkout
   ↓
2. Terraform Tool Installer
   ↓
3. Terraform Init (with Azure backend config)
   ↓
4. Terraform Validate
   ↓
5. Terraform Plan
   ↓
6. Terraform Apply (or Destroy)
   ↓
7. Infrastructure Created/Destroyed in Azure
```

---

## Next Steps

Use this guide to:
- Understand CI/CD automation with Infrastructure as Code
- Set up Azure DevOps Pipeline for Terraform
- Configure GitHub integration with Azure DevOps
- Learn remote state management
- Practice creating and destroying infrastructure through pipelines
- Understand idempotency and infrastructure drift detection
