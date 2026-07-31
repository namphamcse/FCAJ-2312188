---
title: "Configure the ALB and Test the Backend"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Create the Application Load Balancer

Create the internet-facing ALB `shopsflow-alb` across the two public subnets. Create an HTTP `80` listener (and HTTPS `443` when a certificate is configured), an IP target group on port `8080` for ECS Fargate, and a backend health-check endpoint. Attach the ECS service to the target group so tasks register automatically.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)

#### Test the backend

1. Confirm that targets in the target group are healthy.
2. Call the API through the ALB DNS name and check the backend response.
3. Check ECS task logs if a health check or request fails.

The ALB is the backend entry point. Direct ALB calls are useful for testing; the public frontend calls `/api/*` through the CloudFront behavior configured in section 5.3.2.

If a target is unhealthy, first confirm the health-check path and port, then inspect the ECS task stopped reason and application logs. This separates load-balancer routing issues from container startup or database-connection issues.
