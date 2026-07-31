---
title: "Configure the ALB and Test the Backend"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Create the Application Load Balancer

Create an internet-facing ALB across two public subnets. Create an HTTP or HTTPS listener, an IP target group for ECS Fargate, and a backend health-check endpoint. Attach the ECS service to the target group so tasks register automatically.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)

#### Test the backend

1. Confirm that targets in the target group are healthy.
2. Call the API through the ALB DNS name and check the backend response.
3. Check ECS task logs if a health check or request fails.

The ALB is the backend entry point; the frontend should use the ALB DNS name or a custom API domain to call the API.
