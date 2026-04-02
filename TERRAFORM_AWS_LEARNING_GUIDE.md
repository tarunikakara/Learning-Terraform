# Agentic AI / Terraform on AWS Study Notes

## 1. Main Goal of the Session

The speaker explains how to create infrastructure in the **AWS Console using Terraform**.

Main idea:

* write or use existing Terraform code
* connect Terraform to AWS using access credentials
* run Terraform commands
* create AWS infrastructure automatically
* destroy the infrastructure when no longer needed

---

## 2. What the Terraform Code Creates

According to the transcript, the Terraform code is used to create:

* **VPC**
* **Public Subnet**
* **Security Group**
* **Key Pair**
* **EC2 Instance** (Web Server)
* **S3 Bucket**

The speaker later says Terraform created **10 resources total**.

---

## 3. Terraform Files Mentioned

### `main.tf`

This file contains the main AWS infrastructure resources to be created.

Examples from the transcript:

* VPC
* subnet
* security group
* key pair
* EC2 instance
* S3 bucket

### `provider.tf`

This file contains AWS provider configuration.

It helps Terraform connect to AWS using:

* access key
* secret key
* region and provider setup

### `variables.tf`

This file contains input variables used in the Terraform configuration.

The speaker also says that in real projects, credentials should not be hardcoded and should instead be handled through variables or better secure methods.

---

## 4. AWS Authentication Setup

To allow Terraform to create infrastructure in AWS, the speaker creates an **IAM user**.

### Steps mentioned:

1. Go to **IAM**
2. Go to **Users**
3. Create a new user
4. Generate or obtain:

   * **Access Key**
   * **Secret Key**
5. Put these credentials into the Terraform provider configuration

These keys are used by Terraform to authenticate with AWS.

---

## 5. IAM Permissions / Policies Required

The speaker explains that the IAM user must have enough permissions to create resources.

Policies mentioned in the transcript:

* **AdministratorAccess**
* **AmazonEC2FullAccess**
* **AmazonS3FullAccess**

These permissions are attached to the IAM user so Terraform can create EC2, S3, networking, and related resources.

### Important point:

If permissions are missing, Terraform may fail with **Access Denied** errors.

---

## 6. Credentials Handling

The speaker copies the access key and secret key into the Terraform code.

But they also mention an important best practice:

* **Do not hardcode credentials in real-time / production environments**
* instead, use variables or safer credential handling methods

This is an important interview and practical note.

---

## 7. Commands Used in the Session

### `terraform init`

Used to initialize Terraform.

Purpose:

* initialize the working directory
* install/download provider plugins
* prepare Terraform for execution

### `terraform validate`

Used to validate the Terraform code.

Purpose:

* checks whether the Terraform syntax and configuration are valid

### `terraform plan`

Used to preview what Terraform is going to create.

Purpose:

* shows planned resources before applying changes

### `terraform apply`

Used to actually create infrastructure in AWS.

Purpose:

* executes the Terraform configuration
* provisions the AWS resources

### `terraform destroy`

Used to delete everything Terraform created.

Purpose:

* removes all provisioned resources managed by Terraform

---

## 8. Execution Flow in Simple Order

### Step 1

Install Terraform locally.

### Step 2

Prepare Terraform code:

* `main.tf`
* `provider.tf`
* `variables.tf`

### Step 3

Go to AWS IAM and create a user.

### Step 4

Generate / obtain:

* access key
* secret key

### Step 5

Attach required IAM policies:

* AdministratorAccess
* AmazonEC2FullAccess
* AmazonS3FullAccess

### Step 6

Add credentials into Terraform configuration.

### Step 7

Run:

```bash
terraform init
```

### Step 8

Run:

```bash
terraform validate
```

### Step 9

Run:

```bash
terraform plan
```

### Step 10

Run:

```bash
terraform apply
```

### Step 11

Verify resources in AWS Console.

### Step 12

If needed, remove everything using:

```bash
terraform destroy
```

---

## 9. Result After `terraform apply`

