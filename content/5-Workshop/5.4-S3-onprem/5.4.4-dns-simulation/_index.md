---
title: "Configure the Application Load Balancer"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Create the Application Load Balancer

Create the internet-facing ALB `shopsflow-alb` across the two public subnets. Add an HTTP listener on port `80` (and HTTPS on `443` once a certificate is configured), create an IP target group on port `8080` for ECS Fargate, then configure the backend health-check endpoint. Attach the ECS service to the target group so its tasks register automatically.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)
