# AWS Infrastructure Provisioning with Terraform

Terraform project that provisions a custom AWS network and web application stack from an **AWS EC2 server acting as the Terraform control server**.

## Actual Project Setup

The working directory created on the control server was:

```text
Arch/
├── arch.tf
├── terraform.tfstate
└── terraform.tfstate.backup
```

`Arch` is the project directory. The Terraform configuration is stored in `arch.tf`.

The Terraform state files are local working files and should **not** be committed to GitHub.

## AWS Resources

| Resource | Configuration |
|---|---|
| Region | `ap-south-1` |
| VPC | `10.0.0.0/16` |
| Public Subnet 1 | `10.0.1.0/24` — `ap-south-1a` |
| Public Subnet 2 | `10.0.2.0/24` — `ap-south-1b` |
| Internet Gateway | Attached to the VPC |
| Route Table | `0.0.0.0/0` → Internet Gateway |
| EC2 | `c7i-flex.large` |
| Web Server | Apache2 |
| ALB | `example-lb` |
| Target Group | `example-tg` |
| Listener | HTTP : 80 |
| Security Groups | `lb-sg`, `ec2-sg` |

## Architecture

```text
Internet
   |
   v
Application Load Balancer (example-lb)
   |  HTTP : 80
   v
Target Group (example-tg)
   |  HTTP : 80
   v
EC2 Instance (c7i-flex.large)
   |
   +-- Apache2
   +-- "Hello from Terraform"

VPC: 10.0.0.0/16
├── Public Subnet 1: 10.0.1.0/24 (ap-south-1a)
│   └── EC2 + ALB
└── Public Subnet 2: 10.0.2.0/24 (ap-south-1b)
    └── ALB

Internet Gateway → Public Route Table → Public Subnets
```

## Terraform Control Server Workflow

The project was created directly on an AWS EC2 server.

```bash
sudo -i
mkdir Arch
cd Arch
vi arch.tf
```

After adding the Terraform configuration:

```bash
terraform init
terraform plan
terraform apply
```

When prompted by Terraform:

```text
yes
```

## Verification

```bash
terraform show
terraform state list
terraform state show aws_lb.lb
```

The ALB DNS name can also be obtained from the AWS Console or:

```bash
terraform output
```

This project does not currently define `outputs.tf`, so `terraform output` will only return values if outputs are later added.

## Application Verification

The deployed application is accessed through the ALB DNS name:

```text
http://<alb-dns-name>
```

Expected page:

```text
Hello from Terraform
```

## Security Groups

### lb-sg

Allows inbound:

```text
TCP 80 from 0.0.0.0/0
```

### ec2-sg

Allows inbound:

```text
TCP 22 from 0.0.0.0/0
TCP 80 from lb-sg only
```

For production use, SSH should be restricted to a trusted administrator IP or access mechanism.

## Deployment Evidence

The repository contains screenshots showing:

1. EC2 instance details
2. Terraform-managed security groups
3. Application Load Balancer
4. Application response through the ALB

The supplied load-balancer evidence also shows the HTTP:80 listener forwarding to `example-tg` and the registered EC2 target as Healthy.

## Cleanup

To remove the infrastructure:

```bash
terraform destroy
```

Type `yes` when prompted and verify the resources are removed in the AWS Console.

## Important GitHub Note

Do not commit:

```text
terraform.tfstate
terraform.tfstate.backup
.terraform/
*.tfvars
```

Terraform state can contain infrastructure information and, depending on configuration, sensitive values.

## Author

**Vinoth V**

DevOps | AWS | Terraform | Kubernetes | Docker | Jenkins | Python
