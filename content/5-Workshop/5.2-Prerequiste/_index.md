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

#### Configure Internet access and routes

1. Create and attach an Internet Gateway to the VPC.
2. Create a public route table with `0.0.0.0/0` routed to the Internet Gateway, then associate both public subnets.
3. For high availability, create one NAT Gateway and associate one Elastic IP address in each public-subnet Availability Zone.
4. Route each private subnet's Internet-bound traffic through the NAT Gateway in the same Availability Zone when ECS tasks need outbound access, for example to call an external service. A single NAT Gateway is acceptable for a cost-sensitive lab, but it is a deliberate availability trade-off.

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
*Figure 6. Security group rules between ALB, ECS, and RDS.*

#### Security principles

* The ALB security group opens only the application ports required from the Internet or CloudFront.
* The ECS security group allows inbound traffic only from the ALB security group.
* The RDS security group allows PostgreSQL port `5432` only from the ECS security group.

#### Checkpoint

Before deploying the application, confirm that public subnets route through the Internet Gateway, private subnets have the required NAT route, and the traffic path is restricted to ALB → ECS → RDS.
