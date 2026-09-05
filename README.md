# 🚀 AWS Infrastructure Provisioning with Terraform

Provision a complete, production-style AWS network and compute stack — VPC, public subnets, an EC2 web server, and an Application Load Balancer — entirely as code using Terraform.

---

# 📖 Project Overview

This project uses Terraform to provision a highly available web-facing infrastructure on AWS. It builds a custom VPC with two public subnets across separate Availability Zones, launches an EC2 instance running Apache (httpd), and fronts it with an Application Load Balancer (ALB) that distributes incoming traffic to the backend instance through a target group.

The repository is intended for beginners and aspiring DevOps/Cloud Engineers who want hands-on experience provisioning real AWS infrastructure as code, instead of clicking through the console.

---

# 🎯 Project Objectives

- Define AWS infrastructure declaratively using Terraform (Infrastructure as Code)
- Provision a custom VPC with public subnets across multiple Availability Zones
- Configure an Internet Gateway and route tables for public internet access
- Launch an EC2 instance and bootstrap it with a web server using `user_data`
- Apply least-privilege security groups (separate rules for the load balancer and the EC2 instance)
- Provision an Application Load Balancer (ALB), target group, and listener
- Register the EC2 instance with the target group and verify traffic flow end-to-end

---

# 🏗️ Architecture

```
                    Internet
                        │
                        ▼
          Application Load Balancer (ALB)
              (Public Subnets, 2 AZs)
                        │
                        ▼
              Target Group (HTTP:80)
                        │
                        ▼
                  EC2 Instance
              (Apache / httpd, t2.micro)
                        │
                        ▼
                Public Subnet (AZ-a)

  VPC: 10.0.0.0/16
  ├── Public Subnet 1: 10.0.1.0/24 (us-east-1a)
  └── Public Subnet 2: 10.0.2.0/24 (us-east-1b)
  Internet Gateway ── Route Table ── Subnet Associations
```

---

# 🛠 Technologies Used

| Technology | Purpose |
|---|---|
| Terraform | Infrastructure as Code — provisioning and lifecycle management |
| AWS VPC | Isolated network environment |
| AWS Internet Gateway | Public internet connectivity for the VPC |
| AWS Subnets | Public subnets across two Availability Zones |
| AWS Route Table | Routes outbound traffic to the Internet Gateway |
| AWS EC2 | Web server instance (Apache/httpd) |
| AWS Security Groups | Network-level access control for EC2 and the ALB |
| AWS Application Load Balancer | Distributes HTTP traffic to backend targets |
| AWS Target Group | Health-checks and routes traffic to registered EC2 instances |

---

# 📁 Repository Structure

```
aws-terraform-infra-provisioning/
├── README.md
├── main.tf
├── variables.tf          (optional — for parameterizing region, CIDR, instance type)
├── outputs.tf            (optional — for ALB DNS name, instance IP, etc.)
├── .gitignore
└── diagrams/
    └── architecture.png
```

---

# ⚙️ Prerequisites

Before running this project, ensure you have:

- An AWS account with an IAM user/role that has permissions for VPC, EC2, and ELB
- An EC2 instance (e.g., Amazon Linux) to use as your Terraform control/management server
- Terraform installed on that EC2 instance
- AWS CLI installed and configured (`aws configure`) on the same instance, with valid access keys
- A valid AMI ID for your target region (the AMI in this project is region-specific — update it if deploying outside `us-east-1`)

---

# 🚀 Quick Start

This project was built and run directly from an AWS EC2 server acting as the Terraform control node (rather than a local machine).

Launch an EC2 instance and connect to it via SSH, then install Terraform and the AWS CLI on it.

Create a working directory for the project:

```bash
mkdir dir_name
cd dir_name
```

Create the Terraform configuration file:

```bash
vi filename.tf
```

Paste in the configuration, save, and exit (`Esc` → `:wq`).

Run the Terraform lifecycle commands one by one:

```bash
terraform init
terraform plan
terraform apply
```

Type `yes` when prompted to confirm.

---

# 📋 Deployment Workflow

1. Launch an EC2 instance to act as the Terraform control server, and connect via SSH
2. Install Terraform and the AWS CLI on the control server, then configure AWS credentials
3. Create a project directory (`mkdir dir_name`) and a Terraform configuration file (`vi filename.tf`)
4. Terraform initializes the AWS provider (`us-east-1`)
5. Creates a VPC (`10.0.0.0/16`)
6. Creates an Internet Gateway and attaches it to the VPC
7. Creates 2 public subnets across `us-east-1a` and `us-east-1b`
8. Creates a public route table with a default route (`0.0.0.0/0`) to the Internet Gateway, and associates both subnets with it
9. Creates two security groups:
   - `lb-sg` — allows inbound HTTP (80) from the internet
   - `ec2-sg` — allows inbound SSH (22) from anywhere and HTTP (80) only from the load balancer's security group
10. Launches an EC2 instance in the first public subnet, bootstrapped via `user_data` to install and start Apache (`httpd`), serving a simple "Hello from Terraform" page
11. Provisions an Application Load Balancer across both public subnets
12. Creates a target group and an HTTP listener on port 80, forwarding traffic to the target group
13. Registers the EC2 instance with the target group

---

# ✅ Verification Commands

Check what Terraform created:

```bash
terraform show
```

List all resources in state:

```bash
terraform state list
```

Get the ALB's public DNS name:

```bash
terraform state show aws_lb.lb
```

Or, if an `outputs.tf` is configured:

```bash
terraform output
```

---

# 🌐 Access the Application

Once `terraform apply` completes, retrieve the ALB's DNS name from the AWS Console (EC2 → Load Balancers) or via `terraform output`, then open it in a browser:

```
http://<alb-dns-name>
```

Expected output:

```
Hello from Terraform
```

---

# 🧹 Cleanup

To avoid ongoing AWS charges, destroy all resources once you're done:

```bash
terraform destroy
```

Type `yes` when prompted. Always confirm in the AWS Console that the VPC, EC2 instance, and Load Balancer have been removed.

---

# 🛠 Troubleshooting

Common issues covered in this project:

- **ALB shows "unhealthy" target** — verify the security group on the EC2 instance allows inbound HTTP from the ALB's security group, and confirm Apache is running (`systemctl status httpd`)
- **Website not loading** — check that the target group's health check path returns a `200` response, and that the instance is in a public subnet with a route to the Internet Gateway
- **`terraform apply` fails on AMI** — AMI IDs are region-specific; replace the AMI ID if deploying outside `us-east-1`
- **SSH connection refused** — confirm port 22 is open in `ec2-sg` and that you're using the correct key pair

---

# 📚 Learning Outcomes

After completing this project, you will understand:

- Core Terraform workflow: `init`, `plan`, `apply`, `destroy`
- How to structure a VPC with public subnets across multiple Availability Zones
- How security groups control traffic between a load balancer and backend instances
- How an Application Load Balancer, target group, and listener work together
- How to bootstrap an EC2 instance at launch using `user_data`
- Infrastructure as Code principles: repeatability, version control, and state management

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
