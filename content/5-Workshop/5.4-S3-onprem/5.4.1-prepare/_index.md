---
title: "Prepare the Network and Security Groups"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Network placement

The ALB is placed in public subnets to receive Internet traffic. ECS Fargate tasks and RDS PostgreSQL are placed in private subnets. Only the ALB is publicly reachable; ECS and RDS do not accept direct Internet access.

#### Security group rules

* **`alb-sg`:** allows HTTP `80` and HTTPS `443` from users or CloudFront; sends TCP `8080` only to `ecs-sg`.
* **`ecs-sg`:** allows TCP `8080` only from `alb-sg`; allows PostgreSQL `5432` to the database tier and HTTPS outbound for AWS or payment endpoints.
* **`rds-sg`:** allows TCP `5432` only from `ecs-sg`.

Use security-group references rather than CIDR ranges for the ECS and RDS inbound rules. The deployed application uses ALB listener ports `80` and `443`, backend port `8080`, and PostgreSQL port `5432`.

![ALB security group](/FCAJ-2312188/images/5-Workshop/alb_sg_inbound_rules.jpg?featherlight=false)
*Figure 1. Inbound rules for the Application Load Balancer security group.*

![ECS security group](/FCAJ-2312188/images/5-Workshop/ecs_sg_inbound_rules.jpg?featherlight=false)
*Figure 2. Inbound rules for the ECS service security group.*

![RDS security group](/FCAJ-2312188/images/5-Workshop/rds_sg_inbound_rules.jpg?featherlight=false)
*Figure 3. Inbound rules for the RDS PostgreSQL security group.*

#### Verify the network boundary

Confirm that the ECS task has no public IP, the ALB is associated with both public subnets, and the RDS instance is associated with private subnets. An external client must not be able to connect directly to the ECS container or PostgreSQL database.
