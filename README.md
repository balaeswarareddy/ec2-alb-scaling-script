# EC2 and ALB Auto Scaling Infrastructure

This Terraform project provisions a highly available Apache web application on AWS. It creates a custom VPC with public and private subnets across two Availability Zones, an Internet-facing Application Load Balancer (ALB), and an EC2 Auto Scaling Group.

## Architecture

- Custom VPC with `10.0.0.0/16` CIDR
- Public subnets for the ALB and NAT Gateway
- Private subnets for EC2 instances
- Internet Gateway for public internet access
- NAT Gateway for outbound access from private subnets
- Application Load Balancer listening on HTTP port 80
- Target group and listener forwarding traffic to EC2 instances
- Launch template running Apache through EC2 user data
- Auto Scaling Group with a desired/minimum capacity of 2 and a maximum of 3
- Security groups separating internet-to-ALB and ALB-to-EC2 traffic

## Resources Created

| Resource | Purpose |
|---|---|
| VPC | Isolated network for the infrastructure |
| Public/private subnets | Separate internet-facing and application tiers |
| Internet Gateway | Internet access for public subnets |
| NAT Gateway and Elastic IP | Outbound internet access for private instances |
| Application Load Balancer | Distributes HTTP traffic |
| Target Group | Registers EC2 instances for load balancing |
| Launch Template | Defines EC2 instance configuration |
| Auto Scaling Group | Maintains web server capacity |
| Security Groups | Controls traffic between the ALB and EC2 instances |

## Prerequisites

- An AWS account
- AWS CLI configured with credentials and the required permissions
- Terraform CLI 1.0 or later
- An Amazon Linux-compatible AMI ID available in the configured region

## Configuration

The default configuration is located in `infra/veriables.tf`:

| Variable | Default | Description |
|---|---|---|
| `region_name` | `ap-south-1` | AWS region |
| `ami_id` | `ami-0f535a71b34f2d44a` | AMI for EC2 instances |
| `instance_type` | `t2.micro` | EC2 instance type |
| `vpc_availability_zones` | `ap-south-1a`, `ap-south-1b` | Availability Zones |

Verify that the default AMI ID exists in `ap-south-1` and is compatible with the `yum`/`systemctl` commands in `infra/userdata.sh`. Update the variables if needed.

## Deployment

```bash
cd infra
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

After the apply completes, Terraform outputs the ALB DNS name:

```bash
terraform output alb_dns_name
```

Open the value in a browser using `http://`:

```text
http://<alb-dns-name>
```

The default page displays the private IP address of the instance that handled the request.

## Scaling

The Auto Scaling Group is currently configured with:

- **Desired capacity:** 2 instances
- **Minimum:** 2 instances
- **Maximum:** 3 instances

Update `desired_capacity`, `min_size`, or `max_size` in `infra/ec2.tf` and apply the configuration again.

## Cleanup

To remove all provisioned resources:

```bash
cd infra
terraform destroy
```

## Repository Structure

```text
.
├── README.md
└── infra
    ├── ec2.tf          # ALB, target group, launch template, and ASG
    ├── provider.tf      # AWS provider configuration
    ├── userdata.sh      # Apache installation and web page setup
    ├── veriables.tf     # Input variables
    └── vpc.tf           # VPC, subnets, routing, NAT, and internet gateway
```

## Security and Cost Notes

- The ALB security group allows public HTTP traffic on port 80.
- EC2 instances receive no public IP and must be reached through the ALB.
- NAT Gateway, Elastic IP, ALB, and EC2 usage may incur AWS charges.
- Destroy the resources when they are no longer needed.
