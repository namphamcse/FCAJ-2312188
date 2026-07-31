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

* **ALB SG:** allows HTTP/HTTPS from users or CloudFront.
* **ECS SG:** allows the application port only from the ALB SG.
* **RDS SG:** allows TCP `5432` only from the ECS SG.

![ALB security group](/FCAJ-2312188/images/5-Workshop/alb_sg_inbound_rules.jpg?featherlight=false)

![ECS security group](/FCAJ-2312188/images/5-Workshop/ecs_sg_inbound_rules.jpg?featherlight=false)

![RDS security group](/FCAJ-2312188/images/5-Workshop/rds_sg_inbound_rules.jpg?featherlight=false)