The transcript says:

* **Apply complete**
* **10 resources added**
* **0 changed**
* **0 destroyed**

This means Terraform successfully created the AWS infrastructure.

The speaker verifies creation by checking:

* **EC2 instance**
* **VPC**

The EC2 instance is described as a **web server**.

---

## 10. Result After `terraform destroy`

Later, the speaker runs:

```bash
terraform destroy
```

After destroy, the transcript says:

* **10 destroyed**

This means all created resources were removed successfully.

The speaker verifies that:

* VPC is gone
* EC2 instances are terminated

---

## 11. Key Concepts to Learn from This Transcript

### Terraform

Infrastructure as Code tool used to create cloud resources using code.

### Provider

A plugin that lets Terraform interact with a cloud platform like AWS.

### IAM User

AWS identity created so Terraform can authenticate and create resources.

### Access Key

Used by Terraform to identify the AWS IAM user.

### Secret Key

Used together with access key for secure authentication.

### VPC

A private network in AWS where cloud resources are launched.

### Public Subnet

A subnet that can host internet-facing resources.

### Security Group

Acts like a firewall controlling inbound and outbound traffic.

### Key Pair

Used for secure access to EC2 instances.

### EC2 Instance

Virtual machine in AWS.

### S3 Bucket

AWS object storage service.

### `terraform init`

Initializes the Terraform working directory.

### `terraform validate`

Checks whether Terraform code is valid.

### `terraform plan`

Shows what resources Terraform will create.

### `terraform apply`

Creates the infrastructure.

### `terraform destroy`

Deletes the created infrastructure.

---

## 12. Practical Real-World Understanding

This transcript shows a simple real DevOps workflow:

* write Terraform code
* connect Terraform to AWS
* authenticate using IAM credentials
* assign permissions
* create infrastructure automatically
* verify resources in AWS
* destroy everything when done

This is one of the most basic and important Infrastructure as Code workflows for cloud and DevOps learning.

---

## 13. Important Best Practice Note

The speaker clearly mentions that **hardcoding access key and secret key is not good in real environments**.

Better practice is:

* use variables
* use environment variables
* use AWS CLI configured credentials
* use IAM roles where possible

This is an important interview point.

---

## 14. Short Interview Questions and Answers

### Why do we use `terraform init`?

To initialize Terraform and download required provider plugins.

### Why do we use `terraform validate`?

To check whether the Terraform configuration is syntactically valid.

### Why do we use `terraform plan`?

To preview the infrastructure changes before applying them.

### Why do we use `terraform apply`?

To create or update AWS resources.

### Why do we use `terraform destroy`?

To delete the resources created by Terraform.

### Why is an IAM user required here?

Terraform needs AWS credentials to authenticate and manage resources.

### What permissions were attached in this example?

* AdministratorAccess
* AmazonEC2FullAccess
* AmazonS3FullAccess

### What resources were created?

* VPC
* Public Subnet
* Security Group
* Key Pair
* EC2 Instance
* S3 Bucket
* plus other related resources making total 10

---

## 15. Short Revision Version

**Topic:** Creating AWS infrastructure using Terraform

**Simple Flow:**

1. Install Terraform
2. Write or use Terraform code
3. Create IAM user in AWS
4. Get access key and secret key
5. Attach required policies
6. Add credentials in provider configuration
7. Run:

   * `terraform init`
   * `terraform validate`
   * `terraform plan`
   * `terraform apply`
8. Verify EC2 and VPC in AWS Console
9. Delete resources with:

   * `terraform destroy`

---

## 16. One-Line Summary

This session teaches how to use Terraform with AWS by authenticating through an IAM user, assigning the right permissions, creating infrastructure like VPC and EC2 from code, and deleting it later using `terraform destroy`.

---

## Next Steps

Use this guide to:
- Understand the complete Terraform + AWS workflow
- Set up your own IAM user with proper permissions
- Create and destroy AWS infrastructure using code
- Learn Infrastructure as Code best practices
- Practice credential security and variable management
