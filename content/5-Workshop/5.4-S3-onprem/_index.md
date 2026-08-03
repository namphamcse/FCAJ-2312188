---
title: "Deploy the Backend with ALB, ECS Fargate, and RDS PostgreSQL"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Overview

The backend is packaged as a Docker image, stored in Amazon ECR, and run by an ECS Fargate service. The ALB runs in public subnets to receive API requests and sends them to ECS tasks in private subnets. The backend connects to RDS PostgreSQL on port `5432`.

#### Contents

1. [Prepare the network and security groups](5.4.1-prepare/)
2. [Create the ECR repository and ECS task definition](5.4.2-create-interface-enpoint/)
3. [Create RDS PostgreSQL](5.4.3-test-endpoint/)
4. [Configure the Application Load Balancer](5.4.4-dns-simulation/)
5. [Deploy the ECS Fargate service](5.4.5-deploy-ecs/)
