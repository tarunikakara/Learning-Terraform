# Agentic AI / Terraform on Azure Study Notes

## 1. Main Goal of the Session

The speaker explains how to:

* clone Terraform code from GitHub into Visual Studio / VS Code
* configure Azure authentication
* run Terraform commands
* create Azure infrastructure from code
* store the Terraform state file centrally in Azure Storage instead of locally

The end goal is to automate infrastructure creation in Azure using Terraform.

---

## 2. What the Speaker Starts With

Initially, the Azure portal has no resources created for this demo, such as:

* Resource Group
* Virtual Network
* Subnets

The plan is:

1. get Terraform code from GitHub
2. clone it into Visual Studio
3. run Terraform commands
4. watch Azure resources get created in the portal

---

## 3. Terraform Files and What They Do

### `main.tf`

This file creates the actual infrastructure in Azure, such as:

* Resource Group
* Virtual Network
* Public Subnet
* Private Subnet

### `variables.tf`

This file defines input variables.

Example:

* variable name: `environment`
* sample value: `YouTube audience`

This value is reused in naming resources.

### `terraform.tfvars`

This file stores the actual values assigned to variables.

Example:

* `environment = "YouTube audience"`

### `providers.tf`

This file defines the Azure provider plugin needed for Terraform to connect to Azure.

It contains authentication-related details such as:

* subscription ID
* client ID
* tenant ID
* client secret

---

## 4. Azure Authentication Setup

To allow Terraform to create resources in Azure, the speaker creates a **Service Principal**.

### Steps mentioned:

1. Go to **App Registrations** in Azure
2. Create a **new registration**
3. Copy:

   * Client ID
   * Tenant ID
4. Go to **Certificates & Secrets**
5. Create a **new client secret**
6. Copy the **secret value**

   * Important: copy the **value**, not the secret ID

These values are then used in the Terraform provider configuration.

---

## 5. Important Permission Requirement

Even after creating the Service Principal, Terraform may fail unless the Service Principal has enough permissions.

The speaker explains that the Service Principal must be given:

* **Contributor access**
* at the **Subscription level**

### Why?

Because Terraform needs permission to create Azure resources.

Without this access, Terraform throws a permission error.

---

## 6. Remote Terraform State Setup

The speaker does not want the Terraform state file stored locally.

Instead, they want it stored centrally in Azure.

### For that, they manually create:

* a Resource Group
* a Storage Account
* a Container inside the Storage Account

This is done before running Terraform so the state file can be saved there.

### Why remote state is useful:

* centralized storage
* safer than local storage
* useful in real-time environments
* better for team use
* tracks infrastructure changes properly

---

## 7. Resources Planned by Terraform

According to the transcript, Terraform will create:

* 1 Resource Group
* 1 Virtual Network
* 1 Public Subnet
* 1 Private Subnet

That makes a total of **4 resources**.

---

## 8. Terraform Commands Used

### `terraform init`

Used to initialize Terraform in the project.

Purpose:

* downloads and initializes the required provider plugin
* prepares Terraform to work with Azure

### `terraform plan`

Used to preview what resources Terraform is going to create.

Purpose:

* checks configuration
* validates setup
* shows the execution plan before making changes

### `terraform apply`

Used to actually create the resources in Azure.

Purpose:

* applies the Terraform code
* creates infrastructure after confirmation

---

## 9. Error Faced During `terraform plan`

The speaker gets an error saying the Service Principal does not have enough permissions.

### Meaning of the error

Terraform cannot register providers or create Azure resources because the Service Principal is missing required access.

### Fix

The speaker goes to:

* Azure Subscription
* Access Control (IAM)
* Add Role Assignment
* Assign **Contributor** role to the created Service Principal

After that, Terraform works properly.

---

## 10. Naming Logic Used

The variable `environment` has value:

`YouTube audience`

This value gets reused in resource names.

Examples mentioned:

* Resource Group name
* Virtual Network name
* Public Subnet name
* Private Subnet name

