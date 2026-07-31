---
title: "Prepare the VPC and Security Groups"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Preparation objective

Create the network foundation for the application: two public subnets for the ALB and two private subnets for ECS Fargate and RDS PostgreSQL. The subnets span multiple Availability Zones for availability.

#### Prerequisites

Prepare the AWS CLI, Docker, Git, and the frontend build toolchain before deployment. Select one AWS Region and use it consistently for the VPC, ECR, ECS, ALB, RDS, S3, and CloudFront resources.

#### Network components

* **Public subnets:** Host the Application Load Balancer and route to an Internet Gateway.
* **Private subnets:** Host ECS tasks and the RDS instance; the database has no public IP.
* **Security groups:** Allow traffic only along the CloudFront → ALB → ECS → RDS path.

#### Deployed network settings

| Resource | Name or CIDR | Availability Zone |
|---|---|---|
| VPC | `shopsflow-vpc` | `us-east-1` |
| Public subnet 1 | `aws-practice-vpc-subnet-public1-us-east-1a` — `10.0.0.0/20` | `us-east-1a` |
| Public subnet 2 | `aws-practice-vpc-subnet-public2-us-east-1b` — `10.0.16.0/20` | `us-east-1b` |
| Private subnet 1 | `aws-practice-vpc-subnet-private1-us-east-1a` — `10.0.128.0/20` | `us-east-1a` |
| Private subnet 2 | `aws-practice-vpc-subnet-private2-us-east-1b` — `10.0.144.0/20` | `us-east-1b` |

#### Configure Internet access and routes

1. Create `shopsflow-igw` and attach it to `shopsflow-vpc`.
2. Create a public route table with `0.0.0.0/0` routed to the Internet Gateway, then associate both public subnets.
3. Create `shopsflow-nat-a` in public subnet 1 and `shopsflow-nat-b` in public subnet 2, with one Elastic IP address for each NAT Gateway.
4. Route private subnet 1 through `shopsflow-nat-a` and private subnet 2 through `shopsflow-nat-b` when ECS tasks need outbound access, for example to call the payment provider.

The ALB remains the only inbound public component. ECS tasks and RDS stay private even when the tasks use the NAT Gateway for outbound traffic.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)
*Figure 1. VPC architecture for the workshop environment.*

![Public subnet 1](/FCAJ-2312188/images/5-Workshop/public_subnet_1.jpg?featherlight=false)
*Figure 2. First public subnet for the internet-facing ALB.*

![Public subnet 2](/FCAJ-2312188/images/5-Workshop/public_subnet_2.jpg?featherlight=false)
*Figure 3. Second public subnet for high availability.*

![Private subnet 1](/FCAJ-2312188/images/5-Workshop/private_subnet_1.jpg?featherlight=false)
*Figure 4. First private subnet for ECS tasks and RDS.*

![Private subnet 2](/FCAJ-2312188/images/5-Workshop/private_subnet_2.jpg?featherlight=false)
*Figure 5. Second private subnet for high availability.*

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)
*Figure 6. Security groups.*

#### Security principles

* The ALB security group opens only the application ports required from the Internet or CloudFront.
* The ECS security group allows inbound traffic only from the ALB security group.
* The RDS security group allows PostgreSQL port `5432` only from the ECS security group.

#### Checkpoint

Before deploying the application, confirm that public subnets route through the Internet Gateway, private subnets have the required NAT route, and the traffic path is restricted to ALB → ECS → RDS.

#### IAM roles for ECS

Create two separate roles before creating the ECS task definition:

* **Task execution role:** pulls the backend image from ECR and delivers container logs to CloudWatch.
* **Task role:** contains only permissions required by the application code inside the container.

Keep these roles separate and do not place static AWS access keys inside the container.
