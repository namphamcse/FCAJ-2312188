---
title: "Prepare the VPC and Security Groups"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Preparation objective

Create the network foundation for the application: two public subnets for the ALB and two private subnets for ECS Fargate and RDS PostgreSQL. The subnets span multiple Availability Zones for availability.

#### Network components

* **Public subnets:** Host the Application Load Balancer and route to an Internet Gateway.
* **Private subnets:** Host ECS tasks and the RDS instance; the database has no public IP.
* **Security groups:** Allow traffic only along the CloudFront → ALB → ECS → RDS path.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)

![Public subnet 1](/FCAJ-2312188/images/5-Workshop/public_subnet_1.jpg?featherlight=false)

![Public subnet 2](/FCAJ-2312188/images/5-Workshop/public_subnet_2.jpg?featherlight=false)

![Private subnet 1](/FCAJ-2312188/images/5-Workshop/private_subnet_1.jpg?featherlight=false)

![Private subnet 2](/FCAJ-2312188/images/5-Workshop/private_subnet_2.jpg?featherlight=false)

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)

#### Security principles

* The ALB security group opens only the application ports required from the Internet or CloudFront.
* The ECS security group allows inbound traffic only from the ALB security group.
* The RDS security group allows PostgreSQL port `5432` only from the ECS security group.
