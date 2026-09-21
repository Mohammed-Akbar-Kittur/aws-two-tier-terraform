# AWS Two-Tier Architecture with Terraform

Two-tier architecture on AWS using Terraform - VPC, EC2, ALB

## Architecture Overview
- Custom VPC 10.0.0.0/16
- 2 Public Subnets
- Internet Gateway and Route Table
- Security Group for HTTP and SSH
- EC2 Instance with Apache
- Application Load Balancer with Target Group

## How to Deploy
terraform init
terraform plan
terraform apply

## How to Destroy
terraform destroy

## Tech Stack
AWS, Terraform, Region eu-north-1

## Author
Mohammed Akbar Kittur
