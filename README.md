# AWS Secure VPC Infrastructure with Terraform

> Provisioned complete Production-ready VPC Architecture in **eu-north-1 (Stockholm)** using Terraform IaC.
> **Account:** Mohammed Akbar Kittur (844274145793)

## 🏗️ Architecture Diagram
<img width="1920" height="1280" alt="image" src="https://github.com/user-attachments/assets/6ea0dd60-598d-4161-94db-35c84824573e" />


## 📸 Infrastructure Proofs (Text + Screenshots)

### 1. VPC - vpc-0c2b4b004815bf3c2
Provisioned custom VPC `vpc-0c2b4b004815bf3c2` with CIDR `10.0.0.0/16`, DNS hostnames enabled, State Available in eu-north-1. Created via Terraform `aws_vpc`.
![VPC](<img width="1366" height="605" alt="1-vpc" src="https://github.com/user-attachments/assets/5d469b8a-3c6c-4ffa-be74-622f4b69098f" />
)

### 2. Subnets - 2 Public Subnets (HA)
Created 2 public subnets `10.0.1.0/24` in eu-north-1a and `10.0.2.0/24` in eu-north-1b for High Availability. Both with auto-assign public IP.
![Subnets](<img width="1366" height="604" alt="2-subnet" src="https://github.com/user-attachments/assets/2679d552-145e-4fd2-9898-0551fdf4f01d" />
)

### 3. Internet Gateway - igw-01ad8eb072f0f65cf
Provisioned Internet Gateway `igw-01ad8eb072f0f65cf` and attached to VPC `vpc-0c2b4b004815bf3c2` to enable internet access.
![Internet Gateway](<img width="1366" height="606" alt="3-internet gateways" src="https://github.com/user-attachments/assets/df85b67b-79b3-4894-ac2d-6f920d6ef921" />
)

### 4. Route Table - rtb-0ad8d420b924b30d1
Route Table `rtb-0ad8d420b924b30d1` with 2 subnet associations. Routes: `10.0.0.0/16 -> local`, `0.0.0.0/0 -> igw-01ad8eb072f0f65cf`.
![Route Table](<img width="1366" height="606" alt="3-internet gateways" src="https://github.com/user-attachments/assets/0d96018f-2fff-4746-8660-1512a072500d" />
)

### 5. Security Groups - Layered Security
**lb-sg** `sg-057c5bdae31d46e13`: Allows HTTP 80 from `0.0.0.0/0`
**ec2-sg** `sg-07e80ee65c21144db`: Allows SSH 22 from `0.0.0.0/0` and HTTP 80 ONLY from lb-sg (Secure Design)
![Security Groups](<img width="1366" height="604" alt="5-security groups" src="https://github.com/user-attachments/assets/b6347238-67a7-4fd4-a0f3-6d2b7b416e5f" />
)

### 6. EC2 Instance - i-0daa70d8e49f0a2a5
EC2 `t3.micro` Running, 3/3 status checks passed, Public IP `51.21.134.55`, Private IP `10.0.1.82` inside custom VPC. User data installed Nginx with "Hello from Terraform".
![EC2](<img width="1366" height="604" alt="5-security groups" src="https://github.com/user-attachments/assets/6658c6b7-e7b9-49eb-bd26-c8215094f38a" />
)

### 7. Target Group - example-tg
Target Group `example-tg` with ARN `arn:aws:elasticloadbalancing:eu-north-1:844274145793:targetgroup/example-tg/a9b8689c043f4c35`
- Port: 80, Protocol: HTTP, Target type: Instance, Protocol version: HTTP1, IP type: IPv4
- Associated Load Balancer: `example-lb`, VPC: `vpc-0c2b4b004815bf3c2`
- Target `i-0daa70d8e49f0a2a5:80` registered and healthy.
![Target Group](<img width="1366" height="602" alt="target group" src="https://github.com/user-attachments/assets/b0aff9e6-3225-4fcf-bb2f-d9eb51974f5b" />
)

### 8. Application Load Balancer - example-lb (Active)
ALB `example-lb` is **Active**, Type: application, Scheme: Internet-facing, 2 Availability Zones, VPC `vpc-0c2b4b004815bf3c2`. DNS: `example-lb-514878212.eu-north-1.elb.amazonaws.com`
![Load Balancer](<img width="1366" height="605" alt="9-load balancer" src="https://github.com/user-attachments/assets/d0cb5cb0-6005-4188-af25-034cd2d34d6f" />
)

### 9. Listener - HTTP:80 Forward
Listener on port 80 forwarding traffic to Target Group `example-tg` with default forward action.
![Listener](<img width="1366" height="609" alt="listener" src="https://github.com/user-attachments/assets/2213749c-aaa7-4801-9f96-16cad3ffea9d" />
)

### 10. Final Validation - Application Working via ALB [MOST IMPORTANT]
Successfully accessed application via ALB DNS:

**URL:** http://example-lb-514878212.eu-north-1.elb.amazonaws.com

**Browser Result:** `Hello from Terraform`

This proves end-to-end connectivity: **Internet -> ALB (sg-057c5bdae31d46e13) -> Target Group example-tg -> EC2 i-0daa70d8e49f0a2a5 (51.21.134.55) -> Nginx**

![Final Validation](<img width="1366" height="691" alt="10-alb dns" src="https://github.com/user-attachments/assets/4c274cbf-e459-472c-bd26-17de80107cf1" />
)

## 🚀 Deployment

```bash
terraform init
terraform plan
terraform apply -auto-approve
# Test: curl http://example-lb-514878212.eu-north-1.elb.amazonaws.com
terraform destroy -auto-approve