So Terraform dynamically creates names based on variable values.

---

## 11. Final Result After `terraform apply`

After running `terraform apply`, Terraform successfully creates the infrastructure.

The transcript says:

* **4 resources added**
* **0 changed**
* **0 destroyed**

That means the infrastructure creation was successful.

Created resources include:

* Resource Group
* Virtual Network
* Public Subnet
* Private Subnet

---

## 12. Terraform State File Verification

After the resources are created, the speaker checks the Azure Storage Container.

They confirm that the **Terraform state file** is present there.

This proves that:

* remote backend is working
* state file is stored centrally
* Terraform execution was successful

---

## 13. Full Workflow Explained Simply

### Step 1

Clone Terraform code from GitHub into Visual Studio.

### Step 2

Review the Terraform files:

* `main.tf`
* `variables.tf`
* `terraform.tfvars`
* `providers.tf`

### Step 3

Create an Azure Service Principal.

### Step 4

Collect:

* subscription ID
* tenant ID
* client ID
* client secret

### Step 5

Give the Service Principal **Contributor access** at subscription level.

### Step 6

Create backend storage manually in Azure:

* Resource Group
* Storage Account
* Container

### Step 7

Run:

```bash
terraform init
```

### Step 8

Run:

```bash
terraform plan
```

### Step 9

If there are permission errors, fix IAM access.

### Step 10

Run:

```bash
terraform apply
```

### Step 11

Verify resources in Azure portal.

### Step 12

Verify Terraform state file in Azure Storage container.

---

## 14. Key Concepts to Learn from This Transcript

### Terraform

Tool used to create infrastructure using code.

### Provider

Plugin that connects Terraform to a cloud platform like Azure.

### Service Principal

Identity used by Terraform to authenticate with Azure.

### Client ID

Application ID of the Service Principal.

### Tenant ID

Azure Active Directory tenant identifier.

### Client Secret

Password-like credential for the Service Principal.

### Subscription ID

Azure subscription identifier.

### Terraform State File

File that tracks what Terraform has created and manages.

### Remote Backend

Saving Terraform state in a shared central location instead of local machine.

### Resource Group

A container in Azure that holds related resources.

### Virtual Network

Azure network for hosting resources.

### Subnet

Smaller network sections inside a virtual network.

### IAM / Access Control

Permission management in Azure.

---

## 15. Real-Time Practical Understanding

This transcript shows a real-world beginner DevOps workflow:

* code is stored in GitHub
* code is cloned locally
* Terraform reads infrastructure code
* Azure authentication is configured
* IAM permission is granted
* Terraform creates cloud resources
* state is stored remotely for better management

This is very close to how teams work in practical cloud and DevOps projects.

---

## 16. Short Interview Notes

### Why do we use `terraform init`?

To initialize the working directory and download required provider plugins.

### Why do we use `terraform plan`?

To preview what Terraform is going to create or change.

### Why do we use `terraform apply`?

To actually create or update infrastructure.

### Why is Service Principal needed?

Terraform needs an identity to authenticate and work with Azure.

### Why give Contributor access?

Without proper permissions, Terraform cannot create resources.

### Why store Terraform state remotely?

To centralize state, improve safety, and support team environments.

### What resources were created in this example?

* Resource Group
* Virtual Network
* Public Subnet
* Private Subnet

---

## 17. Very Short Revision Version

**Topic:** Creating Azure infrastructure with Terraform

**Flow:**

1. Clone code from GitHub
2. Open in Visual Studio
3. Configure provider details
4. Create Service Principal
5. Copy client ID, tenant ID, secret, subscription ID
6. Give Contributor role at subscription level
7. Create storage account + container for remote state
8. Run:

   * `terraform init`
   * `terraform plan`
   * `terraform apply`
9. Verify Azure resources
10. Check Terraform state file in Azure Storage

---

## Next Steps

Use this guide to:
- Understand the complete Terraform + Azure workflow
- Set up your own Service Principal
- Create infrastructure as code
- Store state files remotely for team environments
