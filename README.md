AWS EC2 Auto Scaling with Application Load Balancer using Terraform

Project Overview

This project demonstrates the deployment of AWS infrastructure using Terraform, including a custom VPC, public and private subnets, Application Load Balancer (ALB), EC2 Auto Scaling Group, NAT Gateway, and Apache Web Server.

The objective is to automate infrastructure provisioning and demonstrate load balancing, application availability, and scalable cloud architecture.

Architecture

Internet → Application Load Balancer → Target Group → EC2 Auto Scaling Group → Apache Web Servers

Networking:

Custom VPC: 10.0.0.0/16

AWS Region: ap-south-1 (Mumbai)

Two Availability Zones: ap-south-1a and ap-south-1b

Two public and two private subnets

Internet Gateway and NAT Gateway

Security Groups for ALB and EC2

AWS Services and Technologies

AWS EC2

Application Load Balancer

EC2 Auto Scaling

Amazon VPC

NAT Gateway and Elastic IP

AWS Security Groups

Terraform

Amazon Linux and Apache HTTP Server

Auto Scaling Configuration

Setting

Value

EC2 Instance Type

t2.micro

Minimum Capacity

2

Desired Capacity

2

Maximum Capacity

3

Application Port

HTTP 80

Deployment Steps

cd infra
terraform init
terraform validate
terraform plan
terraform apply

Review the Terraform plan and AWS costs before applying.

Verification Results

The following checks were completed successfully:

Terraform configuration initialization and validation

Infrastructure deployment: 21 resources added

ALB returned HTTP 200 OK

Apache Web Server returned the expected welcome page

Auto Scaling Group reported two instances in InService state

Pending verification:

Both ALB targets reporting healthy

Automatic scale-out and scale-in under load

Application Verification

Retrieve the ALB DNS name:

terraform output alb_dns_name

Test HTTP connectivity:

curl -I http://YOUR-ALB-DNS-NAME

Test the application:

curl http://YOUR-ALB-DNS-NAME

Expected response:

<h1>Welcome to SLGS TO webserver : 10.0.x.x</h1>

Troubleshooting

IAM Access Denied: Ensure the deployment identity has the required permissions for EC2, VPC, Elastic Load Balancing, and Auto Scaling.

Saved Plan Is Stale: Generate a new Terraform plan after the state changes.

ALB Connection Timeout: Verify public subnet routing, Internet Gateway, listener, security groups, and target health.

Unhealthy EC2 Targets: Check the Apache service, user data execution, target group health checks, and network connectivity.

Cleanup

To avoid ongoing AWS charges, review and destroy the lab infrastructure when it is no longer required:

terraform plan -destroy
terraform destroy

Warning: Destroying infrastructure permanently removes Terraform-managed resources. Back up any important data before proceeding.

Security and Cost Considerations

Use least-privilege IAM permissions and temporary credentials where possible.

Never commit AWS credentials, Terraform state files, or saved plan files.

ALB, NAT Gateway, EC2, EBS, and public IPv4 addresses may incur charges.

Restrict security group rules to required ports and trusted traffic sources.

Project Status

Infrastructure deployment: Completed

Application HTTP connectivity: Verified
