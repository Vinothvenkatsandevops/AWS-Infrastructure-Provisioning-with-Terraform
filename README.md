# 🚀 AWS Infrastructure Provisioning with Terraform

Provision AWS infrastructure using Terraform, including a custom VPC, public subnets, an EC2 web server, and an Application Load Balancer (ALB).

This project was created and deployed **from an AWS EC2 server acting as the Terraform control server**. Instead of running Terraform from a local laptop, the Terraform configuration was created and executed directly on the EC2 server using Terraform CLI commands.

---

# 📖 Project Overview

This project demonstrates how to provision AWS infrastructure using Terraform as Infrastructure as Code (IaC).

The infrastructure includes:

* Custom VPC
* Two public subnets across different Availability Zones
* Internet Gateway
* Public route table
* EC2 instance running Apache (`httpd`)
* Security groups
* Application Load Balancer (ALB)
* Target group
* HTTP listener

The entire infrastructure was provisioned using Terraform commands executed from an AWS EC2 server.

---

# 🖥️ Terraform Control Server

For this project, an AWS EC2 instance was used as the **Terraform control server**.

The workflow was:

```text
AWS EC2 Server
      │
      │ Terraform CLI
      ▼
Terraform Configuration
      │
      ▼
AWS Provider
      │
      ▼
AWS Infrastructure
 ┌────┴─────────────────────────┐
 │                              │
 ▼                              ▼
VPC                         Application
 │                         Load Balancer
 ├── Public Subnet 1            │
 ├── Public Subnet 2            ▼
 ├── Internet Gateway       Target Group
 └── Route Table                 │
                                 ▼
                              EC2 Web Server
                              Apache/httpd
```

The EC2 server was used only as the **Terraform management/control node**. Terraform executed the configuration from this server and provisioned the required AWS resources.

---

# ⚙️ Project Setup

After connecting to the AWS EC2 server through SSH, I created a directory to store the Terraform configuration.

### 1. Create a project directory

```bash
mkdir dir_name
cd dir_name
```

### 2. Create the Terraform configuration file

I created the Terraform configuration file using the `vi` editor:

```bash
vi filename.tf
```

The Terraform configuration was then added to this file and saved using:

```text
Esc
:wq
```

### 3. Initialize Terraform

After creating the configuration file, I initialized Terraform:

```bash
terraform init
```

This initialized the Terraform working directory and downloaded the required AWS provider.

### 4. Review the execution plan

Next, I checked what resources Terraform would create:

```bash
terraform plan
```

Terraform displayed the planned AWS infrastructure changes before making any modifications.

### 5. Provision the infrastructure

I then applied the Terraform configuration:

```bash
terraform apply
```

Terraform displayed the resources that would be created and asked for confirmation.

I entered:

```text
yes
```

Terraform then provisioned the AWS infrastructure.

---

# 📋 Deployment Workflow

The complete workflow followed in this project was:

1. Launch an AWS EC2 instance to act as the Terraform control server.
2. Connect to the EC2 server using SSH.
3. Install and configure Terraform and the AWS CLI.
4. Configure AWS credentials on the Terraform server.
5. Create a project directory:

```bash
mkdir dir_name
cd dir_name
```

6. Create the Terraform configuration file:

```bash
vi filename.tf
```

7. Add the Terraform configuration and save the file.
8. Initialize the Terraform project:

```bash
terraform init
```

9. Review the infrastructure changes:

```bash
terraform plan
```

10. Provision the AWS infrastructure:

```bash
terraform apply
```

11. Confirm the deployment by entering:

```text
yes
```

12. Verify the resources created by Terraform.
13. Access the application through the Application Load Balancer DNS name.
14. Destroy the infrastructure after completing the project:

```bash
terraform destroy
```

---

# 🔄 Terraform Workflow

The project demonstrates the standard Terraform lifecycle:

```text
                 Terraform Control Server
                         │
                         ▼
                  filename.tf
                         │
                         ▼
                  terraform init
                         │
                         ▼
                  terraform plan
                         │
                         ▼
                 terraform apply
                         │
                         ▼
                  AWS Infrastructure
                         │
                         ▼
                    Verification
                         │
                         ▼
                 terraform destroy
```

---

# 🎯 Key Learning

Through this project, I gained hands-on experience with:

* Running Terraform from an AWS EC2 server
* Creating Terraform configuration files using the Linux `vi` editor
* Using the Terraform CLI
* Understanding the `init → plan → apply → destroy` workflow
* Provisioning AWS infrastructure using Infrastructure as Code
* Creating and configuring VPC networking
* Deploying EC2 instances using Terraform
* Configuring security groups
* Creating an Application Load Balancer
* Connecting an ALB to an EC2 target through a target group
* Verifying infrastructure created by Terraform


---

# 🚀 Future Enhancements

- Parameterize the configuration using `variables.tf` (region, CIDR blocks, instance type)
- Add `outputs.tf` to expose the ALB DNS name and instance details
- Move to a **3-tier architecture** by adding private subnets, a NAT Gateway, and a backend database (RDS)
- Add Auto Scaling Group instead of a single EC2 instance for high availability
- Store Terraform state remotely using an S3 backend with DynamoDB state locking
- Enable HTTPS using AWS Certificate Manager (ACM) and an HTTPS listener
- Modularize the configuration (separate modules for networking, compute, and load balancing)

---

# 👨‍💻 Author

**Vinoth V**

DevOps | AWS | Terraform | Kubernetes | Docker | Jenkins | Python
